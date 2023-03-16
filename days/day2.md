# Denial of Service (Unexpected Revert)
This vulnerability arises when a function that's meant to execute a task returns an unexpected error (reverts),  due to some exceptional condition or programming mistake; halting execution of the smart contract and returning the remaining gas.

## Impact
An attacker can subvert important contract logic that depends on the full execution of a function leading to unintended consequences.

## Example
Let's analyze the following code of a simple auction contract:

This contract below allows anyone to place a bid. In order to become the current leader, you have to place a higher bid than the previous leader. Simple right?

![image](https://user-images.githubusercontent.com/35583758/225677804-0bbfda7b-76ee-4de9-8116-7f9de3245420.png)

This code assumes that anyone placing a bid is an EOA (a person or entity) not a smart contract itself. With this assumption, line 10 would always send the amount of the previous highest bid back to the previous leader. However, someone could spin up a smart contract without a receive() or fallback() function to receive ether instead of an EOA to call the bid function to become the new leader. Uh oh.

This results in lines 12-13 unable to be called and the cementing the attacker contract as the leader by preventing the full execution of the function call by anyone else due to unexpectedly reverting the ether transfer💥

## Remediation
To prevent this from happening, it's recommended to use the “Pull Over Push” pattern. This means that users can withdraw their value (pull) through a withdraw function, rather than having the value sent to them (push) as is the case with this contract
