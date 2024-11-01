**Block** - A group of transactions entered into a blockchain. Each block contains a set of transactions that have been validated by the nodes through consensus mechanism. Block consists of number, nonce, data, hash.

**Block gas limit** - set by miners and refers to the cap on the total amount of gas expended by all transactions in the block, which ensures that blocks can't be arbitrary large. **It's the total gas spent all transactions in block**
	Let's say we have 5 transactions(each 20,000 gas), but the block limit is 80,000. The 4th transaction would have to wait for the next block.

**Genesis Block** - The first or first few blocks of a blockchain.

**Block explorers** - portals that allow anyone to see real-time data on blocks, transactions, accounts, contract interaction, etc., e.g. Etherscan

**Blockchain** - A mathematical structure for storing digital transactions or data in an immutable, distributed, decentralized digital ledger consisting of blocks that are linked via cryptographic signature that is nearly impossible to fake, hack or disrupt. 
Blockchain is public. New blocks are always stored lineary and chronilogically. They are added to the end. After a block has been added to the end of the blockchain, previous blocks cannot be changed!

Blockchain has 4 layers. **Layer 1** - Consensus Layer, **Layer 1.5** - Compute Layer(Blockchain compiler), **Layer 2** - Apps, **Layer 3** - UI

**Peer-to-Peer(P2P)** - communication between nodes
****
**Layer 1** - standard base consensus layer where pretty much all transactions are currently settled.

**Layer 2** - refers to any off-chain network, system/app, or technology built **on top of** a [blockchain](https://chain.link/education-hub/blockchain)  that helps extend the capabilities of the underlying base layer network. 
****
### Rollups

The role of the sequencer is to order and package transactions into a block that will be submitted to the Ethereum mainnet. This involves collecting transactions from users, ordering them according to the agreed-upon rules, and constructing a block of transactions.
Once the block is constructed, the sequencer publishes it on-chain, either directly or by submitting it to a smart contract designed to accept and validate rollup blocks.

The rollup sequencer is responsible for ensuring the validity and integrity of the transactions included in the block before submitting it to the Ethereum mainnet. This may involve checking that each transaction is properly signed, verifying that the sender has sufficient funds or permissions, and ensuring that the transactions comply with any protocol rules or constraints.

**Optimistic Rollups** - assume txs are legitimate

**Zero-knowledge(ZK) Rollups** - use validity or zk proof to verify tx correctness
****
*From Mastering Ethereum book:*
### CHAPTER 1:

**Key components of Blockchain:**
    - P2P network connects participants and execute transactions and blocks or verified transactions
    - Messages(Transactions)
    - Chain of blocks. Each block keeps the previous's block hash and they're connected in a chain-like matter
    - ![[Pasted image 20240110133147.png]]

- **Consensus mechanism** - decentralizes the control by making sure the participants cooperate with the consensus rules.

Transactions are bundled into blocks; blocks are chained together using a cryptographic hash as a means of reference. Blocks function as a journal, recording a series of transactions together with the previous block and an identifier for the final state.

Fees
-------------------------------------------------------------------------------------------------------------
Because every transaction published into the blockchain imposes on the network the cost of needing to download and verify it, there is a need for some regulatory mechanism, typically involving transaction fees, to prevent abuse.

Currency And Issuance
-------------------------------------------------------------------------------------------------------------
- 1: wei
- 1012: szabo
- 1015: finney
- 1018: ether