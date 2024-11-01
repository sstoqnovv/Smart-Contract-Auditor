## Description

The Solidity `assert()` function is meant to assert invariants. Properly functioning code should _never_ reach a failing assert statement. A reachable assertion can mean one of two things:

1. A bug exists in the contract that allows it to enter an invalid state;
2. The `assert` statement is used incorrectly, e.g. to validate inputs.

## Remediation

Consider whether the condition checked in the `assert()` is actually an invariant. If not, replace the `assert()` statement with a `require()` statement.

If the exception is indeed caused by unexpected behaviour of the code, fix the underlying bug(s) that allow the assertion to be violated.

## References

- [The use of revert(), assert(), and require() in Solidity, and the new REVERT opcode in the EVM](https://media.consensys.net/when-to-use-revert-assert-and-require-in-solidity-61fb2c0e5a57)
****
## Description

The Solidity `require()` construct is meant to validate external inputs of a function. In most cases, such external inputs are provided by callers, but they may also be returned by callees. In the former case, we refer to them as precondition violations. Violations of a requirement can indicate one of two possible issues:

1. A bug exists in the contract that provided the external input.
2. The condition used to express the requirement is too strong.

## Remediation

If the required logical condition is too strong, it should be weakened to allow all valid external inputs.

Otherwise, the bug must be in the contract that provided the external input and one should consider fixing its code by making sure no invalid inputs are provided.