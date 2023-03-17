# Improper EOA Checks
The `EXTCODESIZE` opcode is used in the Ethereum Virtual Machine (EVM) to determine the size of an account's code. For example, if the size of the caller's code is greater than 0, then the address according to `EXTCODESIZE` is assumed to be a contract. If not, then it is considered an EOA. 
However, relying on `EXTCODESIZE` to differentiate between EOAs and smart contracts can lead to potential vulnerabilities and unexpected behavior as this opcode can be subverted in certain sceenarios. The check for `EXTCODESIZE > 0` is false for:
 
- A contract in construction 
- An externally-owned account
- An address where a contract will be created
- An address where a contract was previously but is not destroyed

It is not recommended to use `EXTCODESIZE` for this purpose as someone could subvert the intended logic. A common means to subvert this type of check for a contract is to deploy a contract and perfrom the necessary malicious actions in the contructor so the contract performing the verification still assumes the the size of the code is still 0.

## Impact
Using `EXTCODESIZE` to check for EOAs can result in vulnerabilities due to the potential for false positives or negatives. For example, a smart contract with empty code would be incorrectly identified as an EOA. This could lead to security risks, as smart contracts and EOAs have different properties and expected behaviors.

## Example

A vulnerable code example using `EXTCODESIZE`:
![image](https://user-images.githubusercontent.com/35583758/226029937-3a7569f8-66ea-487c-8021-652adbe410f7.png)

In this example, the `isEOA` function checks if the input address is an EOA by checking if the code size is 0. This method is vulnerable because it could return false positives for contracts with empty code.

Remediation:
To correctly identify EOAs and contracts, you should use the address.code.length method provided by Solidity, or the staticcall opcode, which is more secure and reliable.

![image](https://user-images.githubusercontent.com/35583758/226033879-4c3975ae-042b-4928-ae48-fca2261fd886.png)

In the remediated example, the `isEOA` function uses staticcall to check if the input address is an EOA. This method returns true if the call is unsuccessful and the result length is 0. This approach is more secure and reliable than relying on `EXTCODESIZE`.
