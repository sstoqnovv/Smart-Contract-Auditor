With `msg.sender` the owner can be a contract.

With `tx.origin` the owner can never be a contract.

In a simple call chain A->B->C->D, inside D `msg.sender` will be C, and `tx.origin` will be A.

`msg.sender` is preferred for the flexibility it provides. Furthermore, for Serenity, even though it's a while out, Vitalik recommends avoiding `tx.origin`

`msg.sender` gives the direct sender of the message, so for example a contract that passed it along.

`tx.origin` gives the **origin** of the transactions, so the user address it was originally sent from. In practice this will always be a user so eth's answer holds true.