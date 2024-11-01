1. Solidity supports multiple inheritance including polymorphism:
    
    1. Polymorphism means that a function call (internal and external) always executes the function of the same name (and parameter types) in the most derived contract in the inheritance hierarchy
    2. When a contract inherits from other contracts, only a single contract is created on the blockchain, and the code from all the base contracts is compiled into the created contract.
    3. Function Overriding: Base functions can be overridden by inheriting contracts to change their behavior if they are marked as _virtual_. The overriding function must then use the _override_ keyword in the function header. 

2. Contract Types:
    
    1. Abstract Contracts: Contracts need to be marked as abstract when at least one of their functions is not implemented. They use the _abstract_ keyword.
    2. Interfaces: They cannot have any functions implemented. There are further restrictions: 
	 - They cannot inherit from other contracts, but they can inherit from other interfaces
	 - All declared functions must be external
	 - They cannot declare a constructor
	 - They cannot declare state variables. 
	 - They use the _interface_ keyword.
    3. Libraries: They are deployed only once at a specific address and their code is reused using the DELEGATECALL opcode. This means that if library functions are called, their code is executed in the context of the calling contract. They use the _library_ keyword.

- State Variable Shadowing: This is considered as an error. A derived contract can only declare a state variable x, if there is no visible state variable with the same name in any of its bases.
- Virtual Functions: Functions without implementation have to be marked virtual outside of interfaces. In interfaces, all functions are automatically considered virtual. Functions with private visibility cannot be virtual.
- Modifier Overriding: Function modifiers can override each other.

3. Library Restrictions: In comparison to contracts, libraries are restricted in the following ways:
    1. they cannot have state variables
    2. they cannot inherit nor be inherited
    3. they cannot receive Ether
    4. they cannot be destroyed
    5. it can only access state variables of the calling contract if they are explicitly supplied (it would have no way to name them, otherwise)
    6. Library functions can only be called directly (i.e. without the use of DELEGATECALL) if they do not modify the state (i.e. if they are view or pure functions), because libraries are assumed to be stateless

 - EVM Storage: Storage is a key-value store that maps 256-bit words to 256-bit words and is accessed with EVM’s SSTORE/SLOAD instructions.
 - Storage Layout: State variables of contracts are stored in storage in a compact way such that multiple values sometimes use the same storage slot.
 - Storage Layout Packing: For each state variable, a size in bytes is determined according to its type. Multiple, contiguous items that need less than 32 bytes are packed into a single storage slot if possible.
 - Storage Layout for Mappings & Dynamically-sized Arrays: Due to their unpredictable size, mappings and dynamically-sized array types cannot be stored “in between” the state variables preceding and following them. Instead, they are considered to occupy only 32 bytes with regards to the rules above and the elements they contain are stored starting at a different storage slot that is computed using a Keccak-256 hash.

 - EVM Memory: EVM memory is linear and can be addressed at byte level and accessed with MSTORE/MSTORE8/MLOAD instructions.
 - Memory Layout: Solidity places new memory objects at the free memory pointer and memory is never freed.

 - Inline Assembly: Inline assembly is a way to access the Ethereum Virtual Machine at a low level.
 - The language used for inline assembly in Solidity is called Yul
 - An inline assembly block is marked by _assembly { ... }_, where the code inside the curly braces is code in the Yul language

4. Inline Assembly Access to External Variables, Functions and Libraries: 
    
    1. You can access Solidity variables and other identifiers by using their name.
    2. Local variables of value type are directly usable in inline assembly.
    3. Local variables that refer to memory/calldata evaluate to the address of the variable in memory/calldata and not the value itself.
    4. For local storage variables or state variables, a single Yul identifier is not sufficient, since they do not necessarily occupy a single full storage slot. Therefore, their “address” is composed of a slot and a byte-offset inside that slot. To retrieve the slot pointed to by the variable x, you use x.slot, and to retrieve the byte-offset you use x.offset. Using x itself will result in an error.
    5. Local Solidity variables are available for assignments
    6. Assignments are possible to assembly-local variables and to function-local variables. Take care that when you assign to variables that point to memory or storage, you will only change the pointer and not the data.
    7. You can assign to the .slot part of a local storage variable pointer. For these (structs, arrays or mappings), the .offset part is always zero. It is not possible to assign to the .slot or .offset part of a state variable, though

 - Functions without implementation outside an interface have to be marked virtual. When overriding a function or modifier, the new keyword override must be used. When overriding a function or modifier defined in multiple parallel bases, all bases must be listed in parentheses after the keyword like so: override(Base1, Base2).
 - The new keyword abstract can be used to mark contracts as abstract. It has to be used if a contract does not implement all its functions. Abstract contracts cannot be created using the new operator, and it is not possible to generate bytecode for them during compilation.
 - The unnamed function commonly referred to as “fallback function” was split up into a new fallback function that is defined using the fallback keyword and a receive ether function defined using the receive keyword.
 - Arithmetic operations revert on underflow and overflow. You can use unchecked { ... } to use the previous wrapping behaviour.
 - enum definitions cannot contain more than 256 members.
 - Zero Address Check: address(0) which is 20-bytes of 0’s is treated specially in Solidity contracts because the private key corresponding to this address is unknown. Ether and tokens sent to this address cannot be retrieved and setting access control roles to this address also won’t work (no private key to sign transactions).
 - _tx.origin_ Check: Recall that Ethereum has two types of accounts: Externally Owned Account (EOA) and Contract Account. Transactions can originate only from EOAs. In situations where contracts would like to determine if the _msg.sender_ was a contract or not, checking if _msg.sender_ is equal to _tx.origin_ is an effective check.
 - Overflow/Underflow Check: Until Solidity version 0.8.0 which introduced checked arithmetic by default, arithmetic was unchecked and therefore susceptible to overflows and underflows which could lead to critical vulnerabilities.
 - OpenZeppelin ERC777: Like ERC20, ERC777 is a standard for fungible tokens with improvements such as getting rid of the confusion around decimals, minting and burning with proper events, among others, but its killer feature is receive hooks. A hook is simply a function in a contract that is called when tokens are sent to it, meaning accounts and contracts can react to receiving tokens.
 - OpenZeppelin ERC1155 Token Standard - Fungability-agnostic. Single Contract - Multiple Tokens, Single Tx - Multiple Tokens
 - OpenZeppelin Pausable: provides an emergency stop mechanism using functions pause and unpause that can be triggered by an authorized account. This module is used through inheritance. It will make available the modifiers _whenNotPaused_ and _whenPaused_, which can be applied to the functions of your contract. Only the functions using the modifiers will be affected when the contract is paused or unpaused.
 - OpenZeppelin ReentrancyGuard: prevents reentrant calls to a function. Inheriting from ReentrancyGuard will make the _nonReentrant_ modifier available, which can be applied to functions to make sure there are no nested (reentrant) calls to them.
 - OpenZeppelin PullPayment: provides a pull-payment strategy, where the paying contract doesn’t invoke any functions on the receiver account which must withdraw its payments itself. Pull-payments are often considered the best practice when it comes to sending Ether, security-wise. It prevents recipients from blocking execution and eliminates reentrancy concerns.

