"question": "Did you clarify all the actors and their allowed interactions in the protocol?",
"description": "This is a general check item. Having a clear understanding of all relevant actors and interactions in the protocol is critical for security.",
"remediation": "List down all the actors and interactions and draw a diagram.",

"question": "Are there functions lacking proper access controls?",
"description": "Access controls determine who can use certain functions of a contract. If these are missing or improperly implemented, it can expose the contract to unauthorized changes or withdrawals.",
"remediation": "Implement and rigorously test access controls like `onlyOwner` or role-based permissions to ensure only authorized users can access certain functions.",

 "question": "Do certain addresses require whitelisting?",
"description": "Whitelisting allows only a specific set of addresses to interact with the contract, offering an additional layer of security against malicious actors.",
"remediation": "Establish a whitelisting mechanism and ensure that only trusted addresses can execute sensitive or restricted operations.",

"question": "Does the protocol allow transfer of privileges?",
"description": "Transfer of critical privileges must be done in two-step process. A two-step transfer process, usually involving a request followed by a confirmation, adds an extra layer of security against unintentional or malicious owner changes.",
"remediation": "Implement a two-step transfer mechanism that requires the new actor to accept the transfer, ensuring better security and intentional ownership changes.",

 "question": "What happens during the transfer of privileges?",
 "description": "The protocol needs to work consistently and reasonably even during the transfer of privileges.",
 "remediation": "Double check how the protocol works during the transfer of privileges.",
 
"question": "Does the contract inherit others?",
"description": "If you do not override a parent contract's function explicitly, the parent's one will be exposed with its visibility and probably a wrong accessibiliy.",
"remediation": "Make sure you check the accessibility to the parent's external/public functions.",

"question": "Does the contract use `tx.origin` in validation?",
"description": "Use of `tx.origin` for authorization may be abused by a malicious contract forwarding calls from the legitimate user. Use `msg.sender` instead. `require( tx.origin == msg.sender)` is a useful check to ensure that the `msg.sender` is an EOA(externally owned account).",
"remediation": "Make sure you know the difference of `tx.origin` and `msg.sender` and use properly.",