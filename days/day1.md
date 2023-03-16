# Transaction Order Dependence (Front-running)
This vulnerability exists due to the ability of one running a node (a miner) being able to see which transactions will occur before being finalized. 

## Impact
The miner can then front-run the sender of the initial transaction by simply paying a higher gas fee.

This results in a race condition that gives preference to the payer of the highest gas and is very problematic when it comes to smart contracts that have answer-based reward mechanisms

## Example
Let's look at an example ⬇️

Let's say that a smart contract exists that gives a reward of 0.1 $ETH for solving a math problem.

John submits the correct answer and pays 50 GWEI in gas to make the transaction.

However, Mark runs his own node and can view the transaction before it has been mined 👀

Mark views John's transaction, copies his answer, and submits another transaction paying 100 GWEI.

As a result of paying higher gas and front-running John, the following occurs:

- Mark wins and receives 0.1 $ETH
- John loses and receives nothing

## Remediation
In order to remediate this:

A contract could use a commit hash reveal scheme allowing one to commit a value while keeping it hidden.

However the best mitigation would be to remove the importance of order or timing in a contract altogether when someone submits a transaction
