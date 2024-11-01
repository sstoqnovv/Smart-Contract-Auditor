External calls in the context of Ethereum smart contracts refer to interactions with other contracts or external addresses. This can be achieved using the `call()`, `delegatecall()`, or `staticcall()` functions in Solidity. External calls allow a contract to invoke functions in other contracts, potentially passing data and Ether.

**`call()`:**
- The `call()` function is a low-level method for making external function calls or sending Ether to another contract. It is often used to interact with contracts whose interface is not known at compile time or to handle generic external calls. Here's a simple example:

```php
address target = 0x123...; // Address of the target contract 
uint256 valueToSend = 1 ether; (bool success, bytes memory data) = target.call{value: valueToSend}(""); if (success) { // Process success } 
else { // Handle failure }
```
The `call()` function does not propagate exceptions. If the call fails, `success` will be `false`, and the contract should handle the failure appropriately.
****
**`delegatecall()`:**
- The `delegatecall()` function is a special variant of `call()` that is often used in the context of proxy contracts. Unlike `call()`, `delegatecall()` executes the called contract's code within the context of the calling contract. This means that the called contract's state variables and storage are modified as if the calling contract's code were being executed. 
- Example:
```php
address target = 0x123...; // Address of the target contract 
(bool success, bytes memory data) = target.delegatecall(msg.data); if (success) { // Process success } 
else { // Handle failure }
```
Care must be taken when using `delegatecall()` to ensure that the called contract's code is compatible with the calling contract's storage layout.
****
**`staticcall()`:**
- The `staticcall()` function is used for read-only operations, without modifying the blockchain or another contract state. It's primarily used for retrieving data. 
- Example:
```php
address target = 0x123...; // Address of the target contract 
(bool success, bytes memory data) = target.staticcall(msg.data); if (success) { // Process data (read-only) } 
else { // Handle failure }
```
Similar to `call()`, `staticcall()` does not propagate exceptions, and the result is indicated by the `success` boolean.
****
### Sending the Transactions

So, how do we transform this understanding into action - how do we populate the data field and then send these custom, data-encoded transactions?  
In solidity, we rely on some low-level keywords - `staticcall` and `call` - to perform this function. `staticcall` and `call` are used for view or pure functions and functions that change the blockchains' state, respectively.  
In these functions, the code that specifies a particular function to execute goes into the parentheses (data field).

```js
function withdraw(address recentWinner) public {
    (bool success, ) = recentWinner.call{value: address.(this).balance}("");
    require(success, "Transfer Failed");
}
```

  
the `{value: address.(this).balance}` segment updates the transaction's value field while the empty parentheses imply there's no function to call; the transaction merely sends money.  
However, if a function needs to be executed or data should be sent, it can be specified in the parentheses, let's combine this with our previous `Method ID` we got from etherscan.  

```js
function enterRaffle(uint256 entryFee) public payable {
    PuppyRaffle puppyRaffle = new PuppyRaffle;
    puppyRaffle.call{value: entryFee}("0x2cfcc539");
}
```

  
In the above example, you can see that we're passing the `entryFee` as an argument to the `value` property of the transaction and in the `data` field we are populating the `function signature`. This will tell the EVM, what to call, where and how much to send.
****
**External Calls:**
- External calls in the context of Ethereum smart contracts refer to interactions with other contracts or external addresses. This can be achieved using the `call()`, `delegatecall()`, or `staticcall()` functions in Solidity. External calls allow a contract to invoke functions in other contracts, potentially passing data and Ether.
    
    Example of an external call using `call()`:
    ```php
    address target = 0x123...; // Address of the target contract 
    uint256 valueToSend = 1 ether; (bool success, bytes memory data) = target.call{value: valueToSend}(""); if (success) { // Process success } 
    else { // Handle failure }
```

> When we use functions like `call()` or `delegatecall()`, we encode the data into bytes