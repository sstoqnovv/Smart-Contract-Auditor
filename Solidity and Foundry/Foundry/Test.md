**Unit Test** - Testing a specific part of our code
**Integration Test** - testing how our code works with other parts of our code
**Forked Test** - Testing our code on a simulated real environment
**Staging** - testing our code in a real env. that is not prod.

`forge test` - compile your contracts and run all the tests found in the `test` directory

`forge test -vvvv` - sets the verbosity to the maximum level, which includes traces, logs, and setup information

`forge test --match-path test/YourTestFile.t.sol` - run specific test file

`forge test --match-test testName` - run specific test function

`forge test --gas-report` - run tests with gas report

`forge test --optimize` - run tests with optimizations

`forge test --fork-url <RPC_URL>` - run test with forking(e.g. testnet)

`forge coverage --fork-url $SEPOLIA_RPC_URL` - returns the test coverage of files

`forge test --fork-url $FORK_URL --match-path test/curve-v1/solutions/CurveV1Swap.test.sol --match-test test_exchange -vvv` - run specific test inside specific file

**Bonus** - get storage layout of specific file

`forge inspect FundMe storageLayout`