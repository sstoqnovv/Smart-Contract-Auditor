{
        "category": "Heuristics",
        "data": [
            {
                "question": "Is there any logic implemented multiple times?",
                "description": "Inconsistent implementations of the same logic can introduce errors or vulnerabilities.",
                "remediation": "Standardize the logic and make it as a separate function.",
            },
            {
                "question": "Does the contract use any nested structures?",
                "description": "If a variable of nested structure is deleted, only the top-level fields are reset by default values (zero) and the nested level fields are not reset.",
                "remediation": "Always ensure that inner fields are deleted before the outer fields of the structure.",
            },
                "question": "Is the `NonReentrant` modifier placed before every other modifier?",
                "description": "The order of modifiers can influence the behavior of a function. Generally,  `NonReentrant` must come first than other modifiers.",
                "remediation": "Reorder modifiers so that `NonReentrant` is placed before other modifiers.",
            },
            {
                "question": "Does the `try/catch` block account for potential gas shortages?",
                "description": "A `try/catch` block without adequate gas can fail, leading to unexpected behaviors.",
                "remediation": "Ensure sufficient gas is supplied when using the `try/catch` block.",
                "references": [
                    "https://forum.openzeppelin.com/t/a-brief-analysis-of-the-new-try-catch-functionality-in-solidity-0-6/2564"
                ],
            },
            {
                "question": "Did you check the relevant EIP recommendations and security concerns?",
                "description": "Incomplete or incorrect implementation of EIP recommendations can lead to vulnerabilities.",
                "remediation": "Read the recommendations and security concerns and ensure all are implemented as per the official recommendations.",
            },
            {
                "question": "Are there any off-by-one errors?",
                "description": "Off-by-one errors are not rare. Is `<=` correct in this context or should `<` be used? Should a variable be set to the length of a list or the length - 1? Should an iteration start at 1 or 0?",
                "remediation": "Review all usages of comparison operators for correctness.",
            },
            {
                "question": "Are logical operators used correctly?",
                "description": "Logical operators like `==`, `!=`, `&&`, `||`, `!` can be overlooked especially when the test coverage is not good.",
                "remediation": "Review all usages of logical operators for correctness.",
            },
            {
                "question": "What happens if the protocol's contracts are inputted as if they are normal actors?",
                "description": "Supplying unexpected addresses can lead to unintended behaviors, especially if the address points to another contract inside the same protocol.",
                "remediation": "Implement checks to validate receiver addresses and ensure the protocol behaves as expected.",
            },
            {
                "question": "Are there rounding errors that can be amplified?",
                "description": "While minor rounding errors can be inevitable in certain operations, they can pose significant issues if they can be magnified. Amplification can occur when a function is invoked multiple times strategically or under specific conditions.",
                "remediation": "Conduct thorough tests to identify and understand potential rounding errors. Ensure that they cannot be amplified to a level that would be detrimental to the system or its users. In cases where significant rounding errors are detected, the implementation should be revised to minimize or eliminate them.",
                "references": [
                    "https://github.com/OpenCoreCH/smart-contract-audits/blob/main/reports/c4/rigor.md#high-significant-rounding-errors-for-interest-calculation"
                ],
            },
            {
                "question": "Is there any uninitialized state?",
                "description": "Checking a variable against its default value might be used to detect initialization. If such defaults can also be valid state, it could lead to vulnerabilities.",
                "remediation": "Avoid solely relying on default values to determine initialization status.",
            },
            {
                "question": "Can functions be invoked multiple times with identical parameters?",
                "description": "Functions that should be unique per parameters set might be callable multiple times, leading to potential issues.",
                "remediation": "Ensure functions have measures to prevent repeated calls with identical or similar parameters, especially when these calls can produce adverse effects.",
            },
            {
                "question": "Is the global state updated correctly?",
                "description": "While working with a `memory` copy for optimization, developers might overlook updating the global state.",
                "remediation": "Always ensure the global state mirrors changes made in `memory`. Consider tools or extensions that can highlight discrepancies.",
            },
            {
                "question": "Is ETH/WETH handling implemented correctly?",
                "description": "Contracts might have special logic for ETH, like wrapping to WETH. Assuming exclusivity between handling ETH and WETH without checks can introduce errors.",
                "remediation": "Clearly differentiate the logic between ETH and WETH handling, ensuring no overlap or mutual exclusivity assumptions without validation.",
            },
            {
                "question": "Does the protocol put any sensitive data on the blockchain?",
                "description": "Data on the blockchain, including that marked 'private' in smart contracts, is visible to anyone who knows how to query the blockchain's state or analyze its transaction history. Private variables are not exempt from public inspection.",
                "remediation": "**Sensitive data should either be kept off-chain or encrypted before being stored on-chain.** It's important to manage encryption keys securely and ensure that on-chain data does not expose private information even when encrypted, if the encryption method is weak or the keys are mishandled.",
            },
            {
                "question": "Are there any code asymmetries?",
                "description": "In many projects, there should be some symmetries for different functions. For instance, a `withdraw` function should (usually) undo all the state changes of a `deposit` function and a `delete` function should undo all the state changes of the corresponding `add` function. Asymmetries in these function pairs (e.g., forgetting to unset a field or to subtract from a value) can often lead to undesired behavior. Sometimes one side of a 'pair' is missing, like missing removing from a whitelist while there is a function to add to a whitelist.",
                "remediation": "Review paired functions for symmetry and ensure they counteract each other's state changes appropriately.",
                "references": [
                    "https://github.com/OpenCoreCH/smart-contract-auditing-heuristics#code-asymmetries"
                ],
            },
            {
                "question": "Does calling a function multiple times with smaller amounts yield the same contract state as calling it once with the aggregate amount?",
                "description": "Associative properties of certain financial operations suggest that performing the operation multiple times with smaller amounts should yield an equivalent outcome as performing it once with the aggregate amount. Variations might be indicative of potential issues such as rounding errors, unintended fee accumulations, or other inconsistencies.",
                "remediation": "Implement tests to validate consistency. Where discrepancies exist, ensure they are intentional, minimal, and well-documented. If discrepancies are unintended, reevaluate the implementation to ensure precision and correctness.",
            }
        ]
    },