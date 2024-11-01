Events in Ethereum signal when something important happens. You can mark up to 3 parameters as `indexed`, which stores them in the “topics” part of the log, allowing efficient searching. Indexed parameters are stored as Keccak-256 hashes if they are reference types. Non-indexed parameters are ABI-encoded(converting data into a binary format that can be understood and executed by EVM) and stored in the data part of the log.

```php
// SPDX-License-Identifier: GPL-3.0
pragma solidity >=0.4.21 <0.9.0;

contract ClientReceipt {
    event Deposit(
        address indexed from,
        bytes32 indexed id,
        uint value
    );

    function deposit(bytes32 id) public payable {
        // Events are emitted using `emit`, followed by
        // the name of the event and the arguments
        // (if any) in parentheses. Any such invocation
        // (even deeply nested) can be detected from
        // the JavaScript API by filtering for `Deposit`.
        emit Deposit(msg.sender, id, msg.value);
    }
}
```