The exciting thing is, though the code deployed to an address is immutable, there's still room for change. In fact, smart contracts update all the time. Think token transfers or any functionality really—they frequently update their balances or variables. In other words, while the logic remains unchangeable, the contract's state changes dynamically.

### Proxies

The idea is to separate the contract's logic (implementation) from its storage (state). The **proxy contract** holds the storage and delegates execution to an **implementation contract** using `delegatecall`.

`delegatecall` - low-level function, where the code in the target contract is executed in the context of the calling contract and `msg.value` and `msg.sender` don't change.
When a contract `A` `delegatecall` to contract `B`, B`s` code is executed with contract A`s` storage, `msg.sender` and `msg.value`.
**`DELEGATECALL` basically says that I'm a contract and I'm allowing (delegating) _you_ to do whatever you want to _my_ storage**

When writing upgradeable contracts, it's important to be aware of certain limitations. **For example, the storage layout of a contract cannot be changed during an upgrade. 
This means that if a state variable has already been declared, it cannot be removed or its type changed. 
Functions and events can be altered freely, and any changes to the storage layout will be flagged by the Upgrades Plugins.**

> State variables(and storage as all) must be declared in the same way in both contracts(Implementations), to prevent from storage collision.
> The storage variables of Upgradable contracts can't be deleted(immutable), however, new storage variables might be added.

![[Pasted image 20240216070532.png]]
Here User call Proxy contract and Proxy's `fallback()` is called. Proxy has itself a `upgradeTo(newImplementation)` function that inly admin can call.
****
#### Proxy Terminology

1. The Implementation Contract
	- Which has all our code/logic of our protocol. When we upgrade, we launch a brand new implementation contract.
2. The Proxy contract
	- Which points to which implementation is the "correct" one, and routes everyone's function calls to that contract. The proxy contract is the entry point for interacting with the upgradable contract system. It delegates calls to the current implementation contract.
3. The user
	- They make calls to the proxy
4. The owner
	- This is the user(or group of users/voters) who upgrade to new implementation contracts. They decide when or will upgrade the contract. It has the authority to change the address of the implementation contract stored in the proxy contract.

**Proxy -> Implementation ||| -> means `delegatecall`**

User ---- tx ---> Proxy ----------> Implementation_v0
                     |
                      ------------> Implementation_v1
                     |
                      ------------> Implementation_v2

> In Upgreadable SC we don't have constructor, we have `Initialize`-tor. Reason we don't use constructors is that we set variables in the Implementation(which is wrong, usage of storage is used in Proxy).

```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.26;

// When contract A executes delegatecall to contract B, B's code is executed
// with contract A's storage, msg.sender and msg.value. So we modify contract A's storage

// NOTE: Deploy this contract first
contract B {
    uint256 public num;
    address public sender;
    uint256 public value;

    function setVars(uint256 _num) public payable {
        num = _num;            
        sender = msg.sender;   
        value = msg.value;     
    }
}

// Contract A uses delegatecall to execute the logic of contract B, but modifies its own storage.
// Contract A's storage layout must match contract B's to ensure correct behavior.
contract A {
    uint256 public num;
    address public sender;
    uint256 public value;

    // Function to call contract B's logic using delegatecall
    // This function will delegate the execution of setVars from B, but it will modify contract A's storage instead of B's
    function setVars(address _contract, uint256 _num) public payable {
        (bool success, bytes memory data) = _contract.delegatecall(
            abi.encodeWithSignature("setVars(uint256)", _num)  // Encodes the function signature and argument
        );
        require(success, "Delegatecall failed");
    }
}
```
****
#### Universal Upgradable Proxy Standard(UUPS) Proxies

Upgrade functions are in the implementation contracts instead of the proxy.UUPS proxies are considered more **gas-efficient** because the proxy contract only needs to store the logic address and manage delegating calls, without maintaining upgrade functionality.

The proxy contract holds the state and delegate calls to the logic contract. When the logic needs to be upgraded, the proxy simply points to the new logic contract, preserving the state of the original contract

#### Transparent Proxy Pattern

Admins can't call implementation contract functions. Admin functions are functions that govern the upgrades. Users still powerless on admin functions. If you are a user you call implementation functions.
****
#### Differences between Transparent Proxy Pattern and UUPS

| Feature                       | **UUPS Proxy**                                             | **Transparent Proxy**                               |
| ----------------------------- | ---------------------------------------------------------- | --------------------------------------------------- |
| **Upgrade Logic Location**    | In the **implementation** contract                         | In the **proxy** contract                           |
| **Gas Efficiency**            | More gas-efficient                                         | Less gas-efficient                                  |
| **Upgrade Control**           | Managed by the **implementation** contract                 | Managed by the **proxy** contract                   |
| **Admin Access**              | Admins directly access upgrade logic in the implementation | Admin cannot call user functions                    |
| **Proxy Complexity**          | Simpler proxy (only stores implementation address)         | More complex proxy (manages upgrades)               |
| **Risk of Breaking Upgrades** | Higher risk if the implementation upgrade logic is removed | Lower risk (upgrade logic always in the proxy)      |
| **Call Transparency**         | Admin and users interact with the same contract            | Admin functions separated from user functions       |
| **Use Case**                  | DeFi protocols, gas-sensitive apps                         | General upgradable contracts, admin-user separation |
****
### Storage Collision 

