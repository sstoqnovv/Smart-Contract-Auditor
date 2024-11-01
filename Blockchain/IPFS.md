**IPFS**(**InterPlanetary File System**) - a decentralized, peer-to-peer file storage and sharing protocol. Its goal is to make the web more distributed by enabling users to store and access files from a network of computers, rather than relying on centralized servers. IPFS hashes the stored data.

- **Content-addressing**: Files are identified by unique cryptographic hashes (CIDs), not URLs, ensuring the content is the same regardless of where it is stored.
- **Decentralization**: Data is distributed across multiple nodes, making the system more resilient and eliminating single points of failure. If someone wants your data - they pin your data and they get copy - not your unique hashed data.
- **Peer-to-peer (P2P) network**: Files are retrieved from the nearest or most accessible peer in the network, rather than from a single centralized server.
- **Immutable files**: Once files are uploaded, they can’t be altered without generating a new CID, ensuring data integrity.
- **Efficient storage**: Files are broken into smaller chunks using a DAG structure, making storage and distribution more efficient across the network.

`http://127.0.0.1:8080/ipfs/QmZiRSUDSbGUPx6tkVzMHntdGpQviN1PzoZP5tgF8PTGhX/` - CID example. Import a file in IPFS Desktop and get CID.
****
### GET NFT URI METADATA

To get the URI of an NFT from OpenSea, follow these steps:

### Option 1: View on OpenSea Website

1. **Go to OpenSea**: Visit [opensea.io](https://opensea.io).
2. **Find the NFT**: Search for the specific NFT or go to the collection it belongs to.
3. **Click on the NFT**: Once you've found the NFT, click on it to view its details.
4. **Scroll to "Details"**: On the NFT's detail page, scroll down to the "Details" section.
5. **View the Contract Address**:
    - You will see the **Contract Address** and **Token ID** here. These are important if you want to programmatically fetch the URI.
    - You can view the full metadata by using this information in the smart contract or API.

### Option 2: Using Etherscan

1. **Open Etherscan**: If the contract address is provided (as seen in the "Details" section on OpenSea), click on it to view the contract on [Etherscan](https://etherscan.io).
2. **Find the `tokenURI` Function**:
    - On Etherscan, go to the **Read Contract** section.
    - Look for a function called `tokenURI` or similar. This function typically returns the metadata URI of the NFT.
3. **Input the Token ID**: Enter the Token ID (available in the NFT’s details) and call the function to fetch the URI.

Example: `ipfs://bafybeig37ioir76s7mg5oobetncojcm3c3hxasyd4rvid4jqhy4gkaheg4/?filename=0-PUG.json`

It returns metadata like: 

```
{
    "name": "PUG",
    "description": "An adorable PUG pup!",
    "image": "https://ipfs.io/ipfs/QmSsYRx3LpDAb1GZQm7zZ1AuHZjfbPkD6J7s9r41xu1mf8?filename=pug.png",
    "attributes": [
        {
            "trait_type": "cuteness",
            "value": 100
        }
    ]
}
```