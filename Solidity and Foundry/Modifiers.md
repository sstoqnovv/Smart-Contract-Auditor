Modifiers can be used to change the behavior of functions in a declarative way. For example, you can use a modifier to automatically check a condition prior to executing the function.

```php
contract Modifiers {
	error NotOwner(address _msg.sender);

	address owner;
	
	constructor() {
		owner = msg.sender;
		}
		
	modifier onlyOwner {
		if(msg.sender != owner){
			revert NotOwner(msg.sender)
			}
			_; // first modifier, then func. executiom
		}

	function iOwnThis() public wiew onlyOwner returns(string memory) {
		return 'You own this!'; // function is secured by the modifier
		}
}
```

> Best practice -> use OpenZeppelin's `onlyOwner` modifier