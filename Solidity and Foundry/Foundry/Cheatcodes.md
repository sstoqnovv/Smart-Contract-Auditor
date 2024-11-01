### **`vm.startBroadcast()`**

- **Purpose**: Used to simulate transactions from an EOA (Externally Owned Account) or multiple accounts. Before broadcasting is not a real tx, after it - yes.
### **`vm.prank()`**

- **Purpose**: Temporarily changes the `msg.sender` to a different address, allowing you to simulate actions from any address.
### **`vm.deal()`**

- **Purpose**: Sets the balance of an address to a specific value, which is helpful for testing functions that require specific balances.

### **`vm.expectEmit(true, true, true, false, checkData)`**

- **Purpose**: Used to specify that you expect a particular event to be emitted during the execution of the following transaction. If the specified event is not emitted, or if the emitted event doesn't match the expectations set by `vm.expectEmit()`, the test will fail..

```php
function testEnteringRaffleEmitsEvent() public {
        // Arrange
        vm.prank(PLAYER);
        // Act
        vm.expectEmit(true, false, false, false, address(raffle)); // bool checks if they are indexed and last bool value is checkData parameter
        emit RaffleEntered(PLAYER);
        // Assert
        raffle.enterRaffle{value: entranceFee};
    }
```

### `vm.warp(uint256 _timestamp)`

**Purpose**: `vm.warp()` allows you to set the block timestamp to a specific value.

```php
// Set the blockchain timestamp to 1 hour in the future
vm.warp(block.timestamp + 1 hours);
```


### `vm.roll(uint256 _blockNumber)`

**Purpose**: `vm.roll()` allows you to set the block number to a specific value.

```php
// Set the blockchain state to block number 1000
vm.roll(1000);
```

### **`vm.recordLogs()`**:

- **Purpose**: Starts recording all the logs (events) emitted during the execution of contract interactions.

### **`vm.assume()`**:

- **Purpose**: If the boolean expression evaluates to false, the fuzzer will discard the current fuzz inputs and start a new fuzz run.

```php
    function test_MostSignificantBitFuzz(uint256 x) public {
        // assume - if false, the fuzzer will discard the current fuzz inputs
        // and start a new fuzz run
        // Skip x = 0
        vm.assume(x > 0); // if false, skip the test and run another test
        assertGt(x, 0);
    }
```

### **`Vm.Log[]`**:

- **Purpose**: Represents an array of logs that were captured during the transaction after `vm.recordLogs()` was called.

### **`vm.getRecordedLogs()`**:

- **Purpose**: Stops the log recording and retrieves the array of logs that have been recorded since `vm.recordLogs()` was called.


```php
function testLogs() public {
    // Arrange
    vm.recordLogs(); // Start recording logs

    // Act
    raffle.enterRaffle{value: entranceFee}(); // Call a function that emits events

    // Assert
    Vm.Log[] memory logs = vm.getRecordedLogs(); // Retrieve the recorded logs

    // Check the first log's address and topics
    assert(logs.length == 1); // Ensure exactly one log was emitted
    assert(logs[0].emitter == address(raffle)); // Ensure the log was emitted by the correct contract

    // Decode the event data
    address player;
    (player) = abi.decode(logs[0].data, (address));
    assert(player == PLAYER); // Ensure the event data is as expected
}
```
### Key Points:

- **`vm.recordLogs()`**: Call this to start recording logs before a function that emits events is executed.
- **`Vm.Log[]`**: After capturing logs, this array holds the recorded log data.
- **`vm.getRecordedLogs()`**: Retrieves the recorded logs for analysis.