1. **Storage Collision**
	- Happens when the storage slot layout in the Implementation contract doesn't match the storage layout in the proxy contract.

**Problem**: When using proxy contracts, a common issue arises with the way variables are stored in the proxy contract. Suppose the proxy contract stores the address of the logic contract (implementation) in its only variable `_implementation`. Now, let's say the logic contract has a variable `_owner` as its first variable. Both `_implementation` and `_owner` are 32 bytes in size and occupy the same slot in the storage layout. When the logic contract writes to `_owner`, it inadvertently writes to the same storage slot occupied by `_implementation`, causing what's known as a "storage collision."

| Proxy | Implementation |  |
| ---- | ---- | ---- |
| address _implementation | address _owner | <=== Storage collision! |
| ... | mapping _balances |  |
|  | uint256 _supply |  |
**Solution - Unstructured Storage Approach**: OpenZeppelin Upgrades implements an approach called "unstructured storage" to overcome this problem. Instead of storing the `_implementation` address at the proxy’s first storage slot, it chooses a pseudo-random slot instead. This slot is selected in such a way that the likelihood of a logic contract declaring a variable at the same slot is extremely low.

| Proxy | Implementation |  |
| ---- | ---- | ---- |
| ... | address _owner |  |
| ... | mapping _balances |  |
| ... | uint256 _supply |  |
| ... | ... |  |
| ... |  |  |
| ... |  |  |
| ... |  |  |
| ... |  |  |
| address _implementation |  | <=== Randomized slot. |
| ... |  |  |
| ... |  |  |
### Function Selector Clashing 

2. **Function Clashing**
	- **Function selector** - first 4 byte hash of a function name and signature that define a function.
	- The way OpenZeppelin Upgrades deals with this problem is via the _transparent proxy_ pattern by:
		- If the caller is the admin of the proxy, then the proxy will **not** delegate any calls, and only answer any messages it understands.
		- If the caller is any other address, the proxy will **always** delegate a call, no matter if it matches one of the proxy’s functions.

**Example:**
```php
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.19;

import "@openzeppelin/contracts/proxy/Proxy.sol";

contract SmallProxy is Proxy {
    // This is the keccak-256 hash of "eip1967.proxy.implementation" subtracted by 1
    bytes32 private constant _IMPLEMENTATION_SLOT = 0x360894a13ba1a3210667c828492db98dca3e2076cc3735a920a3ca505d382bbc;

    function setImplementation(address newImplementation) public {
        assembly {
            sstore(_IMPLEMENTATION_SLOT, newImplementation)
        }
    }

    function _implementation() internal view override returns (address implementationAddress) {
        assembly {
            implementationAddress := sload(_IMPLEMENTATION_SLOT)
        }
    }

    // helper function
    function getDataToTransact(uint256 numberToUpdate) public pure returns (bytes memory) {
        return abi.encodeWithSignature("setValue(uint256)", numberToUpdate);
    }

    function readStorage() public view returns (uint256 valueAtStorageSlotZero) {
        assembly {
            valueAtStorageSlotZero := sload(0)
        }
    }
}

contract ImplementationA {
    uint256 public value;

    function setValue(uint256 newValue) public {
        value = newValue;
    }
}

contract ImplementationB {
    uint256 public value;

    function setValue(uint256 newValue) public {
        value = newValue + 2;
    }
}
```

We deploy both contract in Remix. We get the Implementation address and paste in `setImplementation` of the Proxy contract. Then we call `getDataToTransact()` with number `777`, but in this function we call `setValue()` which the EVM doesn't regognize and invoke `fallback()` function which is derived from `Proxy.sol` of OpenZeppelin, which on other hand delegate the calls to the Implementation contract. `getDataToTransact` returns bytes level encoded data and we copy it and pasted in `calldata` section in Remix and hit `Transact` button. Then we hit the button of `readStorage` function, which in assembly level reads the storage and we get `valueAtStorageSlotZero: 777` as an output.
Let's say then we deploy `ImplementationB` and get the address and paste in `setImplementation` of the Proxy contract. We have now upgraded from `ImplementationA` to `ImplementationB`. Now if we use the same data that returns `bytes` data from the function `getDataToTransact()`, it will still get call `setValue()` in `ImplementationB`, but now, we're going to delegate call to `ImplementationB`. Then again put this data in `calldata` section in Remix and hit `Transact` button, then check again `readStorage`, we will get as an output `779`, because it's `+2`.