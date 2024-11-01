When developing smart contracts, understanding and following the audit process is a crucial step towards achieving a more secure protocol. Here, we'll outline an example of this process.

> If a firm/business give you an Etherscan only codebase, it's very bad sign that the code they want you to audit will not be safe. If you advice the firm/business how is correct to give you the codebase and they telll you "shut up and just audit" - it's a waste of time for you to audit the code. When the attitude is not correct - run. 

**When you take a audit - check for all of the folders(src, test, script). Check them if they're correct. Check the overview and documentation.**

**Check if the protocol that you're gonna audit pass the Rekt test and answer this questions:**
https://github.com/Cyfrin/security-and-auditing-full-course-s23/blob/main/minimal-onboarding-questions.md

**Example of answered questions:**
https://github.com/Cyfrin/3-passwordstore-audit/blob/onboarded/minimal-onboarding-filled.md

**Finding Layout:**
https://github.com/Cyfrin/security-and-auditing-full-course-s23/blob/main/finding_layout.md
## High-Level Overview of The Audit Process

The smart contract audit process can be briefly summed up in these steps:  

1. **Get Context**: Understand the project, its purpose and its unique aspects.
2. **Use Tools**: Employ relevant tools to scan and analyze the codebase.
3. **Manual Reviews**: Make a personal review of the code and spot out unusual or vulnerable code.
4. **Write a Report**: Document all findings and recommendations for the development team.

## Breakdown of the Audit Process

For a more detailed perspective, let’s consider the process as broken into three distinct phases:  
**Initial Review:** The initial review of a protocol can also be broken down into 4 distinct phases.  

- **Scoping** - This is getting a sense of the protocol. In this phase, auditors go through the code to scope it. This gives an idea of how much time might be required for the audit, which can then be used to establish pricing. Key tasks include identification of all the contract’s dependencies and a general overview of the code. At this stage, auditors don’t dig deep into anything yet.
- **Reconnaissance** - Here an auditor starts walking through the code, running tools, interacting with the protocol in an effort to break it.
- **Vulnerability Identification** - An auditor determines which vulnerabilities are present and how they're exploited as well as mitigation.
- **Reporting** - Compile a report detailing all of the identified vulnerabilities and recommendations to make the protocol more secure.


> "Your job is to do whatever it takes to make the protocol more secure."
****
### Tincho process

1. Download the code
2. Read the Documentation. Try to understand it. Re-read. You need to familiarize yourself with the content and context of the codebase, learn the jargon you can expect to see in the code and become comfortable with what the protocol is expected to do.
3. Start with the more complex contract/file.
4. Start wondering how to break something in the code.
5. Use comment **## Issues** or `//` comments under each file with the issue that you find in the code on X line. Take notes and make TO-DOs. 
6. Ask questions to developers/clients.
7. Time-bound yourself.
8. Understand that there's no perfect auditor.  Try to do your best

### Tools and Frameworks

Tincho describes a number of tools he uses while performing security reviews, bring the tools you're most familiar and best with.  

- **Foundry**: As a framework for reviewing codebases Foundry is incredibly fast and allows for quick testing with it's robust test suite
- **CLOC**: A simple command-line utility that helps count lines of code which can give a sense of the complexity of different parts of the codebase.
- **Solidity Metric**: Another tool developed by Consensys that provides useful metrics about your Solidity codebase.

**Remember, there is always a risk of diving too deep into just one part of the code and losing the big picture.**

Make sure you have test suites:
1. Unit Testing
2. Fuzz Testing
3. Static Analysis
4. Formal Verification

At a high-level, formal verification is the act of proving or disproving a property of a system. It does this by generating a mathematical model of the system and using mathematical proofs to identify if a property can be broken.

There are many ways to perform formal verification including:
- Symbolic Execution
- Abstract Interpretation
- Model Checking

```php
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.16;

contract SmallSol {
    // Invariant: Must never revert.
    function f(uint256 a) public returns (uint256) {
        a = a + 1;
        return a;
    }
}
```

In the above simple contract example, the obvious path is returning the `result of a + 1`. Another less obvious path would be this function `f` reverting due to overflow. Symbolic Execution, through it's mathetmatical modelling, would traverse all possible paths, looking for criteria that break our invariant.
****
### Other way of audit process

1. Go through the Code line-by-line & Read the Docs and making sure the code actually does what's supposed to do.
2. Understand what the protocol should do. 
	 - Most bugs are actually business logic issues and the only way you'll know if business logic is wrong is if you understand what the protocol or the business should do.
	 - Repetition is the mother of skills - the more you read code, the more you read docs, the more you do this audits - the better you'll get.
****
How I manage my day-to-day tasks as a smart contract security researcher in 3 steps !:

**Step1.** Listing Tasks: I start by enumerating all the tasks that need to be completed today, for example: 
1/ Write 2 tweets 2/ Audit 150 SLOC of your current private audit 3/ Read 3 audit reports and analyze the attack vectors of the bugs ... 

**Step 2.** Prioritization: After listing the tasks, I prioritize them. Tasks requiring completion by lunchtime take precedence. For instance, submitting a report might fall into this category. On the other hand, tasks like reviewing past reports can be deferred until the evening. 

**Step 3.** Task Tracking: As I complete each task, I mark it as 'completed.' This not only provides a sense of accomplishment but also keeps my mind clear. Additionally, having a written workflow enhances organization and overall productivity. Be organised and get things done on time, make that a habit and you will be way more successful.
****
### Bare minimum onboarding questions to audit the protocol

1. Good documentation, system spceification. Easy to understand the codebase. Goodand easy setup to start the project.
2. Good test suites, scripts...
3. Scope, cloc. Commit hash. Interactions with tokens(if have), different chains, roles(buyer, seller, owner...)
4. Rekt test?
****
`pragma solidity 0.8.18; // q is this the correct compiler version?` - ask devs or answer yourself

```error PasswordStore__NotOwner();
   //  error NotOwner(); // @audit i - maybe use better error naming convention```  - informational