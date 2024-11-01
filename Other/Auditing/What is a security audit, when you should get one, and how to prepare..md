## **How to prepare**

### **Document all of the things!**

**Have a specification**
 - Any product _should_ have documentation that technically and explicitly details the requirements that need to be satisfied. It is crucial in understanding whether or not the technical implementation satisfies the requirements of the project, and what unintended consequences may exist based on that implementation.

**Describe how to interact with it**
 - **So every product needs documentation (we all know most hate this part). Remember, a vendor (or new contributor!) needs to go from zero to hero in as little time as possible, and documentation is the road that is used to get there.** Documentation is more high-level and explains how things fit together, how it is used, etc. Specification details what the product should do (and how) to meet requirements while documentation describes what it actually does.

**Perform threat modeling**
 - Okay! You documented how to use it, you specified exactly how it’s built, and you detailed the intended users that interact with it. Now it’s time to view things from a different lens, that of an “attacker.” A short description of threat modeling is thinking about what an adversary to your system would try to do, where they would try to go to do “bad” things, and what they would have to break in order to “succeed” at it.

### **Test it yourself**

**Perform User Stories**
 - Make sure that the experience is what you expect it to be for individual use cases. A good starting checklist of things to do is what your specification details.

**Color Outside the Lines**
 - That’s the first part, making sure the system does what it is intended for with an acceptable experience. The next step is to try and break it in any way you can possibly see it breaking. Attempt to access things that should not be accessed. Put in random characters where there should be numbers, go nuts. The system should fail gracefully in every attempt outside of the defined scope because users WILL attempt to do things outside of what you expect them to do.

**Create a Testing Corpus**
 - I am not going to belabor you on the various kinds of testing frameworks that are available, but I will point out some things that should be done. Not only should you test what the system should and should not do, but these tests should be codified and run whenever changes are made. That way, if an introduction of a change breaks something that was previously working, you can catch it quickly and fix it before it becomes a problem.
 - The process of testing your system (and documenting your testing) helps you in a variety of ways. First, it allows you to have a higher level of confidence that the code works as intended, and is resilient to new changes breaking that confidence. The more robust a testing framework is, the higher that confidence.

**Use Automated Testing**
 - It is also advised to take advantage of various automated testing software to increase your testing corpus in a manner not feasible by manual testing. Manual testing is done to catch the things you can intuit about how the system works, automated testing is used to try and catch the other stuff.

### **Steps to an Assessment**
**Should you even get an audit?
 - Here are some of the questions that you should consider when evaluating whether an assessment is needed:
	- How much risk is in the system, and how does that compare to overall risk in the organization or ecosystem it is a part of?
	- How complex is the system?
	- Does the system make use of technologies that require specific expertise to implement, and do you have those resources internally?
	- How does the potential cost of an assessment compare to the value (or risk) the system will hold?
	- What is the potential cost of system failure?**

**Define the scope
 - In order for you to properly engage with someone to audit your codebase, you must first define what their task would be. Auditors are people with specialized skill sets for hire. It is your job to come to the table with expectations.
 - That means before engaging them, you should explicitly define what it is that you want them to look at, and the types of questions you want answered as a result**

**Have a budget prepared**
 - Now that you know what you want looked at and come to the conclusion that you can not do it yourselves, you need to have a budget to pay someone to do it.

**Write a Request for Proposals (RfP) document**

The next step in the process of getting an external assessment is to bring it all together into a single document. This document is typically referred to as a Request for Proposals document. Here is a typical outline of such a document:

- **Introduction:** who are we as an organization
- **Project Description:** context of the project in question for assessment
- **Resources:** material and documentation useful for understanding the project
- **Scope:** the scope of work to be done
- **Engagement Description:** what you picture the entire engagement to look like
- **Deliverables:** what you expect to be the results of the assessment
- **Indemnification and Fee Structure:** how are people getting paid
- **Selection Criteria:** what you are looking for in an external assessment organization
- **Bidding Instructions:** how does one go about submitting a proposal
- **Conclusion:** closing remarks and miscellaneous


**Set up channels of communication**
 - You are going to need to communicate with the vendors throughout the selection process and during the assessment. It is worthwhile to set up secure communications beforehand to facilitate that as well as prepare your team for what to expect.

