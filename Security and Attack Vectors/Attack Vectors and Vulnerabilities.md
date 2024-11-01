 ****
**External calls**
Reentrancy can be executed by the availability of an external call to an attacker controlled contract.
A reentrancy attack can occur when create a function that wakes an external call to another untrusted contract before it resolves any effect. 
If the vulnerable contract transfers funds before it sets the balance to zero, the attacker can recurively call the withdraw function  repeatedly and drain the whole contract.
 
All an attacker needs to exploit the function is to get some amount of balance mapped to their SC address and create a fallback function that calls withdraw. After `msg.sender.call.value(amount)()` transfers the correct amount of funds the attacker's fallback function calls `withdraw` again, transferring more funds before `balances [msg.sender] = 0` can stop further transfers. This continues until there is either no ETH remaining or execution reaches the maximum stack size.

```php
function withdraw() external {
	uint256 amount = balances[msg.sender];
	require(msg.sender.call.value(amount)());
	balances[msg.sender] = 0;
}
```
 ****
**send, transfer and call**
 
- `send` and `transfer` functions are considered safer because they're limited to 2 300 gas. The gas limit prevents the expensive function calls back to the target contract.
- `call` - is much more vulnerable. It forwards all remaining gas. Whenever possible, use `send` or `transfer` in place of `call`.
 ****
**Unsafe Low-Level Call**
Low-level calls such as: `address.call(), address.callcode(), address.delegatecall(), address.send()`.
 ****
**Uninitialized Storage Pointer**
 
Data is stored in the EVM as either `storage, memory, or call data`.
 
**As of Solidity 0.5.0, uninitialized storage pointers are no longer an issue, since contracts with uninitialized storage pointers will no longer compile.**
 
**SWC-109** - Uninitialized local storage variables can point to unexpected storage locations in the contract, which can lead to intention/unintentional vulnerabilities.
 
**If a local variable is sufficient, mark the storage location of the variable explicity with the `memory` attribute. If a storage variable is needed then initialize it upon declaration and additionally specify the storage location `storage`.**
 ****
 **Delegatecall to Untrusted Calles**
 
Identical to a message call apart from the fact that the code at the target address is executed in the context of the calling contract and `msg.sender` and `msg.value` do not change their values.
Use `delegatecall` with caution and make sure to never call into untrusted contracts.
 ****
**Incorrect Constructor Name**
 
Before Solidity 0.4.22, the only way to define a constructor was by creating a function with the contract name. This was problematic!
- Now with modern versions of Solidity, the constructor is defined with the `constructor` keyword, effectively, deprecating this vulnerability. Constructors are special functions that are called only during the contract creation. They offer perform critical, privileged actions, such as setting the owner of the contract.
 ****
**Shadowing State Variables**
 
It's possible to use the same variable twice in Solidity, but it can lead to unintended side effects. This is especially difficult regarding working with multiple contracts.

```php
contract SuperContract{
	uint a = 1;
}

contract SubContract is SuperContract{
	uint a = 2;
}
```
 ****
**Weak Sources of Randomness from Chain Attribute**
 
Using attributes for randomness, e.g.: `block.timestamp, blockhash, block.difficulty, blocknumber` can seem like a good idea since they often produce pseudo-random values. Ethereum, though, it's deterministic and al available on-chain data is public. Chain attributes can either be manipulated/predicted and should thus never be used for random number generation.
 
A common solution is to use an oracle such as Chainlink VRF.
****
**Incorrect Inheritance Order**
	In Solidity, it's possible to inherit from multiple sources, which if not properly understood can introduce ambiguity(неяснота). This ambiguity is known as the ***Diamond problem***, where in two base contracts have the same function, which one should be prioritized?
**Luckily Solidity handles this problem gracefully, that is as long as the devloper understands the solution. The solution is to use C3 linearize the inheritance from right to left , so the order of Inheritance matters.**
**It's suggested to start with more general contract and end with more specific contracts to avoid problems.**
****
**Presence of Unused Variables**
	Although, it's allowed, it's best practice to avoid unused variables. They can lead to few problems:
	 - Increase in computations(unnecessary gas consumption)
	 - Indification of bugs
	 - Decrease code readability
- It's slightly recommended to remove all unused variables from the codebase.
****
**Assetring contract from Code Size**
	A common method for asserting whether a sender is a contract or an EoA has been to check the codesize of the sender. This check asserts that if the sender has a `codesize > 0` that it must be a contract and if not it must be an EoA.

```php
function mint(uint256 amount) public {
	if msg.sender.code.length != 0) 
	revert CallerNotEOA();
}
```
As we can see, it's important that we recognize that although we many be certain that an account with a non-zero codesize is a contract, we can't be sure that an account with a zero codesize isn't a contract.
****
**Transaction-Ordering Dependence**

Transactions on Ethereum are grouped together in blocks which are processed in a semi-regular intervals. Before transactions are placed in blocks, they're broadcasted to the mempool where blockbuilders can then processed to place them as is economically optimal. The mempool is public and thus anyone can see transaction before they're executed, giving them the power to frontrun by placing their own transaction executing the same, or similar action with a higher gas price.

**One solution to transaction-ordering dependence is to use a commit-reveal cheme in the case of information being sumbitted on-chain.**
****
**Unexpected `ecrevover()` Null Address**
	`ecrevover()` - cryptographic function which recovers an address associated with the public key from an ECSDA or returns 0 on error.
	The parameter corresponding to the signature are `r, s` and `v`. `ecrecover` return 0 on error. It's possible to do this deterministically by setting `v` as any positive number other than 27 or 28. `ecrecover` is often used to verify that the signer is an authorized account.
****
**Deafult Visibility**
	Visibility specifiers are used to determine where a function or variable can be accessed from.
		- `public` - visible externally and internally. Creates autmatically getter function
		- `private` - only visible in the current contract
		- `external` - only visible externally
		- `internal` - only visible internally
	**The default visibility is `public`!**
****
**Off-byOne**
A common mistake made by programmers in which the intended boundaries are incorrect by only one.
```php
// Incorrectly sets upper bound to users.length-1
// Final user in array doesn't receive token transfer
for (uint256 i; i<user.length-1;++i) {
	token.transfer(user[i], 1 ether);
}
```
****
**Struct Deletion Oversight**
	If you delete a struct containing a mapping, the mapping won't be deleted.
	The bug arises, because Solidity `delete` keyword doesn't reset the storage to its initial state, but other performs a partial reset.
**Mitigation**:
	To fix this bug, you should delete the mapping inside the struct, before deleting the struct itself. 