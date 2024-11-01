- `blockhash(uint blockNumber) returns (bytes32)`: hash of the given block when `blocknumber` is one of the 256 most recent blocks; otherwise returns zero
    
- `blobhash(uint index) returns (bytes32)`: versioned hash of the `index`-th blob associated with the current transaction. A versioned hash consists of a single byte representing the version (currently `0x01`), followed by the last 31 bytes of the SHA256 hash of the KZG commitment ([EIP-4844](https://eips.ethereum.org/EIPS/eip-4844)).
    
- `block.basefee` (`uint`): current block’s base fee ([EIP-3198](https://eips.ethereum.org/EIPS/eip-3198) and [EIP-1559](https://eips.ethereum.org/EIPS/eip-1559))
    
- `block.blobbasefee` (`uint`): current block’s blob base fee ([EIP-7516](https://eips.ethereum.org/EIPS/eip-7516) and [EIP-4844](https://eips.ethereum.org/EIPS/eip-4844))
    
- `block.chainid` (`uint`): current chain id
    
- `block.coinbase` (`address payable`): current block miner’s address
    
- `block.difficulty` (`uint`): current block difficulty (`EVM < Paris`). For other EVM versions it behaves as a deprecated alias for `block.prevrandao` ([EIP-4399](https://eips.ethereum.org/EIPS/eip-4399) )
    
- `block.gaslimit` (`uint`): current block gaslimit
    
- `block.number` (`uint`): current block number
    
- `block.prevrandao` (`uint`): random number provided by the beacon chain (`EVM >= Paris`)
    
- `block.timestamp` (`uint`): current block timestamp as seconds since unix epoch
    
- `gasleft() returns (uint256)`: remaining gas
    
- `msg.data` (`bytes calldata`): complete calldata
    
- `msg.sender` (`address`): sender of the message (current call)
    
- `msg.sig` (`bytes4`): first four bytes of the calldata (i.e. function identifier)
    
- `msg.value` (`uint`): number of wei sent with the message
    
- `tx.gasprice` (`uint`): gas price of the transaction
    
- `tx.origin` (`address`): sender of the transaction (full call chain)

> In version 0.7.0, the alias `now` (for `block.timestamp`) was removed.

> The function `gasleft` was previously known as `msg.gas`, which was deprecated in version 0.4.21 and removed in version 0.5.0.