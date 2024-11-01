In Solidity, `require()`, `revert()`, and `assert()` are control flow mechanisms used to handle conditions that, when not met, trigger exceptional behavior. **Reverts undo any action that have been done, and send the remaining gas back.**

1. **`require(condition, message)`**:
    - Purpose: Used to check conditions and revert the entire transaction if the condition is not met.
    - When to use: Use `require` for input validation, ensuring that certain conditions are met before proceeding with the execution of the contract.
    
    Example:
    
```php
function withdraw(uint amount) public {
	require(amount <= balance, "Insufficient balance");     // Continue with the withdrawal logic }
```

2. **`revert()`**:
    - Purpose: Used to revert the transaction and provide an optional error message.
    - When to use: Use `revert` when you want to explicitly revert the transaction with a custom error message.
    
    Example:
    
```php
function requirePositive(uint number) public pure returns (uint) {
	if (number <= 0) {
	         revert("Number must be positive");     
	         }     
	return number; }
```
    
3. **`assert(condition)`**:
    - Purpose: Used for internal errors and to check conditions that should always be true. If the condition is false, it indicates a bug in the contract.
    - When to use: Use `assert` for conditions that should never be false unless there is a bug in the contract.
    
    Example:
    
```php
function add(uint a, uint b) public pure returns (uint) {
	uint result = a + b;     
	assert(result >= a); // Ensure no overflow     return result; }`
```

```php
contract Token {
    mapping(address => uint) public balanceOf;
    uint public totalSupply;

    function deposit() public payable {
        balanceOf[msg.sender] += msg.value;
        totalSupply += msg.value;
        assert(address(this).balance >= totalSupply); // this must be right
    }
}
```
It's important to note that `require` and `revert` are often used for different purposes in normal contract execution, while `assert` is primarily used for catching bugs during development and testing.
****
Use `require()` to:
 - Validate user inputs
 - Validate the response from an external contract
 - Validate state condition prior to execution
 - Generally, you should use `require()` most often
The [**require** function should be used to ensure valid conditions](https://docs.soliditylang.org/en/latest/control-structures.html#panic-via-assert-and-error-via-require), such as inputs, or contract state variables are met, or to validate return values from calls to external contracts.

Use `revert()` to:
 - Handle the same type of situation as `require()`, but with more complex logic

Use `assert()` to:
 - Check for over/underflow
 - Check Invariants
 - Validate state after making changes
 - Prevent conditions which should never ever be possible
****
**Assert Violation** - it's suggested to replace it with `require()`
`assert()` is meant to assert invariants. Properly functioning code should never reach a failing assert statement. Failing assertion can mean one of 2 things:
- A bug exist in the contract that allows it to enter an invalid state
- `assert` statement is used incorrectly, e.g. validate inputs
- `assert` is bodyguard that protects your SC, but steal your gas in the process

```php - Usage of assert() and require()
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.5.0 <0.9.0;

contract Sharer {
    function sendHalf(address payable addr) public payable returns (uint balance) {
        require(msg.value % 2 == 0, "Even value required.");
        uint balanceBeforeTransfer = address(this).balance;
        addr.transfer(msg.value / 2);
        // Since transfer throws an exception on failure and
        // cannot call back here, there should be no way for us to
        // still have half of the Ether.
        assert(address(this).balance == balanceBeforeTransfer - msg.value / 2);
        return address(this).balance;
    }
}
```

> **Custom errors are cheaper from require() statement!**
