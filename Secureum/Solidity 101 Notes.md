 - Solidity is a high-level language for implementing smart contracts on Ethereum (and the blockchains) targeting the EVM.
 - It is influenced mainly by C++, a little from Python and early-on from JavaScript.
 - Solidity is statically typed, supports inheritance, libraries and complex user-defined types.
 - Solidity source files are recommended to start with a comment indicating its license e.g.:“_// SPDX-License-Identifier: MIT_”, where the compiler includes the supplied string in the bytecode metadata to make it machine readable.
 - The `pragma` keyword is used to enable certain compiler features or checks. This indicates the specific Solidity compiler version to be used.
  - Using the version pragma does not change the version of the compiler. It also does not enable or disable features of the compiler.
  - The SMTChecker module automatically tries to prove that the code satisfies the specification given by require and assert statements.
  - Other checks: Arithmetic underflow and overflow, Division by zero, Trivial conditions and unreachable code, Popping an empty array, Out of bounds index access, Insufficient funds for a transfer.

- Single-line comments (//) and multi-line comments (/*...*/) are possible.

- Contracts: They are similar to classes in object-oriented languages in that they contain persistent data in state variables and functions that can modify these variables. Contracts can inherit from other contracts. They can contain declarations of State Variables, Functions, Function Modifiers, Events, Errors, Struct Types and Enum Types.
- State Variables: They are variables that can be accessed by all functions of the contract and whose values are permanently stored in contract storage

1. State Visibility Specifiers: State variables have to be specified as being public, internal or private:
    
    1. public: Can be either accessed internally or via messages. An automatic getter function is generated.
    2. internal: Can only be accessed internally from within the current contract or contracts deriving from it
    3. private: Can only be accessed from the contract they are defined in and not even in derived contracts. Everything that is inside a contract is visible to all observers external to the blockchain. Making variables private only prevents other contracts from reading or modifying the information, but it will still be visible to the whole world outside of the blockchain.

2. State Variables: Constant & Immutable
    
    1. State variables can be declared as _constant_ or _immutable_. In both cases, the variables cannot be modified after the contract has been constructed. For _constant_ variables, the value has to be fixed at compile-time, while for _immutable_, it can still be assigned at construction time i.e. in the constructor or point of declaration.
    2. For constant variables, the value has to be a constant at compile time and it has to be assigned where the variable is declared. Any expression that accesses storage, blockchain data (e.g. `block.timestamp, address(this).balance or block.number`) or execution data `(msg.value or gasleft())` or makes calls to external contracts is disallowed.
    3. Immutable variables can be assigned an arbitrary value in the constructor of the contract or at the point of their declaration. They cannot be read during construction time and can only be assigned once.
    4. The compiler does not reserve a storage slot for these variables, and every occurrence is replaced by the respective value.
    5. Gas of the constant and immutable variables are much lower.

 - Functions: Functions are the executable units of code. Functions are usually defined inside a contract, but they can also be defined outside of contracts. They have different levels of visibility towards other contracts.
    
1. Function parameters: Function parameters are declared the same way as variables, and the name of unused parameters can be omitted. Function parameters can be used as any other local variable and they can also be assigned to.
2. Function Return Variables: Function return variables are declared with the same syntax after the _returns_ keyword. 

 - Function Modifiers: They can be used to change the behaviour of functions in a declarative way. The function’s control flow continues after the “_” in the preceding modifier. Multiple modifiers are applied to a function by specifying them in a whitespace-separated list and are evaluated in the order presented. The _ symbol can appear in the modifier multiple times. Each occurrence is replaced with the function body.

1. Function Visibility Specifiers: Functions have to be specified as being _public_, _external_, _internal_ or _private:_
    
    1. _public:_ Public functions are part of the contract interface and can be either called internally or via messages. 
    2. _external:_ External functions are part of the contract interface, which means they can be called from other contracts and via transactions. An external function f cannot be called internally.
    3. _internal:_ Internal functions can only be accessed internally from within the current contract or contracts deriving from it
    4. _private:_ Private functions can only be accessed from the contract they are defined in and not even in derived contracts

2. Function Mutability Specifiers: Functions can be specified as being _pure_ or _view_:
    
    1. _view_ functions can read contract state but cannot modify it. The following are considered state modifying: 
	    1) Writing to state variables
	    2) Emitting events 
	    3) Creating other contracts
	    4) Using selfdestruct
	    5) Sending Ether via calls
	    6) Calling any function not marked view or pure
	    7) Using low-level calls
	    8) Using inline assembly that contains certain opcodes.
    2. _pure_ functions can neither read contract state nor modify it. The following are considered reading from state: 
	    1) Reading from state variables
	    2) Accessing address(this).balance or <address>.balance
	    3) Accessing any of the members of block, tx, msg (with the exception of msg.sig and msg.data)
	    4) Calling any function not marked pure
	    5) Using inline assembly that contains certain opcodes.

