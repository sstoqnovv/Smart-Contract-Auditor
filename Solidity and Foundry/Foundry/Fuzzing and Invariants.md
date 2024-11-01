**Fuzz Testing**: Supply random data(inputs) to your system in an attempt to break it.

**Invariant**: Property of our system that should always hold true. **Example:** If we market a balloon as indestructable or unbreakable - the invariant that would hold would, this balloon cannot be broken.
An invariant is a concept in mathematics and computer science that refers to a property or characteristic that remains unchanged under certain operations or transformations.
****
### **Stateless Fuzzing**: 

## 1. Stateless fuzzing - Open

Stateless fuzzing (often known as just "fuzzing") is when you provide random data to a function to get some invariant or property to break.

It is "stateless" because after every fuzz run, it resets the state, or it starts over.

### [](https://github.com/Cyfrin/sc-exploits-minimized/tree/main/src/invariant-break#written-example)Written Example

You can think of it like testing what methods pop a balloon.
1. Fuzz run 1:
    1. Get a new balloon
        1. Do 1 thing to try to pop it (ie: punch it, kick it, drop it)
        2. Record whether or not it is popped
2. Fuzz run 2:
    1. Get a new balloon
        1. Do 1 thing to try to pop it (ie: punch it, kick it, drop it)
        2. Record whether or not it is popped
3. _Repeat..._

#### Code Example:

```php
// myContract
    // Invariant: This function should never return 0
    function doMath(uint128 myNumber) public pure returns (uint256) {
        if (myNumber == 2) {
            return 0;
        }
        return 1;
    }

// Fuzz test that will (likely) catch the invariant break
    function testFuzzPassesEasyInvariant(uint128 randomNumber) public view {
        assert(myContract.doMath(randomNumber) != 0);
    }
```
****

### **Stateful Fuzzing**: 

## 2. Stateful fuzzing - Open

Stateful fuzzing is when you provide random data to your system, and for 1 fuzz run your system starts from the resulting state of the previous input data.

Or more simply, you keep doing random stuff to _the same_ contract.

### [](https://github.com/Cyfrin/sc-exploits-minimized/tree/main/src/invariant-break#written-example-1)Written Example

You can think of it like testing what methods pop a balloon.

1. Fuzz run 1:
    1. Get a new balloon
        1. Do 1 thing to try to pop it (ie: punch it, kick it, drop it)
        2. Record whether or not it is popped
    2. If not popped
        1. Try a different thing to pop it (ie: punch it, kick it, drop it)
        2. Record whether or not it is popped
    3. If not popped... _repeat for a certain number of times_
2. Fuzz run 2:
    1. Get a new balloon
        1. Do 1 thing to try to pop it (ie: punch it, kick it, drop it)
        2. Record whether or not it is popped
    2. If not popped
        1. Try a different thing to pop it (ie: punch it, kick it, drop it)
        2. Record whether or not it is popped
    3. If not popped... _repeat for a certain number of times_
3. _Repeat_

In **stateful fuzzing** we try many things to the same balloon before moving on.

#### Code Example:

```php
// myContract
    uint256 public myValue = 1;
    uint256 public storedValue = 100;
    // Invariant: This function should never return 0
    function doMoreMathAgain(uint128 myNumber) public returns (uint256) {
        uint256 response = (uint256(myNumber) / 1) + myValue;
        storedValue = response;
        return response;
    }
    function changeValue(uint256 newValue) public {
        myValue = newValue;
    }

// Test
    // Setup
    function setUp() public {
        sfc = new StatefulFuzzCatches();
        targetContract(address(sfc));
    }

    // Stateful fuzz that will (likely) catch the invariant break
    function statefulFuzz_testMathDoesntReturnZero() public view {
        assert(sfc.storedValue() != 0);
    }
```
****
### Example of Invariants in practice of SCs

1. New tokens minted < inflation rate - **It must be**
2. Only possible to have 1 winner in a lottery
3. Only withdraw what they deposit
****
**Tip: If you're working with a protocol that isn't doing stateful fuzzing or invariant tests - red flag! Get them to use it!
1. Understand what the Invariants are
2. Write functions that can execute them

**DO NOT GO TO AUDIT WITHOUT THESE.**

### Invariants 

![[Screenshot (20) 2.png]]