Contracts should have a substantial and prolonged testing period - before substantial money is put at risk.

At minimum, you should:

- Have a full test suite with 100% test coverage (or close to it)
- Deploy on your own testnet
- Deploy on the public testnet with substantial testing and bug bounties
- Exhaustive testing should allow various players to interact with the contract at volume
- Deploy on the mainnet in beta, with limits to the amount at risk

##### AUTOMATIC DEPRECATION[¶](https://consensys.github.io/smart-contract-best-practices/development-recommendations/precautions/deployment/#automatic-deprecation "Permanent link")

During testing, you can force an automatic deprecation by preventing any actions, after a certain time period. For example, an alpha contract may work for several weeks and then automatically shut down all actions, except for the final withdrawal.

```php
modifier isActive() {
    require(block.number <= SOME_BLOCK_NUMBER);
    _;
}

function deposit() public isActive {
    // some code
}

function withdraw() public {
    // some code
}
```

##### RESTRICT AMOUNT OF ETHER PER USER/CONTRACT[¶](https://consensys.github.io/smart-contract-best-practices/development-recommendations/precautions/deployment/#restrict-amount-of-ether-per-usercontract "Permanent link")

In the early stages, you can restrict the amount of Ether for any user (or for the entire contract) - reducing the risk.