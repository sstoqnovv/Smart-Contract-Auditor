### Take Warnings Seriously[](https://docs.soliditylang.org/en/latest/security-considerations.html#take-warnings-seriously "Permalink to this heading")

If the compiler warns you about something, you should change it. Even if you do not think that this particular warning has security implications, there might be another issue buried beneath it. Any compiler warning we issue can be silenced by slight changes to the code.

Always use the latest version of the compiler to be notified about all recently introduced warnings.

Messages of type `info`, issued by the compiler, are not dangerous and simply represent extra suggestions and optional information that the compiler thinks might be useful to the user.

### Restrict the Amount of Ether[](https://docs.soliditylang.org/en/latest/security-considerations.html#restrict-the-amount-of-ether "Permalink to this heading")

Restrict the amount of Ether (or other tokens) that can be stored in a smart contract. If your source code, the compiler or the platform has a bug, these funds may be lost. If you want to limit your loss, limit the amount of Ether.

### Keep it Small and Modular[](https://docs.soliditylang.org/en/latest/security-considerations.html#keep-it-small-and-modular "Permalink to this heading")

Keep your contracts small and easily understandable. Single out unrelated functionality in other contracts or into libraries. General recommendations about the source code quality of course apply: Limit the amount of local variables, the length of functions and so on. Document your functions so that others can see what your intention was and whether it is different than what the code does.

### Use the Checks-Effects-Interactions Pattern[](https://docs.soliditylang.org/en/latest/security-considerations.html#use-the-checks-effects-interactions-pattern "Permalink to this heading")

Most functions will first perform some checks and they should be done first (who called the function, are the arguments in range, did they send enough Ether, does the person have tokens, etc.).

As the second step, if all checks passed, effects to the state variables of the current contract should be made. Interaction with other contracts should be the very last step in any function.

Early contracts delayed some effects and waited for external function calls to return in a non-error state. This is often a serious mistake because of the reentrancy problem explained above.

Note that, also, calls to known contracts might in turn cause calls to unknown contracts, so it is probably better to just always apply this pattern.

### Include a Fail-Safe Mode[](https://docs.soliditylang.org/en/latest/security-considerations.html#include-a-fail-safe-mode "Permalink to this heading")

While making your system fully decentralized will remove any intermediary, it might be a good idea, especially for new code, to include some kind of fail-safe mechanism:

You can add a function in your smart contract that performs some self-checks like “Has any Ether leaked?”, “Is the sum of the tokens equal to the balance of the contract?” or similar things. Keep in mind that you cannot use too much gas for that, so help through off-chain computations might be needed there.

If the self-check fails, the contract automatically switches into some kind of “failsafe” mode, which, for example, disables most of the features, hands over control to a fixed and trusted third party or just converts the contract into a simple “give me back my Ether” contract.