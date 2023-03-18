# Improper Access Control 
Access control vulnerabilities in Solidity occur when a smart contract does not properly restrict access to sensitive functions or data. This can lead to unauthorized actions being performed by malicious users or other contracts, compromising the contract's security and integrity. Common access control issues include improper use of function visibility, missing access restrictions, and the reliance on the tx.origin value for authentication.

## Impact
The impact of access control vulnerabilities includes:

1. Unauthorized access to sensitive data or functions, potentially leading to theft of funds or manipulation of contract state.
2. Loss of user confidence in the contract, which may lead to reduced adoption or usage.
3. Damage to the reputation of the contract creator or associated organizations.

## Example
Consider a simple contract that allows the owner to update a stored value:

![image](https://user-images.githubusercontent.com/35583758/226143155-b250c01d-48e8-4d22-a568-48fa17564d54.png)

In this example, the updateValue function is missing access control checks, allowing anyone to update the storedValue variable. This can lead to unauthorized manipulation of the contract's state.

## Remediation
**Use function modifiers**: Implement custom function modifiers that check the caller's access rights before executing sensitive functions.

**Properly set function visibility**: Ensure that function visibility is set correctly (i.e., public, external, internal, or private) to restrict access as needed.

**Avoid relying on tx.origin for authentication**: Use msg.sender instead of tx.origin to check the caller's identity, as tx.origin can be manipulated by malicious contracts. (This is covered as well in the compendium as its own vulnerability).

The SimpleStorage contract can be improved as follows

![image](https://user-images.githubusercontent.com/35583758/226143221-5ae7c9b1-b00c-470d-96c7-0ecf1345dfa4.png)

In this improved version, we implement a custom function modifier `onlyOwner` to ensure that only the contract `owner` can call the `updateValue` function and nobody else. This mitigates the access control vulnerability and makes the contract more secure.
