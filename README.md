# The Compendium
This initiative serves to break out every single solidity vulnerability that has thus far been identified, codify / categorize them, and then present the information into a detailed and concise source of knowledge for the solidity developer and auditor community.

This initiative will be documented live on my twitter: https://twitter.com/theweb3hacker

Day | Topic | Overview
--- | --- | ---
**1** | [Transaction Order Dependence](/days/day1.md) | When a smart contract requires that, two transactions should be submitted and executed in the same order to get the same results.
**2** | [Denial of Service (Unexpected Revert)](/days/day2.md) | When a function that's meant to execute a task returns an unexpected error (reverts),  due to some exceptional condition or programming mistake; halting execution of the smart contract and returning the remaining gas.
**3** | [Denial of Service (Block Gas Limit)](/days/day3.md) | This vulnerability exists when an excessive amount of gas is used to execute a transaction causing it to fail due to reaching the maximum gas limit, effectively rendering the contract unusable and causing a denial of service for other users.
**4** | [Reentrancy - Simple](/days/day4.md) | This vulnerability is introduced when an external call is performed to another contract as it can lead to a change in control flow and changes to the data of the calling function that were not intended
**5** | [Reentrancy - Cross-Functional](https://medium.com/valixconsulting/solidity-smart-contract-security-by-example-04-cross-function-reentrancy-de9cbce0558e) | This form of reentrancy occurs when a function can be called recursively and another function is called in between the calls. 
**6** | [Reentrancy - Cross-Contract](https://medium.com/valixconsulting/solidity-smart-contract-security-by-example-05-cross-contract-reentrancy-30f29e2a01b9) | This form of reentrancy occurs when multiple contracts share the same state variable, and some contracts update that variable insecurely.
**7** | [Selfdestruct (Unexpected Ether)](https://twitter.com/theweb3hacker/status/1626821990144172033) | The selfdestruct function removes all bytecode from the contract address and sends all ether stored there to the address specified by the parameter called with the function. If the specified address is also a contract, no functions (including the fallback) get called.
**8** | [Improper Checks for EOA](/days/day8.md) |
