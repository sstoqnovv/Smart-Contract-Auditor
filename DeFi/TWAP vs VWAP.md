**DEFINITION** - TWAP and VWAP are different methodologies for calculating asset prices used across the DeFi ecosystem.

## What is TWAP?

TWAP stands for “time-weighted average price”. It’s a pricing algorithm used to calculate the average price of an asset over a set period.

In DeFi, a type of [decentralized exchange (DEX)](https://chain.link/education-hub/what-is-decentralized-exchange-dex) known as an [automated market maker (AMM)](https://chain.link/education-hub/what-is-an-automated-market-maker-amm) can be used to generate TWAP prices that can be used in other protocols. TWAP can also refer to a trading strategy used to execute a large-volume order by breaking it into equal parts across a set period in order to minimize slippage and signaling. In this post, we focus on the pricing mechanism rather than the trading strategy.

### How Is TWAP Calculated?

TWAP is calculated by summing prices at multiple points across a set period and then dividing this total by the total number of price points.

Here’s a common TWAP formula:

- TWAP = (TP1+ TP2… + TPn) / n, where;
- TP1 is the price at the first timepoint, and
- n is the total number of timepoints.

For example, imagine we wanted to calculate the TWAP of an asset over one minute using 15-second price point intervals. If the prices were $100 at zero seconds, $102 at 15 seconds, $101 at 30 seconds, $98 at 45 seconds, and $103 at 60 seconds, then to calculate the TWAP we would sum all price points (100, 102, 101, 99, 103) and then divide them by the number of timepoints (five). In this example, the TWAP is $101.
****
## What is VWAP?

VWAP stands for “volume-weighted average price”. It’s a mechanism used to calculate the price of an asset by taking price data from multiple trading environments and weighting each price by the amount of volume on each liquid market an asset is trading on. A VWAP algorithm [underpins the majority of DeFi protocols](https://blog.chain.link/chainlink-price-feeds-secure-defi/), as it is used by the industry-leading price oracle solution: Chainlink Price Feeds.

The VWAP calculation methodology is also used more broadly across finance as a technical indicator for traders, an order option offered by brokers or exchanges, and a benchmark. In this article, we focus on its use as a pricing mechanism.

### How is VWAP Calculated?

VWAP is calculated by taking the price an asset was traded at from across multiple trading environments and weighting those price points with respect to the volume that was traded on each exchange, with wash trading and other outliers commonly filtered out.

Here’s a common VWAP formula:

- VWAP = (V1 x P1 + V2 x P2… + Vn x Pn) / Total Volume, where;
- V1 and P1 are the volume and price an asset was traded at on the first trading environment, and
- n is the total number of trading environments used in the calculation.

As an example, we’ll calculate the VWAP of a fictional asset across a specific timeframe. Let’s say 100 tokens were traded at a price of $101 on exchange X, 150 tokens were traded at a price of $102 on exchange Y, and 500 tokens were traded at a price of $100 on exchange Z. We first multiply the price by the volume on each trading environment (100 x 101 + 150 x 102 + 500 x 100) and then divide the result by the total volume traded (100 + 150 + 500). We get 75,400 divided by 750, giving us a VWAP of $100.53.