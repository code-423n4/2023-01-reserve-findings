# QA REPORT

## [L-1] Use of `block.timestamp`

### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the *[Entropy Illusion](https://hacken.io/discover/most-common-smart-contract-vulnerabilities/#Entropy_Illusion)* for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### ✅ Recommendation
Use oracle instead of `block.timestamp`

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/mixins/Auth.sol#L102|[return block.timestamp < unfreezeAt;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L102 )|
|protocol/contracts/mixins/Auth.sol#L108|[return paused || block.timestamp < unfreezeAt;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L108 )|
|protocol/contracts/mixins/Auth.sol#L123|[freezeUntil(uint48(block.timestamp) + shortFreeze);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L123 )|
|protocol/contracts/mixins/Auth.sol#L140|[freezeUntil(uint48(block.timestamp) + longFreeze);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L140 )|
|protocol/contracts/mixins/Auth.sol#L158|[emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L158 )|
|protocol/contracts/mixins/Auth.sol#L159|[unfreezeAt = uint48(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L159 )|
|protocol/contracts/p1/BackingManager.sol#L114|[if (block.timestamp < basketTimestamp + tradingDelay) return;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L114 )|
|protocol/contracts/p1/BasketHandler.sol#L637|[timestamp = uint48(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L637 )|
|protocol/contracts/p1/Furnace.sol#L42|[lastPayout = uint48(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L42 )|
|protocol/contracts/p1/Furnace.sol#L71|[if (uint48(block.timestamp) < uint64(lastPayout) + period) return;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L71 )|
|protocol/contracts/p1/Furnace.sol#L74|[uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L74 )|
|protocol/contracts/p1/StRSR.sol#L180|[payoutLastPaid = uint48(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L180 )|
|protocol/contracts/p1/StRSR.sol#L313|[require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L313 )|
|protocol/contracts/p1/StRSR.sol#L436|[uint256 time = block.timestamp;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L436 )|
|protocol/contracts/p1/StRSR.sol#L497|[if (block.timestamp < payoutLastPaid + rewardPeriod) return;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L497 )|
|protocol/contracts/p1/StRSR.sol#L498|[uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L498 )|
|protocol/contracts/p1/StRSR.sol#L561|[availableAt = uint64(block.timestamp) + unstakingDelay;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L561 )|
|protocol/contracts/p1/StRSR.sol#L775|[require(block.timestamp <= deadline, "ERC20Permit: expired deadline");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L775 )|
|protocol/contracts/p1/StRSRVotes.sol#L126|[require(block.timestamp <= expiry, "ERC20Votes: signature expired");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L126 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L144|[require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L144 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L172|[require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L172 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L212|[require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L212 )|
|protocol/contracts/plugins/assets/Asset.sol#L95|[lastSave = uint48(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L95 )|
|protocol/contracts/plugins/assets/Asset.sol#L137|[uint48 delta = uint48(block.timestamp) - lastSave; // {s}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L137 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L44|[// _whenDefault > block.timestamp: delayed default may occur as soon as block.timestamp.](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L44 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L46|[// _whenDefault <= block.timestamp: default has already happened (permanently)](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L46 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L134|[lastSave = uint48(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L134 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L171|[} else if (_whenDefault > block.timestamp) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L171 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L183|[if (_whenDefault <= block.timestamp) return; // prevent DISABLED -> SOUND/IFFY](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L183 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L188|[uint256 sum = block.timestamp + uint256(delayUntilDefault);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L188 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L195|[_whenDefault = uint48(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L195 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L200|[return _whenDefault <= block.timestamp;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L200 )|
|protocol/contracts/plugins/assets/OracleLib.sol#L25|[// Downcast is safe: uint256(-) reverts on underflow; block.timestamp assumed < 2^48](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L25 )|
|protocol/contracts/plugins/assets/OracleLib.sol#L26|[uint48 secondsSince = uint48(block.timestamp - updateTime);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L26 )|
|protocol/contracts/plugins/mocks/BadCollateralPlugin.sol#L40|[lastSave = uint48(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadCollateralPlugin.sol#L40 )|
|protocol/contracts/plugins/mocks/ChainlinkMock.sol#L38|[latestTimestamp = block.timestamp;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L38 )|
|protocol/contracts/plugins/mocks/ChainlinkMock.sol#L41|[getTimestamp[latestRound] = block.timestamp;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L41 )|
|protocol/contracts/plugins/mocks/ChainlinkMock.sol#L42|[getStartedAt[latestRound] = block.timestamp;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L42 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L20|[block.timestamp < auctionData[auctionId].auctionEndDate,](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L20 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L28|[block.timestamp < auctionData[auctionId].orderCancellationEndDate,](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L28 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L39|[block.timestamp >= auctionEndDate &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L39 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L174|[require(auctionEndDate > block.timestamp, "auction end date must be in the future");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L174 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L622|[if (auctionData[auctionId].auctionEndDate < block.timestamp) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L622 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L625|[return auctionData[auctionId].auctionEndDate.sub(block.timestamp);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L625 )|
|protocol/contracts/plugins/mocks/GnosisMock.sol#L81|[block.timestamp,](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L81 )|
|protocol/contracts/plugins/mocks/GnosisMock.sol#L100|[require(auction.endTime <= block.timestamp, "too early to close auction");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L100 )|
|protocol/contracts/plugins/mocks/GnosisMockReentrant.sol#L50|[block.timestamp,](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol#L50 )|
|protocol/contracts/plugins/mocks/GnosisMockReentrant.sol#L61|[require(auction.endTime <= block.timestamp, "too early to close auction");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol#L61 )|
|protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol#L40|[block.timestamp,](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L40 )|
|protocol/contracts/plugins/mocks/RTokenCollateral.sol#L23|[// whenDefault > block.timestamp: delayed default may occur as soon as block.timestamp.](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol#L23 )|
|protocol/contracts/plugins/mocks/RTokenCollateral.sol#L25|[// whenDefault <= block.timestamp: default has already happened (permanently)](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol#L25 )|
|protocol/contracts/plugins/mocks/RTokenCollateral.sol#L56|[if (whenDefault <= block.timestamp) return;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol#L56 )|
|protocol/contracts/plugins/mocks/RTokenCollateral.sol#L72|[} else if (whenDefault > block.timestamp) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol#L72 )|
|protocol/contracts/plugins/trading/GnosisTrade.sol#L103|[endTime = uint48(block.timestamp) + auctionLength;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L103 )|
|protocol/contracts/plugins/trading/GnosisTrade.sol#L223|[return status == TradeStatus.OPEN && endTime <= block.timestamp;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L223 )|
---





