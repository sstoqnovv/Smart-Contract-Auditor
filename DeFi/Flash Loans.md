A flash loan is a loan that exists for exactly 1 transaction. A user can borrow any amount of assets from the protocol as long as they pay it back in the same transaction. If they don't pay it back, the transaction reverts and the loan is cancelled.  No need to put money for initial collateral.

Imagine it has a contract with 1000 dollars in it. We have a contract that we control and we call some `flashloan` function to get this flash loan of 1000 dollars. The contract gives the money to us. In our contract we `do any stuff - let's say arbitrage`. We must return the borrowed money back to the contract, that gave it to us in the same transaction, because it checks that it has 1000 dollars, or the transaction will revert and we will end up with no money and a flash loan, if we don't receive the money back to the contract.
But these 1000 dollars aren't free. They are distributed from the liquidity providers. They deposit their tokens and receive share - LP tokens. Because of moving assets/money, the borrower pay fees. 
![[Pasted image 20240215100935.png]]

They allow users to borrow a large amount of cryptocurrency for a very short duration, typically within a single transaction block. They don't require any collateral.

You can borrow massive amount of money for free, but you must pay it back almost immediately!

## What Are Flash Loans Used For?

The most common usage of flash loans is for arbitrage.
**Arbitrage** - trading strategy in which an asset purchased in one market where the price is lower and sold immediately in another market at a higher price, exploiting the price difference to turn a profit.
**Example:** We have two DEXs. In first, ETH is 5 dollars. We buy from them and sell to other DEX, which ETH is 6 dollars worth. We are in profit with 1 dollar worth. Now imagine if this was with thousands dollars. The profit will be 1000 dollars.

### Flash Loans and Price Oracle Attacks

Once a vulnerability is uncovered by a malicious actor, the attacker can manipulate certain functions of the protocol using capital acquired via a flash loan and make a profit while potentially draining funds from its smart contracts. In addition, since flash loan transactions revert on failure, hackers don’t have to put a large amount of their own capital at risk to fund an attack.

Here’s an example of a flash loan-funded attack on a DeFi lending protocol using a DEX-based spot price feed as its sole price oracle:

1. Attacker borrows a large amount of token A from a protocol supporting flash loans.
2. Attacker swaps token A for token B on a DEX (lowering the spot price of token A and increasing the spot price of token B on the DEX).
3. Attacker deposits the purchased token B as collateral on a DeFi protocol that uses the spot price from the above DEX as its sole price feed, and uses the manipulated spot price to borrow a larger amount of token A than should normally be possible.
4. Attacker uses a portion of borrowed token A to fully pay back the original flash loan and keep the remaining tokens, generating a profit using the protocol’s manipulated price feed.
5. As the spot prices of token A and B on the DEX are arbitraged back to the true market-wide price, the DeFi protocol is left with an undercollateralized position.
![[Pasted image 20240109132437.png]]