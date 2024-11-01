A common method for asserting whether a sender is a contract or EOA has been to check the code size of the sender. This check asserts that if the sender has a code size > 0 that it must be a contract and if not then it must be an EOA. For example:

```php
function mint(uint256 amount) public {
  if (msg.sender.code.length != 0) revert CallerNotEOA();
}
```

However, as noted in the [Ethereum Yellow Paper](https://ethereum.github.io/yellowpaper/paper.pdf), "During initialization code execution, EXTCODESIZE on the address should return zero, which is the length of the code of the account while CODESIZE should return the length of the initialization".

### Mitigation

To mitigate this, avoid solely relying on `code.length == 0` to differentiate EOAs from contracts. Consider alternative strategies like using off-chain mechanisms or multi-signature verification for critical functions where EOA-only access is necessary.