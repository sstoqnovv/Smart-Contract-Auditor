**Application Binary Interface(ABI)** - standard way to interact with contracts in the Ethereum ecosystem.

### Function Selector[](https://docs.soliditylang.org/en/latest/abi-spec.html#function-selector "Permalink to this heading")

The first four bytes of the call data for a function call specifies the function to be called. It is the first (left, high-order in big-endian) four bytes of the Keccak-256 hash of the signature of the function. 
For each function defined in a smart contract, the ABI specifies a unique function signature, which is derived from the function name and its parameter types.

### Data Encoding

The ABI defines rules for encoding and decoding data types used in smart contracts, such as integers, addresses, arrays, structs, and custom data structures.
**Data encoding** is the process of converting data from its native representation in a high-level programming language (such as Solidity or JavaScript) into a format suitable for storage on the blockchain or transmission over the network. In Ethereum, data encoding is primarily performed using the Ethereum Contract ABI (Application Binary Interface) standard.

#### Encoding Rules:

- **Static Types**: Simple data types like integers and addresses have fixed-length representations. For example, an address is encoded as a 20-byte value.
- **Dynamic Types**: Complex data types like strings and arrays have variable-length representations. These types are encoded in two parts: the length of the data followed by the data itself.
- **Complex Types**: Structs and nested arrays are recursively encoded, with each component encoded according to its type.

#### Example:

Consider encoding the parameters `(uint256, bool, address[])`:
1. `uint256`: Encoded directly as a 32-byte value.
2. `bool`: Encoded as a single byte, where `true` is `0x01` and `false` is `0x00`.
3. `address[]`: Encoded as a dynamic array, where the first 32 bytes represent the array length, followed by the encoded addresses.

### Data Decoding:

Data decoding is the reverse process of encoding, where binary data retrieved from the blockchain or received over the network is converted back into its original high-level data representation.