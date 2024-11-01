{
        "category": "Centralization Risk",
        "data": [
            {
                "question": "What happens to the user accounting in special conditions?",
                "description": "Users must be allowed to manage their existing positions in all protocol status. For example, users must be able to repay the debt even when the protocol is paused or the protocol should not accrue debts when it is paused.",
                "remediation": "Ensure user positions are protected in special/emergent protocol situations.",
            },
            {
                "question": "Is there a pause mechanism?",
                "description": "Some functionalities must work even when the whole protocol is paused. For example, users must be able to withdraw (or repay) assets even while the protocol is paused.",
                "remediation": "Review the pause mechanism thoroughly to ensure that it only affects intended functions and can't be abused by a malicious operator.",
            },
            {
                "question": "Is there a functionality for the admin to withdraw from the protocol?",
                "description": "Some protocols are written to allow admin pull any amount of assets from the pool. This is a red flag and MUST be disallowed. The best practice is to track the protocol fee and only allow access to that amount.",
                "remediation": "Ensure the admin can not steal user funds. Track the protocol earning separately.",
            },
            {
                "question": "Can the admin change critical protocol property immediately?",
                "description": "Changes in the critical protocol properties MUST go through a cooling period to allow users react on the changes.",
                "remediation": "Implement a timelock for the critical property changes and emit proper events.",
            },
            {
                "question": "Is there any admin setter function missing events?",
                "description": "Events are often used to monitor the protocol status. Without emission of events, users might be affected due to ignorance of the changes.",
                "remediation": "Emit proper events on critical configuration changes.",
            },
            {
                "question": "How is the ownership/privilege transferred??",
                "description": "Critical privileges MUST be transferred via a two-step process and the protocol MUST behave as expected before/during/after transfer.",
                "remediation": "Use two-step process for transferring critical privileges and ensure the protocol works properly before/during/after the transfer.",
            },
            {
                "question": "Is there a proper validation in privileged setter functions?",
                "description": "The validation on the protocol configuration values is often overlooked assuming the admin is trusted. But it is always recommended clarifying the range of each configuration value and validate in setter functions. (e.g. protocol fee should be limited)",
                "remediation": "Ensure the protocol level properties are properly validated in the documented range.",
            }