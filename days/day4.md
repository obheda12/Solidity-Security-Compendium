# Reentrancy
This vulnerability is introduced when an external call is performed to another contract as it can lead to a change in control flow and changes to the data of the calling function that were not intended

![image](https://user-images.githubusercontent.com/35583758/225740444-32e13a79-fef6-4d15-8a1d-0f84dad57cb7.png)

## Impact
A malicious actor can exploit this vulnerability in a smart contract that allows them to repeatedly call back into the contract before the previous invocation has completed, effectively "re-entering" the contract and executing additional code. This can lead to unexpected behavior and can be used to steal funds or execute other malicious actions.

## Example
This code allows users to withdraw funds from their balance.

Where in this code could we see a means for an attacker to overtake the execution logic and perform their own malicious actions?

![image](https://user-images.githubusercontent.com/35583758/225740874-4882d86e-e99c-44e6-97f5-3f194d4b74aa.png)

There are two things we should take note here:

1) A call is performed to the contract or EOA performing the withdraw function to receive their funds.

2) After the funds are received a state change (balance update) occurs.

An attacker can call withdraw from a smart contract with a fallback function. A fallback will execute any arbitrary code (given sufficient gas) upon receipt of any amount of ether. 

Who controls the execution logic then? The attacker's contract can include a call to the withdraw function again within their fallback function. Thereby controlling the execution logic and performing the withdraw repeatedly until the contract is empty 💥

Due to the `balances[msg.sender]` check occurring before the transfer, the attacker can continue to loop and re-enter the withdraw function without ever updating their balance in the contract to drain all avaliable funds 👀

## Remediation
One should use the check-effects-interaction pattern where all state changes should be made BEFORE interacting with external contracts or functions.

For an additional resource on this simple form of reentrancy and the CEF pattern - please give the following article a read: https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html

Lastly here's the previously vulnerable code - now mitigated with the CEF pattern.

![image](https://user-images.githubusercontent.com/35583758/225741106-dcbe516d-7f03-4967-95b5-dfb17a75fdf7.png)
