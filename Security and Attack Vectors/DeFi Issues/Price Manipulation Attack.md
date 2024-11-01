**Description:** Malicious actors intentionally alter the price of assets on decentralized exchanges, usually to exploit dependent contracts or trades.

"question": "How does the protocol get the price of assets?",
"description": "Price, or rates between assets more generally, can be manipulated if it is derived from the ratio of balance. Flash loan and donation are the well-known attack vectors used to manipulate the prices.",
"remediation": "Use the Chainlink oracles for the asset prices and implement internal accounting instead of relying on `balanceOf`.",
"references": ["https://solodit.xyz/issues/h-05-flash-loan-price-manipulation-in-purchasepyroflan-code4rena-behodler-behodler-contest-git",
"https://solodit.xyz/issues/h-05-underlying-assets-stealing-in-autopxgmx-and-autopxglp-via-share-price-manipulation-code4rena-redacted-cartel-redacted-cartel-contest-git",
"https://solodit.xyz/issues/h-02-use-of-slot0-to-get-sqrtpricelimitx96-can-lead-to-price-manipulation-code4rena-maia-dao-ecosystem-maia-dao-ecosystem-git"
                        ],