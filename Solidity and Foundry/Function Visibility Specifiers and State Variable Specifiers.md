### Function Visibility[](https://docs.soliditylang.org/en/latest/contracts.html#function-visibility "Permalink to this heading")

Solidity knows two kinds of function calls: external ones that do create an actual EVM message call and internal ones that do not. Furthermore, internal functions can be made inaccessible to derived contracts. This gives rise to four types of visibility for functions.

`external` - only visible externally(only for functions) - i.e. can only be message-called(via `this.func`); can be called from other contracts and via transactions. An external function `f` cannot be called internally (i.e. `f()` does not work, but `this.f()` works).

`public` - visible externally and internaly(creates a getter function for storage/state variables). It means that other people can call this function.

`internal` - can only be accessed from within the current contract or contracts deriving from it. They cannot be accessed externally.

`private` - only visible in the current contract. They are not visible in derived contracts.

**Warning: Making something `private` or `internal` only prevents other contracts from reading or modifying the information, but it will still be visible to the whole world outside of the blockchain.**
****
### `view` and `pure`

`view` - it's going to read state from the blockchain. It doesn't modify/update state.

```php
// SPDX-License-Identifier: MIT
pragma solidity 0.8.18; // solidity version.

contract SimpleStorage {
    uint256 public favoriteNumber;
    
    function store(uint256 _favoriteNumber) public {
        favoriteNumber = _favoriteNumber; // updating state variable - it costs gas
    }

    function retrieve() public view returns(uint256) {  // getter of favoriteNumber
        return favoriteNumber; // `view` because it reads `favoriteNumber` variable
    }
}
```

The following statements are considered modifying the state:

1. Writing to state variables.
2. [Emitting events](https://docs.soliditylang.org/en/latest/contracts.html#events).
3. [Creating other contracts](https://docs.soliditylang.org/en/latest/control-structures.html#creating-contracts).
4. Using `selfdestruct`.
5. Sending Ether via calls.
6. Calling any function not marked `view` or `pure`.
7. Using low-level calls.
8. Using inline assembly that contains certain opcodes.

`pure` - disallow updating state and disallow reading state of storage. It only returns.

```php
// SPDX-License-Identifier: MIT
pragma solidity 0.8.18; // solidity version.

contract SimpleStorage {
    uint256 public favoriteNumber;
    
    function store(uint256 _favoriteNumber) public {
        favoriteNumber = _favoriteNumber; // updating state variable - it costs gas
    }

    function retrieve() public pure returns(uint256) {  // getter of favoriteNumber
        return 7; // `pure` because it doesn't read or modify anything. It's just returns 7.
    }
}
```

 The following are considered reading from the state:

1. Reading from state variables.
2. Accessing `address(this).balance` or `<address>.balance`.
3. Accessing any of the members of `block`, `tx`, `msg` (with the exception of `msg.sig` and `msg.data`).
4. Calling any function not marked `pure`.
5. Using inline assembly that contains certain opcodes.
****
### `override` and `virtual`

`virtual` - can be inherited and customized by the child contract
`override` - created by the derived(child) contract

```php
// SPDX-License-Identifier: MIT 
pragma solidity ^0.8.0; 

contract Animal { // Virtual function that can be overridden by derived contracts 
	function makeSound() public virtual pure returns (string memory) { 
		return "Generic animal sound"; } 
		}
		
contract Dog is Animal { // Override the virtual function from the base contract 
	function makeSound() public virtual override pure returns (string memory) { 
		return "Woof!"; } }
```

 > Making something `private` or `internal` only prevents other contracts from reading or modifying the information, but it will still be visible to the whole world outside of the blockchain.
 ****
# State Variable Specifiers

`public` - Public state variables differ from internal ones only in that the compiler automatically generates [getter functions](https://docs.soliditylang.org/en/latest/contracts.html#getter-functions) for them, which allows other contracts to read their values. When used within the same contract, the external access (e.g. `this.x`) invokes the getter while internal access (e.g. `x`) gets the variable value directly from storage. Setter functions are not generated so other contracts cannot directly modify their values.

`internal` - Internal state variables can only be accessed from within the contract they are defined in and in derived contracts. They cannot be accessed externally. This is the default visibility level for state variables.

`private` - Private state variables are like internal ones but they are not visible in derived contracts.

> Making something `private` or `internal` only prevents other contracts from reading or modifying the information, but it will still be visible to the whole world outside of the blockchain.

```php
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.16 <0.9.0;

contract C {
    uint public data = 42;
}

contract Caller {
    C c = new C();
    function f() public view returns (uint) {
        return c.data();
    }
}
```