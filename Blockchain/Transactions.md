A **transaction** in the context of blockchain is a record of a transfer of value or information from one participant to another within the network. It represents an atomic operation that, when included in a block and added to the blockchain, becomes a permanent and unalterable part of the distributed ledger. 
Transactions are cryptographically signed instructions from accounts(EoA). An account will initiate a transaction to update the state of the Ethereum network.

**Transactions are grouped together into block**

**Transaction reverts** - for differenet exceptional conditions such as running out of gas, invalid instructions, etc.

**Mempool** - a queue where unconfirmed blockchain transactions are stored

Transactions on Ethereum are grouped together in blocks which are processed in a semi-regular intervals. Before transactions are placed in blocks, they're broadcasted to the mempool where blockbuilders can then processed to place them as is economically optimal. The mempool is public and thus anyone can see transaction before they're executed, giving them the power to frontrun by placing their own transaction executing the same, or similar action with a higher gas price.

An Ethereum transaction refers to an action initiated by an externally-owned account, in other words an account managed by a human, not a contract. 
For example, if Bob sends Alice 1 ETH, Bob's account must be debited and Alice's must be credited. This state-changing action takes place within a transaction.

A submitted transaction includes the following information:

- `from` – the address of the sender, that will be signing the transaction. This will be an externally-owned account as contract accounts cannot send transactions.
- `recipient` – the receiving address (if an externally-owned account, the transaction will transfer value. If a contract account, the transaction will execute the contract code)
- `signature` – the identifier of the sender. This is generated when the sender's private key signs the transaction and confirms the sender has authorized this transaction
- `nonce` - a sequentially incrementing counter which indicates the transaction number from the account
- `value` – amount of ETH to transfer from sender to recipient (denominated in WEI, where 1ETH equals 1e+18wei)
- `input data` – optional field to include arbitrary data
- `gasLimit` – the maximum amount of gas units that can be consumed by the transaction. The [EVM](https://ethereum.org/developers/docs/evm/opcodes) specifies the units of gas required by each computational step
- `maxPriorityFeePerGas` - the maximum price of the consumed gas to be included as a tip to the validator
- `maxFeePerGas` - the maximum fee per unit of gas willing to be paid for the transaction (inclusive of `baseFeePerGas` and `maxPriorityFeePerGas`)

The transaction object will look a little like this:
```php
{
  from: "0xEA674fdDe714fd979de3EdF0F56AA9716B898ec8",
  to: "0xac03bb73b6a9e108530aff4df5077c2b3d481e5a",
  gasLimit: "21000",
  maxFeePerGas: "300",
  maxPriorityFeePerGas: "10",
  nonce: "0",
  value: "10000000000"
}

```
****
### Fallback(), receive(), external call
 
When the contract receives ETH, it will trigger the `fallback()` or `receive()` to execute the corresponding logic, which is a hidden external call.

Transaction Execution
-------------------------------------------------------------------------------------------------------------
The execution of a transaction is the most complex part of the Ethereum protocol: it defines the state transition function. It is assumed that any transactions executed first pass the initial tests of intrinsic validity. 
These include: 
 - (1) The transaction is well-formed RLP, with no additional trailing bytes; 
 - (2) the transaction signature is valid; 
 - (3) the transaction nonce is valid (equivalent to the sender account’s current nonce);
 - (4) the sender account has no contract code deployed; 
 - (5) the gas limit is no smaller than the intrinsic gas, g0, used by the transaction; 
 - (6) the sender account balance contains at least the cost, v0, required in up-front payment.
****
*From book: "Mastering Ethereum"*:

### CHAPTER 6: Transactions

- Signed messages that originate from an EoA's and are recorded on the Ethereum blockchain. Every transaction represents a state change in the Ethereum blockchain. They are what move the Ethereum ecosystem.

 
**Transaction consists of:**
- **Nonce** - sequence number that is used to prevent message replay
- **Gas price (in wei)** - that a originator (sender) is willing to pay
- **Gas limit** - the amount of gas that the originator is willing to buy for this transaction
- **Recipient** - destination address of a transaction
- **Value**
- **Data**
- **v, r, s** - the three components of an ECDSA digital signatures from the originating EoA.

**v, r, s** - ECDSA Signature -> 65 bytes. r, s - Signature, v - recovery identifier - it is used to recover the public key from the signature during the verification process.
 
**Transaction Nonce**
- Represent a scalar value that is equal to the number of transactions (valid) made from this address
- Nonce represents the number of transactions or contract deployments (creations) that have been made.
 
**Transaction gas** - The amount that an account is willing to pay for the execution of a given transaction. The higher the gas price, the faster the transaction should be executed. **Imagine a gas limit as the capacity of the fuel tank in your car (transaction)**
 
**Transaction recipient** - 20 byte address. This can be an EoA or a contract address
 
**Transaction value and data**
- The main payload of a transaction consists of two fields: data and value
 
**A transaction with no data but with value is a payment. A transaction with data but without value is an invocation. A transaction without both of those - waste of gas.**
 
**Digital signatures (DS) in transactions**
- A DS has 2 purposes:
		- DS proves that the owner of a private key has authorized the spending of ether or execution of a contract.
		- DS makes sure that the data has not been and cannot be modified by anyone after the transaction is signed.
 
- DS is a matchematical scheme that consists of two parts:
	- Taking DS algorithm and the message of a signature(+ private key of the originator)
	- Verifying the signature
 
**Transaction propagation**
- A node in the Ethereum blockchain is refered to a client connected to the P2P network. This forms a mesh network. No node is special, they all act as peers.
 
**How does a transaction get verified by other nodes?**
- Transaction starts with the originator note creating (or receiveing) a signed transaction. The transaction is validated and then sent to all of the peers that verifies that transaction and then sends it to its 13 neighbours.
 
**Recording on the blockchain**
- **Valid transactions** are eventually **included** in a **block** of transactions and thus - recorded to the Ethereum blockchain. T ransactions also **modify** the *state** of the **Ethereum singleton** by adding to a balance of an account or by invasing some functionality of a contract.


### **Transaction Lifecycle:**

1. **Initiation:**
    - A user initiates a transaction, specifying the recipient, amount, and any additional data.
2. **Signing:**
    - The transaction is signed with the private key associated with the sender's public address to prove ownership and authorization.
3. **Broadcasting:**
    - The signed transaction is broadcasted to the network, making it visible to nodes.
4. **Validation:**
    - Nodes on the network validate the transaction, ensuring it follows protocol rules and the sender has the necessary funds.
5. **Inclusion in a Block:**
    - Valid transactions are grouped into blocks by miners. This block is added to the blockchain through the consensus mechanism.
6. **Confirmation:**
    - The transaction is considered confirmed when the block containing it receives additional blocks built on top, creating a level of security against potential reversals.


### Transaction Process (Blockchain)

1. A new transaction is entered.
2. The transaction is transmitted to a network of P2P computers accross the world.
3. This network solves equations to confirm the validation of the transaction
4. Once confirmed to be legitimate transactions, they are clustered together into blocks
5. These blocks are then chained together creating a long history of all made transactions that are permanent
6. The transaction is completed


### **Transaction Status:**

1. **Success:**
    - The transaction is successfully executed and included in a block.
2. **Pending:**
    - The transaction is broadcasted but not yet included in a block.
3. **Failed:**
    - The transaction encounters an issue during execution, such as insufficient funds or failed smart contract conditions.
****
When Transaction is sent to the blockchain - it's sent 2 things:
 - **contract.abi**(all the functions)
 - **contract.bin**
When Transaction is sent to the blockchain, you actually sent **contract.bin**. This is all the infoemation in low-level stuff(bytecode/binary data of SC). These binary data is the computer language and tells what the blockchain should do, e.g. *deploy the contract, do the x,y,z thing...* 
All things are called **opcodes** that tell what the computer must do. The opcodes are basically EVM. EVM reads the bytecode(opcodes) and do things.

```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.7;
contract Encoding {
    function combine() public pure returns(string memory) {
        return string(abi.encodePacked("Hi Mom! ", "Miss you!" )); // return bytecode and we typecast it to string
    }
    function encode() public pure returns(bytes memory) {
        bytes memory number = abi.encode(1); // we get the binary data of a number 1
        return number;
        // bytes: 0x0000000000000000000000000000000000000000000000000000000000000001 - This is how the computer understand the number
    }
}
```
****
