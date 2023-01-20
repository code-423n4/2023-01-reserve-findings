## QA Report

| |Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | USE OF FLOATING PRAGMA | 3 |
| [L-2](#L-2) | `setMinTradeVolume` METHOD USES INCORRECT REQUIRE CHECK | 1 |
| [L-3](#L-3) | WRONG OPERATOR USED | 1 |
| [NC-1](#NC-1) | USE REQUIRE INSTEAD OF ASSERT | 20 |
| [NC-2](#NC-2) | EVENT IS MISSING INDEXED FIELDS | 1 |
| [NC-3](#NC-3) | SPELLING MISTAKE IN NATSPEC | 2 |

###  [L-1] USE OF FLOATING PRAGMA

Impact: [swcregistry](https://swcregistry.io/docs/SWC-103)

```solidity
File: libraries/Fixed.sol

3:    pragma solidity ^0.8.9;

```
[Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L3)

```solidity
File: plugins/aave/ERC20.sol

3:    pragma solidity ^0.6.0;

```

[Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L3)

```solidity
File: plugins/aave/ReentrancyGuard.sol

3:    pragma solidity >=0.6.0 <0.8.0;

```

[Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L3)

### [L-2] `setMinTradeVolume` METHOD USES INCORRECT REQUIRE CHECK

As `MIN_TRADE_VOLUME` is already set by default, the `minTradeVolume` assigned by the `governance` should be greater than or equal to `MIN_TRADE_VOLUME` and not less than or equal to.

```solidity
File: p1/mixins/Trading.sol

135:       function setMinTradeVolume(uint192 val) public governance {
136:            require(val <= MIN_TRADE_VOLUME, "invalid minTradeVolume");
137:            emit MinTradeVolumeSet(minTradeVolume, val);
138:            minTradeVolume = val;
139:        }

```
[Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L135-L139)

### [L-3] WRONG OPERATOR USED

In `discharge` method of `RedemptionBatteryLib`, the function should return if anyone of `battery.redemptionRateFloor` or `battery.scalingRedemptionRate` equals zero as suggested in Natspec.

So `&&` operator should be replaced with `||` operator.

```solidity
File: p1/mixins/RedemptionBattery.sol

25:       // for either: set to 0 to disable

38:       if (battery.redemptionRateFloor == 0 && battery.scalingRedemptionRate == 0) return;

```
[Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RedemptionBattery.sol#L38)

### [NC-1] USE REQUIRE INSTEAD OF ASSERT

Assert should not be used except for tests, require should be used.

*Intances (20):*
* [`BackingManager.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249)
* [`BasketHandler.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556)
* [`StRSR.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696)
* [`RecollateralizationLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110)
* [`RewardableLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78)
* [`RewardableLib.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L102)
* [`TradeLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44)
* [`TradeLib.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L108)
* [`TradeLib.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L168)
* [`TradeLib.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L170)
* [`Trading.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L114)
* [`StaticATokenLM.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L345)
* [`Asset.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L98)
* [`Asset.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L112)
* [`Asset.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L147)
* [`FiatCollateral.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L137)
* [`RTokenAsset.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L74)
* [`GnosisTrade.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L63)
* [`GnosisTrade.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L98)
* [`GnosisTrade.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L182)

### [NC-2] EVENT IS MISSING INDEXED FIELDS

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*Instances (1):*
* [`IDistributor.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L28)

### [NC-3] SPELLING MISTAKE IN NATSPEC

Spelling of Auction should be corrected.
Spelling of Maintain should be corrected.

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

47:      // from this trade's acution will all eventually go to origin.

```
* [Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L47)

```solidity
File: p1/BackingManager.sol

89:      /// Mointain the overall backing policy; handout assets otherwise

```
* [Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L89)