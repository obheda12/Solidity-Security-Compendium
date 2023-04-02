# Signature Malleability
In blockchain systems, digital signatures are used to prove that a message or transaction comes from a specific person and has not been tampered with. Signature Malleability is a vulnerability that can occur when improperly utlizing ECDSA (Elliptic Curve Digital Signature Algorithm) signatures. The vulnerability allows an attacker to change the signature slightly without invalidating the signature itself. This often happens when a smart contract doesn't validate signatures properly, enabling attackers to modify them and potentially bypass security measures.

In Ethereum, the `ecrecover` function is used to verify signatures. It is a pre-compiled contract that performs public key recovery for elliptic curve cryptography. This means it can recover a public key (address) from a given signature. 

In ECDSA, a digital signature is represented by a pair of values (`r`, `s`). These values, along with another value called recovery identifier (`v`), are crucial to the signature verification process.

The signature is in form of a 65-byte long string, however, these bytes are always later split to three components as briefly touched on above:

- last one byte — `v`
- first 32 bytes — `r`
- second 32 bytes — `s`


`v` is the last byte, while `r` and `s` is simply the signature split in half. The `v` is the recovery ID, which can have two values: 0 or 1, corresponding to two sides of the y-axis.

![image](https://user-images.githubusercontent.com/35583758/229375008-254586ef-177c-4111-9aa6-60d42ff6a251.png)

- `r`: A value derived from the elliptic curve point (x, y) generated during the signing process. The `r` value is the x-coordinate of that point.
- `s`: A value calculated during the signing process using the private key, message hash, and `r`. The `s` value is meant to prove that the signer has knowledge of the private key without revealing it.
- `v`: The recovery identifier, which is used to recover the correct public key (address) from the signature. 

The ecrecover function, by itself, doesn't provide protection against signature malleability attacks. ECDSA signatures are malleable, meaning they can be altered without invalidating the signature. For every valid signature (`r`, `s`), there exists another valid signature with a different `s` value, let's call it `s`. In Ethereum, the `v` value can also be changed accordingly. This is because the elliptic curve is symmetric, and for each point (x, y) on the curve, there's a corresponding point (x, -y) that maintains the same relationship.

An attacker can exploit this vulnerability by altering the `v`, `r`, and `s` values of a signature and still pass the ecrecover verification.The function takes four arguments: the hash of the message (hash), the recovery identifier (`v`), and two values `r` and `s` that make up the signature.

## ELI5
Imagine you have a magical safe that can only be opened by you using a secret password. You also have a special stamp (your signature) that lets everyone know that you approved something. One day, you decide to send a letter to your friend, asking them to give you some gold. You stamp the letter with your special signature so they know it's really from you.

However, there's a mischievous leprechaun named Owen who knows how your magical stamp works. He figures out that he can create a different-looking stamp that still proves it's from you. Here's how he does it step by step:

1. Owen intercepts your letter before it reaches your friend. He carefully examines your signature and figures out how to make a new stamp that looks different but still works like your original signature.
2. Owen uses this new stamp on a different letter, asking your friend to give him some gold instead.
3. He sends the modified letter to your friend. Now, your friend receives the letter with the different-looking signature, but they still believe it's from you because the magical box can be opened with the signature.
4. Your friend gives the gold to Owen, thinking they're helping you.

In this example, the magical box is like the `ecrecover` function in Solidity, which checks if a signature is valid. Your stamp is the signature (`v`, `r`, `s`) values, and the mischievous leprechaun Owen is the attacker manipulating the signature. The toy represents the action you want to perform in a smart contract.

To protect against this kind of attack, you would need a more secure way of creating and checking your signature, like using a well-tested library or adding extra conditions to your magical stamp. This way, Owen wouldn't be able to trick your friend into giving away the toy.

## Impact
The signature malleability vulnerability can lead to:

1. Unauthorized actions or transactions in a smart contract.
2. Tampering with signature data to avoid security checks.
3. Pretending to be someone else or falsely attributing actions to others.

## Example
OpenZeppelin's ECDSA library, in its older versions, did not implement specific checks to mitigate signature malleability attacks. However, it is essential to note that OpenZeppelin has since improved its library to handle such vulnerabilities.

Here's an example of a vulnerable smart contract that uses an older version of OpenZeppelin's ECDSA library:

![image](https://user-images.githubusercontent.com/35583758/229375957-023d2b02-4bbf-402a-85e5-496fe3dbc243.png)

In this contract, users can claim tokens by providing a signature that proves the owner's approval for the specified amount. The contract relies on OpenZeppelin's older ECDSA library to recover the signer's address from the signature but doesn't validate the s value or use a secure signature validation method.

Here's how an attacker could exploit this vulnerability:

![image](https://user-images.githubusercontent.com/35583758/229376161-a00fd985-04fa-4125-8a16-00e42446c6a0.png)

1. The owner signs a message approving a user to claim 100 tokens.
2. The attacker intercepts this message and modifies the signature by finding an alternative (`r`, `s'`) pair and adjusting the `v` value accordingly.
3. The attacker sends a transaction to the claimTokens function with the manipulated signature.
4. The contract accepts the manipulated signature because it still passes the ecrecover check, allowing the attacker to claim tokens they shouldn't have access to.
5. To mitigate this vulnerability, the smart contract should use the latest version of OpenZeppelin's ECDSA library, which includes specific checks against signature malleability:

## Remediation
To fix the signature malleability vulnerability, follow these steps:

- Make sure to enforce a specific condition on the s value during the signature validation process, ensuring it has a low value. This prevents attackers from manipulating the signature.
- Use a well-tested library like OpenZeppelin's ECDSA.sol that already implements a secure signature validation process.

Here's an improved version of the contract using the OpenZeppelin library:

In this updated contract, we've replaced the custom isValidSignature function with the OpenZeppelin ECDSA.sol library. This library enforces a low s value during signature validation, ensuring a secure implementation that is resistant to signature malleability attacks. The hash.recover(signature) function, which comes from the library, securely checks the signature and returns the address of the signer. The function then checks if the returned address matches the expected signer, ensuring the signature's validity.

## Resources for Study
- https://www.youtube.com/watch?v=V3TJLDHZBFU&t=636s&ab_channel=OwenThurm
- https://cryptogennepal.com/blog/signature-malleability-vulnerabilities-in-smart-contracts/
- https://hackernoon.com/what-is-the-math-behind-elliptic-curve-cryptography-f61b25253da3
- https://eklitzke.org/bitcoin-transaction-malleability
- https://journal0xrusowsky.substack.com/p/digital-signatures-ecrecover
- https://swcregistry.io/docs/SWC-117
- https://github.com/ethereumbook/ethereumbook/blob/develop/04keys-addresses.asciidoc