## [N-1] `require()/revert()` statements should have descriptive reason strings.

### Description
Descriptive reason strings should be used so that users can troubleshot any reverted calls.

### ✅ Recommendation
You should add descriptive messages.

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/plugins/assets/Asset.sol#L102|[if (errData.length == 0) revert(); // solhint-disable-line reason-string](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L102 )|
|protocol/contracts/plugins/assets/Asset.sol#L116|[if (errData.length == 0) revert(); // solhint-disable-line reason-string](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L116 )|
|protocol/contracts/plugins/assets/Asset.sol#L133|[if (errData.length == 0) revert(); // solhint-disable-line reason-string](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L133 )|
|protocol/contracts/plugins/assets/FiatCollateral.sol#L149|[if (errData.length == 0) revert(); // solhint-disable-line reason-string](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L149 )|
|protocol/contracts/plugins/assets/RTokenAsset.sol#L78|[if (errData.length == 0) revert(); // solhint-disable-line reason-string](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L78 )|
|protocol/contracts/plugins/mocks/BadCollateralPlugin.sol#L51|[if (errData.length == 0) revert(); // solhint-disable-line reason-string](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadCollateralPlugin.sol#L51 )|
|protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol#L32|[revert(); // Revert with no reason](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L32 )|
---


## [N-2] For modern and more readable code, update import usages.

### Description
Solidity code is cleaner in the following way: On the principle that clearer code is better code, you should import the things you want to use. Specific imports with curly braces allow us to apply this rule better. Check out this [article](https://betterprogramming.pub/solidity-tutorial-all-about-imports-c65110e41f3a)

### ✅ Recommendation
Import like this: `import {contract1 , contrat2c} from "filename.sol";`

### 🔍 Findings:
All import statements

---

## [N-3] Compliance with solidity styler rules in `constant` expressions.

### Description
Variables declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format..

### ✅ Recommendation
TODO: Conform to the style guide

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/p1/StRSR.sol#L45|[uint8 public constant decimals = 18;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L45 )|
|protocol/contracts/p1/StRSR.sol#L126|[bytes32 private constant _PERMIT_TYPEHASH =](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L126 )|
|protocol/contracts/p1/StRSRVotes.sol#L27|[bytes32 private constant _DELEGATE_TYPEHASH =](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L27 )|
|protocol/contracts/plugins/aave/ReentrancyGuard.sol#L33|[uint256 private constant _NOT_ENTERED = 1;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L33 )|
|protocol/contracts/plugins/aave/ReentrancyGuard.sol#L34|[uint256 private constant _ENTERED = 2;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L34 )|
|protocol/contracts/plugins/mocks/ChainlinkMock.sol#L17|[uint256 public constant override version = 0;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L17 )|
|protocol/contracts/plugins/mocks/GnosisMock.sol#L50|[uint256 public constant feeNumerator = 0; // Does not support a fee](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L50 )|
---
