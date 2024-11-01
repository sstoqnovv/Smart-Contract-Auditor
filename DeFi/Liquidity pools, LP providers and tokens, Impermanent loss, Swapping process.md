**Liquidity** - refers to how easily an asset can be bought or sold without significantly affecting its price. Liquidity depends on factors like trading volume, market depth, and availability of markets. In crypto, liquidity often relates to how quickly an asset can be converted into fiat (cash) or another cryptocurrency without moving its market price significantly.

**Liquidation** - Liquidation is the process of selling collateral to cover a loan/debt. When a user's collateral value drops below a certain threshold, the platform (e.g., MakerDAO in the case of DAI) sells the collateral to repay the debt and avoid default.

**Liquidity pools** are essential part of AMMs, lending and borowwing protocols. It is collection of funds locked in a smart contract, used to faciliate decentrazlied trading.

Users, known as **liquidity providers**, add an equal value of two tokens to the pool to create a market. Liquidity providers are rewarded with trading fees for contributing assets to the pool. When users trade against the pool, a percentage of the transaction value is charged as a fee, and this fee is distributed among liquidity providers in proportion to their share of the pool.
User who deposits an equivalent value of two tokens into the liquditity pool. By doing so, they increase the pool's liquidity without changing the relation of the token supplies, thus maintaining the price.

The relationship between the quantities of the two assets in the liquidity pool is governed by a mathematical formula, often the constant product formula. The most well-known formula used by platforms like Uniswap is x * y = k, where x and y are the quantities of the two assets, and k is a constant.

**LP Token** - are given to liquidity providers when they deposit assets into a liquidity pool. These tokens represent the provider's share of the pool and can be used to withdraw assets or participate in yield farming for additional rewards. LP tokens  represents a proof of ownership of a share in a liquidity pool on decentralized finance (DeFi) platforms.

Buyers try to buy the asset on the lowest price possible(90 dollars). The sellers try to sell the asset on the highest price possible(110 dollars). They understand for a good and fair price. The price of the asset become 100 dollars.
****
**Impermanent loss** - potential risk that liquidity providers face when they deposit their assets into a liquidity pool on a DEX that operates on an AMM model. 
Impermanent loss happens when the price of your deposited assets changes compared to when you deposited them into the pool(the initial value). 
It refers to the temporary decrease in the value of an asset held in a liquidity pool compared to simply holding the asset in a wallet without providing liquidity.

**Example of Impermanent loss:**

Alice deposits 1 ETH and 100 DAI into a liquidity pool. The total pool has 10 ETH and 1,000 DAI, with a combined value of 10,000 USD. 
The price of ETH increases from 100 DAI to 400 DAI. Arbitrage traders adjust the pool, resulting in 5 ETH and 2,000 DAI in the pool, but the total pool value remains 10,000 USD. 
Alice, holding a 10% share of the pool, decides to withdraw. She gets 0.5 ETH and 200 DAI, totaling 400 USD.
If Alice had simply held her 1 ETH and 100 DAI, their combined value would be 500 USD now. 
The difference between what Alice gained by withdrawing from the pool (400 USD) and what she would have had by holding (500 USD) is the impermanent loss. In this case, it's 100 USD.
****
**Swapping process** - refers to exchanging one token for another within a liquidity pool. This process is driven by an equation `(x * y = k)`

Suppose we have a pool that contains `5 ETH` and `25, 000 USDC`. The pool liquidity(K) is `5 ETH * 25, 000 USDC = 125,000`. Now, a user wants to swap 5,000 USDC for some ETH. The number of USDC will increase, while the amount of ETH will decrease.

In the AMMs when one asset is buyed a lot its price increase, because it has more little than the other asset, which his price decrease, because it has more of the asset.

