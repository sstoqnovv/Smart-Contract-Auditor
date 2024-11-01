**Oracle** - any device that interacts with the off-chain world to provide external data or computation to smart contracts. 

**Decentralized oracle networks (DONs)** -  enable the creation of hybrid smart contracts, where onchain code and offchain infrastructure are combined to support advanced decentralized applications (dApps) that react to real-world events and interoperate with traditional systems. It eliminates any single point of failure in a smart contract by utilizing multiple data inputs.

**Price Oracle** - used to provide external prices to a SC.

**Chainlink** - Decentralized Oracle Network that enables smart contracts on blockchain platforms to securely interact with real-world data and off-chain resources.

**Off-chain** - Data stored external to the blockchain.
**On-chain** - Data stored within the blockchain.
****
### The problem with blockchains

Since the blockchain has its distributed ledger nature, each node in the network has to be able to find the same end result given the same input.
![[Pasted image 20240109103623.png]]We can see when we send $5 from Bob to Alice, we can easily add $5 to Alice’s balance and subtract $5 from Bob’s. Every node on the network could easily reproduce this, get the same results, and end up in the same state.

But what if this wasn’t the case? Let’s imagine an imaginary blockchain where they use APIs to validate each other's transactions. Let’s say we want to send a variable amount based on the price of ETH to Alice from Bob’s account. We would have our contract call an API to get the price of ETH and then send that amount to Bob. When every other node goes to verify this, they also have to call this API.
![[Pasted image 20240109103752.png]]But what happens now? If they call the API even an instant later, the API could have changed — been depreciated, hacked, or for a number of other reasons — and all the nodes would get a different result. This means none of the nodes would be able to agree upon what the actual state of the blockchain is.

It’s for this reason the Ethereum blockchain and most blockchains are designed to be entirely deterministic. That means if we were to replay every transaction, we would end up in the correct state. If you include API calls or other non-deterministic sources into the infrastructure of blockchain, there is a good chance that the source will be depreciated, hacked, or even just broken, and we would not be able to validate transactions.
![[Pasted image 20240109104001.png]]
In blockchain, the mechanism for agreeing upon a data value is called [consensus](https://blockgeeks.com/guides/blockchain-consensus/), and determinism is important so that nodes can come to a consensus(Pow, Pos, etc.).

But we need the blockchain world to connect with the real world. We need to get the price of ETH and other cryptocurrencies into a contract so we can have DeFi. We need to get the weather data so we can have decentralized trustless insurance. We need data to use blockchain for one of its most important purposes, smart contracts. So how do we bridge the worlds with this constraint?

### How oracles solve this

A blockchain oracle is any device or entity that connects a deterministic blockchain with off-chain data. These oracles enter every data input through an external transaction. This way, we can be sure that the blockchain itself contains all of the information required to verify itself. This is why oracles are known as blockchain middleware: They are the bridge between the two worlds.

So that’s it, this is great! But what’s this I keep hearing about an “oracle problem”?

### What Is the Oracle Problem?

Let’s take a step back and remember why we are building on-chain in the first place. A huge reason we do anything on-chain is for decentrality. However, data has to come from somewhere.

If we import data from a single API, node, or source, we have now essentially removed the entire purpose of using blockchain in the first place. A centralized oracle means that one entity has the power over your smart contract, and your smart contract is now no better than a regular contract. And even if the centralized oracle has the best intentions, we’ve seen attacks already where a centralized oracle is hacked, attacked, outdated, or not maintained, and catastrophe ensues.

## The solution

[Chainlink](https://chain.link/) solves both of these and is the standard for decentralized oracles.

A [decentralized oracle](https://docs.chain.link/docs/architecture-decentralized-model) or decentralized oracle network is a group of independent blockchain oracles that provide data to a blockchain. Every independent node or oracle in the decentralized oracle network independently retrieves data from an off-chain source and brings it on-chain. The data is then aggregated so the system can come to a deterministic value of truth for that data point. Decentralized oracles solve the oracle problem.
****
**Market Manipulation** - when the price of an asset is artificially manipulated through the alteration of natural supply and demand forces. It's an action undertaken by a malicious actor for making profit idea.
The ability of malicious actor to manipulate prices depends on the liquidity of an asset. The greater an asset's liquidity, the more capital it takes to manipulate its price. Lower the liquidity - cheaper it can be. Low-liquidity assets are more vulnerable to manipulation.
