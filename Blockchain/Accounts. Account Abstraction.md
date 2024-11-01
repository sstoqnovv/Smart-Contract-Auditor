There are two kinds of accounts in Ethereum which share the same address space: **External accounts** that are controlled by public-private key pairs (i.e. humans) and **contract accounts** which are controlled by the code stored together with the account.

The address of an external account is determined from the public key while the address of a contract is determined at the time the contract is created (it is derived from the creator address and the number of transactions sent from that address, the so-called “nonce”).

### Ethereum Accounts

In Ethereum, the state is made up of objects called "accounts", with each account having a 20-byte address and state transitions being direct transfers of value and information between accounts. An Ethereum account contains four fields:

- The **nonce**, a counter used to make sure each transaction can only be processed once
- The account's current **ether balance**
- The account's **contract code**, if present
- The account's **storage** (empty by default)

In general, there are two types of accounts: **externally owned accounts**, controlled by private keys, and **contract accounts**, controlled by their contract code. An externally owned account has no code, and one can send messages from an externally owned account by creating and signing a transaction; in a contract account, every time the contract account receives a message its code activates, allowing it to read and write to internal storage and send other messages or create contracts in turn.
****
### Account Abstraction

Traditionally, users need to manage and use private keys to sign transactions. This can be annoying, confusing, and risky. Losing a private key means losing access to the account. Even worse, a stolen private key means that you've just lost all the value in that account. Account abstraction solves this problem by allowing users to sign transactions without using private keys. Instead, users can use a different type of key that is more user-friendly and secure. This simplifies the process of signing transactions and reduces the risk of losing access to an account, enhancing both security and user experience.

What's more is that this new type of 'key' can be anything you can think of(as long is it can be coded). This means that you can use your phone, Google account, or even your fingerprint to sign transactions. You can even have a group of your friends collectively approve the data. The possibilities are endless.

**Alt-mempool** - off-chain memory pool used to process and validate user operations before they are sent to the blockchain, improving transaction efficiency and flexibility.