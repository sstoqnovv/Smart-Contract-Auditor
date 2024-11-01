Here are some characteristics of interfaces in Solidity:

- **No State Variables**: Interfaces cannot have state variables. They are purely a list of function signatures.
- **No Implementation**: Functions in an interface do not have an implementation. They are only declared with their signature.
- **Inheritance**: Contracts can inherit from interfaces, and they must implement all the functions declared in the interface.
- **Override Modifier**: When a contract inherits from an interface, it must use the `override` modifier for all functions that implement an interface function.

Here's an example of how to declare an interface in Solidity:
```php
pragma solidity ^0.8.0;

interface IWallet { // best practice - `I` before any interface contract
    function deposit() external payable;
    function withdraw(uint amount) external;
}
```

And here's an example of a contract that implements this interface:
```php
pragma solidity ^0.8.0;

import "./IWallet.sol";

contract Wallet is IWallet {
    uint public balance;

    function deposit() external payable override {
        balance += msg.value;
    }

    function withdraw(uint amount) external override {
        require(balance >= amount, "Insufficient balance");
        balance -= amount;
        payable(msg.sender).transfer(amount);
    }
}

```