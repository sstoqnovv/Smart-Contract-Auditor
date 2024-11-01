We have:
 - Fixed-size arrays
 - Dynamic arrays

They are essential in SC development as they enable developers to store and manipulate multiple data elements in Solidity.

#### Fixed-size arrays
Have predefined length, which must be specified at the time of declaration

```php
uint256[10] public fixedArray; // declare fixed array, with 10 elements, which are uint256 data type
```

> We can't push or pop elements in fixed arrays, but we can modify the elements within it.
#### Dynamic arrays
They can change in size needed during the execution of the SC

```php
uint256[] public dynamicArray; // declare dynamic array, which elements are uint256 data type
```

#### Memory arrays
Have a temporary existence and are limited to the scope of a function execution. After the function is executed, the memory array's data is no longer accessible and any changes made to the data aren't persistent on the blockchain. Memory arrays offer fast data nad lower gas cost.

#### Storage arrays
Persistent on the blockchain, allowing access to their data for the entire duration of the SC existence. It's increased in gas cost.
****
### Methods

```php
function addElementToDynamicArray(uint256 _newElement) external {
	dynamicArray.push(_newElement); // append _newElement to the end of the array
}
```

```php
function addElementToDynamicArray() external {
	dynamicArray.pop(); // pop the last element
}
```
```php
function examples() external {
	// Let's say we have an array nums - [1, 2, 3]
	uint x = nums[1]; // 2 - get the index from an array
	nums[2] = 777; // [1, 2, 777, 4] - update
	delete nums[1]; // [1, 0, 777, 4] - delete/doesn't remove the number, but reset it to the default value of the uint, which is 0
	uint len = nums.length; // return the array's length
}
```

> If we try to pop an element from an array that hasn't elements in it, it will get a panic error. 

Returning array from funciton
```php
function returnArray() external view returns(uint[] memory){
	return nums;
}
```
***
