In a default transaction, data is stored and visible on-chain permanently. Blob transactions (binary large objects), classified as transaction type 3, allow data to be stored temporarily on-chain and then deleted after a short period, ranging from 20 to 90 days.

Blobs are a new transaction type that allows us to store data on-chain for a short period of time.
We can't access the data itself, but we can access a hash of the data with the new `BLOBHASH` opcode. 
Blobs were added because rollups wanted a cheaper way to validate transactions.

### EIP-4844

These transactions originate from EIP4844, also known as "Proto-DankSharding", introduced in the Dankun upgrade in March 2024. The purpose of this transaction type is to address Ethereum's high transaction costs.

### Blobs and Transactions

Blobs are temporary data attached to a transaction, which is then validated and deleted. For example, ZK Sync can send its batch of compressed transactions to Ethereum using blobs.

### How rollups validate these transactions?

Validating these blobs involves using the `[BLOBHASH](https://www.evm.codes/#49?fork=cancun)` opcode and a [point evaluation precompile](https://www.evm.codes/precompiled#0x0a?fork=cancun), which execute cryptographic processes to verify the correctness of the blob without requiring on-chain storage.

1. A transaction is submitted with a blob and cryptographic proof data
2. The contract on-chain does not directly access the blob. Instead, it uses the `BLOBHASH` opcode to generate a hash of the blob
3. The _blob hash_ and the _proof data_, is passed to the **point evaluation opcode** to verify the transaction batch