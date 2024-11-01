We have miners and participants. Each of them has a secret key. This secret key signifies ownership(own tokens, data). No one can take it away from you unless you sign it away with using your secret key or someone compels you to give away your key. 
The participants sign transactions and use the secret key to sign them and send to the miners. The Consensus protocol choose randomly a leader among these participants. Different blockchains use different consensus protocols (e.g., Proof of Work, Proof of Stake) to agree on the state of the blockchain and determine who gets to add the next block. The random leader orders the transactions to a block and the block gets posted on the blockchain, if checks for the block are valid. If the block isn't validated it get discarded.

The algorithm for checking if a block is valid, expressed in this paradigm, is as follows:

1. Check if the previous block referenced by the block exists and is valid.
2. Check that the timestamp of the block is greater than that of the previous block[fn2](https://ethereum.org/en/whitepaper/#notes) and less than 2 hours into the future
3. Check that the proof-of-work on the block is valid.
4. Let `S[0]` be the state at the end of the previous block.
5. Suppose `TX` is the block's transaction list with `n` transactions. For all `i` in `0...n-1`, set `S[i+1] = APPLY(S[i],TX[i])` If any application returns an error, exit and return false.
6. Return true, and register `S[n]` as the state at the end of this block.