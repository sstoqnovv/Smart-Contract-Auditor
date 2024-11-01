**Slippage refers to the price difference between when a market participant submits a DeFi swap trade and the actual price when that trade executes.**

This difference is usually negligible but can be significant during times of high volatility and for tokens that have low liquidity. Slippage can result in the user receiving more or (often) fewer tokens than they would have otherwise received if the trade had been instantaneous.

> DeFi platforms should allow users to specify a slippage parameter "minTokensOut" minimum amount of output tokens to be received from the swap, such that the swap will revert if it wouldn't return the user-specified minimum amount of output tokens.

### ### No Slippage Parameter

```php
IUniswapRouterV2(SUSHI_ROUTER).swapExactTokensForTokens(
    toSwap,
    0, // @audit min return 0 tokens; no slippage => user loss of funds
    path,
    address(this),
    now
);
```
This [code](https://github.com/code-423n4/2021-09-bvecvx-findings/issues/57) tells the swap that the user will accept a minimum amount of 0 output tokens from the swap, opening up the user to a catastrophic loss of funds via [MEV bot sandwich attacks](https://medium.com/coinmonks/defi-sandwich-attack-explain-776f6f43b2fd). Platforms should also provide a sensible default if the user doesn't specify a value, but user-specified slippage parameters must always override platform defaults.

### ### No Expiration Deadline

Advanced protocols like Automated Market Makers (AMMs) can allow users to specify a deadline parameter that enforces a time limit by which the transaction must be executed. Without a deadline parameter, the transaction may sit in the mempool and be executed at a much later time potentially resulting in a worse price for the user.

Protocols [shouldn't set the deadline to block.timestamp](https://code4rena.com/reports/2022-11-paraspace#m-13-interactions-with-amms-do-not-use-deadlines-for-operations) as a validator can hold the transaction and the block it is eventually put into will be block.timestamp, so this offers no protection.


