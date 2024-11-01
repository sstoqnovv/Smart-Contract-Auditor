**Token** - Cryptographic tokens represent programmable assets or access rights, managed by a smart contract and an underlying distributed ledger. They are accessible only by the person who has the private key for that address and can only be signed using this private key.

**Token Burn** - The process of removing tokens from circulation in order to reduce the total token supply.

**Token Distribution** - The way in which tokens are initially distributed among investors, developers, and other stakeholders such as users on the network.

**Token Liquidity** - The ease with which tokens can be bought or sold on a market, which affects their overall value and market cap.

**Token Supply** - The total number of tokens that will ever be created for a particular blockchain network.

**Token Utility** - The function or purpose of a token within a blockchain network, such as providing access to certain services or products.

**Token Type - ERC-20** - A type of fungible Ethereum token (i.e., smart contract) standard which is defined by a series of functions that must be supported, including functions to retrieve the total supply, transfer from one wallet to another, and approve a transaction. Typically, any given ERC-20 token has many copies which are held in a variety of crypto wallets.

[https://github.com/ethereum/ercs/blob/master/ERCS/erc-20.md](https://github.com/ethereum/ercs/blob/master/ERCS/erc-20.md)

**Token Type - ERC-721** - A type of non-fungible Ethereum token (i.e., smart contract) standard which is defined by a series of functions that must be supported, including functions to retrieve the total supply, transfer from one wallet to another, and approve a transaction. Each ERC-721 token is unique and non-interchangeable with other tokens (i.e., non-fungible).

https://github.com/ethereum/ercs/blob/master/ERCS/erc-721.md

*From Mastering Ethereum*:

### CHAPTER 10: Tokens

- They can be under any forms.
		- Identity, Cars, Paintings, Currency, etc.
- They can be fungible and non-fungible.
 
**Token and intrinsicality**
- Tokens can be intrinsic (directly from within Ethereum blockchain). They are governed by the consensus rules of the blockchain and are decentralized. To buy a token from a person, you buy it directly from them, no intermediary. If you hold a key to a certain token, it's "truly" yours, without the worry of it being seized or taken by a 3rd party.
- **Extrinsic (external) tokens** - They represent something of value from outside the blockchain. This could be a car, painting, etc. These types of tokens are not governed by the consensus rules of the Ethereum blockchain. It's not guaranteed that the exchange/use of such a token is bound to happen.
 
**Utility vs. equity**
- Utility token might be a voting pass to some ejection. By holding such a token, you have the right to cast a vote.
- Equity token can be represented as a share (part of something) some company, etc.
 
**Tokens on Ethereum**
- Tokens are not like ethers. When exchanging ether, a transaction is created on the blockchain that shows the exact transaction details, logs and the amount of ether that has been exchanged.
- Tokens on the other hand, are not controlled by the protocol layer, they're on the SC layer. This means that a contract can be directly responsible for issuing distributing, trading all of the tokens without the Ethereum blockchain ever directly knowing of this.
 
**ERC-20 Token Standard**
* ERC - Ethereum request for comments. ERC-20 standard is for fungible tokens, which means ERC-20 token can be exchanged for another ERC-20 token.
 
**ERC-20 Required Functions:**

1. **totalSupply():**
    - Returns the total number of tokens in circulation.
2. **balanceOf(address _owner):**
    - Returns the token balance of a given address `_owner`.
3. **transfer(address _to, uint256 _value):**
    - Transfers `_value` amount of tokens to the address `_to`. Returns a boolean indicating success.
4. **transferFrom(address _from, address _to, uint256 _value):**
    - Transfers `_value` amount of tokens from address `_from` to address `_to` using the allowance mechanism. Returns a boolean indicating success.
5. **approve(address _spender, uint256 _value):**
    - Allows `_spender` to withdraw from the sender's account up to the specified `_value`. Returns a boolean indicating success.
6. **allowance(address _owner, address _spender):**
    - Returns the remaining allowance that `_spender` has to spend from `_owner`'s balance.
7. **Transfer(address indexed _from, address indexed _to, uint256 _value):**
    - An event triggered when tokens are successfully transferred. The indexed parameters allow for efficient event filtering.
8. **Approval(address indexed _owner, address indexed _spender, uint256 _value):**
    - An event logged upon a successful call to `approve()`. Indicates the approval of `_spender` to spend up to `_value` tokens from `_owner`.
 
- **There are also 3 more optional functions:**
		- **name** - returns a human readable name ("US Dollars")
		- **symbol** - returns a symbol ("USD")
		- **decimals** - returns the number of decimals used to divide token amounts
 
**Example: Alice wants to send Bob 10 tokens. Her wallet sends a transaction to the token contract address, which then calls the transfer (bob.address, amount), deducating 10 tokens from Alice's balance and adding 10 to Bob's.**
 
**ERC-777**

The ERC-777 token standard is an extension of the ERC-20 standard, introducing additional features and capabilities. Here are some key characteristics and improvements introduced by ERC-777:

1. **Backward Compatible:** ERC-777 is backward compatible with ERC-20, meaning that contracts and applications designed for ERC-20 tokens can still interact with ERC-777 tokens.
2. **Send and Call Functions:**
    - ERC-777 introduces a single `send` function that combines the functionality of the `transfer` and `approve` functions from ERC-20. This simplifies the interaction process for users.
    - The `send` function also enables token transfers with attached data, allowing more complex transactions and interactions to occur in a single atomic operation.
3. **Operator Concept:**
    - ERC-777 introduces the concept of "operators." An operator is an address that is given permission to send or receive tokens on behalf of another address. This allows for more flexible and granular control over token management.
4. **Callbacks and Hooks:**
    - ERC-777 supports the use of hooks and callbacks, enabling contracts to react to token movements. This allows for more dynamic and extensible interactions compared to ERC-20.
5. **Default Operators:**
    - Tokens in ERC-777 can specify default operators during token creation. These default operators are addresses that are automatically given permission to operate on behalf of token holders.
6. **Extended Events:**
    - ERC-777 introduces additional events such as `Sent`, `Received`, and `AuthorizedOperator`. These events provide more information about token movements and changes in operator status.
7. **Granular Control:**
    - ERC-777 provides more granular control over token management and transfers, allowing for a richer set of functionalities compared to ERC-20.
 
**ERC-721** = Non-fungible Token Standard - It's a standard to implement NFTs (unique tokens)
- Using an already existing implementation (such as Open Zeppelin's implementation of the ERC-20 standard) you can assume that the tokens will be secure.