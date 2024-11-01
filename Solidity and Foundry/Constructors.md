### Constructors
 
A special function in a SC. It turns only once when the contract is deployed.
Every contract has a default constructor, even if you don't define one.
You can define your own constructors to customize the initialization of your contract.

Constructor is going to be executed once when the contract is created.
 
If you import a contract from another file, its constructor runs during deployment of the contract that imports it.

A constructor is optional. Only one constructor is allowed, which means overloading is not supported.
 
**Uses of Constructors**
- Set initial value for state variables within a contract
- Establish access control by designating initial owners or admins of the contract.
- Some state variables, once set by the constructor, become immutable.