{
        "category": "External Call",
        "data": [
            {
                "question": "What are the implications if the call reenters a different function?",
                "description": "Reentrant calls to different functions can unpredictably alter contract states. Note that view functions should be checked as well to prevent the read-only reentrancy.",
                "remediation": "Ensure the contract state is maintained reasonably during the external interactions.",
                "references": [
                    "https://medium.com/@zokyo.io/read-only-reentrancy-attacks-understanding-the-threat-to-your-smart-contracts-99444c0a7334",
                    "https://solodit.xyz/issues/m-03-read-only-reentrancy-is-possible-code4rena-angle-protocol-angle-protocol-invitational-git"
                ],
            },
            {
                "question": "Is there a multi-call?",
                "description": "Mismanagement of `msg.value` across multiple calls can lead to vulnerabilities.",
                "remediation": "Do not use ETH in multicall.",
                "references": [
                    "https://solodit.xyz/issues/m-08-passing-multiple-eth-deposits-in-orders-array-will-use-the-same-msgvalue-many-times-code4rena-nested-finance-nested-finance-contest-git"
                ],
            },
            {
                "question": "What are the risks associated with using delegatecall in smart contracts?",
                "description": "A delegatecall is a low-level function call that delegates the execution of a function in another contract while maintaining the original contract's context. It can lead to critical vulnerabilities if the destination address is not secure or can be altered by an unauthorized party.",
                "remediation": "Use delegatecall only with trusted contracts, and ensure that the address to be delegated to is not changeable by unauthorized users. Implement strong access controls and audit the code for potential security issues before deployment.",
                "tags": [
                    "SWC-112"
                ]
            },
            {
                "question": "Is the external contract call necessary?",
                "description": "Unnecessary external calls can introduce vulnerabilities.",
                "remediation": "Evaluate and eliminate non-essential external contract calls.",
            },
            {
                "question": "Has the called address been whitelisted?",
                "description": "Calling untrusted addresses can lead to malicious actions.",
                "remediation": "Ensure that only whitelisted or trusted contract addresses are called.",
                "references": [
                    "https://solodit.xyz/issues/too-generic-calls-in-genericbridgefacet-allow-stealing-of-tokens-spearbit-lifi-pdf",
                    "https://solodit.xyz/issues/hardcode-or-whitelist-the-axelar-destinationaddress-spearbit-lifi-pdf"
                ],
            },
            {
                "question": "Is there suspicion when a fixed gas amount is specified?",
                "description": "Specifying fixed gas amounts can lead to out-of-gas vulnerabilities.",
                "remediation": "Use dynamic gas estimation or ensure sufficient gas is available before the call.",
                "references": [
                    "https://solodit.xyz/issues/m-02-fixed-amount-of-gas-sent-in-call-may-be-insufficient-code4rena-joyn-joyn-contest-git",
                ],
            },
            {
                "question": "What happens if the call consumes all provided gas?",
                "description": "Calls that consume all available gas can halt subsequent actions.",
                "remediation": "Ensure enough gas is reserved for post-call tasks or use dynamic gas estimation.",
                "references": [
                    "https://solodit.xyz/issues/a-malicious-fee-receiver-can-cause-a-denial-of-service-trailofbits-nftx-protocol-v2-pdf",
                ],
            },
            {
                "question": "Is the contract passing large data to an unknown address?",
                "description": "Large data passed to untrusted addresses may be exploited for griefing.",
                "remediation": "Limit data passed or employ inline assembly to manage data transfer.",
                "references": [
                    "https://solodit.xyz/issues/h-2-malicious-user-can-use-an-excessively-large-_toaddress-in-oftcoresendfrom-to-break-layerzero-communication-sherlock-uxd-uxd-protocol-git"
                ],
            },
            {
                "question": "What happens if the call returns vast data?",
                "description": "External calls returning vast data can deplete available gas.",
                "remediation": "Limit or verify data size returned from external sources.",
            },
            {
                "question": "Are there any delegate calls to non-library contracts?",
                "description": "Non-library delegate calls can alter the state of the calling contract.",
                "remediation": "Thoroughly review and verify such delegate calls so that the delegate calls do not change the caller's state unexpectedly.",
            },
            {
                "question": "Is there a strict policy against delegate calls to untrusted contracts?",
                "description": "Delegate calls grant the called contract the context of the caller, risking state alterations.",
                "remediation": "Restrict delegate calls to only trusted, reviewed, and audited contracts.",
                "references": [
                    "https://solodit.xyz/issues/m-01-delegate-call-in-vault_execute-can-alter-vaults-ownership-code4rena-fractional-fractional-v2-contest-git"
                ],
            },
            {
                "question": "Is the address's existence verified?",
                "description": "Calling non-existent addresses can lead to unintended behaviors. Low level calls (call, delegate call and static call) return success if the called contract doesn't exist (not deployed or destructed)",
                "remediation": "Verify the existence of an address before making a call.",
                "references": [
                    "https://solodit.xyz/issues/m-10-call-to-non-existing-contracts-returns-success-code4rena-biconomy-biconomy-hyphen-20-contest-git",
                    "https://solodit.xyz/issues/lack-of-contract-existence-check-on-delegatecall-will-result-in-unexpected-behavior-trailofbits-degate-pdf",
                ],
            },
            {
                "question": "Is the check-effect-interaction pattern being utilized?",
                "description": "The check-effect-interaction pattern prevents reentrancy attacks.",
                "remediation": "Adhere to the CEI pattern and use `reentrancyGuard` judiciously.",
                "references": [
                    "https://solodit.xyz/issues/m-09-malicious-royalty-recipient-can-steal-excess-eth-from-buy-orders-code4rena-caviar-caviar-private-pools-git",
                    "https://solodit.xyz/issues/h-01-re-entrancy-in-settleauction-allow-stealing-all-funds-code4rena-kuiper-kuiper-contest-git"
                ],
            },
            {
                "question": "How is the msg.sender handled?",
                "description": "On interacting with external contracts, the caller becomes a new `msg.sender` instead of the original caller.",
                "remediation": "Ensure the validation is in place to check the actor is handled correctly.",
                "references": [
                    "https://solodit.xyz/issues/m-01-onlycentrifugechainorigin-cant-require-msgsender-equal-axelargateway-code4rena-centrifuge-centrifuge-git"
                ],
            }
        ]
    },
    {