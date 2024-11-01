In Solidity, the `constant` keyword is used to declare functions that do not modify the state of the blockchain. 
> However, it's important to note that in more recent versions of Solidity, the `constant` keyword has been deprecated in favor of the `view` and `pure` modifiers.

Constants are often used to read data from the blockchain without making any changes. For example, a function that returns the current block number or a fixed configuration parameter might be declared as constant.
Constants are values that can't change at runtime. They're computed at compile time and can't change the runtime.

> State variables can be declared as `constant` or `immutable`. In both cases, the variables cannot be modified after the contract has been constructed.
 
Use of Constants:
	- Use constants for known values like math constants(e.g. PI=3,14), conversion rates, or fixed configuration values
	- **When you use a `constant` keyword the variable don't take storage spot(place) anymore.
 
 
```php
function getBlockNumber() public view returns (uint) { // mark as view/constant - block.number won't chnage
    return block.number;
}```

 ```php
 // SPDX-License-Identifier: GPL-3.0
pragma solidity ^0.8.21;

uint constant X = 32**22 + 8;

contract C {
    string constant TEXT = "abc";
    bytes32 constant MY_HASH = keccak256("abc");
    uint immutable decimals = 18;
    uint immutable maxBalance;
    address immutable owner = msg.sender;
```

In Solidity, the `immutable` keyword is used to declare variables that are constant and immutable at the time of deployment. These variables cannot be changed **after deployment**.

Immutables are often used for storing configuration parameters or values that need to be set at the time of contract deployment and remain fixed throughout the contract's lifecycle.
They can be compiled at deployment, but never change afterward.

> The value can be changed at any time **before deployment** and then it becomes permanent.
 
Use of Immutables:
	- Perfect for dynamic values set during contract creation, like contract addresses or user-defined parameters.
	- Great for configuration values, that shouldn't change post deployment.
 
```php
// Example of an immutable variable
// This value is set at the time of deployment and cannot be changed.
uint immutable myImmutableValue = 42;
```

**Both save gas!**