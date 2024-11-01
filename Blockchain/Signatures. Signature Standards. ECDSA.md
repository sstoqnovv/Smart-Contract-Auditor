### Signature Standards

When signing txs, we need an easier way to read and understand tx data which usually looks like this:

```
{"jsonrpc":"2.0","id":1,"result":{"blockHash":"0x949f40920a86f281daccbe8e30dd60a366b22ff270647815f6bfc0402ff38e42",
"blockNumber":"0xce3",
"from":"0x047347096a6dc73f8626afb520c383a02efda314","
gas":"0x15f90",
"gasPrice":"0x4a817c800",
"hash":"0x70a7552c8ab8d2621c80c8a1c149012d10a823c4619cc82235cbdfad0553310b",
"input":"0x021df6f4000000000000000000000000000000000000000000000000000000000000000d48656c6c6f2c20776f726c642100000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000d48656c6c6f2c20776f726c642100000000000000000000000000000000000000",
"nonce":"0x178",
"to":"0xe2412bb63a0a25d7b8973fc6764fd246ebe62c7a",
"transactionIndex":"0x0",
"value":"0x0",
"v":"0x1b",
"r":"0xd693b532a80fed6392b428604171fb32fdbf953728a3a7ecc7d4062b1652c042",
"s":"0x24e9c602ac800b983b035700a14b23f78a253ab762deab5dc27e3555a750b354"}}
```

### Signature Verification

```php
function getSignerSimple(uint256 message, uint8 _v, bytes32 _r, bytes32 _s) public pure returns (address) {
    bytes32 hashedMessage = bytes32(message); // If string, use keccak256(abi.encodePacked(string))
    address signer = ecrecover(hashedMessage, _v, _r, _s);
    return signer;
}
```
Let's start with a basic signature verification contract. It retrieves the **signer address** using the `ecrecover` function and then verifies signatures by comparing the signer with the expected one.

> `ecrecover(bytes32 hash, uint8 v, bytes32 r, bytes32 s)` is a function built into the Ethereum protocol. 
> `ecrecover()` - recover the address associated with the public key from elliptic curve signature or return 0 or an error.

> **v, r, s** - ECDSA Signature -> 65 bytes. r, s - Signature, v - recovery identifier - it is used to recover the public key from the signature during the verification process.

```php
function verifySignerSimple(
    uint256 message,
    uint8 _v,
    bytes32 _r,
    bytes32 _s,
    address signer
) public pure returns (bool) {
    address actualSigner = getSignerSimple(message, _v, _r, _s);
    require(signer == actualSigner);
    return true;
}
```
#### EIP-191(Signed data standard)

EIP-191 facilitates pre-made signatures or _sponsored transactions_. For instance, Bob can sign a message, and Alice can send the transaction and pay for Bob’s gas fees.
This EIP standardizes the signed data format:
`0x19 <1 byte version> <version specific data> <data to sign>

- **0x19 Prefix:** Indicates that the data is a signature.
- **1-byte Version:** Defines the signed data version.
    - `0x00`: Data with an intended validator.
    - `0x01`: Structured data, commonly used in production apps and associated with EIP-712.
    - `0x45`: Personal signed messages.
- **Version Specific Data:** For version `0x01`, this is the validator address.
- **Data to Sign:** The message we want to sign.`

Here is how to set up EIP-191, by encoding and then hashing the message before retrieving the signer:

```php
function getSigner191(uint256 message, uint8 _v, bytes32 _r, bytes32 _s) public view returns (address) {
    // Prepare data for hashing
    bytes1 prefix = bytes1(0x19);
    bytes1 eip191Version = bytes1(0);
    address intendedValidatorAddress = address(this);
    bytes32 applicationSpecificData = bytes32(message);

    // Standardized message format
    bytes32 hashedMessage = keccak256(abi.encodePacked(prefix, eip191Version, intendedValidatorAddress, applicationSpecificData));

    address signer = ecrecover(hashedMessage, _v, _r, _s);
    return signer;
}
```
### EIP-712

EIP-712 is a standard for structuring and signing typed data in Ethereum, enhancing readability and ensuring specificity to certain contracts. The format for signing data using EIP-712 is:

```
0x19 0x01 <domainSeparator> <hashStruct(message)>
```

1. **Domain Separator:** Version-specific data.
2. **hashStruct(message):** The hash of the structured message you want to sign.
#### EIP-712: Domain Separator

To define the domain separator, we first declare a domain separator struct and its type hash:

```php
struct EIP712Domain {
    string name;
    string version;
    uint256 chainId;
    address verifyingContract;
};

bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
```

The domain separator is obtained by encoding and hashing the `EIP712Domain` struct:

```php
bytes32 domainSeparator = keccak256(
  abi.encode(
    EIP712DOMAIN_TYPEHASH,
    keccak256(bytes(eip712Domain.name)),
    keccak256(bytes(eip712Domain.version)),
    eip712Domain.chainId,
    eip712Domain.verifyingContract
  )
);
```

#### EIP-712: Message Hash Struct

First, define the message struct and its type hash:

```php
struct Message {
    uint256 number;
};

bytes32 public constant MESSAGE_TYPEHASH = keccak256("Message(uint256 number)");
```

Then encode and hash them together:

