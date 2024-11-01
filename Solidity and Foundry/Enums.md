Enums, short for enumerations, are user-defined data types in Solidity used to create a set of named constants, also known as enumerators. Enums allow you to define a variable that can only take one of a set of predefined values.

```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract ExampleEnum {
    // Define an enum named State with three possible values: Pending, Approved, and Rejected
    enum State { Pending, Approved, Rejected }

    // Declare a State variable
    State public currentState;

    // Function to update the state
    function updateState(State _newState) public {
        // Assign the new state
        currentState = _newState;
    }

    // Function to get the current state as a string
    function getStateAsString() public view returns (string memory) {
        // Convert the enum value to a string representation
        if (currentState == State.Pending) {
            return "Pending";
        } else if (currentState == State.Approved) {
            return "Approved";
        } else if (currentState == State.Rejected) {
            return "Rejected";
        }
    }
}

```