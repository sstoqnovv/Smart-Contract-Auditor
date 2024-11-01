{
                "category": "General",
                "data": [
                    {
                        "question": "Can the protocol handle ERC20 tokens with decimals other than 18?",
                        "description": "Not all ERC20 tokens use 18 decimals. Overlooking this can lead to computation errors.",
                        "remediation": "Always check and adjust for the decimal count of the ERC20 tokens being handled.",
                    },
                    {
                        "question": "Are there unexpected rewards accruing for user deposited assets?",
                        "description": "Some protocols or platforms may provide additional rewards for staked or deposited assets. If these rewards are not properly accounted for or managed, it could lead to discrepancies in the user's expected vs actual returns.",
                        "remediation": "The protocol should have mechanisms in place to track all potential rewards for user deposited assets. Users should be provided with clear interfaces or methods to claim any unexpected rewards to ensure fairness and transparency.",
                    },
                    {
                        "question": "Could direct transfers of funds introduce vulnerabilities?",
                        "description": "Direct transfers of assets without using the protocol's logic can lead to various problems in accounting especially if the accounting relies on `balanceOf` (or `address.balance`).",
                        "remediation": "Implement the internal accounting so that it is not be affected by direct transfers.",
                    },
                    {
                        "question": "Could the initial deposit introduce any issues?",
                        "description": "The first deposit can set certain parameters or conditions that subsequent deposits rely on.",
                        "remediation": "Test and ensure that the first deposit initializes and sets all necessary parameters correctly.",
                    },
                    {
                        "question": "Are the protocol token pegged to any other asset?",
                        "description": "The target tokens can be depegged.",
                        "remediation": "Ensure the protocol behave as expected during the depeg.",
                    },
                    {
                        "question": "Does the protocol revert on maximum approval to prevent over-allowance?",
                        "description": "Setting high allowances can make funds vulnerable to abuse; protocols sometimes set max to prevent this risk.",
                        "remediation": "Consider implementing a revert on approval functions when an unnecessarily high allowance is set.",
                        "references": [
                            "https://solodit.xyz/issues/m-3-universalapprovemax-will-not-work-for-some-tokens-that-dont-support-approve-typeuint256max-amount-sherlock-dodo-dodo-git"
                        ],
                    },
                    {
                        "question": "What would happen if only 1 wei remains in the pool?",
                        "description": "Leaving residual amounts can lead to discrepancies in accounting or locked funds.",
                        "remediation": "Implement logic to handle minimal residual amounts in the pool.",
                    },
                    {
                        "question": "Is it possible to withdraw in the same transaction of deposit?",
                        "description": "Protocols often provide various benefits to the depositors based on the deposit amount. This can lead to flashloan-deposit-harvest-withdraw attack cycle.",
                        "remediation": "Ensure the withdrawal is protected for some blocks after deposit.",
                    },
                    {
                        "question": "Does the protocol aim to support ALL kinds of ERC20 tokens?",
                        "description": "Not all ERC20 tokens are compliant to the ERC20 standard and there are several weird ERC20 tokens (e.g. Fee-On-Transfer tokens, rebasing tokens, tokens with blacklisting).",
                        "remediation": "Clarify what kind of tokens are supported and whitelist the ERC20 tokens that the protocol would accept.",
                        "references": [
                            "https://github.com/d-xo/weird-erc20"
                        ],
                    }
                ]
            },