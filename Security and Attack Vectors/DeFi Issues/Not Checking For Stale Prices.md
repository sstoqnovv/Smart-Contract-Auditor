Many smart contracts use Chainlink to request off-chain pricing data, but a common error occurs when the smart contract doesn’t check whether that data is stale. Consider this [stale pricing data finding](https://github.com/sherlock-audit/2023-05-USSD-judging/issues/31) from Sherlock’s USSD audit:

```php // @audit no check for stale price data  
(, int256 price, , , ) = priceFeedDAIETH.latestRoundData();  
  
return  
    (wethPriceUSD * 1e18) /  
    ((DAIWethPrice + uint256(price) * 1e10) / 2);

```
Oracle data feeds can return stale pricing data for a variety of [reasons](https://ethereum.stackexchange.com/questions/133242/how-future-resilient-is-a-chainlink-price-feed/133843#133843). If the returned pricing data is stale, this code will execute with prices that don’t reflect the current pricing resulting in a potential loss of funds for the user and/or the protocol. Smart contracts should always check the `updatedAt` parameter returned from `latestRoundData()` and compare it to a staleness threshold:

```php // @audit fixed to check for stale price data  
(, int256 price, , uint256 updatedAt, ) = priceFeedDAIETH.latestRoundData();  
  
if (updatedAt < block.timestamp - 60 * 60 /* 1 hour */) {  
   revert("stale price feed");  
}  
  
return  
    (wethPriceUSD * 1e18) /  
    ((DAIWethPrice + uint256(price) * 1e10) / 2);
```
The staleness threshold should correspond to the heartbeat of the oracle’s price feed. This can be found on [Chainlink’s list of Ethereum mainnet price feeds](https://docs.chain.link/data-feeds/price-feeds/addresses/?network=ethereum) by checking the “Show More Details” box, which will show the “Heartbeat” column for each feed. For networks other than Ethereum mainnet, make sure to select your desired L1/L2 on that page before reading the data columns.