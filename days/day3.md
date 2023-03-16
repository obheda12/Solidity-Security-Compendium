# Denial of Service (Block Gas Limit)
At the time of this post, the block gas limit for the Ethereum main network is about 30,000,000. A transaction that consumes more gas than that cannot be mined at all - which can lead to a denial of service.

This vulnerability exists when an excessive amount of gas is used to execute a transaction causing it to fail due to reaching the maximum gas limit, effectively rendering the contract unusable and causing a denial of service for other users.

## Impact
There are 2 forms of this vulnerability:

1. Gas Limit DoS - Unbounded Contract Operation
This DoS occurs when someone is able to arbitrarily inflate the amount of gas without bounds to exceed the Block Gas Limit and render a function uncallable.

2. Block Stuffing
This DoS occurs when an attacker fills a block with a large number of transactions, which may be invalid or have a low fee, forcing the network to include the invalid transactions and congest the network. As long as those transactions specify a generous enough gas price, rational miners will include the attacker’s transactions at the exclusion of all others.

## Unbounded Contract Operation Example
he following is a simple contract that allows anyone to invest tokens and only the owner to distribute tokens back to investors (stored in an array of arbitrary size). Key word: arbitrary size 👀

![image](https://user-images.githubusercontent.com/35583758/225690860-52729a23-8667-4d0b-b7b1-67d0c6e059b6.png)

An attacker can expand the investors[] array to a large number of arbitrary size by making many investor accounts such that the gas required to execute the for loop exceeds the block gas limit, making the distribute() function inoperable.

## Remediation
Avoid `for` loops where possible, unless they’re bounded by a small constant number of iterations. For example, an auction often has a deadline for accepting bids, so a DoS attack could stop people from bidding. Ensure the goods being auctioned off are not valuable enough that a block stuffing attack becomes financially viable.
