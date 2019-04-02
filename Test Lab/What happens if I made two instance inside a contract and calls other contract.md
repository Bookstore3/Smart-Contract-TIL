# What happens if I make a two instances in one contract about other contract and give it a different value?

In C++ if I make a different instance about a class, they will hold diffrent value if I set each value to different one.

For exapmle

~~~C++

class someValue{

  int tmp;
  
}

int main(){

  someValue a = new someValue();
  someValue b = new someValue();

  a.tmp = 10;
  b.tmp = 20;

  cout<<a.tmp<<endl;
  cout<<b.tmp<<endl;

}

~~~

If I make a code like above, the instance 'a' will have 'tmp' that has the value of 10, while instance 'b' will have 'tmp' that has the value of 20.

I was curious if this would be the same thing in solidity so I've tried to mimic the situation.

~~~Solidity
pragma solidity 0.4.24;

contract InstanceTestObjectContract{
    
    uint256 _myValue;
    
    function setValue(uint _theValue) public {
        
        _myValue = _theValue;
    }
    
    function getValue() public view returns (uint256 returnValue){
        returnValue = _myValue;
        return returnValue;
    }
    
    
}
~~~

This contract above is like the 'class' from C++ contract.
It holds uint256 _myValue and has a Set and Get function.

~~~Solidity
pragma solidity ^0.4.24;

/*
* Used as an interface of the ITOC
*/
contract InstanceTestObjectContract{
    
    function setValue(uint _theValue) public;
    function getValue() public view returns (uint256 returnValue);
    
}

contract InstanceTest{
    
    address _InstanceTestContractAddress;
    InstanceTestObjectContract a1;
    InstanceTestObjectContract a2;
    
    constructor (address _InstanceAddress) public {
        _InstanceTestContractAddress = _InstanceAddress;
        a1 = InstanceTestObjectContract(_InstanceTestContractAddress);
        a2 = InstanceTestObjectContract(_InstanceTestContractAddress);
    }
    
    function setOtherContractInstance() public {
        
        a1.setValue(101);
        a2.setValue(202);
        
    }
    
    function getOtherContractInstance() public view returns(uint256 returnedA1, uint256 returnedA2){
        
        returnedA1 = a1.getValue();
        returnedA2 = a2.getValue();
        
    }
    
}
~~~

So I've made a contract where we set the InstanceTestObjectContract, which will work as class, address as soon as this contract deploys.
Also it will make two instance about the InstanceTestObjectContract, a1 and a2.

I've also made a two function, Set and Get where set will use a1 to set the value inside the InstanceTestObjectContract to 101, while a2 will set the value to 202.
Then with the Get function it will return both a1's value and a2'S value.

### Test Result: They returns the a2's set value 202.
##### Reason: The reason why both a1 and a2 instance returns the value of a2 is because they both point at the same contract.
Unlike C++, where if you make a instance with the key word New it will make a new memory space for that specific New instance, the InstanceTestObjectContract only has one address.
So since they (a1 and a2) points at the same address, the later Set, a2.setValue(202), will overwrite the a1's value.

##### If I want to make a situation like C++ class, then I should deploy another same InstanceTestObjectContract, which will give another address and allocate that address to a2.
Then, a1 and a2 will point at differenct InstanceTestObjectContract, they will have the different value.

This could have easily predicted if I understood the memory allocation in C++ and address concept in solidity but now I do.
