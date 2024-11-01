When the contract receives ETH, it will trigger the `fallback()` or `receive()` to execute the corresponding logic, which is a hidden external call.

**`fallback()` Function:**
- The `fallback()` function is a special function in a Solidity smart contract that is called when a contract receives Ether (ETH) but doesn't match any function signature - basically if there is no function with that name that we want to send money, it triggers `fallback`. This function is used as a fallback mechanism to handle Ether transfers that aren't associated with a specific function call. It can be defined as follows:

```php
fallback() external payable { // Handle Ether received without a specific function call }
```
The `external` keyword indicates that this function can only be called from outside the contract. The `payable` keyword allows the function to receive Ether.

**Fallback function** - a function that has no name or access modifier. It also can't return anything. It's called when no other function is specified in transaction data.
****
**`receive()` Function:**
- The `receive()` function is another special function introduced in Solidity version 0.6.0. It is a more specific fallback function that is automatically called when the contract receives Ether and doesn't contain any data. Its primary purpose is to simplify the handling of Ether transfers. Here's an example:

```php 
receive() external payable { // Handle Ether received without data }
```
****
```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.20;

contract ReceiveEther {
    /*
    Which function is called, fallback() or receive()?

           send Ether
               |
         msg.data is empty?
              / \
            yes  no
            /     \
receive() exists?  fallback()
         /   \
        yes   no
        /      \
    receive()   fallback()
    */
```

> A contract can have at most one `receive` and `fallback`function, declared using `receive() external payable { ... }` (without the `function` keyword); `fallback () external [payable]` or `fallback (bytes calldata input) external [payable] returns (bytes memory output)` (both without the `function` keyword).