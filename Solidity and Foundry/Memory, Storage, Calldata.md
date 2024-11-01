In the context of Ethereum smart contracts, memory, storage, and calldata refer to different types of storage locations and areas where data can be stored or manipulated during the execution of a contract.

> When declaring any dynamic type, we must specify the data location, when we write our code!

```php
function addPersonName(string memory _name, uint256 _favoriteNumber) public {
		_name = "cat"; //  we can change the value of `_name`, becuase we use `memory`, not `calldata`
        listOfPeople.push(Person(_favoriteNumber, _name));
    }
```

```php
# Storage

- 2 ** 256 slots
- 32 bytes for each slot
- data is stored sequentially in the order of declaration
- storage is optimized to save space. If neighboring variables fit in a single
  32 bytes, then they are packed into the same slot, starting from the right
*/
```
```php
uint256 var1 = 256;  // slot 0
uint128 var2 = 1;  // slot 1
uint128 var3 = 2;  // slot 1     128 + 128 = 256 bits, so they are in one slot
// -------------------------------------------------------------------------------
// New program/example:
uint16 a;  // stored in slot 0
uint16 b;  // stored in slot 0
address c; // stored in slot 0
uint64 d; // stored in slot 0

// The total size of all of the 4 variables amounts is exactly 256 bits, which mean that they get packed into one single storage slot
// -------------------------------------------------------------------------------
// Another example:
address a; // stored in slot 0
bool b;  // stored in slot 0         -- total is 128 bytes of address and boolean value
uint256 c;  // stored in slot 1, because it exceeds the limit of the first slot
```
    - **Use Case:** Used for storing long-term contract state and data that needs to be retained between different function calls and transactions.

**We cannot do this:**
```php
function addPersonname(string storage _name, uint256 _favoriteNumber) public {
		_name = "cat"; //  we can't use `storage` keyword in function, because in function we'll use state variable `_name` in a short period of time(within a function call). Storage is persistent storing location!
        listOfPeople.push(Person(_favoriteNumber, _name));
    }
```

In summary:

- **Memory:** Temporary storage used within a function, erased between function calls. It can be updated and manipulated, unlike calldata. Use memory to read-only data.
- **Storage:** Persistent storage used for storing contract state variables, persists between function calls and transactions. Use storage for update and store the values! Storage is composed of a series of slots. There are 2 ** 256 slots for a SC. When declaring variable, we start with 0 slot and increment from there. Each slot it 256 bits(32-bytes)
- **Calldata:** Read-only storage used to access input data passed to a function during a transaction.

****
### Storage Layout in EVM

The EVM stores SC state variables in the order that they were declared in slots on the blockchain. The default value of each slot is always 0, so we don't need to assign a value to 0 when the new declaration is.
If 2 or more variables fit into a single 32-byte slot, they're packed into the same slot, beginning on the right.

`uint8 -> 1 byte; uint16 -> 2 bytes and so on...; uint256 -> 32 bytes; bool -> 1 byte; address -> 20 byte; bytes1 -> 1 byte; bytes2 -> 2 bytes and so on...`

**More explanation for Storage:**
1 storage slot = 256 bits(32 bytes)

When the size of a given variable exceeds the storage slot's capacity, it's allocated onto the next slot. Solidity has 16 persistent storage slots.

> **The EVM is not a register machine but a stack machine, so all computations are performed 
****
**Constants** are not saved in storage

**Constant and Immutable variables are not in storage slots, because they are considered part of the core of the bytecode of the contract. For dynamic values like mappings and dynamic arrays, the elements are stored using a hashing function.(`keccak256()`)**
****
> For contracts that use inheritance, the ordering of state variables is determined by the C3-linearized order of contracts starting with the most base-ward contract. If allowed by the above rules, state variables from different contracts do share the same storage slot.
****
Mappings and dynamic arrays are stored differently in the Ethereum Virtual Machine (EVM) due to their nature and usage.

**Mappings**:
    - **Storage Layout**: Mappings are stored in the Ethereum blockchain's storage, which is persistent and immutable. They are stored in a key-value format, where the keys are hashed and the values are stored at the hashed locations.
    - **Hashing**: When a key-value pair is stored in a mapping, the key is hashed to determine the storage location of the value. This hashing process helps optimize storage access and allows for efficient retrieval of values.

**Dynamic Arrays**:
- **Storage Layout**: Dynamic arrays are also stored in the Ethereum blockchain's storage. However, unlike mappings, dynamic arrays store their elements sequentially in contiguous storage slots.
- **Length Storage**: The length of a dynamic array is stored separately in a single storage slot, usually at the beginning of the array's storage allocation. This length value indicates the number of elements in the array.
- **Expansion and Contraction**: Dynamic arrays can grow and shrink dynamically by allocating or deallocating storage slots as needed. When an array grows beyond its current storage allocation, additional storage slots are allocated to accommodate the new elements.
- ***slot where array element is stored = keccak256(slot)) + (index * elementSize)***