5. OpenZeppelin Address: Collection of functions related to the address type:
    
    1. _isContract(address account)_ → _bool_: Returns true if account is a contract. It is unsafe to assume that an address for which this function returns false is an externally-owned account (EOA) and not a contract. Among others, isContract will return false for the following types of addresses: 
     - an externally-owned account
     - a contract in construction
     - an address where a contract will be created 
     an address where a contract lived, but was destroyed
    2. _sendValue(address payable recipient, uint256 amount)_: Replacement for Solidity’s transfer: sends amount wei to recipient, forwarding all available gas and reverting on errors. EIP1884 increases the gas cost of certain opcodes, possibly making contracts go over the 2300 gas limit imposed by transfer, making them unable to receive funds via transfer. sendValue removes this limitation.
    3. _functionCall(address target, bytes data)_ → _bytes_: Performs a Solidity function call using a low level call. A plain `call` is an unsafe replacement for a function call: use this function instead. If target reverts with a revert reason, it is bubbled up by this function (like regular Solidity function calls). Returns the raw returned data. Requirements: target must be a contract. calling target with data must not revert.
    4. _functionCallWithValue(address target, bytes data, uint256 value)_ → _bytes_: Same as functionCall, but also transferring value wei to target. Requirements: 1) the calling contract must have an ETH balance of at least value. 2) the called Solidity function must be payable.
    5. _functionStaticCall(address target, bytes data)_ → _bytes_: Same as functionCall, but performing a static call.
    6. _functionDelegateCall(address target, bytes data)_ → _bytes_: Same as functionCall, but performing a delegate call.

6. OpenZeppelin Context: Provides information about the current execution context, including the sender of the transaction and its data. While these are generally available via _msg.sender_ and _msg.data_, they should not be accessed in such a direct manner, since when dealing with meta-transactions the account sending and paying for execution may not be the actual sender (as far as an application is concerned). This contract is only required for intermediate, library-like contracts. 
    
161. OpenZeppelin Counters: Provides counters that can only be incremented or decremented by one. This can be used e.g. to track the number of elements in a mapping, issuing ERC721 ids, or counting request ids. Functions are:
    
    1. current(struct Counters.Counter counter) → uint256
    2. increment(struct Counters.Counter counter)
    3. decrement(struct Counters.Counter counter)


7. OpenZeppelin SafeMath: provides mathematical functions that protect your contract from overflows and underflows. Include the contract with using SafeMath for uint256; and then call the functions:
    
    1. myNumber.add(otherNumber): Returns the addition of two unsigned integers, reverting on overflow. Counterpart to Solidity's `+` operator.
    2. myNumber.sub(otherNumber): Returns the subtraction of two unsigned integers, reverting on overflow (when the result is negative). Counterpart to Solidity's `-` operator.
    3. myNumber.div(otherNumber): Returns the division of two unsigned integers, reverting on overflow. The result is rounded towards zero. Counterpart to Solidity's `/` operator.
    4. myNumber.mul(otherNumber): Returns the multiplication of two unsigned integers, reverting on overflow. Counterpart to Solidity's `*` operator.
    5. myNumber.mod(otherNumber): Returns the modulus of two unsigned integers, reverting when dividing by zero. Counterpart to Solidity's `%` operator.


 - Uniswap V2 - an AMM. It allows liquidity providers to create pools of token pairs and whenever anyone provide liquidity to either of the two tokens of the token pair, the new tokens known as LP tokens minted and sent back to the liquidity provider.
 - Uniswap is the most popular protocol on Ethereum currently for swapping tokens.
 - Uniswap also provides support for on-chain Oracles.

 - Chainlink is perhaps the most widely used oracles and source of price feeds for SC on Ethereum.
 - Price data and even other kinds of data are taken from multiple off-chain data providers and they are put on-chain to create these feeds by the decentralized oracles on the chainlink network.