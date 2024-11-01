A **Merkle Airdrop** is a way to distribute tokens efficiently using a **Merkle tree**. Instead of sending tokens to every user one by one (which is costly), the creator posts a single **Merkle root**, a summary of all the data.

A **Merkle tree** is a cryptographic structure that allows for efficient and secure verification of large data sets. In the case of an airdrop, the data set includes a list of addresses and the corresponding number of tokens each should receive. 

A Merkle tree's "root" hash summarizes all the data, and users can verify their inclusion in the airdrop by checking their "branch" or "leaf."

- **Leaf Nodes:** Each data item (like a transaction or file) is hashed.
- **Pairing Hashes:** These hashes are paired and hashed together, creating a new set of hashes.

When an eligible user wants to claim their tokens, they submit their address and a Merkle proof (a cryptographic proof showing they are included in the airdrop list) to the smart contract. The smart contract verifies the proof against the Merkle root and, if valid, releases the corresponding tokens to the user.

A **Merkle proof** is a way to prove that a specific piece of data is in fact in the tree.

![[Pasted image 20240912014422.png]]

❗**IMPORTANT**:br Secure hashing functions, such as `keccak256`, are designed to prevent hash collisions

**Merkle signatures** are a cryptographic technique used to create digital signatures that are both secure and efficient.