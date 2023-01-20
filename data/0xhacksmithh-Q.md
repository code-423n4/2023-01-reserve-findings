### [Low-01] Old solidity version used
-Use a solidity version of at least 0.8.13 to get the ability to use `using for`
 with a list of free functions

-Use a solidity version of at least 0.8.12 to get `string.concat()`
 instead of `abi.encodePacked(<str>,<str>)`

*Instances(ALL)*



### [Low-02] Different solidity version used
Following contracts using solidity ^0.8.9
```solidity
file:: libraries/Fixed.sol
```
And all other contracts using solidity 0.8.0

### [Low-03] Precision loss
preforming division before multiplication cause precision loss

```solidity
file:: contracts/p1/BackingManager.sol
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L232
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L233
```