# Privacy

>The creator of this contract was careful enough to protect the sensitive areas of its storage.
>
>Unlock this contract to beat the level.
>
>Things that might help:
>
>* Understanding how storage works
>* Understanding how parameter parsing works
>* Understanding how casting works
>  
>Tips:
>
>* Remember that metamask is just a commodity. Use another tool if it is presenting problems. Advanced gameplay could involve using remix, or your own web3 provider.
>
```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Privacy {

  bool public locked = true;
  uint256 public ID = block.timestamp;
  uint8 private flattening = 10;
  uint8 private denomination = 255;
  uint16 private awkwardness = uint16(block.timestamp);
  bytes32[3] private data;

  constructor(bytes32[3] memory _data) {
    data = _data;
  }
  
  function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }

  /*
    A bunch of super advanced solidity algorithms...

      ,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`
      .,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,
      *.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^         ,---/V\
      `*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.    ~|__(o.o)
      ^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'  UU  UU
  */
}
```
Nothing is `private` in solidity. It is merely a concept that people assume is reliable. The variables declared as private are not so private as they can be read through storage access.

As long as we have knowledge about the storage layout of solidity and EVM, we can read the value of any private variable by feeding the `getStorageAt` function with contract address and the storage slot.

This `getStorageAt` function is a part of the `web3js` library.

(Some extra information! when `cast storage` is used with `foundry` to read the values stored at a particular storage slot alloted to a particular contract, it implicitly invokes `getStorageAt` function.)

In order to pass this level, All we have to do is understand how storage in solidity works and how different values are stored.

<img width="404" alt="Screenshot 2023-11-26 153349" src="https://github.com/blueh4mster/Ethernaut-writeups/assets/102573660/91d55ab8-2bae-4563-86df-aa2e9ee77014">

Here, we can see that since the value of `locked` is `true`, the value stored in the 0th slot is `0x0000000000000000000000000000000000000000000000000000000000000001`

<img width="420" alt="Screenshot 2023-11-26 153619" src="https://github.com/blueh4mster/Ethernaut-writeups/assets/102573660/846356a2-e81b-4c60-80e0-b4a301bfd03e">

Next, we have the value of variable `ID` at the slot 1 of storage.

<img width="404" alt="Screenshot 2023-11-26 153753" src="https://github.com/blueh4mster/Ethernaut-writeups/assets/102573660/a7a01053-069b-4b1d-96b5-c38f030679c2">

Next, we have `0x00000000000000000000000000000000000000000000000000000000178cff0a` stored in the slot 2. Moving on to the third varible i.e. `flattening` whose `uint8` representation is `0x0a`, fourth `denomination` as `0xff` and the rest characters being attributed to `uint16` representation of `block.timestamp` in the 5th variable `awkwardness`.

So now we are left with the bytes32 array. The next three slots in the storage each contain the 0,1 and 2 index array element. To unlock this level, we must pass the `bytes16` representation of `data[2]` (i.e. first 16 bytes of data[2]) to the `unlock` function.
This data[2] element is stored in the slot 5.

<img width="416" alt="Screenshot 2023-11-26 154444" src="https://github.com/blueh4mster/Ethernaut-writeups/assets/102573660/a1a0db6e-9fe3-4453-a7f1-be28cc357d50">

next, we have to call the `unlock` function with the first half of this value.
We can then confirm that the value of `locked` variable is now set to `false` and then submit the instance.

Further information about solidity storage layout can be read from [here](https://docs.soliditylang.org/en/latest/internals/layout_in_storage.html)


