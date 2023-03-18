# Transaction Order Dependence (Front-running)

**NOTE**: Ethereum is now a POS chain and this is not as prevalent however the context of this vulnerability is still important to understand different attack flows for auditors.

Transaction Order Dependence (TOD) is a vulnerability in smart contracts that arises when the execution outcome of a contract function depends on the order of transactions. This vulnerability can be exploited by malicious actors who manipulate transaction ordering to gain an unfair advantage or disrupt the intended behavior of the smart contract.

## Impact
Transaction Order Dependence can lead to various consequences, such as:

Manipulation of contract state, resulting in unintended behavior or loss of funds.
Front-running attacks, where malicious actors exploit the transaction ordering to gain an unfair advantage in decentralized applications, such as decentralized exchanges or auctions.

## Example
Consider a simple auction contract where users place bids for an item:

![image](https://user-images.githubusercontent.com/35583758/226141545-55ceeea8-fa17-46ab-b0f6-67e6a48b9cb8.png)

In this example, the contract is vulnerable to TOD attacks. A malicious actor can monitor pending transactions and insert their own transaction with a higher gas price to ensure their transaction gets mined first. This allows the attacker to displace the original highest bidder, leading to potential loss or manipulation of auction outcomes.

## Remediation
**Use commit-reveal schemes**: Implement a commit-reveal mechanism, where users first submit a hashed version of their bid (commit) and later reveal their actual bid. This prevents attackers from knowing the bid amounts and order until the reveal phase.

**Batch processing**: Process multiple bids or actions in a single transaction, making it harder for attackers to manipulate the order.

**Randomize transaction order**: Introduce a level of randomness in processing transactions, making it more difficult for malicious actors to predict and manipulate transaction order.

Applying these remediation steps, the auction contract can be improved as follows:

![image](https://user-images.githubusercontent.com/35583758/226141531-8639ffc4-e8eb-4ae7-a0ac-c723212e0cb9.png)

