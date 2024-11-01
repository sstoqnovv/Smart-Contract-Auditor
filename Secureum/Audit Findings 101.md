1. **Unhandled return values of** _**transfer**_ **and** _**transferFrom**_: ERC20 implementations are not always consistent. Some implementations of _transfer_ and _transferFrom_ could return ‘false’ on failure instead of reverting. It is safer to wrap such calls into _require()_ statements to these failures.
    
    1. Recommendation: Check the return value and revert on 0/false or use OpenZeppelin’s _SafeERC20_ wrapper functions
    2. Medium severity finding from [Consensys Diligence Audit of Aave Protocol V2](https://consensys.net/diligence/audits/2020/09/aave-protocol-v2/#unhandled-return-values-of-transfer-and-transferfrom)
        
2. **Random task execution**: In a scenario where a user takes a flash loan, __parseFLAndExecute()_ gives the flash loan wrapper contract (_FLAaveV2_, _FLDyDx_) the permission to execute functions on behalf of the user’s _DSProxy_. This execution permission is revoked only after the entire recipe execution is finished, which means that in case that any of the external calls along the recipe execution is malicious, it might call _executeAction()_ back, i.e. Reentrancy Attack, and inject any task it wishes (e.g. take user’s funds out, drain approved tokens, etc)
    
    1. Recommendation: A reentrancy guard (mutex) should be used to prevent such attack
    2. Critical severity finding from [Consensys Diligence Audit of Defi Saver](https://consensys.net/diligence/audits/2021/03/defi-saver/#random-task-execution)
        
3. **Tokens with more than 18 decimal points will cause issues**: It is assumed that the maximum number of decimals for each token is 18. However uncommon, it is possible to have tokens with more than 18 decimals, as an example YAMv2 has 24 decimals. This can result in broken code flow and unpredictable outcomes
    
    1. Recommendation: Make sure the code won’t fail in case the token’s decimals is more than 18
    2. Major severity finding from [Consensys Diligence Audit of Defi Saver](https://consensys.net/diligence/audits/2021/03/defi-saver/#tokens-with-more-than-18-decimal-points-will-cause-issues)
        
4. **Error codes of Compound’s** _**Comptroller.enterMarket**_**,** _**Comptroller.exitMarket**_ **are not checked**: Compound’s _enterMarket_/_exitMarket_ functions return an error code instead of reverting in case of failure. DeFi Saver smart contracts never check for the error codes returned from Compound smart contracts.
    
    1. Recommendation: Caller contract should revert in case the error code is not 0
    2. Major severity finding from [Consensys Diligence Audit of Defi Saver](https://consensys.net/diligence/audits/2021/03/defi-saver/#error-codes-of-compound-s-comptroller-entermarket-comptroller-exitmarket-are-not-checked)
        
5. **Reversed order of parameters in allowance function call**: the parameters that are used for the allowance function call are not in the same order that is used later in the call to _safeTransferFrom_.
    
    1. Recommendation: Reverse the order of parameters in allowance function call to fit the order that is in the safeTransferFrom function call.
    2. Medium severity finding from [Consensys Diligence Audit of Defi Saver](https://consensys.net/diligence/audits/2021/03/defi-saver/#reversed-order-of-parameters-in-allowance-function-call)
        
6. **Token approvals can be stolen in** _**DAOfiV1Router01.addLiquidity()**_: _DAOfiV1Router01.addLiquidity()_ creates the desired pair contract if it does not already exist, then transfers tokens into the pair and calls _DAOfiV1Pair.deposit()_. There is no validation of the address to transfer tokens from, so an attacker could pass in any address with nonzero token approvals to _DAOfiV1Router_. This could be used to add liquidity to a pair contract for which the attacker is the _pairOwner_, allowing the stolen funds to be retrieved using DAOfiV1Pair.withdraw().
    
    1. Recommendation: Transfer tokens from msg.sender instead of lp.sender
    2. Critical severity finding from [Consensys Diligence Audit of DAOfi](https://consensys.net/diligence/audits/2021/02/daofi/#token-approvals-can-be-stolen-in-daofiv1router01-addliquidity)
        
7. _**swapExactTokensForETH**_ **checks the wrong return value**: Instead of checking that the amount of tokens received from a swap is greater than the minimum amount expected from this swap, it calculates the difference between the initial receiver’s balance and the balance of the router
    
    1. Recommendation: Check the intended values
    2. Major severity finding from [Consensys Diligence Audit of DAOfi](https://consensys.net/diligence/audits/2021/02/daofi/#the-swapexacttokensforeth-checks-the-wrong-return-value)
        
8. _**DAOfiV1Pair.deposit()**_ **accepts deposits of zero, blocking the pool**: _DAOfiV1Pair.deposit()_ is used to deposit liquidity into the pool. Only a single deposit can be made, so no liquidity can ever be added to a pool where deposited == true. The deposit() function does not check for a nonzero deposit amount in either token, so a malicious user that does not hold any of the _baseToken_ or _quoteToken_ can lock the pool by calling deposit() without first transferring any funds to the pool.
    
    1. Recommendation: Require a minimum deposit amount with non-zero checks
    2. Medium severity finding from [Consensys Diligence Audit of DAOfi](https://consensys.net/diligence/audits/2021/02/daofi/#daofiv1pair-deposit-accepts-deposits-of-zero-blocking-the-pool)
        
9. _**GenesisGroup.commit**_ **overwrites previously-committed values**: The amount stored in the recipient’s _committedFGEN_ balance overwrites any previously-committed value. Additionally, this also allows anyone to commit an amount of “0” to any account, deleting their commitment entirely.
    
    1. Recommendation: Ensure the committed amount is added to the existing commitment.
    2. Critical severity finding from [Consensys Diligence Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#genesisgroup-commit-overwrites-previously-committed-values)
        
10. **Purchasing and committing still possible after launch**: Even after _GenesisGroup.launch_ has successfully been executed, it is still possible to invoke _GenesisGroup.purchase_ and _GenesisGroup.commit_.
    
    1. Recommendation: Consider adding validation in _GenesisGroup.purchase_ and _GenesisGroup.commit_ to make sure that these functions cannot be called after the launch.
    2. Critical severity finding from [Consensys Diligence Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#purchasing-and-committing-still-possible-after-launch)
        
11. _**UniswapIncentive**_ **overflow on pre-transfer hooks**: Before a token transfer is performed, Fei performs some combination of mint/burn operations via _UniswapIncentive.incentivize_. Both _incentivizeBuy_ and _incentivizeSell_ calculate buy/sell incentives using overflow-prone math, then mint / burn from the target according to the results. This may have unintended consequences, like allowing a caller to mint tokens before transferring them, or burn tokens from their recipient.
    
    1. Recommendation: Ensure casts in _getBuyIncentive_ and _getSellPenalty_ do not overflow
        
    2. Major severity finding from [Consensys Diligence Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#uniswapincentive-overflow-on-pre-transfer-hooks)
        
12. _**BondingCurve**_ **allows users to acquire FEI before launch**: allocate can be called before genesis launch, as long as the contract holds some nonzero PCV. By force-sending the contract 1 wei, anyone can bypass the majority of checks and actions in allocate, and mint themselves FEI each time the timer expires.
    
    1. Recommendation: Prevent allocate from being called before genesis launch
    2. Medium severity finding from [Consensys Diligence Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#bondingcurve-allows-users-to-acquire-fei-before-launch)
        
13. _**Timed.isTimeEnded**_ **returns true if the timer has not been initialized**: _Timed_ initialization is a 2-step process: 1) _Timed.duration_ is set in the constructor 2) _Timed.startTime_ is set when the method __initTimed_ is called. Before this second method is called, _isTimeEnded_() calculates remaining time using a _startTime_ of 0, resulting in the method returning true for most values, even though the timer has not technically been started.
    
    1. Recommendation: If Timed has not been initialized, _isTimeEnded_() should return false, or revert
    2. Medium severity finding from [Consensys Diligence Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#timed-istimeended-returns-true-if-the-timer-has-not-been-initialized)
        
14. **Overflow/underflow protection**: Having overflow/underflow vulnerabilities is very common for smart contracts. It is usually mitigated by using SafeMath or using solidity version ^0.8 (after solidity 0.8 arithmetical operations already have default overflow/underflow protection). In this code, many arithmetical operations are used without the ‘safe’ version. The reasoning behind it is that all the values are derived from the actual ETH values, so they can’t overflow.
    
    1. Recommendation: In our opinion, it is still safer to have these operations in a safe mode. So we recommend using SafeMath or solidity version ^0.8 compiler.
    2. Medium severity finding from [Consensys Diligence Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#overflow-underflow-protection)
        
15. **Unchecked return value for** _**IWETH.transfer**_ **call**: In _EthUniswapPCVController_, there is a call to _IWETH.transfer_ that does not check the return value. It is usually good to add a require-statement that checks the return value or to use something like _safeTransfer_; unless one is sure the given token reverts in case of a failure.
    
    1. Recommendation: Consider adding a require-statement or using _safeTransfer_
    2. Medium severity finding from [Consensys Diligence Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#unchecked-return-value-for-iweth-transfer-call)
        
16. _**GenesisGroup.emergencyExit**_ **remains functional after launch**: _emergencyExit_ is intended as an escape mechanism for users in the event the genesis launch method fails or is frozen. _emergencyExit_ becomes callable 3 days after launch is callable. These two methods are intended to be mutually-exclusive, but are not: either method remains callable after a successful call to the other. This may result in accounting edge cases.
    
    1. Recommendation: 1) Ensure launch cannot be called if _emergencyExit_ has been called 2) Ensure _emergencyExit_ cannot be called if launch has been called
    2. Medium severity finding from [Consensys Diligence Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#genesisgroup-emergencyexit-remains-functional-after-launch)
        
17. **ERC20 tokens with no return value will fail to transfer**: Although the ERC20 standard suggests that a transfer should return true on success, many tokens are non-compliant in this regard. In that case, the .transfer() call here will revert even if the transfer is successful, because solidity will check that the _RETURNDATASIZE_ matches the ERC20 interface.
    
    1. Recommendation: Consider using OpenZeppelin’s SafeERC20
    2. Major severity finding from [Consensys Diligence Audit of bitbank](https://consensys.net/diligence/audits/2020/11/bitbank/#erc20-tokens-with-no-return-value-will-fail-to-transfer)
        
18. **Reentrancy vulnerability in** _**MetaSwap.swap()**_: If an attacker is able to reenter _swap()_, they can execute their own trade using the same tokens and get all the tokens for themselves.
    
    1. Recommendation: Use a simple reentrancy guard, such as OpenZeppelin’s ReentrancyGuard to prevent reentrancy in _MetaSwap.swap()_
    2. Major severity finding from [Consensys Diligence Audit of MetaSwap](https://consensys.net/diligence/audits/2020/08/metaswap/#reentrancy-vulnerability-in-metaswap-swap)
        
19. **A new malicious adapter can access users’ tokens**: The purpose of the _MetaSwap_ contract is to save users gas costs when dealing with a number of different aggregators. They can just approve() their tokens to be spent by _MetaSwap_ (or in a later architecture, the Spender contract). They can then perform trades with all supported aggregators without having to reapprove anything. A downside to this design is that a malicious (or buggy) adapter has access to a large collection of valuable assets. Even a user who has diligently checked all existing adapter code before interacting with _MetaSwap_ runs the risk of having their funds intercepted by a new malicious adapter that’s added later.
    
    1. Recommendation: Make _MetaSwap_ contract the only contract that receives token approval. It then moves tokens to the Spender contract before that contract _DELEGATECALLs_ to the appropriate adapter. In this model, newly added adapters shouldn’t be able to access users’ funds.
    2. Medium severity finding from [Consensys Diligence Audit of MetaSwap](https://consensys.net/diligence/audits/2020/08/metaswap/#a-new-malicious-adapter-can-access-users-tokens)
        
20. **Owner can front-run traders by updating adapters**: _MetaSwap_ owners can front-run users to swap an adapter implementation. This could be used by a malicious or compromised owner to steal from users. Because adapters are _DELEGATECALL’ed_, they can modify storage. This means any adapter can overwrite the logic of another adapter, regardless of what policies are put in place at the contract level. Users must fully trust every adapter because just one malicious adapter could change the logic of all other adapters.
    
    1. Recommendation: At a minimum, disallow modification of existing adapters. Instead, simply add new adapters and disable the old ones.
        
    2. Medium severity finding from [Consensys Diligence Audit of MetaSwap](https://consensys.net/diligence/audits/2020/08/metaswap/#owner-can-front-run-traders-by-updating-adapters)
        
21. **Users can collect interest from** _**SavingsContract**_ **by only staking mTokens momentarily**: The _SAVE_ contract allows users to deposit _mAssets_ in return for lending yield and swap fees. When depositing _mAsset_, users receive a “credit” tokens at the momentary credit/_mAsset_ exchange rate which is updated at every deposit. However, the smart contract enforces a minimum timeframe of 30 minutes in which the interest rate will not be updated. A user who deposits shortly before the end of the timeframe will receive credits at the stale interest rate and can immediately trigger an update of the rate and withdraw at the updated (more favorable) rate after the 30 minutes window. As a result, it would be possible for users to benefit from interest payouts by only staking mAssets momentarily and using them for other purposes the rest of the time.
    
    1. Recommendation: Remove the 30 minutes window such that every deposit also updates the exchange rate between credits and tokens.
        
    2. Medium severity finding from [Consensys Diligence Audit of mstable-1.1](https://consensys.net/diligence/audits/2020/07/mstable-1.1/#users-can-collect-interest-from-savingscontract-by-only-staking-mtokens-momentarily)
        
22. **Oracle updates can be manipulated to perform atomic front-running attack**: It is possible to atomically arbitrage rate changes in a risk-free way by “sandwiching” the Oracle update between two transactions. The attacker would send the following 2 transactions at the moment the Oracle update appears in the mempool: 1) The first transaction, which is sent with a higher gas price than the Oracle update transaction, converts a very small amount. This “locks in” the conversion weights for the block since _handleExternalRateChange()_ only updates weights once per block. By doing this, the arbitrageur ensures that the stale Oracle price is initially used when doing the first conversion in the following transaction. The second transaction, which is sent at a slightly lower gas price than the transaction that updates the Oracle, performs a large conversion at the old weight, adds a small amount of Liquidity to trigger rebalancing and converts back at the new rate. The attacker can obtain liquidity for step 2 using a flash loan. The attack will deplete the reserves of the pool.
    
    1. Recommendation: Do not allow users to trade at a stale Oracle rate and trigger an Oracle price update in the same transaction.
    2. Critical severity finding from [Consensys Diligence Audit of Bancor v2 AMM](https://consensys.net/diligence/audits/2020/06/bancor-v2-amm-security-audit/#oracle-updates-can-be-manipulated-to-perform-atomic-front-running-attack)
        
23. **Certain functions lack input validation routines**: The functions should first check if the passed arguments are valid first. These checks should include, but not be limited to: 1) uint should be larger than 0 when 0 is considered invalid 2) uint should be within constraints 3) int should be positive in some cases 4) length of arrays should match if more arrays are sent as arguments 5) addresses should not be 0x0
    
    1. Recommendation: Add tests that check if all of the arguments have been validated. Consider checking arguments as an important part of writing code and developing the system.
    2. Major severity finding from [Consensys Diligence Audit of Shell Protocol](https://consensys.net/diligence/audits/2020/06/shell-protocol/#certain-functions-lack-input-validation-routines)
        
24. **Remove** _**Loihi**_ **methods that can be used as backdoors by the administrator**: There are several functions in _Loihi_ that give extreme powers to the shell administrator. The most dangerous set of those is the ones granting the capability to add assimilators. Since assimilators are essentially a proxy architecture to delegate code to several different implementations of the same interface, the administrator could, intentionally or unintentionally, deploy malicious or faulty code in the implementation of an assimilator. This means that the administrator is essentially totally trusted to not run code that, for example, drains the whole pool or locks up the users’ and LPs’ tokens. In addition to these, the function _safeApprove_ allows the administrator to move any of the tokens the contract holds to any address regardless of the balances any of the users have. This can also be used by the owner as a backdoor to completely drain the contract.
    
    1. Recommendation: Remove the _safeApprove_ function and, instead, use a trustless escape-hatch mechanism. For the assimilator addition functions, our recommendation is that they are made completely internal, only callable in the constructor, at deploy time. Even though this is not a big structural change (in fact, it reduces the attack surface), it is, indeed, a feature loss. However, this is the only way to make each shell a time-invariant system. This would not only increase Shell’s security but also would greatly improve the trust the users have in the protocol since, after deployment, the code is now static and auditable.
    2. Major severity finding from [Consensys Diligence Audit of Shell Protocol](https://consensys.net/diligence/audits/2020/06/shell-protocol/#remove-loihi-methods-that-can-be-used-as-backdoors-by-the-administrator)
        
25. **A reverting fallback function will lock up all payouts**: In _BoxExchange.sol_, the internal function __transferEth_() reverts if the transfer does not succeed. The __payment_() function processes a list of transfers to settle the transactions in an _ExchangeBox_. If any of the recipients of an ETH transfer is a smart contract that reverts, then the entire payout will fail and will be unrecoverable.
    
    1. Recommendation: 1) Implement a queuing mechanism to allow buyers/sellers to initiate the withdrawal on their own using a ‘pull-over-push pattern.’ 2) Ignore a failed transfer and leave the responsibility up to users to receive them properly.
    2. Critical severity finding from [Consensys Diligence Audit of Lien Protocol](https://consensys.net/diligence/audits/2020/05/lien-protocol/#a-reverting-fallback-function-will-lock-up-all-payouts)
        
26. _**Saferagequit**_ **makes you lose funds**: _safeRagequit_ and _ragequit_ functions are used for withdrawing funds from the LAO. The difference between them is that ragequit function tries to withdraw all the allowed tokens and _safeRagequit_ function withdraws only some subset of these tokens, defined by the user. It’s needed in case the user or _GuildBank_ is blacklisted in some of the tokens and the transfer reverts. The problem is that even though you can quit in that case, you’ll lose the tokens that you exclude from the list. To be precise, the tokens are not completely lost, they will belong to the LAO and can still potentially be transferred to the user who quit. But that requires a lot of trust, coordination, time and anyone can steal some part of these tokens.
    
    1. Recommendation: Implementing pull pattern for token withdrawals should solve the issue. Users will be able to quit the LAO and burn their shares but still keep their tokens in the LAO’s contract for some time if they can’t withdraw them right now.
    2. Critical severity finding from [Consensys Diligence Audit of The Lao](https://consensys.net/diligence/audits/2020/01/the-lao)
        
27. **Creating proposal is not trustless**: Usually, if someone submits a proposal and transfers some amount of tribute tokens, these tokens are transferred back if the proposal is rejected. But if the proposal is not processed before the emergency processing, these tokens will not be transferred back to the proposer. This might happen if a tribute token or a deposit token transfers are blocked. Tokens are not completely lost in that case, they now belong to the LAO shareholders and they might try to return that money back. But that requires a lot of coordination and time and everyone who ragequits during that time will take a part of that tokens with them.
    
    1. Recommendation: Pull pattern for token transfers would solve the issue
    2. Critical severity finding from [Consensys Diligence Audit of The Lao](https://consensys.net/diligence/audits/2020/01/the-lao)
        
28. **Emergency processing can be blocked**: The main reason for the emergency processing mechanism is that there is a chance that some token transfers might be blocked. For example, a sender or a receiver is in the USDC blacklist. Emergency processing saves from this problem by not transferring tribute token back to the user (if there is some) and rejecting the proposal. The problem is that there is still a deposit transfer back to the sponsor and it could be potentially blocked too. If that happens, proposal can’t be processed and the LAO is blocked.
    
    1. Recommendation: Pull pattern for token transfers would solve the issue
    2. Critical severity finding from [Consensys Diligence Audit of The Lao](https://consensys.net/diligence/audits/2020/01/the-lao)
        
29. **Token Overflow might result in system halt or loss of funds**: If a token overflows, some functionality such as _processProposal_, _cancelProposal_ will break due to SafeMath reverts. The overflow could happen because the supply of the token was artificially inflated to oblivion.
    
    1. Recommendation: We recommend to allow overflow for broken or malicious tokens. This is to prevent system halt or loss of funds. It should be noted that in case an overflow occurs, the balance of the token will be incorrect for all token holders in the system
    2. Major severity finding from [Consensys Diligence Audit of The Lao](https://consensys.net/diligence/audits/2020/01/the-lao)
        
30. **Whitelisted tokens limit**: __ragequit_ function is iterating over all whitelisted tokens. If the number of tokens is too big, a transaction can run out of gas and all funds will be blocked forever.
    
    1. Recommendation: A simple solution would be just limiting the number of whitelisted tokens. If the intention is to invest in many new tokens over time, and it’s not an option to limit the number of whitelisted tokens, it’s possible to add a function that removes tokens from the whitelist. For example, it’s possible to add a new type of proposal that is used to vote on token removal if the balance of this token is zero. Before voting for that, shareholders should sell all the balance of that token.
    2. Major severity finding from [Consensys Diligence Audit of The Lao](https://consensys.net/diligence/audits/2020/01/the-lao)
        
31. **Summoner can steal funds using bailout**: The _bailout_ function allows anyone to transfer kicked user’s funds to the summoner if the user does not call safeRagequit (which forces the user to lose some funds). The intention is for the summoner to transfer these funds to the kicked member afterwards. The issue here is that it requires a lot of trust to the summoner on the one hand, and requires more time to kick the member out of the LAO.
    
    1. Recommendation: By implementing pull pattern for token transfers, kicked member won’t be able to block the ragekick and the LAO members would be able to kick anyone much quicker. There is no need to keep the _bailout_ function.
    2. Major severity finding from [Consensys Diligence Audit of The Lao](https://consensys.net/diligence/audits/2020/01/the-lao)
        
32. **Sponsorship front-running**: If proposal submission and sponsorship are done in 2 different transactions, it’s possible to front-run the _sponsorProposal_ function by any member. The incentive to do that is to be able to block the proposal afterwards.
    
    1. Recommendation: Pull pattern for token transfers will solve the issue. Front-running will still be possible but it doesn’t affect anything.
    2. Major severity finding from [Consensys Diligence Audit of The Lao](https://consensys.net/diligence/audits/2020/01/the-lao)
        
33. **Delegate assignment front-running**: Any member can front-run another member’s _delegateKey_ assignment. If you try to submit an address as your _delegateKey_, someone else can try to assign your delegate address to themselves. While incentive of this action is unclear, it’s possible to block some address from being a delegate forever.
    
    1. Recommendation: Make it possible for a _delegateKey_ to approve _delegateKey_ assignment or cancel the current delegation. Commit-reveal methods can also be used to mitigate this attack.
    2. Medium severity finding from [Consensys Diligence Audit of The Lao](https://consensys.net/diligence/audits/2020/01/the-lao)
        
34. **Queued transactions cannot be canceled**: The Governor contract contains special functions to set it as the admin of the _Timelock_. Only the admin can call _Timelock.cancelTransaction_. There are no functions in Governor that call _Timelock.cancelTransaction_. This makes it impossible for _Timelock.cancelTransaction_ to ever be called.
    
    1. Recommendation: Short term, add a function to the Governor that calls _Timelock.cancelTransaction_. It is unclear who should be able to call it, and what other restrictions there should be around cancelling a transaction. Long term, consider letting Governor inherit from _Timelock_. This would allow a lot of functions and code to be removed and significantly lower the complexity of these two contracts.
    2. High Risk severity finding from [ToB’s Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
35. **Proposal transactions can be executed separately and block** _**Proposal.execute**_ **call**: Missing access controls in the _Timelock.executeTransaction_ function allow Proposal transactions to be executed separately, circumventing the _Governor.execute_ function.
    
    1. Recommendation: Short term, only allow the admin to call _Timelock.executeTransaction_
    2. High Risk severity finding from [ToB’s Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
36. **Proposals could allow** _**Timelock.admin**_ **takeover**: The Governor contract contains special functions to let the guardian queue a transaction to change the _Timelock.admin_. However, a regular Proposal is also allowed to contain a transaction to change the _Timelock.admin_. This poses an unnecessary risk in that an attacker could create a Proposal to change the _Timelock.admin_.
    
    1. Recommendation: Short term, add a check that prevents _setPendingAdmin_ to be included in a Proposal
        
    2. High Risk severity finding from [ToB’s Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
37. **Reentrancy and untrusted contract call in** _**mintMultiple**_: Missing checks and no reentrancy prevention allow untrusted contracts to be called from _mintMultiple_. This could be used by an attacker to drain the contracts.
    
    1. Recommendation: Short term, add checks that cause _mintMultiple_ to revert if the amount is zero or the asset is not supported. Add a reentrancy guard to the _mint_, _mintMultiple_, _redeem_, and _redeemAll_ functions. Long term, make use of Slither which will flag the reentrancy. Or even better, use Crytic and incorporate static analysis checks into your CI/CD pipeline. Add reentrancy guards to all non-view functions callable by anyone. Make sure to always revert a transaction if an input is incorrect. Disallow calling untrusted contracts.
        
    2. High Risk severity finding from [ToB’s Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
38. **Lack of return value checks can lead to unexpected results**: Several function calls do not check the return value. Without a return value check, the code is error-prone, which may lead to unexpected results.
    
    1. Recommendation: Short term, check the return value of all calls mentioned above. Long term, subscribe to Crytic.io to catch missing return checks. Crytic identifies this bug type automatically.
        
    2. High Risk severity finding from [ToB’s Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
39. **External calls in loop can lead to denial of service**: Several function calls are made in unbounded loops. This pattern is error-prone as it can trap the contracts due to the gas limitations or failed transactions.
    
    1. Recommendation: Short term, review all the loops mentioned above and either: 1) allow iteration over part of the loop, or 2) remove elements. Long term, subscribe to Crytic.io to review external calls in loops. Crytic catches bugs of this type.
        
    2. High Risk severity finding from [ToB’s Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
40. **OUSD allows users to transfer more tokens than expected**: Under certain circumstances, the OUSD contract allows users to transfer more tokens than the ones they have in their balance. This issue seems to be caused by a rounding issue when the _creditsDeducted_ is calculated and subtracted.
    
    1. Recommendation: Short term, make sure the balance is correctly checked before performing all the arithmetic operations. This will make sure it does not allow to transfer more than expected. Long term, use Echidna to write properties that ensure ERC20 transfers are transferring the expected amount.
        
    2. High Risk severity finding from [ToB’s Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
41. **OUSD total supply can be arbitrary, even smaller than user balances**: The OUSD token contract allows users to opt out of rebasing effects. At that point, their exchange rate is “fixed”, and further rebases will not have an impact on token balances (until the user opts in).
    
    1. Recommendation: Short term, we would advise making clear all common invariant violations for users and other stakeholders. Long term, we would recommend designing the system in such a way to preserve as many commonplace invariants as possible.
        
    2. High Risk severity finding from [ToB’s Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
42. **Flash minting can be used to redeem** _**fyDAI**_: The flash-minting feature from the _fyDAI_ token can be used to redeem an arbitrary amount of funds from a mature token.
    
    1. Recommendation: Short term, disallow calls to redeem in the _YDai_ and Unwind contracts during flash minting. Long term, do not include operations that allow any user to manipulate an arbitrary amount of funds, even if it is in a single transaction. This will prevent attackers from gaining leverage to manipulate the market and break internal invariants.
        
    2. Medium Risk severity finding from [ToB’s Audit of Yield Protocol](https://github.com/trailofbits/publications/blob/master/reviews/YieldProtocol.pdf)
        
43. **Lack of** _**chainID**_ **validation allows signatures to be re-used across forks**: _YDai_ implements the draft ERC 2612 via the _ERC20Permit_ contract it inherits from. This allows a third party to transmit a signature from a token holder that modifies the ERC20 allowance for a particular user. These signatures used in calls to permit in _ERC20Permit_ do not account for chain splits. The _chainID_ is included in the domain separator. However, it is not updatable and not included in the signed data as part of the permit call. As a result, if the chain forks after deployment, the signed message may be considered valid on both forks.
    
    1. Recommendation: Short term, include the _chainID_ opcode in the permit schema. This will make replay attacks impossible in the event of a post-deployment hard fork. Long term, document and carefully review any signature schemas, including their robustness to replay on different wallets, contracts, and blockchains. Make sure users are aware of signing best practices and the danger of signing messages from untrusted sources.
        
    2. High Risk severity finding from [ToB’s Audit of Yield Protocol](https://github.com/trailofbits/publications/blob/master/reviews/YieldProtocol.pdf)
        
44. **Lack of a contract existence check allows token theft**: Since there’s no existence check for contracts that interact with external tokens, an attacker can steal funds by registering a token that’s not yet deployed. __safeTransferFrom_ will return success even if the token is not yet deployed, or was self-destructed. An attacker that knows the address of a future token can register the token in Hermez, and deposit any amount prior to the token deployment. Once the contract is deployed and tokens have been deposited in Hermez, the attacker can steal the funds. The address of a contract to be deployed can be determined by knowing the address of its deployer.
    
    1. Recommendation: Short term, check for contract existence in _ _safeTransferFrom_. Add a similar check for any low-level calls, including in _WithdrawalDelayer_. This will prevent an attacker from listing and depositing tokens in a contract that is not yet deployed. Long term, carefully review the Solidity documentation, especially the Warnings section. The Solidity documentation warns: The low-level call, _delegatecall_ and _callcode_ will return success if the called account is non-existent, as part of the design of EVM. Existence must be checked prior to calling if desired.
        
    2. High Risk severity finding from [ToB’s Audit of Hermez](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
45. **No incentive for bidders to vote earlier**: Hermez relies on a voting system that allows anyone to vote with any weight at the last minute. As a result, anyone with a large fund can manipulate the vote. Hermez’s voting mechanism relies on bidding. There is no incentive for users to bid tokens well before the voting ends. Users can bid a large amount of tokens just before voting ends, and anyone with a large fund can decide the outcome of the vote. As all the votes are public, users bidding earlier will be penalized, because their bids will be known by the other participants. An attacker can know exactly how much currency will be necessary to change the outcome of the voting just before it ends.
    
    1. Recommendation: Short term, explore ways to incentivize users to vote earlier. Consider a weighted bid, with a weight decreasing over time. While it won’t prevent users with unlimited resources from manipulating the vote at the last minute, it will make the attack more expensive and reduce the chance of vote manipulation. Long term, stay up to date with the latest research on blockchain-based online voting and bidding. Blockchain-based online voting is a known challenge. No perfect solution has been found yet.
        
    2. Medium Risk severity finding from [ToB’s Audit of Hermez](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
46. **Lack of access control separation is risky**: The system uses the same account to change both frequently updated parameters and those that require less frequent updates. This architecture is error-prone and increases the severity of any privileged account compromises.
    
    1. Recommendation: Short term, use a separate account to handle updating the tokens/USD ratio. Using the same account for the critical operations and update the tokens/USD ratio increases underlying risks. Long term, document the access controls and set up a proper authorization architecture. Consider the risks associated with each access point and their frequency of usage to evaluate the proper design.
        
    2. High Risk severity finding from [ToB’s Audit of Hermez](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
47. **Lack of two-step procedure for critical operations leaves them error-prone**: Several critical operations are done in one function call. This schema is error-prone and can lead to irrevocable mistakes. For example, the setter for the whitehack group address sets the address to the provided argument. If the address is incorrect, the new address will take on the functionality of the new role immediately. However, a two-step process is similar to the approve-transferFrom functionality: The contract approves the new address for a new role, and the new address acquires the role by calling the contract.
    
    1. Recommendation: Short term, use a two-step procedure for all non-recoverable critical operations to prevent irrecoverable mistakes. Long term, identify and document all possible actions and their associated risks for privileged accounts. Identifying the risks will assist codebase review and prevent future mistakes.
        
    2. High Risk severity finding from [ToB’s Audit of Hermez](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
48. **Initialization functions can be front-run**: _Hermez_, _HermezAuctionProtocol_, and _WithdrawalDelayer_ have initialization functions that can be front-run, allowing an attacker to incorrectly initialize the contracts. Due to the use of the _delegatecall_ proxy pattern, _Hermez_, _HermezAuctionProtocol_, and _WithdrawalDelayer_ cannot be initialized with a constructor, and have initializer functions. All these functions can be front-run by an attacker, allowing them to initialize the contracts with malicious values.
    
    1. Recommendation: Short term, either: 1) Use a factory pattern that will prevent front-running of the initialization, or 2) Ensure the deployment scripts are robust in case of a front-running attack. Carefully review the Solidity documentation, especially the Warnings section. Carefully review the pitfalls of using delegatecall proxy pattern.
        
    2. High Risk severity finding from [ToB’s Audit of Hermez](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
49. **Missing validation of** _**_owner**_ **argument could indefinitely lock owner role**: A lack of input validation of the __owner_ argument in both the constructor and _setOwner_ functions could permanently lock the owner role, requiring a costly redeploy. To resolve an incorrect owner issue, Uniswap would need to redeploy the factory contract and re-add pairs and liquidity. Users might not be happy to learn of these actions, which could lead to reputational damage. Certain users could also decide to continue using the original factory and pair contracts, in which owner functions cannot be called. This could lead to the concurrent use of two versions of Uniswap, one with the original factory contract and no valid owner and another in which the owner was set correctly. Trail of Bits identified four distinct cases in which an incorrect owner is set: 1) Passing address(0) to the constructor 2) Passing address(0) to the _setOwner_ function 3) Passing an incorrect address to the constructor 4)  Passing an incorrect address to the _setOwner_ function.
    
    1. Recommendation: Several improvements could prevent the four above mentioned cases: 1) Designate _msg.sender_ as the initial owner, and transfer ownership to the chosen owner after deployment. 2) Implement a two-step ownership-change process through which the new owner needs to accept ownership. 3) If it needs to be possible to set the owner to address(0), implement a _renounceOwnership_ function.
        
    2. Medium Risk severity finding from [ToB’s Audit of Uniswap V3](https://github.com/Uniswap/uniswap-v3-core/blob/main/audits/tob/audit.pdf)
        
50. **Incorrect comparison enables swapping and token draining at no cost**: An incorrect comparison in the swap function allows the swap to succeed even if no tokens are paid. This issue could be used to drain any pool of all of its tokens at no cost. The swap function calculates how many tokens the initiator (_msg.sender_) needs to pay (_amountIn_) to receive the requested amount of tokens (_amountOut_). It then calls the _uniswapV3SwapCallback_ function on the initiator’s account, passing in the amount of tokens to be paid. The callback function should then transfer at least the requested amount of tokens to the pool contract. Afterward, a require inside the swap function verifies that the correct amount of tokens (amountIn) has been transferred to the pool. However, the check inside the require is incorrect. The operand used is >= instead of <=.
    
    1. Recommendation: Replace >= with <= in the require statement.
        
    2. High Risk severity finding from [ToB’s Audit of Uniswap V3](https://github.com/Uniswap/uniswap-v3-core/blob/main/audits/tob/audit.pdf)
        
51. **Unbound loop enables denial of service**: The swap function relies on an unbounded loop. An attacker could disrupt swap operations by forcing the loop to go through too many operations, potentially trapping the swap due to a lack of gas.
    
    1. Recommendation: Bound the loops and document the bounds.
        
    2. Medium Risk severity finding from [ToB’s Audit of Uniswap V3](https://github.com/Uniswap/uniswap-v3-core/blob/main/audits/tob/audit.pdf)
        
52. **Front-running pool’s initialization can lead to draining of liquidity provider’s initial deposits**: A front-run on _UniswapV3Pool.initialize_ allows an attacker to set an unfair price and to drain assets from the first deposits. There are no access controls on the initialize function, so anyone could call it on a deployed pool. Initializing a pool with an incorrect price allows an attacker to generate profits from the initial liquidity provider’s deposits.
    
    1. Recommendation: 1) moving the price operations from initialize to the constructor, 2) adding access controls to initialize, or 3) ensuring that the documentation clearly warns users about incorrect initialization.
        
    2. Medium Risk severity finding from [ToB’s Audit of Uniswap V3](https://github.com/Uniswap/uniswap-v3-core/blob/main/audits/tob/audit.pdf)
        
53. **Swapping on zero liquidity allows for control of the pool’s price**: Swapping on a tick with zero liquidity enables a user to adjust the price of 1 wei of tokens in any direction. As a result, an attacker could set an arbitrary price at the pool’s initialization or if the liquidity providers withdraw all of the liquidity for a short time.
    
    1. Recommendation: No straightforward way to prevent the issue. Ensure pools don’t end up in unexpected states. Warn users of potential risks.
        
    2. Medium Risk severity finding from [ToB’s Audit of Uniswap V3](https://github.com/Uniswap/uniswap-v3-core/blob/main/audits/tob/audit.pdf)
        
54. **Failed transfer may be overlooked due to lack of contract existence check**: Because the pool fails to check that a contract exists, the pool may assume that failed transactions involving destructed tokens are successful. _TransferHelper.safeTransfer_ performs a transfer with a low-level call without confirming the contract’s existence. As a result, if the tokens have not yet been deployed or have been destroyed, safeTransfer will return success even though no transfer was executed.
    
    1. Recommendation: Short term, check the contract’s existence prior to the low-level call in _TransferHelper.safeTransfer_. Long term, avoid low-level calls.
        
    2. High Risk severity finding from [ToB’s Audit of Uniswap V3](https://github.com/Uniswap/uniswap-v3-core/blob/main/audits/tob/audit.pdf)
        
55. **Use of undefined behavior in equality check**: On the left-hand side of the equality check, there is an assignment of the variable _outputAmt__. The right-hand side uses the same variable. The Solidity 0.7.3. documentation states that “The evaluation order of expressions is not specified (more formally, the order in which the children of one node in the expression tree are evaluated is not specified, but they are of course evaluated before the node itself). It is only guaranteed that statements are executed in order and short-circuiting for boolean expressions is done” which means that this check constitutes an instance of undefined behavior. As such, the behavior of this code is not specified and could change in a future release of Solidity.
    
    1. Recommendation: Short term, rewrite the if statement such that it does not use and assign the same variable in an equality check. Long term, ensure that the codebase does not contain undefined Solidity or EVM behavior.
        
    2. High Risk severity finding from [ToB’s Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
56. **Assimilators’ balance functions return raw values**: The system converts raw values to numeraire values for its internal arithmetic. However, in one instance it uses raw values alongside numeraire values. Interchanging raw and numeraire values will produce unwanted results and may result in loss of funds for liquidity provider.
    
    1. Recommendation: Short term, change the semantics of the three functions listed above in the CADC, XSGD, and EURS assimilators to return the numeraire balance. Long term, use unit tests and fuzzing to ensure that all calculations return the expected values. Additionally, ensure that changes to the Shell Protocol do not introduce bugs such as this one.
        
    2. High Risk severity finding from [ToB’s Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
57. **System always assumes USDC is equivalent to USD**: Throughout the system, assimilators are used to facilitate the processing of various stablecoins. However, the _UsdcToUsdAssimilator_’s implementation of the _getRate_ method does not use the USDC-USD oracle provided by Chainlink; instead, it assumes 1 USDC is always worth 1 USD. A deviation in the exchange rate of 1 USDC = 1 USD could result in exchange errors.
    
    1. Recommendation: Short term, replace the hard-coded integer literal in the _UsdcToUsdAssimilator_’s _getRate_ method with a call to the relevant Chainlink oracle, as is done in other assimilator contracts. Long term, ensure that the system is robust against a decrease in the price of any stablecoin.
        
    2. Medium Risk severity finding from [ToB’s Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
58. **Assimilators use a deprecated Chainlink API**: The old version of the Chainlink price feed API (_AggregatorInterface_) is used throughout the contracts and tests. For example, the deprecated function _latestAnswer_ is used. This function is not present in the latest API reference (_AggregatorInterfaceV3_). However, it is present in the deprecated API reference. In the worst-case scenario, the deprecated contract could cease to report the latest values, which would very likely cause liquidity providers to incur losses.
    
    1. Recommendation: Use the latest stable versions of any external libraries or contracts leveraged by the codebase
        
    2. Undetermined Risk severity finding from [ToB’s Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
59. _**cancelOrdersUpTo**_ **can be used to permanently block future orders**: Users can cancel an arbitrary number of future orders, and this operation is not reversible. The _cancelOrdersUpTo_ function (Figure 3.1) can cancel an arbitrary number of orders in a single, fixed-size transaction. This function uses a parameter to discard any order with salt less than the input value. However, _cancelOrdersUpTo_ can cancel future orders if it is called with a very large value (e.g., _MAX_UINT256_ - 1). This operation will cancel future orders, except for the one with salt equal to _MAX_UINT256_.
    
    1. Recommendation: Properly document this behavior to warn users about the permanent effects of _cancelOrderUpTo_ on future orders. Alternatively, disallow the cancelation of future orders.
        
    2. High Risk severity finding from [ToB’s Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
60. **Specification-Code mismatch for** _**AssetProxyOwner**_ **timelock period**: The specification for _AssetProxyOwner_ says: "The _AssetProxyOwner_ is a time-locked multi-signature wallet that has permission to perform administrative functions within the protocol. Submitted transactions must pass a 2 week timelock before they are executed." The _MultiSigWalletWithTimeLock_._sol_ and _AssetProxyOwner_._sol_ contracts' timelock-period implementation/usage does not enforce the two-week period, but is instead configurable by the wallet owner without any range checks. Either the specification is outdated (most likely), or this is a serious flaw.
    
    1. Recommendation: Short term, implement the necessary range checks to enforce the timelock described in the specification. Otherwise correct the specification to match the intended behavior. Long term, make sure implementation and specification are in sync. Use Echidna or Manticore to test that your code properly implements the specification.
        
    2. High Risk severity finding from [ToB’s Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
61. **Unclear documentation on how order filling can fail**: The 0x documentation is unclear about how to determine whether orders are fillable or not. Even some fillable orders cannot be completely filled. The 0x specification does not state clearly enough how fillable orders are determined.
    
    1. Recommendation: Define a proper procedure to determine if an order is fillable and document it in the protocol specification. If necessary, warn the user about potential constraints on the orders.
        
    2. High Risk severity finding from [ToB’s Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
62. **Market makers have a reduced cost for performing front-running attacks**: Market makers receive a portion of the protocol fee for each order filled, and the protocol fee is based on the transaction gas price. Therefore market makers are able to specify a higher gas price for a reduced overall transaction rate, using the refund they will receive upon disbursement of protocol fee pools.
    
    1. Recommendation: Short term, properly document this issue to make sure users are aware of this risk. Establish a reasonable cap for the protocolFeeMultiplier to mitigate this issue. Long term, consider using an alternative fee that does not depend on the tx.gasprice to avoid reducing the cost of performing front-running attacks.
        
    2. Medium Risk severity finding from [ToB’s Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
63. _**setSignatureValidatorApproval**_ **race condition may be exploitable**: If a validator is compromised, a race condition in the signature validator approval logic becomes exploitable. The _setSignatureValidatorApproval_ function (Figure 4.1) allows users to delegate the signature validation to a contract. However, if the validator is compromised, a race condition in this function could allow an attacker to validate any amount of malicious transactions.
    
    1. Recommendation: Short term, document this behavior to make sure users are aware of the inherent risks of using validators in case of a compromise. Long term, consider monitoring the blockchain using the _SignatureValidatorApproval_ events to catch front-running attacks.
        
    2. Medium Risk severity finding from [ToB’s Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
64. **Batch processing of transaction execution and order matching may lead to exchange griefing**: Batch processing of transaction execution and order matching will iteratively process every transaction and order, which all involve filling. If the asset being filled does not have enough allowance, the asset’s transferFrom will fail, causing _AssetProxyDispatcher_ to revert. NoThrow variants of batch processing, which are available for filling orders, are not available for transaction execution and order matching. So if one transaction or order fails this way, the entire batch will revert and will have to be re-submitted after the reverting transaction is removed.
    
    1. Recommendation: Short term, implement NoThrow variants for batch processing of transaction execution and order matching. Long term, take into consideration the effect of malicious inputs when implementing functions that perform a batch of operations.
        
    2. Medium Risk severity finding from [ToB’s Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
65. **Zero fee orders are possible if a user performs transactions with a zero gas price**: Users can submit valid orders and avoid paying fees if they use a zero gas price. The computation of fees for each transaction is performed in the _calculateFillResults_ function. It uses the gas price selected by the user and the _protocolFeeMultiplier_ coefficient. Since the user completely controls the gas price of their transaction and the price could even be zero, the user could feasibly avoid paying fees.
    
    1. Recommendation: Short term, select a reasonable minimum value for the protocol fee for each order or transaction. Long term, consider not depending on the gas price for the computation of protocol fees. This will avoid giving miners an economic advantage in the system.
        
    2. Medium Risk severity finding from [ToB’s Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
66. **Calls to** _**setParams**_ **may set invalid values and produce unexpected behavior in the staking contracts**: Certain parameters of the contracts can be configured to invalid values, causing a variety of issues and breaking expected interactions between contracts. _setParams_ allows the owner of the staking contracts to reparameterize critical parameters. However, reparameterization lacks sanity/threshold/limit checks on all parameters.
    
    1. Recommendation: Add proper validation checks on all parameters in _setParams_. If the validation procedure is unclear or too complex to implement on-chain, document the potential issues that could produce invalid values.
        
    2. Medium Risk severity finding from [ToB’s Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
67. **Improper Supply Cap Limitation Enforcement**: The _openLoan()_ function does not check if the loan to be issued will result in the supply cap being exceeded. It only enforces that the supply cap is not reached before the loan is opened. As a result, any account can create a loan that exceeds the maximum amount of sETH that can be issued by the _EtherCollateral_ contract.
    
    1. Recommendation: Introduce a require statement in the _openLoan()_ function to prevent the total cap from being exceeded by the loan to be opened.
        
    2. High Risk severity finding from [Sigma Prime's Audit of Synthetix EtherCollateral](https://github.com/sigp/public-audits/blob/master/synthetix/ethercollateral/review.pdf)
        
68. **Improper Storage Management of Open Loan Accounts**: When loans are open, the associated account address gets added to the _accountsWithOpenLoans_ array regardless of whether the account already has a loan/is already included in the array. Additionally, it is possible for a malicious actor to create a denial of service condition exploiting the unbound storage array in _accountsSynthLoans_. 
    
    1. Recommendation: 1) Consider changing the _storeLoan_ function to only push the account to the _accountsWithOpenLoans_ array if the loan to be stored is the first one for that particular account ; 2) Introduce a limit to the number of loans each account can have.
        
    2. High Risk severity finding from [Sigma Prime's Audit of Synthetix EtherCollateral](https://github.com/sigp/public-audits/blob/master/synthetix/ethercollateral/review.pdf)
        
69. **Contract Owner Can Arbitrarily Change Minting Fees and Interest Rates**: The _issueFeeRate_ and _interestRate_ variables can both be changed by the _EtherCollateral_ contract owner after loans have been opened. As a result, the owner can control fees such as they equal/exceed the collateral for any given loan.
    
    1. Recommendation: While "dynamic" interest rates are common, we recommend considering the minting fee ( _issueFeeRate_ ) to be a constant that cannot be changed by the owner.
        
    2. Medium Risk severity finding from [Sigma Prime's Audit of Synthetix EtherCollateral](https://github.com/sigp/public-audits/blob/master/synthetix/ethercollateral/review.pdf)
        
70. **Inadequate Proxy Implementation Preventing Contract Upgrades**: The _TokenImpl_ smart contract requires Owner , name , symbol and decimals of _TokenImpl_ to be set by the _TokenImpl_ constructor. Consider two smart contracts, contract A and contract B . If contract A performs a delegatecall on contract B , the state/storage variables of contract B are not accessible by contract A . Therefore, when _TokenProxy_ targets an implementation of _TokenImpl_ and interacts with it via a _DELEGATECALL_ , it will not be able to access any of the state variables of the _TokenImpl_ contract. Instead, the _TokenProxy_ will access its local storage, which does not contain the variables set in the constructor of the _TokenImpl_ implementation. When the _TokenProxy_ contract is constructed it will only initialize and set two storage slots: • The proxy admin address ( __setAdmin_ internal function) • The token implementation address ( __setImplementation_ private function) Hence when a proxy call to the implementation is made, variables such as _Owner_ will be uninitialised (effectively set to their default value). This is equivalent to the owner being the 0x0 address. Without access to the implementation state variables, the proxy contract is rendered unusable.
    
    1. Recommendation: 1) Set fixed constant parameters as Solidity constants. The solidity compiler replaces all occurrences of a constant in the code and thus does not reserve state for them. Thus if the correct getters exist for the ERC20 interface, the proxy contract doesn’t need to initialise anything. 2) Create a constructor-like function that can only be called once within _TokenImpl_ . This can be used to set the state variables as is currently done in the constructor, however if called by the proxy after deployment, the proxy will set its state variables. 3) Create getter and setter functions that can only be called by the owner . Note that this strategy allows the owner to change various parameters of the contract after deployment. 4) Predetermine the slots used by the required variables and set them in the constructor of the proxy. The storage slots used by a contract are deterministic and can be computed. Hence the variables Owner , name , symbol and decimals can be set directly by their slot in the proxy constructor.
        
    2. Critical Risk severity finding from [Sigma Prime's Audit of InfiniGold](https://github.com/sigp/public-audits/raw/master/infinigold/review.pdf)
        
71. **Blacklisting Bypass via** _**transferFrom()**_ **Function**: The _transferFrom()_ function in the _TokenImpl_ contract does not verify that the sender (i.e. the from address) is not blacklisted. As such, it is possible for a user to allow an account to spend a certain allowance regardless of their blacklisting status.
    
    1. Recommendation: At present the function _transferFrom()_ uses the _notBlacklisted(address)_ modifier twice, on the msg.sender and to addresses. The _notBlacklisted(address)_ modifier should be used a third time against the from address.
        
    2. High Risk severity finding from [Sigma Prime's Audit of InfiniGold](https://github.com/sigp/public-audits/raw/master/infinigold/review.pdf)
        
72. **Wrong Order of Operations Leads to Exponentiation of** _**rewardPerTokenStored**_: _rewardPerTokenStored_ is mistakenly used in the numerator of a fraction instead of being added to the fraction. The result is that _rewardPerTokenStored_ will grow exponentially thereby severely overstating each individual's rewards earned. Individuals will therefore either be able to withdraw more funds than should be allocated to them or they will not be able to withdraw their funds at all as the contract has insufficient SNX balance. This vulnerability makes the Unipool contract unusable.
    
    1. Recommendation: Adjust the function _rewardPerToken()_ to represent the original functionality.
        
    2. Critical Risk severity finding from [Sigma Prime's Audit of Synthetix Unipool](https://github.com/sigp/public-audits/blob/master/synthetix/unipool/review.pdf)
        
73. **Staking Before Initial notifyRewardAmount Can Lead to Disproportionate Rewards**: If a user successfully stakes an amount of UNI tokens before the function _notifyRewardAmount()_ is called for the first time, their initial _userRewardPerTokenPaid_ will be set to zero. The staker would be paid out funds greater than their share of the SNX rewards.
    
    1. Recommendation: We recommend preventing _stake()_ from being called before _notifyRewardAmount()_ is called for the first time.
        
    2. High Risk severity finding from [Sigma Prime's Audit of Synthetix Unipool](https://github.com/sigp/public-audits/blob/master/synthetix/unipool/review.pdf)
        
74. **External Call Reverts if Period Has Not Elapsed**: The function _notifyRewardAmount()_ will revert if _block.timestamp >= periodFinish_. However this function is called indirectly via the _Synthetix.mint()_ function. A revert here would cause the external call to fail and thereby halt the mint process. _Synthetix.mint()_ cannot be successfully called until enough time has elapsed for the period to finish.
    
    1. Recommendation: Consider handling the case where the reward period has not elapsed without reverting the call.
        
    2. High Risk severity finding from [Sigma Prime's Audit of Synthetix Unipool](https://github.com/sigp/public-audits/blob/master/synthetix/unipool/review.pdf)
        
75. **Gap Between Periods Can Lead to Erroneous Rewards**: The SNX rewards are earned each period based on reward and duration as specified in the _notifyRewardAmount()_ function. The contract will output more rewards than it receives. Therefore if all stakers call _getReward()_ the contract will not have enough SNX balance to transfer out all the rewards and some stakers may not receive any rewards.
    
    1. Recommendation: We recommend enforcing each period start exactly at the end of the previous period.
        
    2. Medium Risk severity finding from [Sigma Prime's Audit of Synthetix Unipool](https://github.com/sigp/public-audits/blob/master/synthetix/unipool/review.pdf)
        
76. **Malicious Users Can DOS/Hijack Requests From Chainlinked Contracts**: Malicious users can hijack or perform Denial of Service (DOS) attacks on requests of Chainlinked contracts by replicating or front-running legitimate requests. The Chainlinked (_Chainlinked.sol_) contract contains the _checkChainlinkFulfillment_() modifier. This modifier is demonstrated in the examples that come with the repository. In these examples this modifier is used within the functions which contracts implement that will be called by the Oracle when fulfilling requests. It requires that the caller of the function be the Oracle that corresponds to the request that is being fulfilled. Thus, requests from Chainlinked contracts are expected to only be fulfilled by the Oracle that they have requested. However, because a request can specify an arbitrary callback address, a malicious user can also place a request where the callback address is a target Chainlinked contract. If this malicious request gets fulfilled first (which can ask for incorrect or malicious results), the Oracle will call the legitimate contract and fulfil it with incorrect or malicious results. Because the known requests of a Chainlinked contract gets deleted, the legitimate request will fail. It could be such that the Oracle fulfils requests in the order in which they are received. In such cases, the malicious user could simply front-run the requests to be higher in the queue.
    
    1. Recommendation: This issue arises due to the fact that any request can specify its own arbitrary callback address. A restrictive solution would be where callback addresses are localised to the requester themselves.
        
    2. High Risk severity finding from [Sigma Prime's Audit of Chainlink](https://github.com/sigp/public-audits/blob/master/chainlink-1/review.pdf)
        
77. **Lack of event emission after sensitive actions**: The __getLatestFundingRate_ function of the _FundingRateApplier_ contract does not emit relevant events after executing the sensitive actions of setting the _fundingRate_, _updateTime_ and _proposalTime_, and transferring the rewards.
    
    1. Recommendation: Consider emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following the contract’s activity.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of UMA Phase 4](https://blog.openzeppelin.com/uma-audit-phase-4/)
        
78. **Functions with unexpected side-effects**: Some functions have side-effects. For example, the __getLatestFundingRate_ function of the _FundingRateApplier_ contract might also update the funding rate and send rewards. The _getPrice_ function of the OptimisticOracle contract might also settle a price request. These side-effect actions are not clear in the name of the functions and are thus unexpected, which could lead to mistakes when the code is modified by new developers not experienced in all the implementation details of the project.
    
    1. Recommendation: Consider splitting these functions in separate getters and setters. Alternatively, consider renaming the functions to describe all the actions that they perform.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of UMA Phase 4](https://blog.openzeppelin.com/uma-audit-phase-4/)
        
79. **Mooniswap pairs cannot be unpaused**: The _MooniswapFactoryGovernance_ contract has a shutdown function that can be used to pause the contract and prevent any future swaps. However there is no function to unpause the contract. There is also no way for the factory contract to redeploy a Mooniswap instance for a given pair of tokens. Therefore, if a Mooniswap contract is ever shutdown/paused, it will not be possible for that pair of tokens to ever be traded on the Mooniswap platform again, unless a new factory contract is deployed.
    
    1. Recommendation: Consider providing a way for Mooniswap contracts to be unpaused.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of 1inch Liquidity Protocol Audit](https://blog.openzeppelin.com/1inch-liquidity-protocol-audit/)
        
80. **Attackers can prevent honest users from performing an instant withdraw from the Wallet contract**: An attacker who sees an honest user’s call to _MessageProcessor.instantWithdraw_ in the mempool can grab the _oracleMessage_ and _oracleSignature_ parameters from the user’s transaction, then submit their own transaction to _instantWithdraw_ using the same parameters, a higher gas price (so as to frontrun the honest user’s transaction), and carefully choosing the gas limit for their transactions such that the internal call to the _callInstantWithdraw_ will fail on line 785 with an out-of-gas error, but will successfully execute the _if(!success)_ block. The result is that the attacker’s instant withdraw will fail (so the user will not receive their funds), but the _userInteractionNumber_ will be successfully reserved by the _ReplayTracker_. As a result, the honest user’s transaction will revert because it will be attempting to use a _userInteractionNumber_ that is no longer valid.
    
    1. Recommendation: Consider adding an access control mechanism to restrict who can submit _oracleMessages_ on behalf of the user.
        
    2. High Risk severity finding from [OpenZeppelin’s Audit of Futureswap V2](https://blog.openzeppelin.com/futureswap-v2-audit/)
        
81. **Not using upgrade safe contracts in** _**FsToken**_ **inheritance**: The _FsToken_ contract is intended to be an upgradeable contract, used behind a proxy (namely, the _FsTokenProxy_ contract). However, the contracts _ERC20Snapshot_, _ERC20Mintable_ and _ERC20Burnable_ in the inheritance chain of FsToken are not imported from the upgrade safe library _@openzeppelin/contracts-ethereum-package_ but instead from _@openzeppelin/contracts_.
    
    1. Recommendation: Use the upgrades safe library in this case will ensure the inheritance from Initializable and the other contracts is always linearized as expected by the compiler.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Futureswap V2](https://blog.openzeppelin.com/futureswap-v2-audit/)
        
82. **Unchecked output of the ECDSA recover function**: The _ECDSA.recover_ function (in version 2.5.1) returns address(0) if the signature provided is invalid. This function is used twice in the Futureswap code: Once to recover an _oracleAddress_ from an _oracleSignature_, and again to recover the user’s address from their signature. If the oracle signature was invalid, the _oracleAddress_ is set to address(0). Similarly, if the user’s signature is invalid, then the _userMessage.signer_ or the withDrawer is set to address(0). This can result in unintended behavior.
    
    1. Recommendation: Consider reverting if the output of the _ECDSA.recover_ is ever address(0)
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Futureswap V2](https://blog.openzeppelin.com/futureswap-v2-audit/)
        
83. **Adding new variables to multi-level inherited upgradeable contracts may break storage layout**: The Notional protocol uses the OpenZeppelin/SDK contracts to manage upgradeability in the system, which follows the unstructured storage pattern. When using this upgradeability approach, and when working with multi-level inheritance, if a new variable is introduced in a parent contract, that addition can potentially overwrite the beginning of the storage layout of the child contract, causing critical misbehaviors in the system.
    
    1. Recommendation: consider preventing these scenarios by defining a storage gap in each upgradeable parent contract at the end of all the storage variable definitions as follows: _uint256[50] __gap; // gap to reserve storage in the contract for future variable additions._ In such an implementation, the size of the gap would be intentionally decreased each time a new variable was introduced, thereby avoiding overwriting preexisting storage values.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Notional Protocol](https://blog.openzeppelin.com/notional-audit/)
        
84. **Unsafe division in** _**rdivide**_ **and** _**wdivide**_ **functions**: The function _rdivide_ on line 227 and the function _wdivide_ on line 230 of the _GlobalSettlement_ contract, accept the divisor y as an input parameter. However, these functions do not check if the value of y is 0. If that is the case, the call will revert due to the division by zero error.
    
    1. Recommendation: consider adding a _require_ statement in the functions to ensure _y > 0_, or consider using the _div_ functions provided in OpenZeppelin’s SafeMath libraries
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of GEB Protocol](https://blog.openzeppelin.com/geb-protocol-audit/)
        
85. **Incorrect** _**safeApprove**_ **usage**: The _safeApprove_ function of the OpenZeppelin SafeERC20 library prevents changing an allowance between non-zero values to mitigate a possible front-running attack. Instead, the _safeIncreaseAllowance_ and _safeDecreaseAllowance_ functions should be used. However, the _UniERC20_ library simply bypasses this restriction by first setting the allowance to zero. This reintroduces the front-running attack and undermines the value of the _safeApprove_ function. Consider introducing an _increaseAllowance_ function to handle this case.
    
    1. Recommendation: _safeIncreaseAllowance_ and _safeDecreaseAllowance_ functions should be used
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of 1inch Exchange Audit](https://blog.openzeppelin.com/1inch-exchange-audit/)
        
86. **ETH could get trapped in the protocol**: The Controller contract allows users to send arbitrary actions such as possible flash loans through the __call_ internal function. Among other features, it allows sending ETH with the action to then perform a call to a _CalleeInterface_ type of contract. To do so, it saves the original _msg.value_ sent with the operate function call in the _ethLeft_ variable and it updates the remaining ETH left after each one of those calls to revert in case that it is not enough. Nevertheless, if the user sends more than the necessary ETH for the batch of actions, the remaining ETH (stored in the ethLeft variable after the last iteration) will not be returned to the user and will be locked in the contract due to the lack of a _withdrawEth_ function.
    
    1. Recommendation: Consider either returning all the remaining ETH to the user or creating a function that allows the user to collect the remaining ETH after performing a Call action type, taking into account that sending ETH with a push method may trigger the fallback function on the caller’s address.
        
    2. High Risk severity finding from [OpenZeppelin’s Audit of Opyn Gamma Protocol](https://blog.openzeppelin.com/opyn-gamma-protocol-audit/)
        
87. **Use of transfer might render ETH impossible to withdraw**: When withdrawing ETH deposits, the _PayableProxyController_ contract uses Solidity’s _transfer_ function. This has some notable shortcomings when the withdrawer is a smart contract, which can render ETH deposits impossible to withdraw. Specifically, the withdrawal will inevitably fail when: 1) The withdrawer smart contract does not implement a payable fallback function. 2) The withdrawer smart contract implements a payable fallback function which uses more than 2300 gas units. 3) The withdrawer smart contract implements a payable fallback function which needs less than 2300 gas units but is called through a proxy that raises the call’s gas usage above 2300.
    
    1. Recommendation:  _sendValue_ function available in OpenZeppelin Contract’s Address library can be used to transfer the withdrawn Ether without being limited to 2300 gas units. Risks of reentrancy stemming from the use of this function can be mitigated by tightly following the “Check-effects-interactions” pattern and using OpenZeppelin Contract’s _ReentrancyGuard_ contract.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Opyn Gamma Protocol](https://blog.openzeppelin.com/opyn-gamma-protocol-audit/)
        
88. **Not following the Checks-Effects-Interactions pattern**: The _finalizeGrant_ function of the Fund contract is setting the _grant.complete_ storage variable after a token transfer. Solidity recommends the usage of the Check-Effects-Interaction Pattern to avoid potential security issues, such as reentrancy. The _finalizeGrant_ function can be used to conduct a reentrancy attack, where the token transfer in line 129 can call back again the same function, sending to the admin multiple times an amount of fee, before setting the grant as completed. In this way the grant.recipient can receive less than expected and the contract funds can be drained unexpectedly leading to an unwanted loss of funds.
    
    1. Recommendation: Consider always following the “Check-Effects-Interactions” pattern, thus modifying the contract’s state before making any external call to other contracts.
        
    2. High Risk severity finding from [OpenZeppelin’s Audit of Endaoment](https://blog.openzeppelin.com/endaoment-audit/)
        
89. **Updating the Governance registry and Guardian addresses emits no events**: In the Governance contract the _registryAddress_ and the _guardianAddress_ are highly sensitive accounts. The first one holds the contracts that can be proposal targets, and the second one is a superuser account that can execute proposals without voting. These variables can be updated by calling _setRegistryAddress_ and _transferGuardianship_, respectively. Note that these two functions update these sensitive addresses without logging any events. Stakers who monitor the Audius system would have to inspect all transactions to notice that one address they trust is replaced with an untrusted one.
    
    1. Recommendation: Consider emitting events when these addresses are updated. This will be more transparent, and it will make it easier for clients to subscribe to the events when they want to keep track of the status of the system.
        
    2. High Risk severity finding from [OpenZeppelin’s Audit of Audius](https://blog.openzeppelin.com/audius-contracts-audit/#high)
        
90. **The quorum requirement can be trivially bypassed with sybil accounts**: While the final vote on a proposal is determined via a token-weighted vote, the quorum check in the _evaluateProposalOutcome_ function can be trivially bypassed by splitting one’s tokens over multiple accounts and voting with each of the accounts. Each of these sybil votes increases the _proposals[_proposalId].numVotes_ variable. This means anyone can make the quorum check pass.
    
    1. Recommendation: Consider measuring quorum size by the percentage of existing tokens that have voted, rather than the number of unique accounts that have voted.
        
    2. High Risk severity finding from [OpenZeppelin’s Audit of Audius](https://blog.openzeppelin.com/audius-contracts-audit/#high)
        
91. **Inconsistently checking initialization**: When a contract is initialized, its _isInitialized_ state variable is set to true. Since interacting with uninitialized contracts would cause problems, the __requireIsInitialized_ function is available to make this check. However, this check is not used consistently. For example, it is used in the _getVotingQuorum_ function of the Governance contract, but it is not used in the _getRegistryAddress_ function of the same contract. There is no obvious difference between the functions to explain this difference, and it could be misleading and cause uninitialized contracts to be called.
    
    1. Recommendation: Consider calling __requireIsInitialized_ consistently in all the functions of the _InitializableV2_ contracts. If there is a reason to not call it in some functions, consider documenting it. Alternatively, consider removing this check altogether and preparing a good deployment script that will ensure that all contracts are initialized in the same transaction that they are deployed. In this alternative, it would be required to check that contracts resulting from new proposals are also initialized before they are put in production.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Audius](https://blog.openzeppelin.com/audius-contracts-audit/#medium)
        
92. **Voting period and quorum can be set to zero**: When the Governance contract is initialized, the values of _votingPeriod_ and _votingQuorum_ are checked to make sure that they are greater than 0. However, the corresponding setter functions _setVotingPeriod_ and setVotingQuorum allow these variables to be reset to 0. Setting the _votingPeriod_ to zero would cause spurious proposals that cannot be voted. Setting the quorum to zero is worse because it would allow proposals with 0 votes to be executed.
    
    1. Recommendation: Consider adding the validation to the setter functions
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Audius](https://blog.openzeppelin.com/audius-contracts-audit/#medium)
        
93. **Some state variables are not set during initialize**: The Audius contracts can be upgraded using the unstructured storage proxy pattern. This pattern requires the use of an initializer instead of the constructor to set the initial values of the state variables. In some of the contracts, the initializer is not initializing all of the state variables.
    
    1. Recommendation: Consider setting all the required variables in the initializer. If there is a reason for leaving them uninitialized, consider documenting it, and adding checks on the functions that use those variables to ensure that they are not called before initialization.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Audius](https://blog.openzeppelin.com/audius-contracts-audit/#medium)
        
94. **Expired and/or paused options can still be traded**: Option tokens can still be freely transferred when the Option contract is either paused or expired (or both). This would allow malicious option holders to sell paused / expired options that cannot be exercised in the open market to exchanges and users who do not take the necessary precautions before buying an option minted by the Primitive protocol.
    
    1. Recommendation: Should this be the system’s expected behavior, consider clearly documenting it in user-friendly documentation so as to raise awareness in option sellers and buyers. Alternatively, if the described behavior is not intended, consider implementing the necessary logic in the Option contract to prevent transfers of tokens during pause and after expiration.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Primitive](https://blog.openzeppelin.com/primitive-audit/)
        
95. **ERC20 transfers can misbehave**: The __transferFromERC20_ function is used throughout _ACOToken.sol_ to handle transferring funds into the contract from a user. It is called within mint, within _mintTo_, and within __validateAndBurn_. In each case, the destination is the ACOToken contract. Such transfers may behave unexpectedly if the token contract charges fees. As an example, the popular USDT token does not presently charge any fees upon transfer, but it has the potential to do so. In this case the amount received would be less than the amount sent. Such tokens have the potential to lead to protocol insolvency when they are used to mint new ACOTokens. In the case of __transferERC20_, similar issues can occur, and could cause users to receive less than expected when collateral is transferred or when exercise assets are transferred.
    
    1. Recommendation: Consider thoroughly vetting each token used within an ACO options pair, ensuring that failing _transferFrom_ and _transfer_ calls will cause reverts within _ACOToken.sol_. Additionally, consider implementing some sort of sanity check which enforces that the balance of the ACOToken contract increases by the desired amount when calling __transferFromERC20_. 
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of ACO Protocol](https://blog.openzeppelin.com/aco-protocol-audit/)
        
96. **Incorrect event emission**: The _UniswapWindowUpdate_ event of the _UniswapAnchoredView_ contract is currently being emitted in the _pokeWindowValues_ function using incorrect values. In particular, as it is being emitted before relevant state changes are applied to the _oldObservation_ and _newObservation_ variables, the data logged by the event will be outdated.
    
    1. Recommendation: Consider emitting the _UniswapWindowUpdate_ event after changes are applied so that all logged data is up-to-date.
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of Compound Open Price Feed – Uniswap Integration](https://blog.openzeppelin.com/compound-open-price-feed-uniswap-integration-audit/)
        
97. **Anyone can liquidate on behalf of another account**: The Perpetual contract has a public liquidateFrom function that bypasses the checks in the liquidate function. This means that it can be called to liquidate a position when the contract is in the _SETTLED_ state. Additionally, any user can set an arbitrary from address, causing a third-party user to confiscate the under-collateralized trader’s position. This means that any trader can unilaterally rearrange another account’s position. They could also liquidate on behalf of the Perpetual Proxy, which could break some of the Automated Market Maker invariants, such as the condition that it only holds LONG positions.
    
    1. Recommendation: Consider restricting _liquidateFrom_ to internal visibility
        
    2. Critical Risk severity finding from [OpenZeppelin’s Audit of MCDEX Mai Protocol](https://blog.openzeppelin.com/mcdex-mai-protocol-audit/)
        
98. **Orders cannot be cancelled**: When a user or broker calls _cancelOrder_, the cancelled mapping is updated, but this has no subsequent effects. In particular, _validateOrderParam_ does not check if the order has been cancelled.
    
    1. Recommendation: Consider adding this check to the order validation to ensure cancelled orders cannot be filled.
        
    2. Critical Risk severity finding from [OpenZeppelin’s Audit of MCDEX Mai Protocol](https://blog.openzeppelin.com/mcdex-mai-protocol-audit/)
        
99. **Re-entrancy possibilities**: There are several examples of interactions preceding effects: 1) In the deposit function of the Collateral contract, collateral is retrieved before the user balance is updated and an event is emitted. 2) In the __withdraw_ function of the Collateral contract, collateral is sent before the event is emitted 3) The same pattern occurs in the _depositToInsuranceFund_, _depositEtherToInsuranceFund_ and _withdrawFromInsuranceFund_ functions of the Perpetual contract. It should be noted that even when a correctly implemented ERC20 contract is used for collateral, incoming and outgoing transfers could execute arbitrary code if the contract is also ERC777 compliant. These re-entrancy opportunities are unlikely to corrupt the internal state of the system, but they would affect the order and contents of emitted events, which could confuse external clients about the state of the system. 
    
    1. Recommendation: Consider always following the “Check-Effects-Interactions” pattern or use _ReentrancyGuard_ contract is now used to protect those functions
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of MCDEX Mai Protocol](https://blog.openzeppelin.com/mcdex-mai-protocol-audit/)
        
100. **Governance parameter changes should not be instant**: Many sensitive changes can be made by any account with the _WhitelistAdmin_ role via the functions _setGovernanceParameter_ within the _AMMGovernance_ and _PerpetualGovernance_ contracts. For example, the _WhitelistAdmin_ can change the fee schedule, the initial and maintenance margin rates, or the lot size parameters, and these new parameters instantly take effect in the protocol with important effects. For example, raising the maintenance margin rate could cause _isSafe_ to return False when it would have previously returned True. This would allow the user’s position to be liquidated. By changing _tradingLotSize_, trades may revert when being matched, where they would not have before the change. These are only examples; the complexity of the protocol, combined with unpredictable market conditions and user actions means that many other negative effects likely exist as well.
    
    1. Recommendation: Since these changes are occasionally needed, but can create risk for the users of the protocol, consider implementing a time-lock mechanism for such changes to take place. By having a delay between the signal of intent and the actual change, users will have time to remove their funds or close trades that would otherwise be at risk if the change happened instantly. 
        
    2. Medium Risk severity finding from [OpenZeppelin’s Audit of MCDEX Mai Protocol](https://blog.openzeppelin.com/mcdex-mai-protocol-audit/)
        
101. **Votes can be duplicated**: The Data Verification Mechanism uses a commit-reveal scheme to hide votes during the voting period. The intention is to prevent voters from simply voting with the majority. However, the current design allows voters to blindly copy each other’s submissions, which undermines this goal. In particular, each commitment is a masked hash of the claimed price, but is not cryptographically tied to the voter. This means that anyone can copy the commitment of a target voter (for instance, someone with a large balance) and submit it as their own. When the target voter reveals their salt and price, the copycat can “reveal” the same values. Moreover, if another voter recognizes this has occurred during the commitment phase, they can also change their commitment to the same value, which may become an alternate Schelling point.
    
    1. Recommendation: Consider including the voter address within the commitment to prevent votes from being duplicated. Additionally, as a matter of good practice, consider including the relevant timestamp, price identifier and round ID as well to limit the applicability (and reusability) of a commitment.
        
    2. High Risk severity finding from [OpenZeppelin’s Audit of UMA Phase 1](https://blog.openzeppelin.com/uma-audit-phase-1/)