The following code causes an overflow because the result of the addition is too large to be stored in the type `uint8`:
```php
uint8 x = 255;
uint8 y = 1;
return x + y;
```

Solidity has two modes in which it deals with these overflows: Checked and Unchecked or “wrapping” mode.

The default checked mode will detect overflows and cause a failing assertion. You can disable this check using `unchecked { ... }`, causing the overflow to be silently ignored. The above code would return `0` if wrapped in `unchecked { ... }`.

> Even in checked mode, do not assume you are protected from overflow bugs. In this mode, overflows will always revert. If it is not possible to avoid the overflow, this can lead to a smart contract being stuck in a certain state.

In general, read about the limits of two’s complement representation, which even has some more special edge cases for signed numbers.

Try to use `require` to limit the size of inputs to a reasonable range and use the [SMT checker](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#smt-checker) to find potential overflows.

