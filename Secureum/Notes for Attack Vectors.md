Always check if these business logic breakers are not present when you audit a lending and borrowing based DeFi protocol! 
1. The buyer's collateral cannot be liquidated when the loan is not paid back or the collateral drops below the threshold. 
2. The due date of the loan principal or payments is improperly moved to an earlier or later date. 
3. A bug where paying back the principal does not lead to principal reduction.
****
### When transferring something to another account check for receive(), fallback() functions which the contract must receive the e.g. ETH. Check for withdraw() funciton because if you transfer money to a contract which has no withrdaw() the funds will be stuck. 
****
Next time you are auditing/developing a lending protocol watch out for hookable tokens(ERC777). These tokens allow arbitrary callbacks via hooks that are called during token transfers and may cause serious problems. Its recommended to avoid using them in such protocols.
****
### Auditing Tips for NFT Projects -> [Auditing Tips for NFT Projects. Greetings, dear readers! We continue… | by Officer's Notes | Pessimistic Security](https://blog.pessimistic.io/auditing-tips-for-nft-projects-355872213848)
****
### Building secure SC - guide do Authentication, Complexity managment, Arithmetic issues, Decentralization and others -> [building-secure-contracts/development-guidelines/code_maturity.md at master · crytic/building-secure-contracts (github.com)](https://github.com/crytic/building-secure-contracts/blob/master/development-guidelines/code_maturity.md#rating-system)
****


