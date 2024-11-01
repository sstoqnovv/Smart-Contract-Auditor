**Ethereum** - A public(open-source, globally, decentralized) blockchain that supports smart contracts. It's infrastructure for dApps using smart contracts. It's distributed ledger, maintained by a network of nodes. It allows to develop and deploy smart contracts and dApps where they can create their own arbitrary rules for ownership, ransaction and funds.

**Ethereum's core components**:
 - **P2P network**
 - **Transactions** - network messages that include a **sender, value, data payload**
 - **State machine** - EVM(executes bytecode)
 - **Consensus** - PoS algorithm
 - **Economic security**
 - **Clients**

Each instruction has a predefined cost in **units of gas**
Ethereum uses public key cryptography to create **public-private keys** pairs

Ethereum account has 4 fields:
 - **Nonce** - counter representing the number of transactions sent from the account(increments with each transaction sent from the account. The nonce helps prevent replay attacks). It ensures that transactions are processed in the correct order and helps prevent replay attacks.
 - **Account's current ETH balance** - The balance field indicates the amount of ether (ETH) held by the externally owned account.
 - **Account's contract code**
 - **Account's storage** - This value represents the financial resources available to the account. It's applicable onlt for **contract accounts**

Ethereum has 2 types of accounts:
- **Externally owned Accounts(EoAs)** controlled by private keys
- **Contract accounts** - controlled by their contract code

Ownership of ETH by EoAs is established through private keys. Ethereum addresses and digital signatures. Anyone with a private key has control of the correspnding EoA account and any ETH it holds.

### Externally Owned Accounts (EOAs):

1. **Owned by Individuals:**
    - EOAs are accounts that are owned and controlled by individual users. These users hold the private keys associated with their EOAs, which are used to sign transactions.
2. **Human-Readable Address:**
    - EOAs have Ethereum addresses that are typically represented as hexadecimal strings. These addresses are used for sending and receiving Ether (ETH) and interacting with smart contracts.
3. **Balance and Ether Transactions:**
    - EOAs have a balance field, which represents the amount of Ether they hold. Users can send transactions from their EOAs to other accounts, including other EOAs and smart contracts.
### Contract Accounts:

1. **Code Execution:**
    - Contract accounts, unlike EOAs, contain executable code. They are deployed on the Ethereum blockchain and can perform computations and store data. Contracts are written in programming languages like Solidity.
2. **Ethereum Address:**
    - Contract accounts also have Ethereum addresses, similar to EOAs. Transactions can be sent to these addresses to trigger the execution of the contract's code.
3. **Balance and Ether Transactions:**
    - Contract accounts have a balance field, just like EOAs. They can hold and manage Ether. However, unlike EOAs, contract accounts can't initiate transactions on their own; they execute code when transactions are sent to them.
4. **Code Hash and Storage Root:**
    - Contract accounts have a code hash, which is the hash of their executable code. Additionally, they have a storage root, representing the root of the Merkle Patricia tree that stores the contract's persistent data.
5. **Smart Contract Interactions:**
    - Smart contracts can interact with other smart contracts and EOAs by receiving and sending messages. These interactions can trigger the execution of code within the recipient contract.

**Ethereum address** - address of an EoAs account is the last 20 bytes of the keccak256()


Here's a breakdown of the process of deriving the address from keccak256():

1. **Public Key Generation:**
    - An EOA is associated with a public-private key pair. The public key is derived from the private key using ECDSA.
2. **Hashing with keccak256:**
    - The keccak256 hashing algorithm (SHA-3) is applied to the public key to produce a 256-bit hash.
3. **Hexadecimal Representation:**
    - The final Ethereum address is commonly represented in hexadecimal format and is 40 characters long.

**Ether(ETH)** - native cryptocurrency of the Ethereum platform. It's used as a value of exchange to pay transaction fees. ETH -> 18 decimals. Wei -> 10 ** 18 wei -> 1 ETH
****
*From Mastering Ethereum book:*

### CHAPTER 2:
- Ether currency units. Ether is subdivided the smalles unit possible, called wei
    - 1 wei = 1 000 000 000 000 000 000 000 (Ether) - 10 ** 18
- Metamask - the most common used wallet
- Ether is meant to be mainly used for the execution of Smart Contracts
- Externally owned accounts(EoA) and Contract Metamask accounts are EoAs. It's an account that has a private key.

### CHAPTER 3:

**Ethereum clients** - software application that implmenets the Ethereum specification and communicates together with other clients with the P2P network.
- Clients written in different languages can work together if they all follow the same set of rules.