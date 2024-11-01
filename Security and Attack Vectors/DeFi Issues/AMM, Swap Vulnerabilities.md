"category": "Defi",
        "description": "",
        "data": [
            {
                "category": "AMM/Swap",
                "description": "",
                "data": [
                    {
                        "question": "Is hardcoded slippage used?",
                        "description": "Using hardcoded slippage can lead to poor trades and freezing user funds during times of high volatility.",
                        "remediation": "Allow users to specify the slippage parameter in the actual asset amount which was calculated off-chain.",
                        "references": [
                            "https://dacian.me/defi-slippage-attacks#heading-on-chain-slippage-calculation-can-be-manipulated"
                        ],
                    },
                    {
                        "question": "Is there a deadline protection?",
                        "description": "Without deadline protection, user transactions are vulnerable to sandwich attacks.",
                        "remediation": "Allow a user specify the deadline of the swap.",
                        "references": [
                            "https://defihacklabs.substack.com/p/solidity-security-lesson-6-defi-slippage?utm_source=profile&utm_medium=reader2"
                        ],
                    },
                    {
                        "question": "Is there a validation check for protocol reserves?",
                        "description": "Protocols may face risks if reserves are not validated and can be lent out, affecting the system's solvency.",
                        "remediation": "Ensure reserve validation logic is in place to safeguard the protocol's liquidity and overall health.",
                        "references": [
                            "https://github.com/sherlock-audit/2022-08-sentiment-judging/blob/main/122-M/1-report.md"
                        ],
                    },
                    {
                        "question": "Does the AMM utilize forked code?",
                        "description": "Using forked code, especially from known projects like Uniswap, can introduce known vulnerabilities if not updated or audited properly.",
                        "remediation": "Review the differences. Utilize tools such as contract-diff.xyz to compare and identify the origin of code snippets.",
                    },
                    {
                        "question": "Are there rounding issues in product constant formulas?",
                        "description": "Rounding issues in the formulas can lead to inaccuracies or imbalances in token swaps and liquidity provisions.",
                        "remediation": "Review the mathematical operations in the AMM's formulas, ensuring they handle rounding appropriately without introducing vulnerabilities.",
                    },
                    {
                        "question": "Can arbitrary calls be made from user input?",
                        "description": "Allowing arbitrary calls based on user input can expose the contract to various vulnerabilities.",
                        "remediation": "Validate and sanitize user inputs. Avoid executing arbitrary calls based solely on input data.",
                    },
                    {
                        "question": "Is there a mechanism in place to protect against excessive slippage?",
                        "description": "Without slippage protection, traders might experience unexpected losses due to large price deviations during a trade.",
                        "remediation": "Incorporate a slippage parameter that users can set to limit their maximum acceptable slippage.",
                    },
                    {
                        "question": "Does the AMM properly handle tokens of varying decimal configurations and token types?",
                        "description": "If the AMM doesn't support tokens with varying decimals or types, it might lead to incorrect calculations and potential losses.",
                        "remediation": "Ensure compatibility with tokens of varying decimal places and validate token types before processing them.",
                    },
                    {
                        "question": "Does the AMM support the fee-on-transfer tokens?",
                        "description": "Fee-on-transfer tokens can cause problems because the sending amount and the received amount do not match.",
                        "remediation": "Ensure the fee-on-transfer tokens are handled correctly if they are supposed to be supported.",
                    },
                    {
                        "question": "Does the AMM support the rebasing tokens?",
                        "description": "Rebasing tokens can change the actual balance.",
                        "remediation": "Ensure the rebasing tokens are handled correctly if they are supposed to be supported.",
                    },
                    {
                        "question": "Does the protocol calculate `minAmountOut` before a token swap?",
                        "description": "Protocols integrating AMMs should determine the `minAmountOut` prior to swaps to avoid unfavorable rates. The source of the rates and potential for manipulation should also be considered.",
                        "remediation": "Ensure that the protocol calculates `minAmountOut` before executing swaps. If external oracles are used, validate their trustworthiness and consider potential vulnerabilities like sandwich attacks.",
                        "references": [
                            "https://blog.chain.link/guide-to-sandwich-attacks/"
                        ],
                    },
                    {
                        "question": "Does the integrating contract verify the caller address in its callback functions?",
                        "description": "Callback functions can be manipulated if they don't validate the calling contract's address. This is especially crucial for functions like `swap()` that involve tokens or assets.",
                        "remediation": "Implement checks in the callback functions to validate the address of the calling contract. Additionally, review the logic for any potential bypasses to this check.",
                    },
                    {
                        "question": "Is the slippage calculated on-chain?",
                        "description": "ON-chain slippage calculation can be manipulated.",
                        "remediation": "Allow users to specify the slippage parameter in the actual asset amount which was calculated off-chain.",
                        "references": [
                            "https://dacian.me/defi-slippage-attacks#heading-on-chain-slippage-calculation-can-be-manipulated"
                        ],
                    },
                    {
                        "question": "Is the slippage parameter enforced at the last step before transferring funds to users?",
                        "description": "Enforcing slippage parameters for intermediate swaps but not the final step can result in users receiving less tokens than their specified minimum",
                        "remediation": "Enforce slippage parameter as the last step before transferring funds to users",
                        "references": [
                            "https://dacian.me/defi-slippage-attacks#heading-mintokensout-for-intermediate-not-final-amount"
                        ],
                    }
                ]
            },