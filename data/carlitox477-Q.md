# \[Non-Critical\] ```Auth.freezeUntil``` error message is not necessary correct
```require(newUnfreezeAt > unfreezeAt, "frozen");``` indicates that the cause of the error is that the contract is currently frozen, and this is not necessary the case. The constraint is ```newUnfreezeAt > unfreezeAt``` meaning that the new unfreeze time is greater than the current unfreeze time. The error thrown should be something like ```ERROR_NEW_FREEZE_TIME_LEQ_THAN_CURRENT_FREEZE_TIME()```
Then: ```require(newUnfreezeAt > unfreezeAt, ERROR_NEW_FREEZE_TIME_LEQ_THAN_CURRENT_FREEZE_TIME());``` would be better.

# \[Non-Critical\] ```Auth.unfreeze``` does not verifies that unfreezeAt > now 
According to comment, this contraint should be checked in the function. It should be refactor to:
```diff
function unfreeze() external onlyRole(OWNER) {
+   uint48 _unfreezeAt = unfreezeAt;
+   require(_unfreezeAt > block.timestamp, ERROR_UNFREEZE_TIME_ALREADY_ACHIEVED());
-   emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));
+   emit UnfreezeAtSet(_unfreezeAt, uint48(block.timestamp));
    unfreezeAt = uint48(block.timestamp);
}
```

# \[Non-Critical\] ```Auth.pause``` allow redundant event emission
This happens because the function does not check the current value of paused variable. ```Auth.pause``` should be refactor to:

```solidity
function pause() external onlyRole(PAUSER) {
    bool _paused = paused;
    require(!_paused, ERROR_NOT_PAUSED());
    emit PausedSet(_paused, true);
    paused = true;
}
```

# \[Non-Critical\] ```Auth.unpause``` allow redundant event emission
This happens because the function does not check the current value of paused variable. ```Auth.pause``` should be refactor to:

```solidity
function pause() external onlyRole(PAUSER) {
    bool _paused = paused;
    require(_paused, ERROR_PAUSED());
    emit PausedSet(_paused, false);
    paused = false;
}
```

# Fixed.sol: ```shiftl_toFix``` comment should be modified for better understanding
Currently, the code related to conditions for avoiding overflow is
```solidity
if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57
```

The comments are wrong, the code should be change to:
```solidity
if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**39 < FIX_MAX < 10**40
```

# Fixed.sol: ```Fixed.mul``` comment can be modified for better understanding
The [current comment](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L257) seems ambigous, it interpretation suggest that the function output should be equal to $x * (y/10^{18})$, when it really does $(x * y)/10^{18}$

The same happens with [mul(uint192,uint192) comment](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L250), which should be change to ```// as-ints: (x * y)/1e18  [division using ROUND, not FLOOR]```

# ```AssetRegistry.unregister(IAsset)```: Wrong effect comment
The effects comment is wrong, it should be change to:
```diff
-   // effects: assets' = assets - {asset.erc20():_} + {asset.erc20(), asset}
+   // effects: assets' = assets - {asset.erc20(): asset} + {asset.erc20():_} 
```

# ```TradingP1.MIN_TRADE_VOLUME``` should change its name to ```TradingP1.MIN_TRADE_VOLUME_UPPER_CONSTRAINT```
It is semantically incorrect due to what happen in function ```setMinTradeVolume```:
```solidity
function setMinTradeVolume(uint192 val) public governance {
    // @audit new min trade volume should be less than MIN_TRADE_VOLUME ? It does not sounds ok
    require(val <= MIN_TRADE_VOLUME, "invalid minTradeVolume");
    emit MinTradeVolumeSet(minTradeVolume, val);
    minTradeVolume = val;
}
```

Then ```MIN_TRADE_VOLUME``` should change its name to ```MIN_TRADE_VOLUME_UPPER_CONSTRAINT```

# ```GnosisTrade.settle``` does not check that ``` block.timestamp >= endTime ```
This is never done in the function, even though it is clearly stated in the checks comments. 

Adding a check at the begining of the function would avoid losing gas due to rest of function execution.
