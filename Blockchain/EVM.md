**EVM** - Ethereum Virtual Machine is the core engine of Ethereum. It's designed to execute SC on the network. 

**The Ethereum Virtual Machine or EVM** is the runtime environment for smart contracts in Ethereum. It is not only sandboxed but actually completely isolated, which means that code running inside the EVM has no access to network, filesystem or other processes. Smart contracts even have limited access to other smart contracts.

All smart contracts and state changes on the Ethereum blockchain are executed by [transactions](https://ethereum.org/en/developers/docs/transactions/). The EVM handles all of the transaction processing on the Ethereum network. Under the hood, the EVM uses a set of opcode instructions to execute specific tasks.

**OPCODES**(operation code) - executable operations. One byte -> one operation.

The EVM executes as a [stack machine(opens in a new tab)](https://wikipedia.org/wiki/Stack_machine) with a depth of 1024 items. Each item is a 256-bit word, which was chosen for the ease of use with 256-bit cryptography (such as Keccak-256 hashes or secp256k1 signatures). The EVM is not a register machine but a stack machine, so all computations are performed on a data area called the **stack**. It has a maximum size of 1024 elements and contains words of 256 bits.
 
**EVM Components**
	- Worlds State, Accounts, Storage, Gas, Opcodes, Execution stack, Memory, Program Counter, Logs, Message calls
 
**EVM Execution Model** - When a transaction is submitted to the network, the EVM first verifies its validity. If the transaction is found to be invalid,it will be dismissed by the network.
 
Transaction -> Valid? -> If yes, then Execute -> Update state, consume gas, generate logs
					  -> If not -> Reject
 
**Gas and Opcode Execution**
Every opcode in a SC carries a specific gas cost which reflects the computational neccessary for its execition.
* opcode - low-level instruction executed by the EVM
 
SC -> Opcode -> Opcode Execution -> If success -> Update the state
								 -> If not Revert state and consume gas
 
**Stack and Memory** - critical components of the EVM. They enable SC to manage temporary data during opcode execution. The stack is a **LIFO(last in-first out)** data structure that is used for temporary storing values during opcode execution. The stacks supports two primary operations: push and pop. The push operation adds a value to the top of the stack, while the pop removes the top value from the stack.
 
Opcode execution -> Stack -> Push or Pop
				 -> Memory -> Write or Read

 
**Stack**
In the EVM so in the stack for the EVM we store 32 bytes word and we push them on and pop them off.(LIFO) So, when we store something in a stack. It will be a hex 32 byte code. If we want to store 1 - it looks - 0x000...001. I our hex the first char is the first 4 bytes - 0000.
 
**Memory** - Again 32 bytecode, but instead of stack you can write two specific memory words and choose the bits that you want to write to. Memory is used to store - structs, arrays, mappings, strings(always stored in memory). Memory  Variables declared outside of functions are in memory. Variables are temorary, external and are erased between function calls
 
**Calldata** - Store arbitrary bytes, but here you can only read from this area. That's why it's much chepaer.
 
**Storage** - the most expensive of all these different areas where you can store data. Also the only one of these areas that is permanent. Here we have storage slots. It's like a blockchain database.
 
Stack, Memory, Calldata - they are all temporary places to store data during a transaction
Variables are stored permanently on the blockchain. State variables are in storage by default.### EVM
****
*From Mastering Ethereum book:*

### Chapter 13: EVM

- Part of Ethereum that handles the execution and deployments of SC. Simple EoA to EoA everything else that interacts with a SC does.
- **EVM** - global decentralized computer that has milions of executable objects in storage and is shared accross a P2P network.
- The EVM has immutable code
- Volatile memory
- Permanent storage

**A transaction is received by a SC and the EVM is instantiated. EVM checks for transaction data and loads any bytecode into the stack as OPCODES(executable operations). EVM starts the execution of each OPCODE**

EVM can be seen as a computation engine and storage that has the ability to deploy and run different SC. The job of the EVM is to update the global state, triggered by a transaction execution of a SC.
Every OPCODE(operation code) has a fixed gas price. It's adjustable by how much an EoA is willing to pay for the transaction.

Gas fees have to be paid in Ethereum's native currency, ether (ETH). Gas prices are usually quoted in gwei, which is a denomination of ETH. Each gwei is equal to one-billionth of an ETH (0.000000001 ETH or 10-9 ETH).

**Gas cost vs. gas price**
 - **gas limit**: refers to the maximum price a [cryptocurrency](https://academy.binance.com/en/glossary/cryptocurrency) user is willing to pay when sending a transaction, or performing a [smart contract](https://academy.binance.com/en/articles/what-are-smart-contracts) function, in the [Ethereum](https://academy.binance.com/en/articles/what-is-ethereum) blockchain
 - **gas cost** - refers to the computational effort required to execute operations or transactions on the network.
 - **gas price** - refers to the amount of cryptocurrency (usually measured in Ether or Gwei) a user is willing to pay for each unit of gas to execute a transaction or run a smart contract on the Ethereum network.

**| Gas Cost = Gas Used * Gas Price**
**Example:** If a transaction consumes 50,000 gas and the gas price is set at 20 Gwei (0.000000001 Ether) per gas, the gas cost would be 50,000 gas * 20 Gwei/gas = 1 Ether.

**| Transaction fee = total gas used * gas price paid(in ETH)

**Negative gas costs(refund)**
 - Not all actions cost positive gas on the Ethereum blockchain. There are two actions that refund gas:
	 - Deleting a contract(selfdestruct) refunds 24,000 gas.
	 - Changing a storage address value from non-zero is 15,000 gas.

**Block gas limit**
	Let's say we have 5 transactions(each 20,000 gas), but the block limit is 80,000. The 4th transaction would have to wait for the next block.
