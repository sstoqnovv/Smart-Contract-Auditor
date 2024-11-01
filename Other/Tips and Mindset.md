### Improve yourself

1. Read Audit reports in Code4rena, Sherlock. Understand the codebase.
2. Solve various smart contract CTFs to gain valuable knowledge in security research
3. Taking notes and stay informed. Learn more.

**TIP:** To find more vulnerabilities get the **Attacker's Mindset**(Looking thje codebase and try to break as many things as possible).

**TIP:** To improve your knowledge for audits, reports, gas..., read a lot of reports and of course practice.

**TIP:** To learn more about Web-3 Security it's for example you want to learn English - you must read books/news in English, watch videos/podcasts/movies in English, talk in English(practice). In Web-3 Security world you must read a lot of reports, spend time on Twitter, read a lot of stuff, practice, surround yourself with people that are in Web-3 Security as you. Read books. Focus and you will be able to find more vulnerabilities.

**TIP:** Impact is the most importhant thing. Every time you find a vulnerability, you need to explain how the protocl can be impacted, how the protocol can lose money, how can users lose money, how the protocol can be DoS. Explain it well Give recommendations too. Don't overexplain your finding. Because the Judge is a human-being and he need to read all of the reports.
 
**Audit Tip:**
1. Read the Architecture of the code
2. Read with attention the code and to find vulnerabilities
3. Time! You're in contest(competition)!
4. If you find something impossible for you - just move. Don't stick to it.
5. Put your focused effort(5-6 hours) for a  bigger problems and 2-3 hours for smaller ones.

**MINDSET:** If you find something hard(for learning, reports, etc.), remember that this is hard for other people too. Other people find it hard as you and this is normal. It's hard for everyone. You just mustn't give up. If someone is better than you, it's probably, because he has faced many more challenges and problems than you and he has more experience.

**MINDSET**: 
1. If someone else found a vulnerability, you can find it too!  
2. You must need to learn the knwoledge they know and learn the questions they asked their thought, patterns to find the same vulnerability.
3. Give yourself permission to make mistakes and learn from them
4. Try to be kind, be helpful, be a blessing to others
5. Believe in yourself that you can do this

**MINDSET:** Even if you don't find any issues in the code - the clients can be happy again. When you're auditing - ask qestions linked with the codebase/protocol.

**Auditing Mindset**:

Auditing is invisible puzzle
1. Focus on what the contract's supposed to do and compare it to what's doing
2. Focus on business logic and intention
3. Think in terms of *"Going home one state to another"* when interacting with a contract. How it is changing the state when it's performing externall calls for example.
4. Understand all of the contracts fully, let the information sink in and step over it
5. Make some diagrams and take notes

**Mental Models**:

1. **Comparison mode**: take the function's name and compare the current implementation with existing/standard ones.
2. **Mirror mode**: Takes linked/opposite variables/functions and compare how the're coded
3. **Speedrun mode**: Focus on asset(the money - where are they, are they transferred), Actors(power of roles and normal users), Actions
4. Understand the protocol/expected scenarios through tests and play around with them
5. ***"What should happen and  What shouldn't happen" models***: Understand the system and its specifications, invariants, properties. Find flaws in the flow. Poke at the code. Fuzz it.
6. Ask a lot "Why". Read something, re-read, simplify it.

### How to find clients?

You must use Twitter, LinkedIn. You market yourself! This is your marketing. Tweet a lot and a valuable content(for solo auditing). You must be able to wait. PROVIDE A VALUE!
To attract clients: Use Twitter, post Web-3 Security related stuff, give a valuable content.

### How to find vulnerabilities

*"My auditing process goes like this:"* First I read the documents and try to understand what the contract is supposed to do and to get an idea of the protocol. What service are they offering?(token swap, oracle service). You must try to gather as much information aspossible about the code you're going to audit.
After that I take look at the code that I have to audit, starting with the smaller contracts and going forward with the bigger ones. I start looking at the codebase line-by-line again starting with the smaller contracts. Read every little detail! You must understand everything.

### Audit Tips

1. Read light/general/overview documents if available, but don't get lost in documents.
2. Take notes on actors/entities in the system interactions, where value is stored and how it flows through the system
3. Start reading the code bottom-up, choose and understand smaller files, if there's an ERC-20/ERC-721 token I almost start there.
4. Want to understand the building blocks first to build up a mental model of how the system fits together.
5. Use @audit comments tags to ask questions about code, comeback and answer them once you understand more of the system.

### How to find critical issue in every private audit

1. Choose a standart medium-severity issue
2. Crank it up to critical severity issue

## What does your auditing process look like? - CMichel

My auditing process is pretty straightforward. First I read the documentation. Then I read the code from top to bottom, I order the contracts in a way that makes sense for me: for example, I read the base class contract first before I read the derived class contract. I don’t use any tools, but I heavily take notes and scribble all over the code. 😃 I’m using the “Solidity Visual Developer” extension which comes with the `@audit`, `@audit-info`, `@audit-ok`, `@audit-issue` markers which I all use to categorize my notes. After having read the entire codebase once, I revisit my notes and resolve any loose ends or things I didn’t understand earlier. Afterwards, I create my audit report out of these notes.

