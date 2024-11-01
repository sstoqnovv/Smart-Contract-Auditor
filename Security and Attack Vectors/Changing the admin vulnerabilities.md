```php
pragma solidity 0.8.4;

contract test {
	// Assume other required functionality is correctly implemented
	// Assume admin is correctly set to the contract deployer in constructor address admin

function setAdmin(address _newAdmin) external {
	admin = _newAdmin;
}
}
```

**Missing access control on critical function. Incorrect access control** - Contract function executing critical logic have appopriate access control onforced via address checks typically in modifiers. Missing checks allows attackers to control critical logics.

**Missing zero-address violation** - Setters of address type parameters should include zero-address checks, otherwise contract functionality may become inaccessible or tokens burnt forever.

**Critical address change** - Changing critical address in contract should be a 2 step process where:
 - The first transaction(from the old/current address) registers the new address and
 - The second transaction(from the new address) replaces the old address with the new one.
This gives an opportunity to recover from incorrect addresses mistakenly used in first step. If not, contract functionality might become inaccessible.

**Missing events** - Events for critical state changes should be emitted for tracking this off-chain.

To change the admin - you must do: **2 steps ownership transfer**![[Pasted image 20230724171124.png]]
 - This is vulnerable and it's Medium severity issue.
```php
/**
   * @dev Allows the current owner to transfer control of the contract to a newOwner.
   * @param newOwner The address to transfer ownership to.
   */
  function transferOwnership(address newOwner) public onlyOwner {
    require(newOwner != address(0));
    OwnershipTransferred(owner, newOwner);
    owner = newOwner;
  }
```
 - This is good practice/pitfall.

46. **Privileged roles and EOAs**: Trusted actors who have privileged roles with capabilities to deploy contracts, change critical parameters, pause/unpause system, trigger emergency shutdown, withdraw/transfer/drain funds and allow/deny other actors should be addresses controlled by multiple, independent, mutually distrusting entities. They should not be controlled by private keys of EOAs but with Multisigs with a high threshold (e.g. 5-of-7, 9-of-11) and eventually by a DAO of token holders. EOA has a single point of failure.
47. **Two-step change of privileged roles**: When privileged roles are being changed, it is recommended to follow a two-step approach: 1) The current privileged role proposes a new address for the change 2) The newly proposed address then claims the privileged role in a separate transaction. This two-step change allows accidental proposals to be corrected instead of leaving the system operationally with no/malicious privileged role. For e.g., in a single-step change, if the current admin accidentally changes the new admin to a zero-address or an incorrect address (where the private keys are not available), the system is left without an operational admin and will have to be redeployed.