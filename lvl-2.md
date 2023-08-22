# Fallout

>Claim ownership of the contract below to complete this level.

> Things that might help
> 1. Solidity Remix IDE


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

import 'openzeppelin-contracts-06/math/SafeMath.sol';

contract Fallout {
  
  using SafeMath for uint256;
  mapping (address => uint) allocations;
  address payable public owner;


  /* constructor */
  function Fal1out() public payable {
    owner = msg.sender;
    allocations[owner] = msg.value;
  }

  modifier onlyOwner {
	        require(
	            msg.sender == owner,
	            "caller is not the owner"
	        );
	        _;
	    }

  function allocate() public payable {
    allocations[msg.sender] = allocations[msg.sender].add(msg.value);
  }

  function sendAllocation(address payable allocator) public {
    require(allocations[allocator] > 0);
    allocator.transfer(allocations[allocator]);
  }

  function collectAllocations() public onlyOwner {
    msg.sender.transfer(address(this).balance);
  }

  function allocatorBalance(address allocator) public view returns (uint) {
    return allocations[allocator];
  }
}
```
We know how constructors can also be defined by defining a function with the same name as the contract, thus allowing solidity compiler to understand that this function is the constructor and is to be called only at the time of deployment.

However, in this challenge, a vulnerability has arisen due to some mistake by the developer.

The name of the constructor function is not the same as that of the contract thus allowing this function to be called repetitively like any other function.
All we need to do is call this Fal1out function.

```javascript
await contract.Fal1out()
```
After the transaction is mined, we can confirm that we have claimed the ownership of the contract and submit the instance.

