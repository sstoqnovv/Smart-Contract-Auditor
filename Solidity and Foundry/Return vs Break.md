**Description:** `BankContractBug::addBanks()` exhibits an incorrect usage of the return statement within a loop iteration, resulting in unintended termination of the loop. The return statement is placed inside the inner loop, causing premature exit from the function before completing the iteration over all bank addresses.

**Mitigation:**

Use break instead of return.

```BackContractBug
contract BankContractBug {
    struct Bank {
        address bankAddress;
        string bankName;
    }

    Bank[] public banks;

    function addBanks(
        address[] memory bankAddresses,
        string[] memory bankNames
    ) public {
        require(
            bankAddresses.length == bankNames.length,
            "Input arrays must have the same length."
        );

        for (uint i = 0; i < bankAddresses.length; i++) {
            if (bankAddresses[i] == address(0)) {
                continue;
            }

            // i++ is not executed when return is executed
            for (i = 0; i < bankAddresses.length; i++) {
                banks.push(Bank(bankAddresses[i], bankNames[i]));
                return; // returns is used within the loop, which cause premature end of the loop
            }
        }
    }

    function getBankCount() public view returns (uint) {
        return banks.length;
    }
}
```

```FixedBackContractBug
contract FixedBank {
    struct Bank {
        address bankAddress;
        string bankName;
    }

    Bank[] public banks;

    function addBanks(
        address[] memory bankAddresses,
        string[] memory bankNames
    ) public {
        require(
            bankAddresses.length == bankNames.length,
            "Input arrays must have the same length."
        );

        for (uint i = 0; i < bankAddresses.length; i++) {
            if (bankAddresses[i] == address(0)) {
                continue;
            }

            for (uint i = 0; i < bankAddresses.length; i++) {
                banks.push(Bank(bankAddresses[i], bankNames[i]));
                break; // Correct usage of break to terminate the inner loop
            }
        }
    }

    function getBankCount() public view returns (uint) {
        return banks.length;
    }
}