1. Function Overloading: A contract can have multiple functions of the same name but with different parameter types. This process is called “overloading.”
    
    1. Overloaded functions are selected by matching the function declarations in the current scope to the arguments supplied in the function call.
    2. Return parameters are not taken into account for overload resolution.

 - Events: They are an abstraction on top of the EVM’s logging functionality. Emitting events cause the arguments to be stored in the transaction’s log - a special data structure in the blockchain.
 - Indexed Event Parameters: Adding the attribute _indexed_ for up to three parameters adds them to a special data structure known as “topics” instead of the data part of the log.
 - Emit: Events are emitted using `emit`, followed by the name of the event and the arguments e.g. “_emit Deposit(msg.sender, _id, msg.value);_”

 - Constructor: When a contract is created, its constructor (a function declared with the _constructor_ keyword) is executed once. A constructor is optional and only one constructor is allowed. After the constructor has executed, the final code of the contract is stored on the blockchain. This code includes all public and external functions and all functions that are reachable from there through function calls. The deployed code does not include the constructor code or internal functions only called from the constructor.

1. Receive Function: A contract can have at most one _receive_ function, declared using _receive() external payable { ... }_ without the function keyword. This function cannot have arguments, cannot return anything and must have external visibility and payable state mutability.
    
    1. The receive function is executed on a call to the contract with empty calldata. This is the function that is executed on plain Ether transfers via _.send()_ or _.transfer()_.
        
2. Fallback Function: A contract can have at most one fallback function, declared using either _fallback () external [payable]_ or _fallback (bytes calldata _input) external [payable] returns (bytes memory _output),_ both without the function keyword. This function must have external visibility.
    
    1. The fallback function is executed on a call to the contract if none of the other functions match the given function signature, or if no data was supplied at all and there is no receive Ether function. The fallback function always receives data, but in order to also receive Ether it must be marked payable.
    2. In the worst case, if a payable fallback function is also used in place of a receive function, it can only rely on 2300 gas being available

 - Default Values: A variable which is declared will have an initial default value whose byte-representation is all zeros. For example, the default value for a bool is false. The default value for the uint or int types is 0. For statically-sized arrays and bytes1 to bytes32, each individual element will be initialized to the default value corresponding to its type. For dynamically-sized arrays, bytes and string, the default value is an empty array or string. For the enum type, the default value is its first member.


