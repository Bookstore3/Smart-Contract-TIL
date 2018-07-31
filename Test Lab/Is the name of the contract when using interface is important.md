# Is it important to match the interface contract's name to the original?

I've always wondered if I should match the interface contract's name exactly same as the contract that I want to use.

For example, if I want to call balanceOf function from other contract that uses ERC20 token interface, do I need to match the interface name with it?

### Testing

~~~Solidity
pragma solidity ^0.4.24;

//The origianl name is just ERC20Basic, from OpenZepplin. However, I've changed it to myOwnERC20Basic
contract myOwnERC20Basic {
  function totalSupply() public view returns (uint256);
  function balanceOf(address who) public view returns (uint256);
  function transfer(address to, uint256 value) public returns (bool);
  event Transfer(address indexed from, address indexed to, uint256 value);
}

//Same goes with ERC20
contract myOwnERC20 is myOwnERC20Basic {
  function allowance(address owner, address spender) public view returns (uint256);
  function transferFrom(address from, address to, uint256 value) public returns (bool);
  function approve(address spender, uint256 value) public returns (bool);
  event Approval(address indexed owner, address indexed spender, uint256 value);
}

//Like so, the contract can interact with any contract (or Token contract) that implements the ERC20 standard.

contract ERC20NameTestContract{
    
    
    function SimpleCheckingBalance(myOwnERC20Basic _tokenAddress) public view returns(uint256 _balance){
        
        myOwnERC20Basic basicInstance = myOwnERC20Basic(_tokenAddress);
        
        return basicInstance.balanceOf(msg.sender);
        
    }
    
    
}
~~~

Then I have deployed my own test token contract, TestToken.sol

### Test Result: Successful
##### Conclusion: The name of the interface contract does not matter, it only matters when making an instance. Only thing that matters when using other contract's function using the interface is the interface function's name and structure.


