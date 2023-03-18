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
Consider a simple contract that stores a list of addresses and allows users to remove their address from the list:

![image](https://user-images.githubusercontent.com/35583758/226142650-704b5161-3f1e-4e1f-8e20-82cda3574710.png)

In this example, the `removeAddress` function iterates through the entire addresses array. If the list grows too large, the gas required to execute the function may exceed the block gas limit, rendering it unusable and effectively causing a DoS attack.

## Remediation
**Use mapping instead of arrays**: In cases where data storage and retrieval are the primary requirements, consider using mappings instead of arrays to reduce gas costs and improve performance.

**Limit iterations**: Limit the number of iterations in loops, and consider breaking down large operations into smaller transactions that can be executed separately.

**Batch processing**: Process multiple items in a single transaction or implement a mechanism that allows users to process items in smaller chunks.

![image](https://user-images.githubusercontent.com/35583758/226142743-8401b6c5-0fa6-4f9b-a557-8a25a43d6cd0.png)

In this improved version, we use a mapping instead of an array to store addresses. The `addAddress` and `removeAddress` functions now have constant gas costs and do not depend on the number of addresses stored, mitigating the block gas limit vulnerability and making the contract more resistant to DoS attacks.

