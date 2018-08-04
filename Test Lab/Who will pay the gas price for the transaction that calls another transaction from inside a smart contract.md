# Who will pay the gas price for the transaction that calls another transaction from inside a smart contract

The gas price for account A to send account B is about 21000 in Ropsten testnet.
If so, what will happen if A send to smart contract C then the C immediately sends the value to account B?
The situation will involve in two transactions, one from A to C contract and another from C contract to B.

We can guess that the first transaction will be paid by A.
Then what about the second transaction?
Will the smart contract pay the fee for it?

So does that mean that if the C doesn't have enough Ether, then the whole transaction will fail?

Let's find out.

~~~Solidity
pragma solidity ^0.4.24;

contract InstantTransferTest{
    
    address instantPaidaddress = 0x9983F1926d3E19Fb9fb699af41fA3fb4DfA59EB0;
    
    event Log(string _message, uint256 _amount);
    
    function() public payable {
       emit Log("Sending money to Master CA", msg.value);
       instantPaidaddress.transfer(msg.value);
    }
    
    function getBalance() public view returns(uint256 _ethBalance){
        return _ethBalance = address(this).balance;
    }
    
}
~~~

So I have prepared a situation where when the InstantTransferTest contract receives Ether to its payable function(fallback), 
it will immediately send the value to pre appointed address.

The appointed address is the second account address from the Remix in Java Script Environment.

Also, in order to get the balance of the contract, I've made a simple function that returns the balance of the contract.
Since we have not sent any Ether to this contract, and even if we did it would go immediately to the appointed account, the balance would be 0.

### Test Result: The account that sends the Ether through this contract pays the fee for the whole transaction, not the contract.

##### Conclusion: 
When we make a contract that sends the Ether to others, it will not be recorded as normal transaction. 
It will use something called 'internal transaction' which is used when someone makes the transaction to the contract and that transaction provokes another transaction inside the contract.
The internal transaction will be not recorded as individually, rather it will be recorded as a data value. Basically it's similar with Call. Also, gas price for account A to send account B is about 21000 in Ropsten testnet, while gas price for account A to send to B through C is about 31000. So it is little bit more expensive. 

