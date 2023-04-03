# Oracle Price Manipulation

Oracle price manipulation vulnerability occurs when a smart contract relies on an external data source (an oracle) to provide information, such as prices for tokens, and an attacker manipulates that data source to provide false information, leading to unintended consequences in the smart contract execution. Oracles are third-party services that provide external data to smart contracts since they cannot access off-chain information by themselves.

## Impact
An attacker who successfully manipulates an oracle's data can exploit the smart contract, leading to various undesirable outcomes, such as the theft of funds, incorrect token swaps, or unintended liquidations. Oracle price manipulation vulnerability can severely undermine the trust in a smart contract, as users cannot rely on the correctness of the information provided by the oracle.

## Example
Consider a simple smart contract that relies on an oracle for determining the exchange rate between two tokens:

![image](https://user-images.githubusercontent.com/35583758/229410631-8bb342d7-7d2a-4ad5-8da4-5a587a4925ed.png)

In this example, the smart contract TokenSwap relies on the getExchangeRate() function provided by an external oracle to determine the exchange rate between tokenA and tokenB. If an attacker can manipulate the oracle's data and alter the exchange rate, they can exploit the smart contract and exchange tokens at a rate that is beneficial to them, but detrimental to other users.

Let's consider an attacker that has control over a malicious oracle and can manipulate the exchange rate provided by the oracle. The attacker can then exploit the TokenSwap contract to trade tokens at an unfair rate:

![image](https://user-images.githubusercontent.com/35583758/229410956-c1a7238b-a9a7-4eb1-a727-45f80dff34d1.png)

This malicious oracle contract allows the attacker to set a manipulated exchange rate through the `setManipulatedExchangeRate` function.

1. Deploy the `MaliciousOracle` contract to the blockchain.
2. Deploy the `TokenSwap` contract, passing the address of the `MaliciousOracle` contract as the oracle parameter in the constructor.
3. Set the manipulated exchange rate in the `MaliciousOracle` contract to a rate that is favorable for the attacker, e.g., a very low rate for selling tokenA and buying tokenB.
4. Interact with the `TokenSwap` contract's `swapTokens` function, passing an amount of tokenA to be sold. The contract will use the manipulated exchange rate, allowing the attacker to buy tokenB at a significantly lower price than the fair market rate.
5. Repeat this process to exploit the vulnerability and accumulate a large amount of tokenB at an unfair rate.

## Remediation
**Use multiple trusted oracles:** Instead of relying on a single oracle, you can implement a system that uses multiple trusted oracles to fetch data. This reduces the risk of price manipulation, as an attacker would need to manipulate multiple oracles.

**Use a decentralized oracle network:** Decentralized oracle networks, such as Chainlink, provide more security and resilience against oracle manipulation. Integrating your smart contract with a decentralized oracle network can significantly reduce the risk of price manipulation attacks.

**Verify the integrity of the oracle data:** Implement mechanisms to ensure the data provided by the oracle is within reasonable bounds. You can use historical data and statistical methods to verify if the oracle's data is within an acceptable range.

To mitigate the oracle price manipulation vulnerability, here is us using a decentralized oracle network like Chainlink, which provides secure and reliable price feeds. Here's the updated `TokenSwap` contract:

![image](https://user-images.githubusercontent.com/35583758/229411383-a08ad242-1ce8-47d8-9342-bf77bebbf149.png)

In this mitigated contract, we have made the following changes:

1. We have imported the `AggregatorV3Interface` from the Chainlink library, which is a standardized interface for interacting with price feeds provided by Chainlink.

2. Instead of relying on a single custom oracle, we're now using the Chainlink's decentralized oracle network, which aggregates data from multiple sources and ensures a more secure and reliable exchange rate.

3. We have replaced the custom oracle with the `AggregatorV3Interface` instance called `priceFeed`. This instance will hold the address of the Chainlink price feed contract for our desired token pair.

4. The `getLatestPrice` function retrieves the latest price from the Chainlink price feed using the `latestRoundData` function. This price is more secure and reliable than a single custom oracle, as it is based on data from multiple sources and secured by the decentralized oracle network.

By making these changes, we have significantly reduced the risk of oracle price manipulation attacks. The updated `TokenSwap` contract now relies on a decentralized and secure price feed provided by Chainlink, which makes it much more difficult for an attacker to manipulate the exchange rate for their benefit.