1. Members of Address Type:
    
    1. _<address>.balance (uint256)_: balance of the Address in Wei
    2. _<address>.code (bytes memory)_: code at the Address (can be empty)
    3. _<address>.codehash (bytes32)_: the codehash of the Address
    4. _<address payable>.transfer(uint256 amount)_: send given amount of Wei to Address, reverts on failure, forwards 2300 gas stipend, not adjustable
    5. _<address payable>.send(uint256 amount)_ returns (bool): send given amount of Wei to Address, returns false on failure, forwards 2300 gas stipend, not adjustable
    6. _<address>.call(bytes memory)_ returns (bool, bytes memory): issue low-level CALL with the given payload, returns success condition and return data, forwards all available gas, adjustable
    7. _<address>.delegatecall(bytes memory)_ returns (bool, bytes memory): issue low-level DELEGATECALL with the given payload, returns success condition and return data, forwards all available gas, adjustable
    8. _<address>.staticcall(bytes memory)_ returns (bool, bytes memory): issue low-level STATICCALL with the given payload, returns success condition and return data, forwards all available gas, adjustable


 - Transfer: The _transfer_ function fails if the balance of the current contract is not large enough or if the Ether transfer is rejected by the receiving account. The _transfer_ function reverts on failure. The code in _receive_ function or if not present then in _fallback_ function is executed with the transfer call. If that execution runs out of gas or fails in any way, the Ether transfer will be reverted and the current contract will stop with an exception.
 - Send: The _send_ function is the low-level counterpart of _transfer_. If the execution fails then _send_ only returns false and does not revert unlike _transfer._ So the return value of _send_ must be checked by the caller.
    
 - Call/Delegatecall/Staticcall: In order to interface with contracts that do not adhere to the ABI, or to get more direct control over the encoding, the functions _call_, _delegatecall_ and _staticcall_ are provided. They all take a single _bytes_ memory parameter and return the success condition (as a bool) and the returned data _(bytes memory)_. The functions _abi.encode_, _abi.encodePacked_, _abi.encodeWithSelector_ and _abi.encodeWithSignature_ can be used to encode structured data.
    
    1. _gas_ and _value_ modifiers can be used with these functions (_delegatecall_ doesn’t support _value_) to specify the amount of gas and Ether value passed to the callee.
    2. With _delegatecall_, only the code of the given address is used but all other aspects (storage, balance, msg.sender etc.) are taken from the current contract. The purpose of _delegatecall_ is to use library/logic code which is stored in callee contract but operate on the state of the caller contract
    3. With staticcall, the execution will revert if the called function modifies the state in any way

1. Reference Types & Data Location: Every reference type has an additional annotation — the data location where it is stored. There are three data locations: _memory_, _storage_ and _calldata_. 
    
    1. _memory_: whose lifetime is limited to an external function call
    2. _storage_: whose lifetime is limited to the lifetime of a contract and the location where the state variables are stored
    3. _calldata_: which is a non-modifiable, non-persistent area where function arguments are stored and behaves mostly like memory. It is required for parameters of external functions but can also be used for other variables.

Arrays: Arrays can have a compile-time fixed size, or they can have a dynamic size. Indices are zero based.
1. Array members:
    
    1. _length_: returns number of elements in array
    2. _push()_: appends a zero-initialised element at the end of the array and returns a reference to the element
    3. _push(x)_:  appends a given element at the end of the array and returns nothing
    4. _pop_: removes an element from the end of the array and implicitly calls delete on the removed element

Gas costs of push and pop: Increasing the length of a storage array by calling push() has constant gas costs because storage is zero-initialised, while decreasing the length by calling pop() has a cost that depends on the “size” of the element being removed.

Array Slices: Array slices are a view on a contiguous portion of an array. They are written as x[start:end].


1. Struct Types: Structs help define new aggregate types by combining other value/reference types into one unit. Struct types can be used inside mappings and arrays and they can themselves contain mappings and arrays. It is not possible for a struct to contain a member of its own type
2. Mapping Types: Mappings define key-value pairs and are declared using the syntax _mapping(_KeyType => _ValueType) _VariableName._ 
    1. The __KeyType_ can be any built-in value type, bytes, string, or any contract or enum type. Other user-defined or complex types, such as mappings, structs or array types are not allowed. __ValueType_ can be any type, including mappings, arrays and structs.      
    2. You cannot iterate over mappings, i.e. you cannot enumerate their keys. It is possible, though, to implement a data structure on top of them and iterate over that.. The have no length, no iteration.
delete

1. delete a assigns the initial value for the type to a.

! Do not rely on `block.timestamp or blockhash` as a source of randomness. Both the timestamp and the block hash can be influenced by miners to some degree.
-------------------------------------------------------------------------------------------------------------
1. ABI Encoding and Decoding Functions:
    
    1. _abi.decode(bytes memory encodedData, (...)) returns (...)_: ABI-decodes the given data, while the types are given in parentheses as second argument.
    2. _abi.encode(...)_ returns (bytes memory): ABI-encodes the given arguments
    3. _abi.encodePacked(...)_ returns (bytes memory): Performs packed encoding of the given arguments. Note that packed encoding can be ambiguous!
    4. _abi.encodeWithSelector(bytes4 selector, ...) returns (bytes memory)_: ABI-encodes the given arguments starting from the second and prepends the given four-byte selector
    5. _abi.encodeWithSignature(string memory signature, ...) returns (bytes memory)_: Equivalent to abi.encodeWithSelector(bytes4(keccak256(bytes(signature))), …)

