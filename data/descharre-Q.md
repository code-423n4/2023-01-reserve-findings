# Summary
## Low Risk
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|1       |Other contract addresses can only be set once|*|
|2       |Precision loss with division|1|

## Non critical
|ID     | Finding| Instances |
|:----: | :---           |   :----:         |
|1       | Add custom error messages instead of an empty return | 5 |
|2       |Use modifier instead of duplicate require|2 |
|3       |Use a more recent version of solidity|1 |
|4       |Useless statements|1 |
|5       |Require() or revert() statement that check input arguments should be at the top of the function|4|
|6       |Place structs on top of the contract for overall readability|6|
|7       |Use defined values instead of variables and computations for constants|5|
|8       |Combine constants and variables with the exact same value|1|
|9       |Event is missing indexed fields|3|
|10      |Using safemath when compiler is ^0.8.0|2|
|11      |Not using the named return variables in a function|2|
|12      |Miscellaneous|1|
# Details
## Low Risk
## 1 Other contract addresses can only be set once
Other contract addresses can only be set once in the initializer. There is no other setter method for this. This makes it so there is no room for error and can lead to redeployment.

To fix this you can make a setter method for each address that only the owner can call.
## 2 Precision loss with division
When using division with small numbers there can be a precision loss because all integer divisions round down to the nearest integer. To fix this you can make use of multiplier that needs to be accounted for when working with it in the future.

[Distributor.sol#L99](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L99)

[StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L386)
```solidity
L386:  uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

L401:  uint256 draftRSRToTake = (draftRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

L417:  seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;
```
## Non critical
## 1 Add custom error messages instead of an empty return
To make error handling better use custom error messages instead of returning nothing.
[BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L109)
```solidity
L109:  if (tradesOpen > 0) return;

L114:  if (block.timestamp < basketTimestamp + tradingDelay) return;
```
[StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L310)
```solidity
L310:  if (endId == 0 || firstId >= endId) return;

L327:  if (rsrAmount == 0) return;

L497:  if (block.timestamp < payoutLastPaid + rewardPeriod) return;
```
## 2 Use modifier instead of duplicate require
[ComponentRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L37)
```solidity
L37:   require(address(val) != address(0), "invalid RToken address");

L45:   require(address(val) != address(0), "invalid StRSR address");

L53:   require(address(val) != address(0), "invalid AssetRegistry address");

L61:   require(address(val) != address(0), "invalid BasketHandler address");

L69:   require(address(val) != address(0), "invalid BackingManager address");

L77:   require(address(val) != address(0), "invalid Distributor address");

L85:   require(address(val) != address(0), "invalid RSRTrader address");

L93:   require(address(val) != address(0), "invalid RTokenTrader address");

L101:   require(address(val) != address(0), "invalid Furnace address");

L109:   require(address(val) != address(0), "invalid Broker address");
```
Even if they have different error messages you can still make a modifier
```diff
+   modifier checkAddress0(IRToken val, string memory errormessage){
+       require(address(val) != address(0), errormessage);
+       _;
+   }

L36:
+    function _setRToken(IRToken val) private checkAddress0(val, "invalid RToken address"){
-    function _setRToken(IRToken val) private {
-        require(address(val) != address(0), "invalid RToken address");
        emit RTokenSet(rToken, val);
        rToken = val;
    }
```

Same thing can be done for:

[StRSRVotes.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L77): can be done without the parameter errormassage because error message is the same
```solidity
L77, L83, L89:   require(blockNumber < block.number, "ERC20Votes: block not yet mined");
```
## 3 Use a more recent version of solidity

## 4 Useless staments
[BasketHandler.sol#L556](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556)

TargetIndex will 100% of the time be smaller than targetsLength because it's specified like that in the for loop.

## 5 Require() or revert() statement that check input arguments should be at the top of the function
This way no gas is wasted when the function is destined to fail.

- [BasketHandler.sol#L660](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L660)
- [RToken.sol#L448](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L448)
- [StRSR.sol#L259](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L259)
- [StRSR.sol#L305](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L305)
## 6 Place structs on top of the contract for overall readability
- [Distributor.sol#L67-L71)](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L67-L71)

## 7 Use defined values instead of variables and computations for constants
When it's a constant value it's best to use a 
[RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L15)

```solidity
L15:   uint192 constant MIN_BLOCK_ISSUANCE_LIMIT = 10_000 * FIX_ONE;

L18:  uint192 constant MAX_ISSUANCE_RATE = FIX_ONE;
```
[StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L39)
```solidity
L39   uint192 public constant MAX_REWARD_RATIO = FIX_ONE;

L65:  uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE;

L87   uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE;
```

## 8 Combine constants and variables with the exact same value
If two constants have the exact same value, see if it's a possibility to make it one variable

[StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L39)
```solidity
L65:  uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE;

L87   uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE;
```

## 9 Event is missing indexed fields
Because of this it can be hard for off-chain scripts to efficiently filter those events.
- [IDeployerRegistry.sol#L7-L9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L7-L9)
- [IDistributor.sol#L28](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L28)
- [IRToken.sol#L83](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L83)

## 10 Using safemath when compiler is ^0.8.0
The compiler has built-in under and overflow checks so there is no need to use SafeMath
- [ERC20.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol)
- [StaticATokenLM.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol)
## 11 Not using the named return variables in a function
- [AssetRegistry.sol#L162-L17](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L162-L177): variable swapped is never used
- [RToken.sol#L634-L636](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L634-L636): variables left and right are never used
## Miscellaneous
### Use for loop instead of duplicated code
For better readability it's better to use a for loop to avoid duplicated code.

[Fixed.sol#L495-L521](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L495-L521)
```solidity
        r *= 2 - z * r;
        r *= 2 - z * r;
        r *= 2 - z * r;
        r *= 2 - z * r;
        r *= 2 - z * r;
        r *= 2 - z * r;
        r *= 2 - z * r;
        r *= 2 - z * r;
```