**Miner or maximal extractable value (MEV)** is the value extracted by miners or validators by utilizing their ability to order transactions within a block;  refers to the profit that miners can extract by reordering, front-running, or censoring transactions in a blockchain network.

The three main types of MEV on Ethereum are arbitrage, liquidations, and sandwiching.

> Rather than being a force of good or evil, MEV is an unavoidable consequence of what Ethereum is designed to do. Attempts to mitigate MEV, much like regulation in the traditional financial markets, must be implemented with careful consideration of tradeoffs and possible third-order consequences that encourage dark market activity and private transactions pools.

### Types of MEV on Ethereum

![[Pasted image 20240302130520.png]]

1. **Arbitrage** - Extracting profit from price discrepancies
![[Pasted image 20240302130835.png]]
2.  **Liquidation**
![[Pasted image 20240302131450.png]]
1. Decentralized lending platforms require borrowers to maintain a minimum amount of collateral to secure their loans. If the value of this collateral falls below the minimum requirement, the lending application automatically initiates a liquidation process to sell off the collateral.
2. **Liquidation Strategy**: Some traders, called searchers in this context, specialize in monitoring large outstanding loans to identify opportunities for liquidation. They wait for collateral values to drop significantly below the minimum requirement, enabling them to buy the assets at a discounted price during the liquidation process.
3. **Reselling at Higher Prices**: After purchasing the discounted assets, these searchers resell them at market value, potentially at a higher price. This strategy allows them to profit from the price difference between the discounted purchase price and the higher resale price.

3. **Sandwiching** - front-running and back-running

1. **Front Running Large Purchases**: Searchers identify when someone is making a large purchase of a cryptocurrency asset. They then quickly buy up the same asset before the original purchase is finalized on the blockchain. This action drives up the price of the asset for the original buyer.
2. **Driving Up Prices**: By purchasing the asset before the original buyer, the searcher artificially inflates the price of the asset. This ensures that when the original buyer's trade is executed, it happens at a slightly higher price than originally intended.
3. **Profit from Price Difference**: After the sale is complete, the searcher sells the assets they bought earlier, returning the asset's price back to normal. However, they pocket the difference between the asset's original price and the artificially inflated one.

### Mitigation:
 - Flashbots Auctions
****
### MEV Boost

#### Takeaways:

- New stakeholders, known as **builders**, are responsible for constructing a full block containing transactions optimized to maximize rewards from priority fees and MEV.
- Builders specialize in developing proprietary models for simulating blocks and propagating them efficiently through off-chain MEV marketplaces known as **relays**.
- To combat the negative network effects of builder centralization, Ethereum developers and the Flashbots team are discussing **several potential solutions**, such as inclusion lists, partial block auctions, and S.U.A.V.E.

### Searchers

Searchers identify opportunities to extract MEV by ordering the execution of user transactions within a block. They are actively monitoring the public mempool and identifying profit-maximizing ways to extract value from user transactions, sometimes by causing increased slippage and worse execution on decentralized exchanges (DEXs). - Discover how to capture MEV opportunities on Ethereum responsibly with Flashbots.
![[Pasted image 20240305073137.png]]

### Builders

Builders are the new stakeholders in the MEV supply chain responsible for packaging transaction bundles from searchers, along with individual transactions from the public mempool and private transaction order flow, into blocks. Block builders, often referred to as "builders", are specialized entities that receive transactions from users and searchers. Their primary role is to construct the most profitable block from these transactions. Once a block is built, it is transmitted to validators via an mev-boost relay.
>  Not all builders can be trusted

### Eth Users

Link your wallet to Flashbots Protect to safeguard against frontrunning, benefit from backrunning MEV through the MEV-Share protocol, and receive assistance in recovering funds from scams.

### Validators, Builders & Relayers

Leverage MEV-Boost to access a competitive block-building market, fostering greater competition, decentralization, and censorship-resistance for Ethereum.

### Relays

Their main responsibility is to securely store blocks received from builders and subsequently make them accessible to validators.

### Bots

Arbitrage bots typically look for specific types of transactions in the mempool (such a DEX trade or an oracle update) and try to frontrun them according to a predetermined algorithm. Generalized frontrunners look for _any_ transaction that they could profitably frontrun by copying it and replacing addresses with their own. They can even execute the transaction and copy profitable _internal transactions_ generated by its execution trace.
****
## Flash Auction

Flashbots Auction provides a private transaction pool and a sealed bid blockspace auction mechanism. This enables block proposers (validators; previously "miners" in PoW) to trustlessly outsource the task of finding the optimal block construction.

In the standard Ethereum transaction pool, users broadcast transactions to the public peer-to-peer network, specifying a gas price that represents their willingness to pay for each unit of computation on the Ethereum chain. Block builders receive these transactions, sort them by gas price, and employ a greedy algorithm to construct a block that aims to maximize the value derived from transaction fees. This mechanism is a hybrid of an [English auction](https://en.wikipedia.org/wiki/English_auction) and an [all-pay auction](https://en.wikipedia.org/wiki/All-pay_auction), where bids for blockspace are made openly, the highest bidder secures the opportunity, and all participants bear a cost.

Here are the key issues with this mechanism:

1. The open nature of the regular transaction pool leads to bidding wars for blockspace. This results in unnecessary network load and gas price volatility. It also puts less sophisticated network participants at a disadvantage, as they may lack access to advanced bidding strategies.
2. The all-pay nature of the auction results in failed bids reverting on-chain, unnecessarily consuming blockspace. This leads bidders to underprice their bids due to the risk of execution failure, creating artificial blockspace scarcity and reducing validator (previously "miner") revenues.
3. The dependency on gasPrice restricts bidders from expressing detailed ordering preferences, as they are limited to bidding for the top position in the block. This limitation encourages alternative strategies such as spamming to increase the chances of winning, thereby exacerbating the deadweight loss.

Instead, the Flashbots Auction infrastructure uses a [first-price sealed-bid auction](https://en.wikipedia.org/wiki/First-price_sealed-bid_auction) which allows users to privately communicate their bid and granular transaction order preference without paying for failed bids. This mechanism maximizes validator payoffs, while providing an efficient venue for price discovery on the value of a given MEV opportunity. Crucially, this mechanism eliminates frontrunning vulnerabilities.