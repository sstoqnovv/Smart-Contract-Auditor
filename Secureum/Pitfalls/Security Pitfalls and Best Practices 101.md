1. **Solidity versions**: Using very old versions of Solidity prevents benefits of bug fixes and newer security checks.
2. **Unlocked pragma**: Contracts should be deployed using the same compiler version/flags with which they have been tested.
3. **Multiple Solidity pragma**: It is better to use one Solidity compiler version across all contracts.
4. **Unprotected withdraw function**: Unprotected (_external_/_public_) function calls sending Ether/tokens to user-controlled addresses may allow users to withdraw unauthorized funds.
5. **Modifier side-effects:** Modifiers should only implement checks and not make state changes and external calls which violates the [checks-effects-interactions](https://solidity.readthedocs.io/en/develop/security-considerations.html#use-the-checks-effects-interactions-pattern) pattern.
6. **Incorrect modifier**: If a modifier does not execute ___ or _revert_, the function using that modifier will return the default value causing unexpected behavior.

7. **Constructor names:** Before _solc 0.4.22_, constructor names had to be the same name as the contract class containing it.
8. **Void constructor:** Calls to base contract constructors that are unimplemented leads to misplaced assumptions.

9. **Controlled delegatecall:** _delegatecall()_ or _callcode()_ to an address controlled by the user allows execution of malicious contracts in the context of the caller’s state.

10. **Reentrancy vulnerabilities**: Untrusted external contract calls could callback leading to unexpected results such as multiple withdrawals or out-of-order events.
11. **Avoid** _**transfer()**_**/**_**send()**_ **as reentrancy mitigations**: Although _transfer()_ and _send()_ have been recommended as a security best-practice to prevent reentrancy attacks because they only forward 2300 gas, the gas repricing of opcodes may break deployed contracts.

12. **Private on-chain data**: Marking variables _private_ does not mean that they cannot be read on-chain. Private data should not be stored unencrypted in contract code or state but instead stored encrypted or off-chain.

1. **Integer overflow/underflow**: Not using OpenZeppelin’s SafeMath (or similar libraries) that check for overflows/underflows may lead to vulnerabilities or unexpected behavior if user/attacker can control the integer operands of such arithmetic operations.
2. **Divide before multiply:** Performing multiplication before division is generally better to avoid loss of precision because Solidity integer division might truncate.

3. _**fallback**_ **vs** _**receive()**_: Check that all precautions and subtleties of _fallback_/_receive_ functions related to visibility, state mutability and Ether transfers have been considered.
4. **Locked Ether**: Contracts that accept Ether via _payable_ functions but without withdrawal mechanisms will lock up that Ether. Remove _payable_ attribute or add withdraw function.

5. **State-modifying functions**: Functions that modify state (in assembly or otherwise) but are labelled _constant_/_pure_/_view_ revert

6. **Account existence check for low-level calls**: Low-level calls _call_/_delegatecall_/_staticcall_ return true even if the account called is non-existent (per EVM design). Account existence must be checked prior to calling if needed.
7. **Missing zero address validation**: Setters of address type parameters should include a zero-address check otherwise contract functionality may become inaccessible or tokens burnt forever.

8. _**require()**_ **vs** _**assert()**_**:** _require()_ should be used for checking error conditions on inputs and return values while _assert()_ should be used for invariant checking.

9. **Incorrect inheritance order**: Contracts inheriting from multiple contracts with identical functions should specify the correct inheritance order i.e. more general to more specific to avoid inheriting the incorrect function implementation.

10. **Private methods overridden**: While private methods of base contracts are not visible and cannot be called directly from the derived contract, it is still possible to declare a function of the same name and type and thus change the behaviour of the base contract's function.

11. **Avoid** _**selfdestruct**_ **or** _**delegatecall**_ **in proxy-based upgradeable contracts**: This will cause the logic contract to be destroyed and all contract instances will end up delegating calls to an address without any code.

12. FOR LOOPS + STATE VARIABLES === NO


```php 
contract CostlyOperationsInLoop{
	uint loop_count = 100;
	uint state-variable = 0;
	
	function bad() external{
	for (uint i=0; i < loop_count; i++{
		state_variable++; // here we increment the state variable in every iteration, which is not gas efficient
	}
}
}

	function good() external {
	uint local_var = state_var; // while here we create local variable and increment it in every iteration and finally we simply update the state variable
	for(uint i=0; i < loop_count; i++) {
		local_var++;
		}
		state_variable = local_var;
}
```

25. Withdraw Functions need to be protected to Access Control using Modifiers.
26. _selfdestruct_ destroys contract and transfers all the Ether balance to the sepcified recipient address. It must be protected by access.
27. _Public vs External_ ---> Public consume more gas, because the args. of public functions need to be copied from the calldata component of EVM to the memory component. This copy produces more bytecode for such public functions which therefore consumes more gas.
28. _Dead code_ ---> Dead code is any contract code is unused from the contracts perspective or even unreachable from a control flow perspective. This could be indicative of programmer error or missing logic of the developer.
29. A costly loop can happen when you're consistenlty updating a state variable(they are stroing on the blockchain) within a loop, instead loop over a local variable, then update the state variable when complete. The Opcode "SSTORE" is an expensive one.
30. Generating randomness in Solidity, the programming language for Ethereum smart contracts, can be a challenge due to the deterministic nature of blockchain systems. Therefore, it is difficult to obtain true randomness within the contract itself. However, there are some approaches you can consider to introduce randomness in Solidity:
	 - **External Oracles**: You can use external oracles(like Chainlink) or off-chain data sources that provide random values. These oracles can be queried by your smart contract to obtain random data. However, it's important to ensure the reliability and security of the chosen oracle, as it can introduce a centralized point of failure or manipulation.
	 - **Blockhash**: Solidity provides a built-in function called `blockhash` that returns the hash of a specific block. You can use the hash of a recent block as a source of randomness. However, be cautious when relying solely on this method, as miners have some influence over which blocks get included in the blockchain.
31. When you're logging event - you must emit it, because by default it is not emitted off-chain when the event is happened.
