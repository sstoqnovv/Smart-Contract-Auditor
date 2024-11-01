https://scsfg.io/hackers/oracle-manipulation/

{
                "category": "Oracle",
                "description": "Price oracle related vulnerabilities",
                "data": [
                    {
                        "question": "Is the Oracle using deprecated Chainlink functions?",
                        "description": "Usage of deprecated Chainlink functions like `latestRoundData()` might return stale or incorrect data, affecting the integrity of smart contracts.",
                        "remediation": "Replace deprecated functions with the current recommended methods to ensure accurate data retrieval from oracles.",
                        "references": [
                            "https://github.com/code-423n4/2022-04-backd-findings/issues/17"
                        ],
                    },
                    {
                        "question": "Is the returned price validated to be non-zero?",
                        "description": "Price feed might return zero and this must be handled as invalid.",
                        "remediation": "Ensure the returned price is not zero.",
                    },
                    {
                        "question": "Is the price update time validated?",
                        "description": "Price feeds might not be supported in the future. To ensure accurate price usage, it's vital to regularly check the last update timestamp against a predefined delay.",
                        "remediation": "Implement a mechanism to check the heartbeat of the price feed and compare it against a predefined maximum delay (`MAX_DELAY`). Adjust the `MAX_DELAY` variable based on the observed heartbeat.",
                    },
                    {
                        "question": "Is there a validation to check if the rollup sequencer is running?",
                        "description": "The rollup sequencer can become offline, which can lead to potential vulnerabilities due to stale price.",
                        "remediation": "Utilize the sequencer uptime feed to confirm the sequencers are up.",
                        "references": [
                            "https://docs.chain.link/data-feeds/l2-sequencer-feeds"
                        ],
                    },
                    {
                        "question": "Is the Oracle's TWAP period appropriately set?",
                        "description": "An inadequately set TWAP (Time-Weighted Average Price) period could be exploited to manipulate prices.",
                        "remediation": "Adjust the TWAP period to a duration that mitigates the risk of manipulation while providing timely price updates.",
                    },
                    {
                        "question": "Is the desired price feed pair supported across all deployed chains?",
                        "description": "In multi-chain deployments, it's crucial to ensure the desired price feed pair is available and consistent across all chains.",
                        "remediation": "Review the supported price feed pairs on all chains and ensure they are consistent.",
                    },
                    {
                        "question": "Is the heartbeat of the price feed suitable for the use case?",
                        "description": "A price feed heartbeat that's too slow might not be suitable for some use cases.",
                        "remediation": "Assess the requirements of the use case and ensure the price feed heartbeat aligns with them.",
                    },
                    {
                        "question": "Are there any inconsistencies with decimal precision when using different price feeds?",
                        "description": "Different price feeds might have varying decimal precisions, which can lead to inaccuracies.",
                        "remediation": "Ensure that the contract handles potential variations in decimal precision across different price feeds.",
                    },
                    {
                        "question": "Is the price feed address hard-coded?",
                        "description": "Hard-coded price feed addresses can be problematic, especially if they become deprecated or if they're not accurate in the first place.",
                        "remediation": "Review and verify the hardcoded price feed addresses. Consider mechanisms to update the address if required in the future.",
                    },
                    {
                        "question": "What happens if oracle price updates are front-run?",
                        "description": "Oracle price updates can be front-run and cause various problems.",
                        "remediation": "Ensure the protocol is not affected in the case where oracle price updates are front-run.",
                    },
                    {
                        "question": "How does the system handle potential oracle reverts?",
                        "description": "Unanticipated oracle reverts can lead to Denial-Of-Service.",
                        "remediation": "Implement try/catch blocks around oracle calls and have alternative strategies ready.",
                    },
                    {
                        "question": "Are the price feeds appropriate for the underlying assets?",
                        "description": "Using an ETH price feed for stETH or a BTC price feed for WBTC can introduce risks associated with the underlying assets deviating from their pegs.",
                        "remediation": "Ensure that the price feeds accurately represent the underlying assets to address potential depeg risks.",
                    },
                    {
                        "question": "Is the contract vulnerable to oracle manipulation, especially using spot prices from AMMs?",
                        "description": "Reliance on AMM spot prices as oracles can be manipulated via flashloan.",
                        "remediation": "Choose reliable and tamper-resistant oracle sources. Avoid using spot prices from AMMs directly without additional checks.",
                    },
                    {
                        "question": "How does the system address potential inaccuracies during flash crashes?",
                        "description": "During flash crashes, oracles might return inaccurate prices.",
                        "remediation": "Implement checks to ensure that the price returned by the oracle lies within an expected range to guard against potential flash crash vulnerabilities.",
                    }
                ]
            },

