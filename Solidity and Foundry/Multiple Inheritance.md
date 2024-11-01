```php
  X
/ | 
Y |
\ |
  Z

// order of most base like to derived
// X, Y, Z
```

When we need to Inherit from multiple contract we must put first the most base to derived contract.
**Example:** If `Y` inherits from `X`, `X` doesn't Inherit from no other contract.  Let's say `Z` must Inherit from both contract. `Z` must Inherit first from `X`, because `X` is the most base class and doesn't Inherit from no other contract, while `Y` Inherit from `X` and it's derived and will be on 2'nd place in `Z` Inheritance order.

```php
contract X {
	function foo() public pure virtual returns (string memory) {
	return "X";
	}
}

contract Y is X {
	function foo() public pure override returns (string memory) {
	return "Y";
	}
}

contract Z is X, Y {
	function foo() public pure override(X, Y) returns (string memory) {
	return "Z";
	}
}
```
****
#### Calling Parent Constructors

```php
contract S {
	string public name;

	constructor(string memory _name) {
		name = _name;
		}

contract T {
	string public text;

	constructor(string memory _text) {
		text = _text;
		}
} 

contract V is S, T {
	constructor(string memory _name, string memory _text) S(_name) T(_text) {} // We pass the constructor from both classes, again from  most base like to derived
}
```

> Solidity uses **C3 linearization** to resolve multiple inheritance, which means that the order in which the base contracts are listed in the derived contract's declaration determines the order in which they are linearized and their functions are resolved.
****
#### Calling Parent Functions

```php
/*
calling parent functions
 -  direct
 - super keyword

   E
 /   \
F     G
 \    /
   H
*/

contract E {
	event Log(string message);

	function foo() public virtual {
		emit Log("E.foo");
	}

	function bar() public virtual {
		emit Log("E.bar");
	}
}

contract F is E {
	function foo() public virtual override {
		emit Log("F.foo");
		E.foo(); // 1st way is using direct call
	}

	function bar() public virtual override {
		emit Log("F.bar");
		super.bar(); // 2nd way to call parent function
	}
}
```

> Using `super` keyword calls all of the function that are with the same names in all of the contracts, while if we use directly call, we specify from which contract we want to call the function. 