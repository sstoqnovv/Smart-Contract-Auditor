#### Array Check

When in Solidity function has an argument of type array. Allways check:
1. What if the array length is 0?
2. What if there are duplicated elements in the array?
3. What if there are 0 value elements in the array?
4. Ordering is correct?
5. Can it be calldata vs memory?
6. Invalid value checked, e.g. an address that mustn't be a certain value.
7. Array length will be > 0 even when the array contains values only.

Solidity function has an argument of type array? 
Always check: 
- Also, the array length will be > 0 even when the array contains default values only. Example: It could be a dynamic array of addresses, and it could have 10 entries all of address(0), and the array length will be > 0.
- Size not too big 
- Size is correct 
- Ordering is correct 
- Invalid values checked e.g an address that must not be a certain value 
- Can it be calldata vs memory 
- Can it be Array struct instead if taking multiple related arrays that work in tandem

### Loop Check

There are a few things that you should always check when you see a for loop: 
1. Is there a storage array involved that can grow infinitely and potentially make the transaction run out of gas? If so, the array should be capped to a reasonable size. 
2. Can an array be cached outside of the loop to make it gas optimized? 
3. Are there any external calls within each loop that can cause a DoS? If one of the calls fails, the whole transaction will revert and may brick something. A try/catch implementation can be useful in this case.
****
"question": "What happens on the first and the last cycle of the iteration?",
"description": "Sometimes the first and last cycles have a different logic from others and there can be problems.",
"remediation": "Ensure the logic is correct for the first and the last cycles.",

"question": "How does the protocol remove an item from an array?",
"description": "`delete` does not rearrange the array but just resets the element.",
"remediation": "Copy the last element to the index of the element to be removed and decrease the length of an array.",

"question": "Does any function get an index of an array as an argument?",
"description": "If an array is supposed to be updated (removal in the middle), the indexes will change.",
"remediation": "Do not use an index of an array that is supposed to be updated as a parameter of a function.",

 "question": "Is it fine to have duplicate items in the array?",
"description": "In most cases, an array (especially an input array by users) is supposed to be unique.",
"remediation": "Add a validation to check the array is unique.",

"question": "Is there possibility of iteration of a huge array?",
"description": "Due to the block gas limit, there is a clear limitation in the amount of operation that can be handled in a transaction.",
"remediation": "Ensure the number of iterations is properly bounded.",

"question": "Is there a potential for a Denial-of-Service (DoS) attack in the loop?",
"description": "Loops that contain external calls or are dependent on user-controlled input can be exploited to halt the contract's functions. (e.g. sending ETH to multiple users)",
"remediation": "Ensure a failure of a single iteration does not revert the whole operation.",

"question": "Is `msg.value` used within a loop?",
"description": "`msg.value` is consistent for the whole transaction. If it is used in the for loop, it is likely there is a mistake in accounting.",
"remediation": "Avoid using `msg.value` inside loops. Refer to multi-call vulnerability.",

"question": "Is there a break or continue inside a loop?",
"description": "Sometimes developers overlook the edge cases that can happened due to the break or continue in the middle of the loop.",
"remediation": "Make sure the break or continue inside a loop does not lead to unexpected behaviors.",
****
### Map

"question": "Is there need to delete the existing item from a map?",
"description": "If a variable of nested structure is deleted, only the top-level fields are reset by default values (zero) and the nested level fields are not reset.",
"remediation": "Always ensure that inner fields are deleted before the outer fields of the structure.",
****
### Events

"question": "Does the protocol emit events on important state changes?",
"description": "Emitting events properly is important especially if the change is critical.",
"remediation": "Ensure to emit events in all important functions.",
