102. **Document potential edge cases for hook receiver contracts**: The functions _withdrawTokenAndCall_() and _withdrawTokenAndCallOnBehalf_() make a call to a hook contract designated by the owner of the withdrawing stealth address. There are very few constraints on the parameters to these calls in the Umbra contract itself. Anyone can force a call to a hook contract by transferring a small amount of tokens to an address that they control and withdrawing these tokens, passing the target address as the hook receiver. 
    
    1. Recommendation: Developers of these _UmbraHookReceiver_ contracts should be sure to validate both the caller of the _tokensWithdrawn_() function and the function parameters.
        
    2. [ConsenSys's Audit of Umbra](https://consensys.net/diligence/audits/2021/03/umbra-smart-contracts/#document-potential-edge-cases-for-hook-receiver-contracts)
        
103. **Document token behavior restrictions**: As with any protocol that interacts with arbitrary ERC20 tokens, it is important to clearly document which tokens are supported. Often this is best done by providing a specification for the behavior of the expected ERC20 tokens and only relaxing this specification after careful review of a particular class of tokens and their interactions with the protocol. 
    
    1. Recommendation: Known deviations from “normal” ERC20 behavior should be explicitly noted as NOT supported by the Umbra Protocol: 1) Deflationary or fee-on-transfer tokens: These are tokens in which the balance of the recipient of a transfer may not be increased by the amount of the transfer. There may also be some alternative mechanism by which balances are unexpectedly decreased. While these tokens can be successfully sent via the _sendToken()_ function, the internal accounting of the Umbra contract will be out of sync with the balance as recorded in the token contract, resulting in loss of funds. 2) Inflationary tokens: The opposite of deflationary tokens. The Umbra contract provides no mechanism for claiming positive balance adjustments. 3) Rebasing tokens: A combination of the above cases, these are tokens in which an account’s balance increases or decreases along with expansions or contractions in supply. The contract provides no mechanism to update its internal accounting in response to these unexpected balance adjustments, and funds may be lost as a result.
        
    2. [ConsenSys's Audit of Umbra](https://consensys.net/diligence/audits/2021/03/umbra-smart-contracts/#document-token-behavior-restrictions)
        
104. **Full test suite is recommended**: The test suite at this stage is not complete and many of the tests fail to execute. For complicated systems such as DeFi Saver, which uses many different modules and interacts with different DeFi protocols, it is crucial to have a full test coverage that includes the edge cases and failed scenarios. Especially this helps with safer future development and upgrading each module. As we’ve seen in some smart contract incidents, a complete test suite can prevent issues that might be hard to find with manual reviews.
    
    1. Recommendation: Add a full coverage test suite.
        
    2. [ConsenSys's Audit of DeFi Saver](https://consensys.net/diligence/audits/2021/03/defi-saver/#full-test-suite-is-recommended)
        
105. **Kyber getRates code is unclear**: Function names don’t reflect their true functionalities, and the code uses some undocumented assumptions.
    
    1. Recommendation: Refactor the code to separate getting rate functionality with getSellRate and getBuyRate. Explicitly document any assumptions in the code ( slippage, etc).
        
    2. [ConsenSys's Audit of DeFi Saver](https://consensys.net/diligence/audits/2021/03/defi-saver/#kyber-getrates-code-is-unclear)
        
106. **Return value is not used for** _**TokenUtils.withdrawTokens**_: The return value of _TokenUtils.withdrawTokens_ which represents the actual amount of tokens that were transferred is never used throughout the repository. This might cause discrepancy in the case where the original value of __amount_ was _type(uint256).max_.
    
    1. Recommendation: The return value can be used to validate the withdrawal or used in the event emitted
        
    2. [ConsenSys's Audit of DeFi Saver](https://consensys.net/diligence/audits/2021/03/defi-saver/#return-value-is-not-used-for-tokenutils-withdrawtokens)
        
107. **Missing access control for** _**DefiSaverLogger.Log**_: _DefiSaverLogger_ is used as a logging aggregator within the entire dapp, but anyone can create logs.
    
    1. Recommendation: Add access control to all functions appropriately
        
    2. [Consensys Audit of DeFi Saver](https://consensys.net/diligence/audits/2021/03/defi-saver/#missing-access-control-for-defisaverlogger-log)
        
108. **Remove stale comments**: Remove inline comments that suggest the two uint256 values _DAOfiV1Pair.reserveBase_ and _DAOfiV1Pair.reserveQuote_ are stored in the same storage slot. This is likely a carryover from the _UniswapV2Pair_ contract, in which _reserve0_, _reserve1_, and _blockTimestampLast_ are packed into a single storage slot.
    
    1. Recommendation: Remove stale comments
        
    2. [ConsenSys's Audit of DAOfi](https://consensys.net/diligence/audits/2021/02/daofi/#remove-stale-comments)
        
109. **Discrepancy between code and comments**: There is a mismatch between what the code implements and what the corresponding comment describes that code implements.
    
    1. Recommendation: Update the code or the comment to be consistent
        
    2. [ConsenSys's Audit of mstable-1.1](https://consensys.net/diligence/audits/2020/07/mstable-1.1/#discrepancy-between-code-and-comments)
        
110. **Remove unnecessary call to** _**DAOfiV1Factory.formula**_**()**: The _DAOfiV1Pair_ functions _initialize_(), _getBaseOut_(), and _getQuoteOut_() all use the private function __getFormula_(), which makes a call to the factory to retrieve the address of the _BancorFormula_ contract. The formula address in the factory is set in the constructor and cannot be changed, so these calls can be replaced with an immutable value in the pair contract that is set in its constructor.
    
    1. Recommendation: Remove unnecessary calls
        
    2. [ConsenSys's Audit of DAOfi](https://consensys.net/diligence/audits/2021/02/daofi/#remove-unnecessary-call-to-daofiv1factory-formula) 
        
111. **Deeper validation of curve math**: Increased testing of edge cases in complex mathematical operations could have identified at least one issue raised in this report. Additional unit tests are recommended, as well as fuzzing or property-based testing of curve-related operations. Improperly validated interactions with the _BancorFormula_ contract are seen to fail in unanticipated and potentially dangerous ways, so care should be taken to validate inputs and prevent pathological curve parameters.
    
    1. Recommendation: More validation of mathematical operations
        
    2. [ConsenSys's Audit of DAOfi](https://consensys.net/diligence/audits/2021/02/daofi/#deeper-validation-of-curve-math) 
        
112. _**GovernorAlpha**_ **proposals may be canceled by the proposer, even after they have been accepted and queued**: _GovernorAlpha_ allows proposals to be canceled via cancel. A proposer may cancel proposals in any of these states: Pending, Active, Canceled, Defeated, Succeeded, Queued, Expired. 
    
    1. Recommendation: Prevent proposals from being canceled unless they are in the Pending or Active states.
        
    2. [ConsenSys's Audit of Fei Protocol](https://consensys.net/diligence/audits/2021/01/fei-protocol/#governoralpha-proposals-may-be-canceled-by-the-proposer-even-after-they-have-been-accepted-and-queued) 
        
113. **Require a delay period before granting** _**KYC_ADMIN_ROLE**_  **Acknowledged**: The KYC Admin has the ability to freeze the funds of any user at any time by revoking the _KYC_MEMBER_ROLE_. The trust requirements from users can be decreased slightly by implementing a delay on granting this ability to new addresses. While the management of private keys and admin access is outside the scope of this review, the addition of a time delay can also help protect the development team and the system itself in the event of private key compromise.
    
    1. Recommendation: Use a _TimelockController_ as the _KYC_DEFAULT_ADMIN_ of the eRLC contract
        
    2. [ConsenSys's Audit of eRLC](https://consensys.net/diligence/audits/2021/01/erlc-iexec/#erlc-require-a-delay-period-before-granting-kyc-admin-role)
        
114. **Improve inline documentation and test coverage**: The source-units hardly contain any inline documentation which makes it hard to reason about methods and how they are supposed to be used. Additionally, test-coverage seems to be limited. Especially for a public-facing exchange contract system test-coverage should be extensive, covering all methods and functions that can directly be accessed including potential security-relevant and edge-cases. This would have helped in detecting some of the findings raised with this report.
    
    1. Recommendation: Consider adding natspec-format compliant inline code documentation, describe functions, what they are used for, and who is supposed to interact with them. Document function or source-unit specific assumptions. Increase test coverage.
        
    2. [ConsenSys's Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#improve-inline-documentation-and-test-coverage)
        
115. **Unspecific compiler version pragma**: For most source-units the compiler version pragma is very unspecific _^0.6.0_. While this often makes sense for libraries to allow them to be included with multiple different versions of an application, it may be a security risk for the actual application implementation itself. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up actually checking a different evm compilation that is ultimately deployed on the blockchain.
    
    1. Recommendation: Avoid floating pragmas. We highly recommend pinning a concrete compiler version (latest without security issues) in at least the top-level “deployed” contracts to make it unambiguous which compiler version is being used. Rule of thumb: a flattened source-unit should have at least one non-floating concrete solidity compiler version pragma.
        
    2. [ConsenSys's Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)
        
116. **Use of hardcoded gas limits can be problematic**: Hardcoded gas limits can be problematic as the past has shown that gas economics in ethereum have changed, and may change again potentially rendering the contract system unusable in the future.
    
    1. Recommendation: Be conscious about this potential limitation and prepare for the case where gas prices might change in a way that negatively affects the contract system.
        
    2. [ConsenSys's Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#use-of-hardcoded-gas-limits-can-be-problematic)
        
117. **Anyone can steal all the funds that belong to** _**ReferralFeeReceiver**_: The _ReferralFeeReceiver_ receives pool shares when users _swap()_ tokens in the pool. A _ReferralFeeReceiver_ may be used with multiple pools and, therefore, be a lucrative target as it is holding pool shares. Any token or ETH that belongs to the _ReferralFeeReceiver_ is at risk and can be drained by any user by providing a custom mooniswap pool contract that references existing token holdings. It should be noted that none of the functions in _ReferralFeeReceiver_ verify that the user-provided mooniswap pool address was actually deployed by the linked MooniswapFactory.
    
    1. Recommendation: Enforce that the user-provided mooniswap contract was actually deployed by the linked factory. Other contracts cannot be trusted. Consider implementing token sorting and de-duplication (_tokenA!=tokenB_) in the pool contract constructor as well. Consider employing a reentrancy guard to safeguard the contract from reentrancy attacks. Improve testing. The methods mentioned here are not covered at all. Improve documentation and provide a specification that outlines how this contract is supposed to be used.
        
    2. Critical finding in [ConsenSys's Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#out-of-scope-referralfeereceiver-anyone-can-steal-all-the-funds-that-belong-to-referralfeereceiver)
        
118. **Unpredictable behavior for users due to admin front running or general bad timing**: In a number of cases, administrators of contracts can update or upgrade things in the system without warning. This has the potential to violate a security goal of the system. Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes. In general users of the system should have assurances about the behavior of the action they’re about to take.
    
    1. Recommendation: We recommend giving the user advance notice of changes with a time lock. For example, make all system-parameter and upgrades require two steps with a mandatory time window between them. The first step merely broadcasts to users that a particular change is coming, and the second step commits that change after a suitable waiting period. This allows users that do not accept the change to withdraw immediately.
        
    2. [ConsenSys's Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unpredictable-behavior-for-users-due-to-admin-front-running-or-general-bad-timing)
        
119. **Improve system documentation and create a complete technical specification**: A system’s design specification and supporting documentation should be almost as important as the system’s implementation itself. Users rely on high-level documentation to understand the big picture of how a system works. Without spending time and effort to create palatable documentation, a user’s only resource is the code itself, something the vast majority of users cannot understand. Security assessments depend on a complete technical specification to understand the specifics of how a system works. When a behavior is not specified (or is specified incorrectly), security assessments must base their knowledge in assumptions, leading to less effective review. Maintaining and updating code relies on supporting documentation to know why the system is implemented in a specific way. If code maintainers cannot reference documentation, they must rely on memory or assistance to make high-quality changes. Currently, the only documentation for Growth DeFi is a single README file, as well as code comments.
    
    1. Recommendation: Improve system documentation and create a complete technical specification
        
    2. [ConsenSys's Audit of Growth DeFi](https://consensys.net/diligence/audits/2020/12/growth-defi-v1/#improve-system-documentation-and-create-a-complete-technical-specification)
        
120. **Ensure system states, roles, and permissions are sufficiently restrictive**: Smart contract code should strive to be strict. Strict code behaves predictably, is easier to maintain, and increases a system’s ability to handle nonideal conditions. Our assessment of Growth DeFi found that many of its states, roles, and permissions are loosely defined.
    
    1. Recommendation: Document the use of administrator permissions. Monitor the usage of administrator permissions. Specify strict operation requirements for each contract.
        
    2. [ConsenSys's Audit of Growth DeFi](https://consensys.net/diligence/audits/2020/12/growth-defi-v1/#ensure-system-states-roles-and-permissions-are-sufficiently-restrictive)
        
121. **Evaluate all tokens prior to inclusion in the system**: Review current and future tokens in the system for non-standard behavior. Particularly dangerous functionality to look for includes a callback (ie. ERC777) which would enable an attacker to execute potentially arbitrary code during the transaction, fees on transfers, or inflationary/deflationary tokens.
    
    1. Recommendation: Evaluate all tokens prior to inclusion in the system
        
    2. [ConsenSys's Audit of Growth DeFi](https://consensys.net/diligence/audits/2020/12/growth-defi-v1/#evaluate-all-tokens-prior-to-inclusion-in-the-system)
        
122. **Use descriptive names for contracts and libraries**: The code base makes use of many different contracts, abstract contracts, interfaces, and libraries for inheritance and code reuse. In principle, this can be a good practice to avoid repeated use of similar code. However, with no descriptive naming conventions to signal which files would contain meaningful logic, codebase becomes difficult to navigate.
    
    1. Recommendation: Use descriptive names for contracts and libraries
        
    2. [ConsenSys's Audit of Growth DeFi](https://consensys.net/diligence/audits/2020/12/growth-defi-v1/#use-descriptive-names-for-contracts-and-libraries)
        
123. **Prevent contracts from being used before they are entirely initialized**: Many contracts allow users to deposit / withdraw assets before the contracts are entirely initialized, or while they are in a semi-configured state. Because these contracts allow interaction on semi-configured states, the number of configurations possible when interacting with the system makes it incredibly difficult to determine whether the contracts behave as expected in every scenario, or even what behavior is expected in the first place.
    
    1. Recommendation: Prevent contracts from being used before they are entirely initialized
        
    2. [ConsenSys's Audit of Growth DeFi](https://consensys.net/diligence/audits/2020/12/growth-defi-v1/#prevent-contracts-from-being-used-before-they-are-entirely-initialized)
        
124. **Potential resource exhaustion by external calls performed within an unbounded loop**: _DydxFlashLoanAbstraction_.__requestFlashLoan_ performs external calls in a potentially-unbounded loop. Depending on changes made to DyDx’s _SoloMargin_, this may render this flash loan provider prohibitively expensive. In the worst case, changes to _SoloMargin_ could make it impossible to execute this code due to the block gas limit.
    
    1. Recommendation: Reconsider or bound the loop
        
    2. [ConsenSys's Audit of Growth DeFi](https://consensys.net/diligence/audits/2020/12/growth-defi-v1/#potential-resource-exhaustion-by-external-calls-performed-within-an-unbounded-loop)
        
125. **Owners can never be removed**: The intention of _setOwners_() is to replace the current set of owners with a new set of owners. However, the _isOwner_ mapping is never updated, which means any address that was ever considered an owner is permanently considered an owner for purposes of signing transactions.
    
    1. Recommendation: In _setOwners__(), before adding new owners, loop through the current set of owners and clear their _isOwner_ booleans
        
    2. Critical finding in [ConsenSys's Audit of Paxos](https://consensys.net/diligence/audits/2020/11/paxos/#owners-can-never-be-removed)
        
126. **Potential manipulation of stable interest rates using flash loans**: Flash loans allow users to borrow large amounts of liquidity from the protocol. It is possible to adjust the stable rate up or down by momentarily removing or adding large amounts of liquidity to reserves. 
    
    1. Recommendation: This type of manipulation is difficult to prevent especially when flash loans are available. Aave should monitor the protocol at all times to make sure that interest rates are being rebalanced to sane values.
        
    2. [ConsenSys's Audit of Aave Protocol V2](https://consensys.net/diligence/audits/2020/09/aave-protocol-v2/#potential-manipulation-of-stable-interest-rates-using-flash-loans)
        
127. **Only whitelist validated assets**: Because some of the functionality relies on correct token behavior, any whitelisted token should be audited in the context of this system. Problems can arise if a malicious token is whitelisted because it can block people from voting with that specific token or gain unfair advantage if the balance can be manipulated.
    
    1. Recommendation: Make sure to audit any new whitelisted asset.
        
    2. [ConsenSys's Audit of Aave Governance DAO](https://consensys.net/diligence/audits/2020/08/aave-governance-dao/#only-whitelist-validated-assets)
        
128. **Underflow if** _**TOKEN_DECIMALS**_ **are greater than 18**: In _latestAnswer_(), the assumption is made that _TOKEN_DECIMALS_ is less than 18.
    
    1. Recommendation: Add a simple check to the constructor to ensure the added token has 18 decimals or less
        
    2. [ConsenSys's Audit of Aave CPM Price Provider](https://consensys.net/diligence/audits/2020/05/aave-cpm-price-provider/#underflow-if-token-decimals-are-greater-than-18)
        
129. **Chainlink’s performance at times of price volatility**: In order to understand the risk of the Chainlink oracle deviating significantly, it would be helpful to compare historical prices on Chainlink when prices are moving rapidly, and see what the largest historical delta is compared to the live price on a large exchange.
    
    1. Recommendation: Review Chainlink’s performance at times of price volatility
        
    2. [ConsenSys's Audit of Aave CPM Price Provider](https://consensys.net/diligence/audits/2020/05/aave-cpm-price-provider/#review-chainlink-s-performance-at-times-of-price-volatility) 
        
130. **Consider an iterative approach to launching. Be aware of and prepare for worst-case scenarios**: The system has many components with complex functionality and no apparent upgrade path. 
    
    1. Recommendation: We recommend identifying which components are crucial for a minimum viable system, then focusing efforts on ensuring the security of those components first, and then moving on to the others. During the early life of the system, have a method for pausing and upgrading the system. 
        
    2. [ConsenSys's Audit of Lien Protocol](https://consensys.net/diligence/audits/2020/05/lien-protocol/#consider-an-iterative-approach-to-launching)
        
131. **Use of modifiers for repeated checks**: It is recommended to use modifiers for common checks within different functions. This will result in less code duplication in the given smart contract and adds significant readability into the code base.
    
    1. Recommendation: Use of modifiers for repeated checks
        
    2. [ConsenSys's Audit of Balancer Finance](https://consensys.net/diligence/audits/2020/05/balancer-finance/#use-of-modifiers-for-repeated-checks)
        
132. **Switch modifier order**: _BPool_ functions often use modifiers in the following order: __logs__, __lock__. Because __lock__ is a reentrancy guard, it should take precedence over __logs_._
    
    1. Recommendation: Place __lock__ before other modifiers; ensuring it is the very first and very last thing to run when a function is called.
        
    2. [ConsenSys's Audit of Balancer Finance](https://consensys.net/diligence/audits/2020/05/balancer-finance/#switch-modifier-order-in-bpool)
        
133. **Address codebase fragility**: Software is considered “fragile” when issues or changes in one part of the system can have side-effects in conceptually unrelated parts of the codebase. Fragile software tends to break easily and may be challenging to maintain.
    
    1. Recommendation: Building an anti-fragile system requires careful thought and consideration outside of the scope of this review. In general, prioritize the following concepts: 1) Follow the single-responsibility principle of functions 2) Reduce reliance on external systems
        
    2. [ConsenSys's Audit of MCDEX Mai Protocol V2](https://consensys.net/diligence/audits/2020/05/mcdex-mai-protocol-v2/#address-codebase-fragility)
        
134. **Reentrancy could lead to incorrect order of emitted events**: The order of operations in the __moveTokensAndETHfromAdjustment_ function in the _BorrowOperations_ contract may allow an attacker to cause events to be emitted out of order. In the event that the borrower is a contract, this could trigger a callback into _BorrowerOperations_, executing the _ _adjustTrove_ flow above again. As the __moveTokensAndETHfromAdjustment_ call is the final operation in the function the state of the system on-chain cannot be manipulated. However, there are events that are emitted after this call. In the event of a reentrant call, these events would be emitted in the incorrect order. The event for the second operation i s emitted first, followed by the event for the first operation. Any off-chain monitoring tools may now have an inconsistent view of on-chain state.
    
    1. Recommendation: Apply the checks-effects-interactions pattern and move the event emissions above the call to _ _moveTokensAndETHfromAdjustment_ to avoid the potential reentrancy.
        
    2. [ToB's Audit of Liquidity](https://github.com/trailofbits/publications/blob/master/reviews/Liquity.pdf)
        
135. **Variable shadowing from OUSD to ERC20**: OUSD inherits from ERC20, but redefines the _ _allowances_ and _ _totalSupply_ state variables. As a result, access to these variables can lead to returning different values.
    
    1. Recommendation: Remove the shadowed variables (_ _allowances_ and _ _totalSupply_) in OUSD.
        
    2. [ToB's Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
136. **VaultCore.rebase functions have no return statements**: _VaultCore.rebase()_ and _VaultCore.rebase(bool)_ return a _uint_ but lack a return statement. As a result these functions will always return the default value, and are likely to cause issues for their callers. Both _VaultCore.rebase()_ and _VaultCore.rebase(bool)_ are expected to return a _uint256_. _rebase()_ does not have a return statement. _rebase(bool)_ has one return statement in one branch (return 0), but lacks a return statement for the other paths. So both functions will always return zero. As a result, a third-party code relying on the return value might not work as intended.
    
    1. Recommendation: Add the missing return statement(s) or remove the return type in _VaultCore.rebase()_ and _VaultCore.rebase(bool)_. Properly adjust the documentation as necessary.
        
    2. [ToB's Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
137. **Multiple contracts are missing inheritances**: Multiple contracts are the implementation of their interfaces, but do not inherit from them. This behavior is error-prone and might lead the implementation to not follow the interface if the code is updated.
    
    1. Recommendation: Ensure contracts inherit from their interfaces
        
    2. [ToB's Audit of Origin Dollar](https://github.com/trailofbits/publications/blob/master/reviews/OriginDollar.pdf)
        
138. **Solidity compiler optimizations can be dangerous**: Yield Protocol has enabled optional compiler optimizations in Solidity. There have been several bugs with security implications related to optimizations. Moreover, optimizations are actively being developed . Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6 .
    
    1. Recommendation: Short term, measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.
        
    2. [ToB's Audit of Yield Protocol](https://github.com/trailofbits/publications/blob/master/reviews/YieldProtocol.pdf)
        
139. **Permission-granting is too simplistic and not flexible enough**: The Yield Protocol contracts implement an oversimplified permission system that can be abused by the administrator. The Yield Protocol implements several contracts that need to call privileged functions from each other. However, there is no way to specify which operation can be called for every privileged user. All the authorized addresses can call any restricted function, and the owner can add any number of them. Also, the privileged addresses are supposed to be smart contracts; however, there is no check for that. Moreover, once an address is added, it cannot be deleted.
    
    1. Recommendation: Rewrite the authorization system to allow only certain addresses to access certain functions
        
    2. [ToB's Audit of Yield Protocol](https://github.com/trailofbits/publications/blob/master/reviews/YieldProtocol.pdf) 
        
140. **Lack of validation when setting the maturity value:** When a _fyDAI_ contract is deployed, one of the deployment parameters is a maturity date, passed as a Unix timestamp. This is the date at which point _fyDAI_ tokens can be redeemed for the underlying Dai. Currently, the contract constructor performs no validation on this timestamp to ensure it is within an acceptable range. As a result, it is possible to mistakenly deploy a _YDai_ contract that has a maturity date in the past or many years in the future, which may not be immediately noticed.
    
    1. Recommendation: Short term, add checks to the _YDai_ contract constructor to ensure maturity timestamps fall within an acceptable range. This will prevent maturity dates from being mistakenly set in the past or too far in the future. Long term, always perform validation of parameters passed to contract constructors. This will help detect and prevent errors during deployment.
        
    2. [ToB's Audit of Yield Protocol](https://github.com/trailofbits/publications/blob/master/reviews/YieldProtocol.pdf) 
        
141. **Delegates can be added or removed repeatedly to bloat logs**: Several contracts in the Yield Protocol system inherit the Delegable contract. This contract allows users to delegate the ability to perform certain operations on their behalf to other addresses. When a user adds or removes a delegate, a corresponding event is emitted to log this operation. However, there is no check to prevent a user from repeatedly adding or removing a delegation that is already enabled or revoked, which could allow redundant events to be emitted repeatedly.
    
    1. Recommendation: Short term, add a _require_ statement to check that the delegate address is not already enabled or disabled for the user. This will ensure log messages are only emitted when a delegate is activated or deactivated. Long term, review all operations and avoid emitting events in repeated calls to idempotent operations. This will help prevent bloated logs.
        
    2. [ToB's Audit of Yield Protocol](https://github.com/trailofbits/publications/blob/master/reviews/YieldProtocol.pdf)
        
142. **Lack of events for critical operations**: Several critical operations do not trigger events, which will make it difficult to review the correct behavior of the contracts once deployed. Users and blockchain monitoring systems will not be able to easily detect suspicious behaviors without events.
    
    1. Recommendation: Short term, add events where appropriate for all critical operations. Long term, consider using a blockchain monitoring system to track any suspicious behavior in the contracts. 
        
    2. [ToB's Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
143.  _**_assertStakingPoolExists**_ **never returns true**: The __assertStakingPoolExists_ should return a bool to determine if the staking pool exists or not; however, it only returns false or reverts. The __assertStakingPoolExists_ function checks that a staking pool exists given a pool id parameter. However, this function does not use a return statement and therefore, it will always return false or revert.
    
    1. Recommendation: Add a return statement or remove the return type. Properly adjust the documentation, if needed.
        
    2. [ToB's Audit of 0x Protocol](https://github.com/trailofbits/publications/blob/master/reviews/0x-protocol.pdf)
        
144. **_min* and _max* have unorthodox semantics**: Throughout the Curve contract, __minTargetAmount_ and __maxOriginAmount_ are used as open ranges (i.e., ranges that exclude the value itself). This contravenes the standard meanings of the terms “minimum” and “maximum,” which are generally used to describe closed ranges.
    
    1. Recommendation: Short term, unless they are intended to be strict, make the inequalities in the require statements non-strict. Alternatively, consider refactoring the variables or providing additional documentation to convey that they are meant to be exclusive bounds. Long term, ensure that mathematical terms such as “minimum,” “at least,” and “at most” are used in the typical way—that is, to describe values inclusive of minimums or maximums (as relevant).
        
    2. [ToB's Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
145. _**CurveFactory.newCurve**_ **returns existing curves without provided arguments**: _CurveFactory.newCurve_ takes values and creates a Curve contract instance for each __baseCurrency_ and __quoteCurrency_ pair, populating the Curve with provided weights and assimilator contract references. However, if the pair already exists, the existing Curve will be returned without any indication that it is not a newly created Curve with the provided weights. If an operator attempts to create a new Curve for a base-and-quote-currency pair that already exists, _CurveFactory_ will return the existing Curve instance regardless of whether other creation parameters differ. A naive operator may overlook this issue.
    
    1. Recommendation: Consider rewriting _newCurve_ such that it reverts in the event that a base-and-quote-currency pair already exists. A view function can be used to check for and retrieve existing Curves without any gas payment prior to an attempt at Curve creation.
        
    2. [ToB's Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
146. **Missing zero-address checks in** _**Curve.transferOwnership**_ **and** _**Router.constructor**_: Like other similar functions, _Curve._transfer_ and _Orchestrator.includeAsset_ perform zero-address checks. However, _Curve.transferOwnership_ and the Router constructor do not. This may make sense for _Curve.transferOwnership_, because without zero-address checks, the function may serve as a means of burning ownership. However, popular contracts that define similar functions often consider this case, such as OpenZeppelin’s _Ownable_ contracts. Conversely, a zero-address check should be added to the Router constructor to prevent the deployment of an invalid Router, which would revert upon a call to the zero address.
    
    1. Recommendation: Short term, consider adding zero-address checks to the Router’s constructor and Curve’s _transferOwnership_ function to prevent operator errors. Long term, review state variables which referencing contracts to ensure that the code that sets the state variables performs zero-address checks where necessary
        
    2. [ToB's Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
147. _**safeApprove**_ **does not check return values for approve call**: Although the Router contract uses OpenZeppelin’s _SafeERC20_ library to perform safe calls to ERC20’s approve function, the Orchestrator library defines its own _safeApprove_ function. This function checks that a call to approve was successful but does not check returndata to verify whether the call returned true. In contrast, OpenZeppelin’s _safeApprove_ function checks return values appropriately. This issue may result in uncaught approve errors in successful Curve deployments, causing undefined behavior.
    
    1. Recommendation: Short term, leverage OpenZeppelin’s _safeApprove_ function wherever possible. Long term, ensure that all low-level calls have accompanying contract existence checks and return value checks where appropriate.
        
    2. [ToB's Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
148. **ERC20 token Curve does not implement symbol, name, or decimals**: _Curve.sol_ is an ERC20 token and implements all six required ERC20 methods: _balanceOf_, _totalSupply_, _allowance_, _transfer_, _approve_, and _transferFrom_. However, it does not implement the optional but extremely common view methods _symbol_, _name_, and _decimals_.
    
    1. Recommendation: Short term, implement _symbol_, _name_, and _decimals_ on Curve contracts. Long term, ensure that contracts conform to all required and recommended industry standards.
        
    2. [ToB's Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
149. **Insufficient use of** _**SafeMath**_: _CurveMath.calculateTrade_ is used to compute the output amount for a trade. However, although _SafeMath_ is used throughout the codebase to prevent underflows/overflows, it is not used in this calculation. Although we could not prove that the lack of _SafeMath_ would cause an arithmetic issue in practice, all such calculations would benefit from the use of _SafeMath_.
    
    1. Recommendation: Review all critical arithmetic to ensure that it accounts for underflows, overflows, and the loss of precision. Consider using _SafeMath_ and the safe functions of _ABDKMath64x64_ where possible to prevent underflows and overflows.
        
    2. [ToB's Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
150. _**setFrozen**_ **can be front-run to deny deposits/swaps**: Currently, a Curve contract owner can use the _setFrozen_ function to set the contract into a state that will block swaps and deposits. A contract owner could leverage this process to front-run transactions and freeze contracts before certain deposits or swaps are made; the contract owner could then unfreeze them at a later time.
    
    1. Recommendation: Short term, consider rewriting _setFrozen_ such that any contract freeze will not last long enough for a malicious user to easily execute an attack. Alternatively, depending on the intended use of this function, consider implementing permanent freezes.
        
    2. [ToB's Audit of DFX Finance](https://github.com/dfx-finance/protocol/blob/main/audits/2021-05-03-Trail_of_Bits.pdf)
        
151. **Account creation spam:** Hermez has a limit of _2**MAX_NLEVELS_ accounts. There is no fee on account creation, so an attacker can spam the network with account creation to fill the tree. If _MAX_NLEVELS_ is below 32, an attacker can quickly reach the account limit. If _MAX_NLEVELS_ is above or equal to 32, the time required to fill the tree will depend on the number of transactions accepted per second, but will take at least a couple of months. Ethereum miners do not have to pay for account creation. Therefore, an Ethereum miner can spam the network with account creation by sending L1 user transactions. 
    
    1. Recommendation: Short term, add a fee for account creation or ensure _MAX_NLEVELS_ is at least 32. Also, monitor account creation and alert the community if a malicious coordinator spams the system. This will prevent an attacker from spamming the system to prevent new accounts from being created. Long term, when designing spam mitigation, consider that L1 gas cost can be avoided by Ethereum miners.
        
    2. [ToB's Audit of Hermez Network](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
152. **Using empty functions instead of interfaces leaves contract error-prone**: _WithdrawalDelayerInterface_ is a contract meant to be an interface. It contains functions with empty bodies instead of function signatures, which might lead to unexpected behavior. A contract inheriting from _WithdrawalDelayerInterface_ will not require an override of these functions and will not benefit from the compiler checks on its correct interface.
    
    1. Recommendation: Short term, use an interface instead of a contract in _WithdrawalDelayerInterface_. This will make derived contracts follow the interface properly. Long term, properly document the inheritance schema of the contracts. Use Slither’s inheritance-graph printer to review the inheritance.
        
    2. [ToB's Audit of Hermez Network](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
153. _**cancelTransaction**_ **can be called on non-queued transaction**: Without a transaction existence check in _cancelTransaction_, an attacker can confuse monitoring systems. _cancelTransaction_ emits an event without checking that the transaction to be canceled exists. This allows a malicious admin to confuse monitoring systems by generating malicious events.
    
    1. Recommendation: Short term, check that the transaction to be canceled exists in _cancelTransaction_. This will ensure that monitoring tools can rely on emitted events. Long term, write a specification of each function and thoroughly test it with unit tests and fuzzing. Use symbolic execution for arithmetic invariants.
        
    2. [ToB's Audit of Hermez Network](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
154. **Contracts used as dependencies do not track upstream changes**: Third-party contracts like __concatStorage_ are pasted into the Hermez repository. Moreover, the code documentation does not specify the exact revision used, or if it is modified. This makes updates and security fixes on these dependencies unreliable since they must be updated manually. __concatStorage_ is borrowed from the solidity-bytes-utils library, which provides helper functions for byte-related operations. Recently, a critical vulnerability was discovered in the library’s slice function which allows arbitrary writes for user-supplied inputs. 
    
    1. Recommendation: Short term, review the codebase and document each dependency’s source and version. Include the third-party sources as submodules in your Git repository so internal path consistency can be maintained and dependencies are updated periodically. Long term, identify the areas in the code that are relying on external libraries and use an Ethereum development environment and NPM to manage packages as part of your project.
        
    2. [ToB's Audit of Hermez Network](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
155. **Expected behavior regarding authorization for adding tokens is unclear**: _addToken_ allows anyone to list a new token on Hermez. This contradicts the online documentation, which implies that only the governance should have this authorization. It is unclear whether the implementation or the documentation is correct.
    
    1. Recommendation: Short term, update either the implementation or the documentation to standardize the authorization specification for adding tokens. Long term, write a specification of each function and thoroughly test it with unit tests and fuzzing. Use symbolic execution for arithmetic invariants.
        
    2. [ToB's Audit of Hermez Network](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
156. **Contract name duplication leaves codebase error-prone**: The codebase has multiple contracts that share the same name. This allows buidler-waffle to generate incorrect json artifacts, preventing third parties from using their tools. Buidler-waffle does not correctly support a codebase with duplicate contract names. The compilation overwrites compilation artifacts and prevents the use of third-party tools, such as Slither.
    
    1. Recommendation: Short term, prevent the re-use of duplicate contract names or change the compilation framework. Long term, use Slither, which will help detect duplicate contract names.
        
    2. [ToB's Audit of Hermez Network](https://github.com/trailofbits/publications/blob/master/reviews/hermez.pdf)
        
157. **Use of hard-coded addresses may cause errors**: Each contract needs contract addresses in order to be integrated into other protocols and systems. These addresses are currently hard-coded, which may cause errors and result in the codebase’s deployment with an incorrect asset. Using hard-coded values instead of deployer-provided values makes these contracts incredibly difficult to test.
    
    1. Recommendation: Short term, set addresses when contracts are created rather than using hard-coded values. This practice will facilitate testing. Long term, to ensure that contracts can be tested and reused across networks, avoid using hard-coded parameters.
        
    2. [ToB's Audit of Advanced Blockchains](https://github.com/trailofbits/publications/blob/master/reviews/AdvancedBlockchain.pdf)
        
158. **Borrow rate depends on approximation of blocks per year**: The borrow rate formula uses an approximation of the number of blocks mined annually. This number can change across different blockchains and years. The current value assumes that a new block is mined every 15 seconds, but on Ethereum mainnet, a new block is mined every ~13 seconds. To calculate the base rate, the formula determines the approximate borrow rate over the past year and divides that number by the estimated number of blocks mined per year. However, _blocksPerYear_ is an estimated value and may change depending on transaction throughput. Additionally, different blockchains may have different block-settling times, which could also alter this number.
    
    1. Recommendation: Short term, analyze the effects of a deviation from the actual number of blocks mined annually in borrow rate calculations and document the associated risks. Long term, identify all variables that are affected by external factors, and document the risks associated with deviations from their true values.
        
    2. [ToB's Audit of Advanced Blockchains](https://github.com/trailofbits/publications/blob/master/reviews/AdvancedBlockchain.pdf)
        
159. **Flash loan rate lacks bounds and can be set arbitrarily**: There are no lower or upper bounds on the flash loan rate implemented in the contract. The Blacksmith team could therefore set an arbitrarily high flash loan rate to secure higher fees. The Blacksmith team sets the __flashLoanRate_ when the Vault is first initialized. The _blackSmithTeam_ address can then update this value by calling _updateFlashloanRate_. However, because there is no check on either setter function, the flash loan rate can be set arbitrarily. A very high rate could enable the Blacksmith team to steal vault deposits.
    
    1. Recommendation: Short term, introduce lower and upper bounds for all configurable parameters in the system to limit privileged users’ abilities. Long term, identify all incoming parameters in the system as well as the financial implications of large and small corner-case values. Additionally, use Echidna or Manticore to ensure that system invariants hold.
        
    2. [ToB's Audit of Advanced Blockchains](https://github.com/trailofbits/publications/blob/master/reviews/AdvancedBlockchain.pdf)
        
160. **Logic duplicated across code**: The logic in the repositories provided to Trail of Bits contains a significant amount of duplicated code. This development practice increases the risk that new bugs will be introduced into the system, as bug fixes must be copied and pasted into files across the system.
    
    1. Recommendation: Short term, use inheritance to allow code to be reused across contracts. Changes to one inherited contract will be applied to all files without requiring developers to copy and paste them. Long term, minimize the amount of manual copying and pasting required to apply changes made to one file to other files.
        
    2. [ToB's Audit of Advanced Blockchains](https://github.com/trailofbits/publications/blob/master/reviews/AdvancedBlockchain.pdf)
        
161. **Insufficient testing**: The repositories under review lack appropriate testing, which increases the likelihood of errors in the development process and makes the code more difficult to review.
    
    1. Recommendation: Short term, ensure that the unit tests cover all public functions at least once, as well as all known corner cases. Long term, integrate coverage analysis tools into the development process and regularly review the coverage.
        
    2. [ToB's Audit of Advanced Blockchains](https://github.com/trailofbits/publications/blob/master/reviews/AdvancedBlockchain.pdf)
        
162. **Project dependencies contain vulnerabilities**: Although dependency scans did not yield a direct threat to the projects under review, yarn audit identified dependencies with known vulnerabilities. Due to the sensitivity of the deployment code and its environment, it is important to ensure dependencies are not malicious. Problems with dependencies in the JavaScript community could have a significant effect on the repositories under review.
    
    1. Recommendation: Short term, ensure dependencies are up to date. Several node modules have been documented as malicious because they execute malicious code when installing dependencies to projects. Keep modules current and verify their integrity after installation. Long term, consider integrating automated dependency auditing into the development workflow. If dependencies cannot be updated when a vulnerability is disclosed, ensure that the codebase does not use and is not affected by the vulnerable functionality of the dependency.
        
    2. [ToB's Audit of Advanced Blockchains](https://github.com/trailofbits/publications/blob/master/reviews/AdvancedBlockchain.pdf)
        
163. **Lack of contract documentation makes codebase difficult to understand**: The codebase lacks code documentation, high-level descriptions, and examples, making the contracts difficult to review and increasing the likelihood of user mistakes. The documentation would benefit from more detail.
    
    1. Recommendation: Short term, review and properly document the above mentioned aspects of the codebase. Long term, consider writing a formal specification of the protocol.
        
    2. [ToB's Audit of Advanced Blockchains](https://github.com/trailofbits/publications/blob/master/reviews/AdvancedBlockchain.pdf)
        
164. **ABIEncoderV2 is not production-ready**: The contracts use the new Solidity ABI encoder, _ABIEncoderV2_. This experimental encoder is not ready for production. More than 3% of all GitHub issues for the Solidity compiler are related to experimental features, primarily _ABIEncoderV2_. Several issues and bug reports are still open and unresolved. _ABIEncoderV2_ has been associated with more than [20 high-severity bugs](https://github.com/ethereum/solidity/issues?q=is:issue+abiencoderv2+label:%22bug+:bug:%22+sort:created-desc), some of which are so recent that they have not yet been included in a Solidity release. For example, in March 2019 a [severe bug](https://blog.ethereum.org/2019/03/26/solidity-optimizer-and-abiencoderv2-bug/) introduced in Solidity 0.5.5 was found in the encoder. 
    
    1. Recommendation: Short term, use neither _ABIEncoderV2_ nor any other experimental Solidity feature. Refactor the code such that structs do not need to be passed to or returned from functions. Long term, integrate static analysis tools like Slither into your CI pipeline to detect unsafe pragmas.
        
    2. [ToB's Audit of Advanced Blockchains](https://github.com/trailofbits/publications/blob/master/reviews/AdvancedBlockchain.pdf)
        
165. **Contract owner has too many privileges**: The owner of the contracts has too many privileges relative to standard users. Users can lose all of their assets if a contract owner private key is compromised. The contract owner can do the following: 1) Upgrade the system’s implementation to steal funds 2) Upgrade the token’s implementation to act maliciously 3)  Increase the amount of _iTokens_ for reward distribution to such an extent that rewards cannot be disbursed 4) Arbitrarily update the interest model contracts The concentration of these privileges creates a single point of failure. It increases the likelihood that the owner will be targeted by an attacker, especially given the insufficient protection on sensitive owner private keys. Additionally, it incentivizes the owner to act maliciously.
    
    1. Recommendation: Short term: 1) Clearly document the functions and implementations the owner can change. 2) Split privileges to ensure that no one address has excessive ownership of the system. Long term, document the risks associated with privileged users and single points of failure. Ensure that users are aware of all the risks associated with the system.
        
    2. [ToB's Audit of dForce Lending](https://github.com/dforce-network/documents/blob/master/audit_report/Lending/dForceLending-Audit-Report-TrailofBits-Mar-2021.pdf)
        
166. **Poor error-handling practices in test suite**: The test suite does not properly test expected behavior, as the contracts run in production. Additionally, certain components lack error-handling methods. These deficiencies can cause failed tests to be overlooked. In particular, the tests fail to properly check error messages. For example, errors are silenced with a try-catch statement. If this error is silenced, there will be no guarantee that a smart contract call has reverted for the right reason. As a result, if the test suite passes, it will provide no guarantee that the transaction call reverted correctly.
    
    1. Recommendation: Short term, test these operations against a specific error message. Testing will ensure that errors are never silenced, and the test suite will check that a contract call has reverted for the right reason. Long term, follow standard testing practices for smart contracts to minimize the number of issues during development.
        
    2. [ToB's Audit of dForce Lending](https://github.com/dforce-network/documents/blob/master/audit_report/Lending/dForceLending-Audit-Report-TrailofBits-Mar-2021.pdf)
        
167. **Redundant and Unused Code**: The __recordLoanClosure()_ function returns a boolean ( _loanClosed_ ) which is never used by the calling function (see __closeLoan_() , line [312]). Furthermore, since the __recordLoanClosure_() function is only called via the __closeLoan_() function, this means that _synthLoan.timeClosed_ is always equal to zero (see _require_ statement on line [305]). Therefore, the if statement on line [357] is redundant and unnecessary.
    
    1. Recommendation: 1) Using the return value of the __recordLoanClosure_() function or changing the function definition to stop returning _loanClosed_ 2) Removing the if statement in line [357]
        
    2. [Sigma Prime's Audit of Synthetix EtherCollateral](https://github.com/sigp/public-audits/blob/master/synthetix/ethercollateral/review.pdf)
        
168. **Single Account Can Capture All Supply**: The _EtherCollateral_ smart contract does not rely on a _maxLoanSize_ to limit the amount of ETH that can be locked for a loan. As a result, a single account can issue a loan that will reach the total minting supply.
    
    1. Recommendation: Make sure this behaviour is understood and consider introducing and enforcing a cap ( _maxLoanSize_ ) on the size of the loans allowed to be opened.
        
    2. [Sigma Prime's Audit of Synthetix EtherCollateral](https://github.com/sigp/public-audits/blob/master/synthetix/ethercollateral/review.pdf)
        
169. **Insufficient Input Validation**: The constructor of the _EtherCollateral_ smart contract does not check the validity of the addresses provided as input parameters. It is possible to deploy an instance of the _EtherCollateral_ contract with the _synthProxy_ , _sUSDProxy_ and depot addresses set to zero. Similarly, the effective interest rate can be equal to zero if _interestRate_ is set to any value lesser than 31536000 ( _SECONDS_IN_A_YEAR_ ), as _interestPerSecond_ will be null.
    
    1. Recommendation: Consider introducing require statements to perform adequate input validation.
        
    2. [Sigma Prime's Audit of Synthetix EtherCollateral](https://github.com/sigp/public-audits/blob/master/synthetix/ethercollateral/review.pdf)
        
170. **Unused Event Logs**: log events are declared but never emitted.
    
    1. Recommendation: Remove these events from the EtherCollateral contract.
        
    2. [Sigma Prime's Audit of Synthetix EtherCollateral](https://github.com/sigp/public-audits/blob/master/synthetix/ethercollateral/review.pdf)
        
171. **Possible Unintended Token Burning in** _**transferFrom**_**() Function**: _InfiniGold_ allows users to convert/exchange their PMGT tokens to "gold certificates", which are digital artefacts effectively redeemable for actual gold. To do so, users are supposed to send their PMGT tokens to a specific burn address. The _transferFrom_() function does not check the to address against this burn address. Users may send tokens to the burn address, using the _transferFrom_() function, without triggering the emission of the _Burn(address indexed burner, uint256 value)_ event, which dictates how the gold certificates are created and distributed.
    
    1. Recommendation: Prevent sending tokens to the burn address in the _transferFrom_() function. This can be achieved by adding a _require_ within _transferFrom_() which disallows the to address to be the _burnAddress_ .
        
    2. [Sigma Prime's Audit of InfiniGold](https://github.com/sigp/public-audits/raw/master/infinigold/review.pdf)
        
172. **Denial of Service Vector from Unbound List**: The _reset_() internal function (called by the _replaceAll_() function) resets the role linked list by deleting all the elements (i.e. nodes) part of the bearer mapping. The caller is bound by the number of elements that are being removed for a particular role . Calling the _reset_() function will exceed the current block gas limit (i.e. 8,000,0000) for more than 371 total elements in a role linked list. Similarly, the _size_() and _toArray_() functions also loop through the linked list. This essentially means that listers, unlisters, minters, pausers, unpausers and owners can perform denial of service attacks on the lists they administer. In a scenario where the Roles library is leveraged by other smart contracts, calling these two functions will also result in a potential denial of service after a certain number of elements have been included in the linked list (this number would depend on the gas cost of the Opcodes implemented by the calling functions).
    
    1. Recommendation: One way to ensure that the current block gas limit is not exceeded would be to introduce a condition in the _add_() function to check that the linked list size is strictly lesser than 371 elements before adding a new element. This additional condition would significantly increase the gas cost associated with calling the _add_() function, as a call to the _size_() function would be required to fetch the exact number of nodes in the linked list. Alternatively, the _gasleft_() Solidity special function could be used to make sure that going through the linked list does not exceed the block gas limit. Finally, the _reset_() could be changed to allow for removing an arbitrary number of nodes (by taking this number as a function parameter).
        
    2. [Sigma Prime's Audit of InfiniGold](https://github.com/sigp/public-audits/raw/master/infinigold/review.pdf)
        
173. **ERC20 Implementation Vulnerable to Front-Running**: Front-running attacks involve users watching the blockchain for particular transactions and, upon observing such a transaction, submitting their own transactions with a greater gas price. This incentivises miners to prioritise the later transaction. The ERC20 implementation is known to be affected by a front-running vulnerability, in its _approve_() function.
    
    1. Recommendation: Be aware of the front-running issues in _approve_() , potentially add extended approve functions which are not vulnerable to the front-running vulnerability for future third-party-applications. See the Open-Zeppelin [8] solution for an example. We note that modifying the ERC20 standard to address this issue may lead to backward incompatibilities with external third-party software.
        
    2. [Sigma Prime's Audit of InfiniGold](https://github.com/sigp/public-audits/raw/master/infinigold/review.pdf)
        
174. **Unnecessary** _**require**_ **Statement**: The following _require_ statement in _Blacklistable.sol_ can be removed: _require(to != address(0));_ Indeed, this check is implemented in the __transfer_() function in the _ERC20.sol_ smart contract.
    
    1. Recommendation: Consider removing the require statement for gas saving purposes.
        
    2. [Sigma Prime's Audit of InfiniGold](https://github.com/sigp/public-audits/raw/master/infinigold/review.pdf)
        
175. **Rounding to Zero if Duration is Greater Than Reward**: The _rewardRate_ value is calculated as follows: _rewardRate = reward/duration_. Due to the integer representation of these variables, if duration is larger than reward the value of _rewardRate_ will round to zero. Thus, stakers will not receive any of the reward for their stakes. Furthermore, due to the integer rounding, the total rewards distributed may be rounded down by up to one less than duration . As a result, the Unipool contract may slowly accumulate SNX.
    
    1. Recommendation: Beware of the rounding issues when calling the _notifyRewardAmount_() function. We also recommend some way of allowing the excess SNX reward from rounding to be claimed or withdrawn from the Unipool contract.
        
    2. [Sigma Prime's Audit of Synthetix Unipool](https://github.com/sigp/public-audits/blob/master/synthetix/unipool/review.pdf)
        
176. **Withdrawn Event Log Poisoning**: Calling the _withdraw_() function will emit the Withdrawn event. No UNI tokens are required as this function can be called with _amount_ = 0 . As a result a user could continually call this function, creating a potentially infinite amount of events. This can lead to an event log poisoning situation where malicious external users spam the Unipool contract to generate arbitrary Withdrawn events.
    
    1. Recommendation: Consider adding a _require_ or _if_ statement preventing the _withdraw_() function from emitting the Withdrawn event when the amount variable is zero.
        
    2. [Sigma Prime's Audit of Synthetix Unipool](https://github.com/sigp/public-audits/blob/master/synthetix/unipool/review.pdf)
        
177. **Insufficient incentives to liquidator**: The liquidation process is a very important part of every DeFi project because it allows to extinguish the problem of having the whole system under-collateralized under critical conditions of the market, and it needs a design that incentivizes its speed of execution. The Holdefi contract implements the liquidation process for those accounts that may have an under-collateralized balance or that may have been inactive for a whole year without interacting with the project. The liquidator would end up paying for the expensive liquidation process, without receiving any benefit. Buying discounted collateral assets could be considered as an incentive to the liquidators
    
    1. Recommendation: Consider improving the incentive design to give the liquidators higher incentives to execute the liquidation process
        
    2. [OpenZeppelin's Audit of HoldeFi](https://blog.openzeppelin.com/holdefi-audit)
        
178. **Markets can become insolvent**: When the value of all collateral is worth less than the value of all borrowed assets, we say a market is insolvent. The Holdefi codebase can do many things to reduce the risk of market insolvency, including: prudent selection of collateral-ratios, incentivizing third-party collateral liquidation, careful selection of which tokens are listed on the platform, etc. However, the risk of insolvency cannot be entirely eliminated, and there are numerous ways a market can become insolvent.
    
    1. Recommendation: This risk is not unique to the Holdefi project. All collateralized loans (even non-blockchain loans) have a risk of insolvency. However, it is important to know that this risk does exist, and that it can be difficult to recover from even a small dip into insolvency. Consider adding more targeted tests for these scenarios to better understand the behavior of the protocol, and designing relevant mechanics to make sure the platform operates properly. Also consider communicating the potential risks to the users if needed.
        
    2. [OpenZeppelin's Audit of HoldeFi](https://blog.openzeppelin.com/holdefi-audit)
        
179. **Not using OpenZeppelin contracts**: OpenZeppelin maintains a library of standard, audited, community-reviewed, and battle-tested smart contracts. Instead of always importing these contracts, the Holdefi project reimplements them in some cases, while in other cases it just copies them. This increases the amount of code that the Holdefi team will have to maintain and misses all the improvements and bug fixes that the OpenZeppelin team is constantly implementing with the help of the community.
    
    1. Recommendation: Consider importing the OpenZeppelin contracts instead of reimplementing or copying them. These contracts can be extended to add the extra functionalities required by Holdefi.
        
    2. [OpenZeppelin's Audit of HoldeFi](https://blog.openzeppelin.com/holdefi-audit)
        
180. **Lack of indexed parameters in events**: Throughout the Holdefi’s codebase, none of the parameters in the events defined in the contracts are indexed.
    
    1. Recommendation: Consider indexing event parameters to avoid hindering the task of off-chain services searching and filtering for specific events.
        
    2. [OpenZeppelin's Audit of HoldeFi](https://blog.openzeppelin.com/holdefi-audit)
        
181. **Named return variables**: There is an inconsistent use of named return variables across the entire codebase. 
    
    1. Recommendation: Consider removing all named return variables, explicitly declaring them as local variables in the body of the function, and adding the necessary explicit return statements where appropriate. This should favor both explicitness and readability of the project.
        
    2. [OpenZeppelin's Audit of HoldeFi](https://blog.openzeppelin.com/holdefi-audit)
        
182. **block.timestamp Unreliable**: Code uses the _block.timestamp_ as part of the calculations and time checks. Nevertheless, timestamps can be slightly altered by miners to favor them in contracts that have logics that depend strongly on them.
    
    1. Recommendation: Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future.
        
    2. [OpenZeppelin's Audit of HoldeFi](https://blog.openzeppelin.com/holdefi-audit)
        
183. **Assignment in** _**require**_ **statement**: In the _YieldOracle_ contract, there is a _require_ statement that makes an assignment. This deviates from the standard usage and intention of _require_ statements and can easily lead to confusion.
    
    1. Recommendation: Consider moving the assignment to its own line before the _require_ statement and then using the _require_ statement solely for condition checking.
        
    2. [OpenZeppelin's Audit of BarnBrige Smart Yield Bonds](https://blog.openzeppelin.com/barnbridge-smart-yield-bonds-audit/)
        
184. **Commented code**: Throughout the codebase there are lines of code that have been commented out with //. This can lead to confusion and is detrimental to overall code readability. 
    
    1. Recommendation: Consider removing commented out lines of code that are no longer needed.
        
    2. [OpenZeppelin's Audit of BarnBrige Smart Yield Bonds](https://blog.openzeppelin.com/barnbridge-smart-yield-bonds-audit/)
        
185. **Misleading** _**revert**_ **messages**: Error messages are intended to notify users about failing conditions, and should provide enough information so that the appropriate corrections needed to interact with the system can be applied. Uninformative error messages greatly damage the overall user experience, thus lowering the system’s quality. 
    
    1. Recommendation: Consider not only fixing the specific issues mentioned, but also reviewing the entire codebase to make sure every error message is informative and user-friendly enough. Furthermore, for consistency, consider reusing error messages when extremely similar conditions are checked.
        
    2. [OpenZeppelin's Audit of Compound Governor Bravo](https://blog.openzeppelin.com/compound-governor-bravo-audit/)
        
186. **Multiple outdated Solidity versions in use**: Outdated versions of Solidity are being used in all contracts. The compiler options in the truffle-config file specifies version 0.6.6, which was released on April 6, 2020. Throughout the codebase there are also different versions of Solidity being used. 
    
    1. Recommendation: As Solidity is now under a fast release cycle, consider using a more recent version of the compiler, such as version 0.7.6. In addition, to avoid unexpected behavior, consider specifying explicit Solidity versions in pragma statements.
        
    2. [OpenZeppelin's Audit of Fei Protocol](https://blog.openzeppelin.com/fei-protocol-audit/)
        
187. **Test and production constants in the same codebase**: The _CoreOrchestrator_ contract defines the _TEST_MODE_ boolean variable which is used to define several constants in the system. This decreases legibility of production code, and makes the system’s integral values more error-prone. 
    
    1. Recommendation: Consider having different environments for production and testing, with different contracts.
        
    2. [OpenZeppelin's Audit of Fei Protocol](https://blog.openzeppelin.com/fei-protocol-audit/)
        
188. **Unnecessarily small integer sizes**: In Solidity, using integers smaller than 256 bits tends to increase gas costs because the Ethereum Virtual Machine must perform additional operations to zero out the unused bits. This can be justified by savings in storage costs in some scenarios, however, that is not generally the case in this codebase.
    
    1. Recommendation: Consider using integers of size 256 bits to improve gas efficiency and mitigate function reverts.
        
    2. [OpenZeppelin's Audit of Fei Protocol](https://blog.openzeppelin.com/fei-protocol-audit/)
        
189. **Use of** _**uint**_ **instead of** _**uint256**_: Across the codebase, there are hundreds of instances of _uint_, as opposed to _uint256_.
    
    1. Recommendation: In favor of explicitness, consider replacing all instances of _uint_ with _uint256_.
        
    2. [OpenZeppelin's Audit of Fei Protocol](https://blog.openzeppelin.com/fei-protocol-audit/)
        
190. **Functions with unexpected side-effects**: Some functions have side-effects. For example, the __getLatestFundingRate_ function of the _FundingRateApplier_ contract might also update the funding rate and send rewards. The getPrice function of the _OptimisticOracle_ contract might also settle a price request. These side-effect actions are not clear in the name of the functions and are thus unexpected, which could lead to mistakes when the code is modified by new developers not experienced in all the implementation details of the project.
    
    1. Recommendation: Consider splitting these functions in separate getters and setters. Alternatively, consider renaming the functions to describe all the actions that they perform.
        
    2. [OpenZeppelin's Audit of Uma Phase 4](https://blog.openzeppelin.com/uma-audit-phase-4/)
        
191. **Unsafe casting**: In line 554 of the _TaxCollector_ contract, the value of _coinBalance(receiver)_ is an _uint_. This is cast to an _int_ and then negated. However, since _uint_ can store higher values than _int_, it is possible that casting from _uint_ to _int_ may create an overflow.
    
    1. Recommendation: Consider verifying that the value of _coinBalance(receiver)_ is within the acceptable range for negative _int_ values before casting and negating. Consider using OpenZeppelin’s _SafeCast_ contract, which provides functions for safely casting between types.
        
    2. [OpenZeppelin's Audit of GEB Protocol](https://blog.openzeppelin.com/geb-protocol-audit/)
        
192. **Missing error messages in** _**require**_ **statements**: There are many places where _require_ statements are correctly followed by their error messages, clarifying what was the triggered exception. However, there are places where _require_ statements are not followed by the corresponding error messages. If any of those _require_ statements were to fail the checked condition, the transaction  
    would revert silently without an informative error message.
    
    1. Recommendation: Consider including specific and informative error messages in all _require_ statements.
        
    2. [OpenZeppelin's Audit of GEB Protocol](https://blog.openzeppelin.com/geb-protocol-audit/)
        
193. **Uncommented assembly block**: The _OracleRelayer_ contract includes an assembly block in the _rpower_() function. The same assembly block is repeated in the _TaxCollector_ and _CoinSavingsAccount_ contracts. While this does not pose a security risk per se, it is at the same time a complicated and critical part of the system. Moreover, as this is a low-level language that is harder to parse by readers, consider including extensive documentation regarding the rationale behind its use, clearly explaining what every single assembly instruction does. This will make it easier for users to trust the code, for reviewers to verify it, and for developers to build on top of it or update it. Note that the use of assembly discards several important safety features of Solidity, which may render the code unsafer and more error-prone.
    
    1. Recommendation: Consider implementing thorough tests to cover all potential use cases of these functions to ensure they behave as expected.
        
    2. [OpenZeppelin's Audit of GEB Protocol](https://blog.openzeppelin.com/geb-protocol-audit/)
        
194. **Unnecessary** _**require**_ **statements**: There are several instances in the code base where the _require_ statements or conditional checks are unnecessary. For instance: In the _OracleRelayer_ contract, the _require_ statement in the _modifyParameters_ function at line 189 checks if the input parameter data > 0. This is unnecessary since the same condition is already checked in the _require_ statement at line 187.
    
    1. Recommendation: To simplify the code and prevent wastage of gas, consider removing the unnecessary checks.
        
    2. [OpenZeppelin's Audit of GEB Protocol](https://blog.openzeppelin.com/geb-protocol-audit/)
        
195. **Unnecessary event emission**: The _popDebtFromQueue_ function of the _AccountingEngine_ contract is emitting a useless event whenever someone tries to call it with a _debtBlockTimestamp_ that has not been saved before.
    
    1. Recommendation: To simplify the code and prevent wastage of gas, avoid emitting unnecessary events.
        
    2. [OpenZeppelin's Audit of GEB Protocol](https://blog.openzeppelin.com/geb-protocol-audit/)
        
196. _**oToken**_ **can be created with a non-whitelisted collateral asset**: A product consists of a set of assets and an option type. Each product has to be whitelisted by the admin using the _whitelistProduct_ function from the Whitelist contract.
    
    1. Recommendation: Consider validating if the assets involved in a product have been already whitelisted before allowing the creation of _oTokens_.
        
    2. [OpenZeppelin's Audit of Opyn Gamma Protocol](https://blog.openzeppelin.com/opyn-gamma-protocol-audit/)
        
197. **Mismatches between contracts and interfaces**: Interfaces define the exposed functionality of the implemented contracts. However, in several interfaces there are functions from the counterpart contracts that are not defined. 
    
    1. Recommendation: Consider applying the necessary changes in the mentioned interfaces and contracts so that definitions and implementations fully match.
        
    2. [OpenZeppelin's Audit of Opyn Gamma Protocol](https://blog.openzeppelin.com/opyn-gamma-protocol-audit/)
        
198. **Actions not executed atomically might lead to inconsistent state**: The _setAssetPricer_, _setLockingPeriod,_ and _setDisputePeriod_ functions of the Oracle contract execute actions that are always expected to be performed atomically. Failing to do so can lead to inconsistent states in the system.
    
    1. Recommendation: Consider implementing an additional function that calls the _setAssetPricer_, _setLockingPeriod_, and _setDisputePeriod_ functions, so that these actions can be executed atomically in a single transaction.
        
    2. [OpenZeppelin's Audit of Opyn Gamma Protocol](https://blog.openzeppelin.com/opyn-gamma-protocol-audit/)
        
199. **Chainlink pricer is using a deprecated API**: The Chainlink Pricer is currently using multiple functions from a deprecated Chainlink API such as _latestAnswer_() in L61, _getTimestamp_() in L74. These functions might suddenly stop working if Chainlink stopped supporting deprecated APIs.
    
    1. Recommendation: Consider refactoring these to use the latest Chainlink API.
        
    2. [OpenZeppelin's Audit of Opyn Gamma Protocol](https://blog.openzeppelin.com/opyn-gamma-protocol-audit/)
        
200. **Funds can be lost**: The _sweepTimelockBalances_ function accepts a list of users with unlocked balances to distribute. However, if there are duplicate users in the list, their balances will be counted multiple times when calculating the total amount to withdraw from the yield service.
    
    1. Recommendation: Consider checking for duplicate users when calculating the amount to withdraw.
        
    2. [OpenZeppelin's Audit of PoolTogether V3](https://blog.openzeppelin.com/pooltogether-v3-audit/)
        
201. **Use** _**delete**_ **to clear variables**: The Controller contract sets a variable to the zero address in order to clear it. Similarly, the _SetToken_ clears the locker by assigning the zero address.
    
    1. Recommendation: The _delete_ key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with _delete_ statements.
        
    2. [OpenZeppelin's Audit of Set Protocol](https://blog.openzeppelin.com/set-protocol-audit/)