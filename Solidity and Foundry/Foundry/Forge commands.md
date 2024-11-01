`forge-init` - Initializes a new Foundry project in the current directory. It sets up a default project structure with some example contracts and test files.

`forge-build` - Compiles all Solidity smart contracts in the `src` directory.

`forge test` - run all tests in `test` folder

`forge fmt` - format the code

`forge install OpenZeppelin/openzeppelin-contracts` - Installs a Solidity library from a GitHub repository or other supported source.

`forge test --gas-report` - - Runs your tests and generates a gas usage report for each function call.

`forge script <script_file> --fork-url <RPC_URL>` - run a script

**Example:** 
`forge script script/DeploySimpleStorage.s.sol `

`forge create <ContractName> --rpc-url <RPC_URL> --private-key <PRIVATE_KEY>` - deploy a contract

`forge script <script_name>.sol --rpc-url <network_rpc_url> --private-key <private_key>` - Deploy to a Testnet or Mainnet

**Example:**
`forge create SimpleStorage --rpc-url http://127.0.0.1:8545 --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80`

**Example of running script with anvil and deploying contract with private key:**

`forge script script/DeploySimpleStorage.s.sol --rpc-url http://127.0.0.1:8545 --broadcast --private-key 0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80`

`forge script script/DeploySimpleStorage.s.sol --rpc-url $SEPOLIA_RPC_URL --private-key $PRIVATE_KEY --broadcast` - connect to SEPOLIA TESTNET with RPC_URL and PRIVATE_KEY

```.env
# PRIVATE_KEY=0xac0974bec39a17e36ba4a6b4d238ff944bacb478cbed5efcae784d7bf4f2ff80S

PRIVATE_KEY=5520d046ccfaf88a99186c3208075d21e04eec0c1a0c22b6a59d6896ec382339

RPC_URL=http://127.0.0.1:8545

SEPOLIA_RPC_URL=https://eth-sepolia.g.alchemy.com/v2/eq-vbz79BKjzH5lJwK5UdK_-2mJiBfvQ
```

Provide the private key from MetaMask and HTTPS from Alchemy App and `source .`, then `echo PRIVATE_KEY` and run the command above.

We receive **CHAIN** - 1115511 - SEPOLIA TESTNET CHAIN ID
**Contract address** - 0xDc04792796bc743f2B9121441B4c6738bcb9BCd6
**Hash** - 0x1545e16d0fd7142ef0dc7de438754974d93cd9bf4576a93952a57b330d5680ef

`forge snapshot` - returns all tests with gas used. Returns:

`[PASS] testAddsFunderToArrayOfFunders() (gas: 100154)
`[PASS] testCheaperWithdrawFromMultipleFunders() (gas: 441274)
`[PASS] testFundFailsWithoutEnoughETH() (gas: 23050)
`[PASS] testFundedUpdatesFundedDataStructure() (gas: 99857)
`[PASS] testMinimumUsdDollarsIsFive() (gas: 8400)
`[PASS] testOnlyOwnerCanWithdraw() (gas: 100079)
`[PASS] testOwnerIsMsgSender() (gas: 8555)
`[PASS] testPriceFeedVersionIsAccurate() (gas: 13643)
`[PASS] testWithDrawWithASingleFunder() (gas: 87832)
`[PASS] testWithdrawFromMultipleFunders() (gas: 442124)`

**Bonus: Makefile** - short command instead of writing commands every time

```Makefile
-include .env

build:; forge build

deploy-sepolia:
    forge script script/DeployFundMe.s.sol:DeployFundMe --rpc-url $(SEPOLIA_RPC_URL) -- private-key $(PRIVATE_KEY) --broadcast --verify --etherscan-api-key $(ETHERSCAN_API_KEY) -vvvv
```

You must have environment variables in `.env` file.

`forge coverage` - shows test coverage

`forge coverage --report debug > coverage.txt` - create new file `coverage.txt` and includes lines of code that are not covered

`forge test --debug FILENAME` - opcode debugger