**DEFINITION**: Hybrid smart contracts combine code running on the blockchain (on-chain) with data and computation from outside the blockchain (off-chain) provided by decentralized oracle networks to create a feature-rich dApp.
By being able to seamlessly combine on-chain and off-chain components, hybrid smart contracts can unlock [smart contract](https://chain.link/education/smart-contracts) use cases and functionality that wouldn’t be possible by using just one of the components.

### How Oracles Extend Blockchain-Based Collaboration

**Oracles** extend blockchain-based collaboration by acting as secure intermediaries between blockchains and the external world.

### The Composition of Hybrid Smart Contracts

A hybrid smart contract is an application that consists of two parts: 
1) [smart contract](https://chain.link/education/smart-contracts)—code that runs exclusively on the blockchain, and 
2) [decentralized oracle network(s)](https://blog.chain.link/what-is-chainlink/)—secure off-chain services that support the smart contract. 

The two components interact with one another seamlessly and securely to form a single hybrid smart contract application. The result is on-chain code that is augmented in a variety of unique and important ways, opening up many new use cases that would not be possible through on-chain code alone due to technical, legal, or financial constraints.

Hybrid smart contracts synchronize two distinctly different computing environments to create a superior application that neither a blockchain nor an oracle network could achieve alone, particularly because each environment specializes in providing features that the other does not.

On-chain code runs in an extremely secure and limited-functionality blockchain environment with a reduced attack surface area, providing users a high degree of execution and storage determinism—the code will run exactly as written and results will remain permanently and immutably stored.

Conversely, DONs run off-chain and thus offer infinitely more functionality flexibility and data accessibility.

#### Off-Chain: Decentralized Oracle Network

- Fetch, validate, secure, and deliver data from [external APIs](https://blog.chain.link/understanding-how-data-and-apis-power-next-generation-economies/) to smart contracts running on blockchains and [layer-2](https://blog.chain.link/what-is-a-layer-2/) solutions.

![[Pasted image 20240109101105.png]]
### Off-Chain Data

DONs can be used to bridge various types of external data to and from blockchains, enabling hybrid smart contracts to be written around those specific pieces of data. Some of the initial data types made accessible include:

- **Price Feeds** — asset price data that’s been aggregated from hundreds of exchanges, weighted by volume, and cleaned of outliers and wash trading.
- **Proof of Reserve** — up-to-date data about the current reserve balances backing tokenized assets, such as the BTC reserves collateralizing WBTC or the USD bank account collateralizing TUSD.
- **Any API** — premium data from password-protected APIs, ranging anywhere from weather forecasts and sports match results to information from an enterprise backend and IoT network.
- **Blockchain Middleware** — abstraction layer for an off-chain system to read and write data to and from smart contracts on any blockchain network.