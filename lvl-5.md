# Token

>The goal of this level is for you to hack the basic token contract below.
>
>You are given 20 tokens to start with and you will beat the level if you somehow manage to get your hands on any additional tokens. Preferably a very large amount of tokens.

>Things that might help:
> - What is an odometer?


```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Token {

  mapping(address => uint) balances;
  uint public totalSupply;

  constructor(uint _initialSupply) public {
    balances[msg.sender] = totalSupply = _initialSupply;
  }

  function transfer(address _to, uint _value) public returns (bool) {
    require(balances[msg.sender] - _value >= 0);
    balances[msg.sender] -= _value;
    balances[_to] += _value;
    return true;
  }

  function balanceOf(address _owner) public view returns (uint balance) {
    return balances[_owner];
  }
}
```
In Solidity, integers are represented using a fixed number of bits. For example, uint16 uses 16 bits and can store integer values from 0 to 131071 (2^15-1).
When an integer is incremented beyond its maximum value or decremented below its minimum value, an overflow or underflow occurs, respectively.

In other words, integer overflow occurs when we try to store a value in a uint variable which is greater than its bytes size.

In this challenge, we are required to increase our token balance. To do that, we simply call the transfer function with `_value` being 21 and `_to` being any arbitrary address. 

`balance[msg.sender] - _value` evaluates to be -1, however, since -1 is not in the range of uint256, integer underflow occurs and `balance[msg.sender] - _value` becomes the maximum integer in the range set of uint256 i.e. `2^255 - 1`.
Executing the above approach,

```javascript
await contract.transfer("0x4E238321ed92d96AcEe377EB607FAc8C845aAC75", 21)
```

We can now check our token balance by calling the balanceOf function and then submit the instance.