```php
bytes32 hashedMessage = keccak256(abi.encode(MESSAGE_TYPEHASH, Message({ number: message })));
```

### EIP-712: Implementation

Steps for EIP-712 implementation:

1. Define a domain separator struct with essential data.
2. Hash the struct and its type hash to create the domain separator.
3. Create a message type hash and combine it with the message data to generate a hashed message.
4. Combine all elements with a prefix and version byte to form a final digest.
5. Use `ecrecover` with the digest and signature to retrieve the signer's address and verify authenticity.

```php
contract SignatureVerifier {

    function getSignerEIP712(uint256 message, uint8 _v, bytes32 _r, bytes32 _s) public view returns (address) {
        // Prepare data for hashing
        bytes1 prefix = bytes1(0x19);
        bytes1 eip712Version = bytes1(0x01); // EIP-712 is version 1 of EIP-191
        bytes32 hashStructOfDomainSeparator = domainSeparator;

        // Hash the message struct
        bytes32 hashedMessage = keccak256(abi.encode(MESSAGE_TYPEHASH, Message({ number: message })));

        // Combine all elements
        bytes32 digest = keccak256(abi.encodePacked(prefix, eip712Version, hashStructOfDomainSeparator, hashedMessage));
        return ecrecover(digest, _v, _r, _s);
    }
}
```

We can then verify the signer as in the first example, but using `verifySignerEIP712`:

```php
function verifySignerEIP712(
    uint256 message,
    uint8 _v,
    bytes32 _r,
    bytes32 _s,
    address signer
) public view returns (bool) {
    address actualSigner = getSignerEIP712(message, _v, _r, _s);
    require(signer == actualSigner);
    return true;
}
```

### EIP 712: OpenZeppelin

It's recommended to use OpenZeppelin libraries to simplify the process, by using `EIP712::_hashTypedDataV4` function:

- Create the message type hash and hash it with the message data:
    
    ```php
    bytes32 public constant MESSAGE_TYPEHASH = keccak256("Message(uint256 message)");
    
    function getMessageHash(uint256 _message) public view returns (bytes32) {
        return _hashTypedDataV4(
            keccak256(
                abi.encode(
                    MESSAGE_TYPEHASH,
                    Message({message: _message})
                )
            )
        );
    }
    ```
    
- Retrieve the signer with `ECDSA.tryRecover` and compare it to the actual signer:

```php
function getSignerOZ(uint256 digest, uint8 _v, bytes32 _r, bytes32 _s) public pure returns (address) {
    (address signer, /* ECDSA.RecoverError recoverError */, /* bytes32 signatureLength */ ) = ECDSA.tryRecover(digest, _v, _r, _s);
    return signer;
}
```

```php
function verifySignerOZ(
    uint256 message,
    uint8 _v,
    bytes32 _r,
    bytes32 _s,
    address signer
) public pure returns (bool) {
    address actualSigner = getSignerOZ(getMessageHash(message), _v, _r, _s);
    require(actualSigner == signer);
    return true;
}
```

> 👀❗**IMPORTANT**:br EIP 712 prevents replay attacks by uniquely identifying the transaction.

### Conclusion

EIP-191 standardizes the format of signed data, while EIP-712 extends data standardization to structured data and introduces domain separators to prevent cross-domain replay attacks.
****
### Signature Replay Attacks

Where the same tx can be sent more than once or the same signature used more than once.
****
### ECDSA

**Signatures** - Provide authentication in blockchain technology. Verify that the message(tx) originates from the intended sender. Proof of Ownership includes public and private keys and signatures.

**Private key** is what is used to sign the message and is used to derive the public key. 
**Public key** is derived from the private key. It is used to verify that a signature was indeed created by the corresponding private key. The public key can be shared with others unlike private key or SRP.

**Address** - used as identification and is the last 20bytes of the hash of the public key.

**ECDSA(Elliptic Curve Digital Signature Algorithm**). It is a widely used cryptographic algorithm that provides a method for creating digital signatures, generating key pairs, verifying signatures.

### Creating Signature process

ECDSA signatures consist of three integers: `v`, `r`, and `s`:

1. The message is hashed
2. A random number `k` (the nonce) is generated.
3. **Calculating Signature Components**:
    - **r**: Represents the x-coordinate on the elliptic curve of the point resulting from multiplying the nonce `k` by the generator point `G`.
    - **s**: Serves as proof of the signer's knowledge of the private key, calculated using the nonce `k`, the hash of the message, the private key, and the `r` value.
    - **v**: Indicates the polarity (positive or negative y-axis) of the point on the elliptic curve.
- `r = x-coordinate of (k * G)`

### Verifying Signatures

Verifying ECDSA signatures involves using the signed message, the signature, and the public key to check if the signature is valid. This process essentially reverses the signing algorithm to ensure the provided `r` coordinate matches the calculated one.

**Signature malleability is property of ECDSA that allows anyone to modify a valid digital signature to create another valid signature for the same message. **

>👮‍♂️ **BEST PRACTICE**:br Using `ecrecover` directly can lead to security issues such as signature malleability. This can be mitigated by restricting the value of `s` to one half of the curve. The use of **OpenZeppelin's ECDSA library** is recommended, which provides protection against signature malleability and prevents invalid signatures from returning a zero address.