***From "Mastering Ethereum" book:***
### CHAPTER 7: Smart Contracts & Solidity

**Datatypes**
- Boolean (bool) - with logical operators: ! (not); && (and); || (or), == (equal), != (not equal)
- Signed (int) and unsigned (uint) integers, declared in increments of 8 bits from int8 to uint256.
- Address - a 20-byte Ethereum address
- Byte array (static) - Fixed-size arrays of bytes, declared with bytes1 up to bytes32.
- Byte array (dynamic)
- Struct
- Mapping
- Time units (sec., min., hours, days) and Ether units (wei, finney, ether)
 
**Transaction/message context**
- The msg object is the transaction call or message call that launched this contract execution.
		- **msg.sender** - the address that initiated this contract call
		- **msg.value** - the value of ether sent with this call (in wei)
		- **msg.gas** - the amount of gas left in the gas supply of this execution environment
		- **msg.data** - the data payload of this call into our contract
		- **msg.sig** - the first four bytes of the data payload
 
- The tx object provides a means of accessing transaction related info:
		- **tx-gasprice**
		- **tx-origin** - the address of the originating EoA for this transaction
 
**Block context** - the block object contains information about the current block
- **block.blockhash (blockNumber)** - the block hash of the specified block number
- **block.coinbase** - address of the recipient of the current block's fees and block reward
- **block.difficulty** - the difficulty (PoW) of the current block
- **block.gaslimit** - maximum amount of gas that can be spent accross all transactions included in the current block
- **block.number** - the current block number (blockchain height)
- **blockchain.timestamp** - the timestamp placed in the current block by the miner
 
**Address object**
- **address.balance** - the balance of the address (in wei)
- **address.transfer (amount)** - transfers the amount (in wei) to this address, throwing an exception/error.
- **address.send (amount)** - similar to transfer, only instead of throwing an exception, it returns false on error.
- **address.call** - Low-level CALL function - can construct an arbitrary message call with a data payload.
- **address.callcode (payload)**
- **address.delegate call()**
 
**Function behavior modifiers**
- **pure** - pure function promises the Solidity compiler that it doesn't read nor write any of the state variables.
- **view** - when a function is specified as view It promises that it might look into the state variables but not write.
- **payable** - function that can accept incoming payments

```php
// Version of Solidity compiler this program was written for
pragma solidity ^0.4.22;

// Our first contract is a Faucet

contract Faucet{
	address owner;
// Initialize Faucet contract: set owner
	constructor() {
		owner = msg.sender;
		}
	[...]
}
```

```php
// Contract destructor
function destroy() public {
	require(msg.sender==owner);
	selfdestruct(owner);
}  // Allowing for deletion of the contract given that the originator address is the owner
```

```php
modifier onlyOwner{
require(msg.sender==owner);
_;
}  // Modifiers are usually used to "require()" some condition
```
**Access modifiers**
1. **public** - can be called by any EoA or contracts
2. **private** - it can be called only from within the contract
3. **external** - EoA's and contracts can call these functions. They cannot be called from inside the contract unless the keyword "this" is specified
4. **internal** - They can be called only from within a contract. Whenever a contract inherits (derives) from a contract with internal functions they can be called.