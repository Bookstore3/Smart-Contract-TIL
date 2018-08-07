# What is the difference between tx.origin and msg.sender in solidity

In solidity there are two key words, tx.origin and msg.sender.

If we have studied or made some smart contract before, the key word 'msg.sender' wouldn't be that unfamiliar.
However, the 'tx.origin' on the other hand, is not quite familiar word.

In solidity transaction, there could be a chain of transactions in one transaction.

For example, if person A called the 'b' function in the contract B, and that 'b' function calls another smart contract C, 
then we have 2 internal transaction within one transaction. 

In this case, the msg.sender for contract B is A and the msg.sender for contract C is B and the tx.origin in this case is the person A.
Like the meaning of the word 'origin', the subject that casued this chain of the transaction becomes the tx.origin.

tx.origin is not a recommanded way to track the original msg.sender due to some dangerous situation it could cause when used on smart contract.
Below contract is the example of abusing the tx.origin.

~~~Solidity
pragma Solidity ^0.4.18;

contract OriginTestContract{ // Has a mapping structure that manages the token amount

  function victimFunction(address _to, uint256 _value){
    balance[tx.origin].sub(_value);
    balance[_to].add(_value);
  }

}

contract maliciousContract{ // Imported OriginTestContract's interface

  address originTestContractInstance; // pre made OriginTestContract's instance

  function() payable{
    originTestContractInstance.victimFunction(attackerAddress, 1000);
  }

}

~~~

There are two contracts above. One is the OriginTestContract which involves in managing the token of the clients. 
Another one is the maliciousContract which was made by the attacker and we will call the attacker a Cypher.

Now, as you can see in OriginTestContract's victimFunction, the function subs the _value amount of token from the tx.origin and give it to
_to address.

Let's say in this situation, the Cypher knows someone, let's say Gabriel, who has some tokens in OriginTestContract. So he exchange some services or merchants with him
and tell him to send the Ether to his own wallet address which is in fact a maliciousContract address.

So Gabriel sends the Ether to Cypher's maliciousContract address. Then, it will be sent to fallback function since the transaction couldn't 
particularly specify the method. Then it will call victimFunction as internal transaction.

Inside the OriginTestContract, the victimFunction will be called and the token amount of tx.origin, which is Gabriel's token, will be sent
to the parameter _to, which will be the attackerAddress.

In conclusion, Cypher now has successfully made Gabriel to transafer 1000 amount of token to his address without having Gabriel involved directly with the token contract, OriginTestContract.

This is the reason why using tx.origin is not recommanded when tracking down the origin of the transaction.
We should rather give the address of the origin through parameter with msg.sender to prevent from abusing.
