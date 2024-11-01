***From "Mastering Ethereum" book****
### CHAPTER 4: Cryptography
    
**Keys and addresses**
- Ownership of ether by EOAs is established with digital private keys, an Ethereum address and digital signatures.
- The private key can also be used to derive an Ethereum address -> an account. **They shouldn't appear in messages nor should they be passed around in the network!**
- Access and control of funds is done with digital signatures. The digital signatures are created using the private key.
    

**Private key controls the access by being a unique piece when needed to create digital signatures. Those digital signatures are used to "sign" transactions and to spend any funds from the account. The digital signatures also act as a proof of authentication when it comes down to SC or transaction owners.**    

**Digital signatures can be used to sign any messages. A transaction is a basically a request to access a particular account on the Ethereum network in order to move funds or to interact with a contract.**
    
**When a transaction is made (sent) on the Ethereum blockchain, it needs to send together with a digital sign. Anyone can verify that the details of the transaction are valid (authentic) with only the public key. The private key remains secret and doesn't get exposed to anyone.**
    
**In Ethereum there is no encryption at all. All of the messages/transactions on the Ethereum blockchain can be read/viewed by everyone. Private keys are used to create a digital sign which is used only to verify the validity and authenticity of the onwer/contents of a transaction/message.**
    
- Private keys are basically very long unsigned numbers that are picked at random. They must be kept secret at all costs, because their exposal means giving access to all of the ether on that particular account, as well as access to all the SC.
     **Example: 0x2478hdisdfh34898h978...**
    
**Cryptographic hash functions**
    **Hash functions are a one way mathematical functions where a given input gets changed by addition, swapping and other operations.
    - They are a way to get a unique identifier that is impossible to guess or to bruteforce.
    

**Ethereum addresses** - the last 20 bytes of keccak256 (public key)