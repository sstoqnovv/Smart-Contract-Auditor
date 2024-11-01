**Gas** - Gas is a unit of measurement for the amount of work(computational effort) required to execute an operation or a smart contract. More complicated transaction - more computational power is need to execute a transaction.

Each tx is made of smaller components(computational power) in EVM. It;s made of opcodes. More complicated tx - more opcodes to do - more gas to execute.

The gas fee is **the amount of gas used to do some operation, multiplied by the cost per unit gas**. The fee is paid regardless of whether a transaction succeeds or fails.

**Base fee** - the minimum *gas price* to send your transaction
**Max fee** - the maximum fee per gas they are willing to pay total

**Priority fee** - maximum fee per gas they are willing to give to miners to incentivize them to include their transaction by miners

**Gas refund** - any unused gas(gasLimit - gas used by the transaction) is refunded to the sender's account at the same gasPrice.

**Block gas limit** - set by miners and refers to the cap on the total amount of gas expended by all transactions in the block, which ensures that blocks can't be arbitrary large. **It's the total gas spent all transactions in block**
	Let's say we have 5 transactions(each 20,000 gas), but the block limit is 80,000. The 4th transaction would have to wait for the next block.

 - **gasPrice:** A scalar value equal to the number of Wei to be paid per unit of gas for all computation costs incurred as a result of the execution of this transaction. More tx send from other people - more expensive gas price
 - **gasLimit:** maximum amount of gas that should be used in executing this transaction. One of the primary benefits of block gas limit is that it prevents attackers from creating an infinite transactions loop. If the gas usage of a transaction exceeds this limit, the transaction will fail.
 -  **gas cost** - refers to the computational effort required to execute operations or transactions on the network.

Every opcode in a SC carries a specific gas cost which reflects the computational neccessary for its execition.

**opcode** - low-level instruction executed by the EVM.

**SC -> Opcode -> Opcode Execution -> Success > Update the state
							   -> Fail -> Revert State
Both consume gas!

Gas fees have to be paid in Ethereum's native currency, ether (ETH). Gas prices are usually quoted in gwei, which is a denomination of ETH.

**| Gas Cost = Gas Used * Gas Price**
**Example:** If a transaction consumes 50,000 gas and the gas price is set at 20 Gwei (0.000000001 Ether) per gas, the gas cost would be 50,000 gas * 20 Gwei/gas = 1 Ether.

**Negative gas costs(refund)**
 - Not all actions cost positive gas on the Ethereum blockchain. There are two actions that refund gas:
	 - Deleting a contract(selfdestruct) refunds 24,000 gas.
	 - Changing a storage address value from non-zero is 15,000 gas.

The more people send transactions at the same time the more expensive your gas cost are

**| Transaction fee = total gas used * gas price paid(in ETH)
****
- In order to avoid issues of network abuse, all programmable computation in Ethereum is subject to fees. The fee schedule is specified in units of gas. Thus any given fragment of programmable computation (this includes creating contracts, making message calls, utilising and accessing account storage and executing operations on the virtual machine) has a universally agreed cost in terms of gas. 
 ****
### EIP-1559
Proposal for improving the fee market and transaction pricing mechanism on the Ethereum blockchain. The primary goals of EIP-1559 include improving the user experience, making transaction fees more predictable, and addressing issues related to network congestion.

Key features of EIP-1559 include:

1. **Base Fee:** EIP-1559 introduces the concept of a "base fee," which is a dynamically adjusted fee that users must pay for their transactions to be included in a block. The base fee is burned, meaning it is removed from circulation, reducing the overall supply of Ethereum.
2. **Gas Target:** Users can include a "tip" or priority fee along with the base fee to incentivize miners to prioritize their transactions. The sum of the base fee and the tip is known as the "gas target," and it represents the total fee paid by the user.
3. **Dynamic Block Size:** EIP-1559 adjusts the block size dynamically based on network demand. If the demand for block space is high, the block size increases, and if demand is low, the block size decreases. This is intended to keep block sizes within a target range.
4. **Fee Burn Mechanism:** The base fee, which is burned with each transaction, has an impact on Ethereum's overall supply. This fee burn mechanism is designed to make Ethereum a deflationary asset over time, potentially affecting its scarcity and value.
5. **Predictable Fees:** Users can have more predictable and transparent fee estimation because the base fee is adjusted automatically based on network demand. This contrasts with the previous model, where users competed for block space by setting higher gas prices.
