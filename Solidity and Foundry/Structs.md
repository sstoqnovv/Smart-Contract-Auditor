Struct is a user-defined data type that allows you to group different data types together under a single name. It's like a class in other programming languages.
Structs allows you to create more complicated data types that have multiple properties.

```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract StructExample {
    // Define a struct named 'Person'
    struct Person {
        string name;
        uint256 age;
    }

    // Declare a state variable of type 'Person'
    Person public myPerson;

    // Function to set the values of the 'myPerson' struct
    function setPerson(string memory _name, uint256 _age) public {
        // Create a new 'Person' struct instance and assign values
        myPerson = Person(_name, _age);
    }

    // Function to get the values of the 'myPerson' struct
    function getPerson() public view returns (string memory, uint256) {
        // Return the values of the 'myPerson' struct
        return (myPerson.name, myPerson.age);
    }

	function example() external {
		Person memory michael = Person('Michael', 18); // First way
		Person memory steve = Person({name:'Steve', age: 19}); // Second way
		}
}
```