Inheritance in Solidity is a feature that allows one contract to inherit the properties and methods of another contract.
In Solidity, a contract can inherit from another contract, and the contract that is being inherited from is called the base contract, while the contract that inherits is called the derived contract

```php
pragma solidity ^0.8.0;

// Base contract A
contract A {
    string public baseData = "Base data";

    function getBaseData() public view returns (string memory) {
        return baseData;
    }
}

// Derived contract B inherits from base contract A
contract B is A {
    string public derivedData = "Derived data";

    function getDerivedData() public view returns (string memory) {
        return derivedData;
    }
}

```
In this example, contract `B` inherits from contract `A`. Contract `B` can access the `baseData` state variable and the `getBaseData` function from contract `A`. It can also define its own state variable and function, as shown with `derivedData` and `getDerivedData`.
****
### `override` and `virtual`

Solidity allows function overriding, which means a derived contract can redefine a function that is already defined in the base contract. The function signature must remain the same, including the number and types of parameters and the return type. If there is a difference in output parameters, the compilation will fail.

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
#### Multiple Inheritance

Solidity supports multiple inheritance, where a contract can inherit from more than one base contract. In the case of multiple inheritance, function calls using `super` give preference to the most derived contract.

Multiple inheritance in Solidity allows a single contract to inherit from multiple contracts simultaneously. This is achieved by using the `is` keyword and declaring functions as `virtual` or overriding them with the `override` keyword. 
Solidity uses **C3 linearization** to resolve multiple inheritance, which means that the order in which the base contracts are listed in the derived contract's declaration determines the order in which they are linearized and their functions are resolved.

```php
pragma solidity ^0.8.0;

// Base contract A
contract A {
    function foo() public virtual returns (string memory) {
        return "A";
    }
}

// Base contract B
contract B {
    function foo() public virtual returns (string memory) {
        return "B";
    }
}

// Derived contract C inherits from A and B
contract C is A, B {
    function foo() public override(A, B) returns (string memory) {
        return super.foo();
    }
}
```
In this example, contract `C` inherits from both contracts `A` and `B`. Both `A` and `B` have a function named `foo` that returns a string. In contract `C`, the `foo` function is overridden to call the `foo` function of the most derived contract in the linearization order. In this case, since `B` is listed last in the inheritance list, the `foo` function of `B` is called, and the output would be "B".

> However, it's important to note that Solidity does not support multiple inheritance if the base contracts have functions with the same name and same arguments, regardless of whether they're private or public. This is because Solidity cannot determine which function to use in the derived contract, leading to a compilation error.