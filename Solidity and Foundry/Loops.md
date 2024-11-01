In Solidity, loops are used to execute a piece of code repeatedly until a certain condition is met. There are three types of loops available: `for`, `while`, and `do-while`.

#### For-Loop
```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract LoopExample {
    function sum(uint256 n) public pure returns (uint256) {
        uint256 result = 0;
        for (uint256 i = 1; i <= n; i++) {
            result += i;
        }
        return result;
    }
}
```
#### While-Loop
```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract LoopExample {
    function countDigits(uint256 num) public pure returns (uint256) {
        uint256 digits = 0;
        while (num != 0) {
            num /= 10;
            digits++;
        }
        return digits;
    }
}

```