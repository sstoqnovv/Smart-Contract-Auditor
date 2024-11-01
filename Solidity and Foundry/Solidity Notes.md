`^` symbol means "compatible with version 0.8.0 or any later version up to, but not including, version 0.9.0." 
****
`uint` - the bigger the `uint` is - the bigger the number is; `uint256` = `uint(default)`. `uint` can't be negative or decimal number, unlike normal `int`.
****
The address type comes in two largely identical flavors:

- `address`: Holds a 20 byte value (size of an Ethereum address).
- `address payable`: Same as `address`, but with the additional members `transfer` and `send`;  address you can send Ether to.
****
```php
contract SimpleStorage {
    uint256 public favoriteNumber;

    function store(uint256 _favoriteNumber) public {
        favoriteNumber = _favoriteNumber; // updating state variable - it costs gas

    }
```

Updating the state - it costs gas - 27, 000. 
****
`bytes32` - hexes the information. 
Example: `bytes32="cat";` will look like something as `0x9qmsa989wdjs9d...`
****
```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.18;

import "./SimpleStorage.sol"; // instead of copy-paste the file here.

contract StorageFactory {
    SimpleStorage public simpleStorage; // `contract` creates a new type as `struct` doest
	    function createSimpleStorageContract() public {
        // uint256 public FavoriteNumber
        // type  visibility  name
        simpleStorage = new SimpleStorage(); // deploy a new contract from within a contract
    }
}
```

As a `struct` does, we can create a new data type with `contract` keyword. We create variable with reference to other contract and then create and deploy this contract using `new` keyword.
****
```php
(bool callSuccess, bytes memory dataReturned) = payable(msg.sender).call{value:address(this).balance}(""); //("") here we call function usually. // If we call any function and he send some data it's saved in `bytes dataReturned`
```
****
```php
    modifier onlyOwner{
        require(msg.sender == owner, "Sender is not owner!");
        _; // when modifier is in function declaration, firstly the modifier have been executed and then the function
    }
}
```
****
Custom errors save gas than `require() or revert()`
****
