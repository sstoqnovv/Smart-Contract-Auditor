**Private Key:**
- **Definition:** The private key is a secret piece of information known only to the owner. It's a 256-bit random number that uniques determines an address -> **Example: 0x2478hdisdfh34898h978...**
- **Function:** It is used to generate digital signatures and decrypt messages that have been encrypted with the corresponding public key. It is used to "sign" transactions. KEEP SECRET!
- **Security:** It must be kept secure and should never be shared. If someone gains access to the private key, they could potentially impersonate the key owner or decrypt confidential information.

**Private key controls the access by being a unique piece when needed to create digital signatures. Those digital signatures are used to "sign" transactions and to spend any funds from the account. The digital signatures also act as a proof of authentication when it comes down to SC or transaction owners.**    

A public key is a unique string of characters derived from a private key which is used to encrypt a message or data. The private key is used to decrypt the message or data.

`Encryption is the process by which a readable message is converted to an unreadable form to prevent unauthorized parties from reading it. Decryption is the process of converting an encrypted message back to its original (readable) format.`

**Public Key:**
- **Definition:** The public key is a freely distributable piece of information that is derived from the private key. It's an elliptic curve calculated from the private key.
- **Function:** It is used to verify digital signatures created with the corresponding private key and encrypt messages that can only be decrypted with the private key.
- **Distribution:** While the public key can be freely shared, it does not compromise the security of the system. It is often included in digital certificates or made available in public directories.

**Public key is created by a private key using ECSDA.**

**Note:** The public key is derived from the private key using elliptic curve cryptography.
****
**Digital Signature** - cryptographic technique used to verify the authenticity and integrity of digital messages/transactions or documents

Here's how digital signatures generally work:

1. **Key Pair:**
    - The sender generates a pair of cryptographic keys—a private key and a public key. The private key is kept secret and known only to the owner, while the public key can be shared with others.
2. **Signing:**
    - To sign a digital message or document, the sender uses their private key to create a unique digital signature. The process typically involves applying a mathematical algorithm (such as RSA or ECDSA) to the content of the message.
3. **Verification:**
    - The recipient, or anyone with access to the sender's public key, can verify the digital signature. They use the sender's public key to check the authenticity of the signature and ensure that the content has not been altered.
4. **Authenticity and Integrity:**
    - If the verification is successful, it provides assurance of both the authenticity of the sender (only the true owner of the private key could have produced the signature) and the integrity of the message (the content has not been tampered with).
5. **Non-Repudiation:**
    - Digital signatures provide non-repudiation, meaning that the sender cannot later deny their involvement in creating or sending the message. The act of signing with a private key is considered a binding commitment.
6. **Timestamping (Optional):**
    - In some cases, digital signatures are accompanied by timestamps to provide evidence of when the signature was created.

**Digital signatures** - Use to ensures transaction authorization governance roles
 
If you don't have or someone else who haven't the signing key, they can't sign in your in your behalf.

Access and control of funds is done with digital signatures. The digital signatures are created using the private key.

**Digital signatures (DS) in transactions**
- A DS has 2 purposes:
		- DS proves that the owner of a private key has authorized the spending of ether or execution of a contract.
		- DS makes sure that the data has not been and cannot be modified by anyone after the transaction is signed.
 
- DS is a matchematical scheme that consists of two parts:
	- Taking DS algorithm and the message of a signature(+ private key of the originator)
	- Verifying the signature
****
**Secret phrase >> Private key ||| Public key > Address** - *||| is barrier. You want to keep it secret 

**Secret phrase(Mnemonic) and Private key should be private and keep it in secret! Address and Public keys shouldn't be keep it as a secret.

**Anyone with a private key can create digital signatures, which can be used to control the ETH that is present in the EoA**

**v, r, s** - ECDSA Signature -> 65 bytes. r, s - Signature, v - recovery identifier - it is used to recover the public key from the signature during the verification process.
****
#### Simple steps of How it works

0. We sign a message, e.g. "hello". It's been hashed and we get private key: `156263732749237493475983479274r7329847983274`, we sign the message and we get message signature: `3729858dj29472983jd9ej29983792dj9dejfdieujr8934ru9843jf`.

So, we have message -> `"hello"`.
Private key + message -> signed message
Signed message + public key -> confirmed signed
And verify it.

#### How signing works:
1. Take private key + message (data, function selector, parameters)
2. Smash it into ECDSA
	1. This outputs v, r, and s, that sign the message/data
	2. We can use these values to verify someones signature using `ecrecover`
> `ecrecover` - is like hitting "Verify" button. We verify the signature

#### How verification works:
1. Get the signed message
2. Break into v, r, and s
3. Get the data itself
4. Use it as input parameters for `ecrecover`

https://andersbrownworth.com/blockchain/public-private-keys/transaction
****
### More info:

EIP-191 - https://eips.ethereum.org/EIPS/eip-191
EIP-712 - https://eips.ethereum.org/EIPS/eip-712
****
# Secret Recovery Phrase

Your Secret Recovery Phrase (SRP) is a unique 12-word phrase that is generated when you first set up MetaMask. Your funds are connected to that phrase. If you ever lose your password, your SRP allows you to recover your wallet and your funds. Write it down on paper and hide it somewhere, put it in a safety deposit box, or use a secure password manager. Some users even engrave their phrases into metal plates! (Storing your SRP in a physical, offline format eliminates the risk of hacking.)