1. Error Handling:
    
    1. _assert(bool condition)_: causes a Panic error and thus state change reversion if the condition is not met - to be used for internal errors.
    2. _require(bool condition)_: reverts if the condition is not met - to be used for errors in inputs or external components.
    3. _require(bool condition, string memory message)_: reverts if the condition is not met - to be used for errors in inputs or external components. Also provides an error message.
    4. _revert()_: abort execution and revert state changes
    5. _revert(string memory reason)_: abort execution and revert state changes, providing an explanatory string

1. Mathematical and Cryptographic Functions:
    
    1. _addmod(uint x, uint y, uint k) returns (uint)_: compute (x + y) % k where the addition is performed with arbitrary precision and does not wrap around at 2**256. Assert that k != 0 starting from version 0.5.0.
    2. _mulmod(uint x, uint y, uint k) returns (uint)_: compute (x * y) % k where the multiplication is performed with arbitrary precision and does not wrap around at 2**256. Assert that k != 0 starting from version 0.5.0.
    3. _keccak256(bytes memory) returns (bytes32)_: compute the Keccak-256 hash of the input
    4. _sha256(bytes memory) returns (bytes32)_: compute the SHA-256 hash of the input
    5. _ripemd160(bytes memory) returns (bytes20)_: compute RIPEMD-160 hash of the input
    6. _ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s) returns (address):_ recover the address associated with the public key from elliptic curve signature or return zero on error. The function parameters correspond to ECDSA values of the signature: _r = first 32 bytes of signature, s = second 32 bytes of signature, v = final 1 byte of signature. ecrecover_ returns an address, and not an address payable.

 - If you use _ecrecover_, be aware that a valid signature can be turned into a different valid signature without requiring knowledge of the corresponding private key. This is usually not a problem unless you require signatures to be unique or use them to identify items. OpenZeppelin has a ECDSA helper library that you can use as a wrapper for _ecrecover_ without this issue.

 - Contract Related:
    
    1. _this_ (current contract’s type): the current contract, explicitly convertible to Address
    2. _selfdestruct(address payable recipient)_: Destroy the current contract, sending its funds to the given Address and end execution.

 - _try/catch_: The _try_ keyword has to be followed by an expression representing an external function call or a contract creation (_new ContractName()_). Errors inside the expression are not caught (for example if it is a complex expression that also involves internal function calls), only a revert happening inside the external call itself. The returns part (which is optional) that follows declares return variables matching the types returned by the external call. In case there was no error, these variables are assigned and the contract’s execution continues inside the first success block. If the end of the success block is reached, execution continues after the _catch_ blocks.

- 1. Solidity supports different kinds of catch blocks depending on the type of error:

    
    1. _catch Error(string memory reason) { ... }_: This catch clause is executed if the error was caused by revert("reasonString") or require(false, "reasonString") (or an internal error that causes such an exception).
    2. _catch Panic(uint errorCode) { ... }_: If the error was caused by a panic, i.e. by a failing assert, division by zero, invalid array access, arithmetic overflow and others, this catch clause will be run.
    3. _catch (bytes memory lowLevelData) { ... }_: This clause is executed if the error signature does not match any other clause, if there was an error while decoding the error message, or if no error data was provided with the exception. The declared variable provides access to the low-level error data in that case.
    4. _catch { ... }_: If you are not interested in the error data, you can just use _catch { ... }_ (even as the only catch clause) instead of the previous clause.

ETH Transfer:
transfer ---> receive/fallback
Security: Reentrancy Mitigation, Gass Assumption

ETH Send
send ---> receive/fallback
Security: Reentrancy Mitigation, Gas Assumption, Return Value Check

Time Units
seconds ---> Base Unit 1 == 1 seconds, 1 min. == 60 sec., etc.

Block & Tx Properties:
Block: Hash, ChainId, Number, Timestamp
Block: Coinbase, Difficulty, GasLimit
Msg: Value, Data, Sender, Sig
Tx: Gasprice, Gasleft, Origin