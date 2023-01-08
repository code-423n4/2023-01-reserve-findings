

## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
contracts/libraries/Fixed.sol::3 => pragma solidity ^0.8.9;
contracts/plugins/aave/ERC20.sol::3 => pragma solidity ^0.6.0;
contracts/plugins/aave/ReentrancyGuard.sol::3 => pragma solidity >=0.6.0 <0.8.0;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
contracts/mixins/Auth.sol::102 => return block.timestamp < unfreezeAt;
contracts/mixins/Auth.sol::108 => return paused || block.timestamp < unfreezeAt;
contracts/mixins/Auth.sol::123 => freezeUntil(uint48(block.timestamp) + shortFreeze);
contracts/mixins/Auth.sol::140 => freezeUntil(uint48(block.timestamp) + longFreeze);
contracts/mixins/Auth.sol::158 => emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));
contracts/mixins/Auth.sol::159 => unfreezeAt = uint48(block.timestamp);
contracts/p1/BackingManager.sol::114 => if (block.timestamp < basketTimestamp + tradingDelay) return;
contracts/p1/BasketHandler.sol::637 => timestamp = uint48(block.timestamp);
contracts/p1/Furnace.sol::42 => lastPayout = uint48(block.timestamp);
contracts/p1/Furnace.sol::71 => if (uint48(block.timestamp) < uint64(lastPayout) + period) return;
contracts/p1/Furnace.sol::74 => uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;
contracts/p1/StRSR.sol::180 => payoutLastPaid = uint48(block.timestamp);
contracts/p1/StRSR.sol::313 => require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");
contracts/p1/StRSR.sol::436 => uint256 time = block.timestamp;
contracts/p1/StRSR.sol::497 => if (block.timestamp < payoutLastPaid + rewardPeriod) return;
contracts/p1/StRSR.sol::498 => uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;
contracts/p1/StRSR.sol::561 => availableAt = uint64(block.timestamp) + unstakingDelay;
contracts/p1/StRSR.sol::775 => require(block.timestamp <= deadline, "ERC20Permit: expired deadline");
contracts/p1/StRSRVotes.sol::126 => require(block.timestamp <= expiry, "ERC20Votes: signature expired");
contracts/plugins/aave/StaticATokenLM.sol::144 => require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
contracts/plugins/aave/StaticATokenLM.sol::172 => require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
contracts/plugins/aave/StaticATokenLM.sol::212 => require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
contracts/plugins/assets/Asset.sol::95 => lastSave = uint48(block.timestamp);
contracts/plugins/assets/Asset.sol::137 => uint48 delta = uint48(block.timestamp) - lastSave; // {s}
contracts/plugins/assets/FiatCollateral.sol::44 => // _whenDefault > block.timestamp: delayed default may occur as soon as block.timestamp.
contracts/plugins/assets/FiatCollateral.sol::46 => // _whenDefault <= block.timestamp: default has already happened (permanently)
contracts/plugins/assets/FiatCollateral.sol::134 => lastSave = uint48(block.timestamp);
contracts/plugins/assets/FiatCollateral.sol::171 => } else if (_whenDefault > block.timestamp) {
contracts/plugins/assets/FiatCollateral.sol::183 => if (_whenDefault <= block.timestamp) return; // prevent DISABLED -> SOUND/IFFY
contracts/plugins/assets/FiatCollateral.sol::188 => uint256 sum = block.timestamp + uint256(delayUntilDefault);
contracts/plugins/assets/FiatCollateral.sol::195 => _whenDefault = uint48(block.timestamp);
contracts/plugins/assets/FiatCollateral.sol::200 => return _whenDefault <= block.timestamp;
contracts/plugins/assets/OracleLib.sol::25 => // Downcast is safe: uint256(-) reverts on underflow; block.timestamp assumed < 2^48
contracts/plugins/assets/OracleLib.sol::26 => uint48 secondsSince = uint48(block.timestamp - updateTime);
contracts/plugins/trading/GnosisTrade.sol::103 => endTime = uint48(block.timestamp) + auctionLength;
contracts/plugins/trading/GnosisTrade.sol::223 => return status == TradeStatus.OPEN && endTime <= block.timestamp;
```

## [L-03] safeApprove() is deprecated

safeApprove() is deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead

```
contracts/p1/BackingManager.sol::75 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
contracts/p1/BackingManager.sol::76 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
contracts/p1/RevenueTrader.sol::61 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), 0);
contracts/p1/RevenueTrader.sol::62 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), bal);
contracts/p1/mixins/Trading.sol::116 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);
contracts/p1/mixins/Trading.sol::117 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);
contracts/plugins/aave/StaticATokenLM.sol::101 => ASSET.safeApprove(address(pool), type(uint256).max);
contracts/plugins/trading/GnosisTrade.sol::136 => IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), 0);
contracts/plugins/trading/GnosisTrade.sol::137 => IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), initBal);
```

## [L-04] decimals() not part of ERC20 standard

decimals() is not part of the official ERC20 standard and might fail for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

```
contracts/p1/BackingManager.sol::228 => int8(IERC20Metadata(address(erc20s[i])).decimals())
contracts/p1/BasketHandler.sol::421 => int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),
contracts/plugins/aave/StaticATokenLM.sol::89 => _setupDecimals(IERC20Detailed(aToken).decimals());
contracts/plugins/assets/Asset.sol::58 => erc20Decimals = erc20.decimals();
contracts/plugins/assets/CTokenFiatCollateral.sol::28 => referenceERC20Decimals = IERC20Metadata(erc20.underlying()).decimals();
contracts/plugins/assets/OracleLib.sol::30 => return shiftl_toFix(uint256(p), -int8(chainlinkFeed.decimals()));
contracts/plugins/assets/RTokenAsset.sol::37 => erc20Decimals = erc20_.decimals();
contracts/plugins/trading/GnosisTrade.sol::106 => worstCasePrice = shiftl_toFix(req.minBuyAmount, -int8(buy.decimals())).div(
contracts/plugins/trading/GnosisTrade.sol::107 => shiftl_toFix(req.sellAmount, -int8(sell.decimals()))
contracts/plugins/trading/GnosisTrade.sol::125 => DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))
contracts/plugins/trading/GnosisTrade.sol::202 => uint192 clearingPrice = shiftl_toFix(boughtAmt, -int8(buy.decimals())).div(
contracts/plugins/trading/GnosisTrade.sol::203 => shiftl_toFix(adjustedSoldAmt, -int8(sell.decimals()))
```

## [L-05] pragma experimental ABIEncoderV2 is deprecated

Use pragma abicoder v2 instead

```
contracts/plugins/aave/IAaveIncentivesController.sol::3 => pragma experimental ABIEncoderV2;
contracts/plugins/aave/IStaticATokenLM.sol::3 => pragma experimental ABIEncoderV2;
contracts/plugins/aave/RayMathNoRounding.sol::3 => pragma experimental ABIEncoderV2;
contracts/plugins/aave/StaticATokenLM.sol::3 => pragma experimental ABIEncoderV2;
```

## [L-06] require() should be used instead of assert()

require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking

```
contracts/p1/BackingManager.sol::249 => assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
contracts/p1/BasketHandler.sol::556 => assert(targetIndex < targetsLength);
contracts/p1/StRSR.sol::696 => assert(totalStakes + amount < type(uint224).max);
contracts/p1/mixins/RecollateralizationLib.sol::110 => assert(doTrade);
contracts/p1/mixins/RewardableLib.sol::78 => assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
contracts/p1/mixins/RewardableLib.sol::102 => assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
contracts/p1/mixins/TradeLib.sol::44 => assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
contracts/p1/mixins/TradeLib.sol::108 => assert(
contracts/p1/mixins/TradeLib.sol::168 => assert(errorCode == 0x11 || errorCode == 0x12);
contracts/p1/mixins/TradeLib.sol::170 => assert(keccak256(reason) == UIntOutofBoundsHash);
contracts/p1/mixins/Trading.sol::114 => assert(address(trades[sell]) == address(0));
contracts/plugins/aave/StaticATokenLM.sol::345 => assert(amt == amountToWithdraw);
contracts/plugins/assets/Asset.sol::98 => assert(low == 0);
contracts/plugins/assets/Asset.sol::112 => assert(low <= high);
contracts/plugins/assets/Asset.sol::147 => assert(lotLow <= lotHigh);
contracts/plugins/assets/FiatCollateral.sol::137 => assert(low == 0);
contracts/plugins/assets/RTokenAsset.sol::74 => assert(low <= high);
contracts/plugins/trading/GnosisTrade.sol::63 => assert(status == TradeStatus.PENDING);
contracts/plugins/trading/GnosisTrade.sol::98 => assert(origin_ != address(0));
contracts/plugins/trading/GnosisTrade.sol::182 => assert(isAuctionCleared());
```

## [L-08] Events not emitted for important state changes

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

```
contracts/p1/BasketHandler.sol::75 => function setFrom(Basket storage self, Basket storage other) internal {
contracts/p1/Distributor.sol::61 => function setDistribution(address dest, RevenueShare memory share) external governance {
contracts/p1/StRSR.sol::803 => function setName(string calldata name_) external governance {
contracts/p1/StRSR.sol::807 => function setSymbol(string calldata symbol_) external governance {
contracts/plugins/trading/GnosisTrade.sol::169 => function settle()
```

## [L-09] _safeMint() should be used rather than _mint() wherever possible

Some tokens do not implement the ERC20 standard properly but are still accepted by most code that accepts ERC20 tokens. For example Tether (USDT)'s transfer() and transferFrom() functions do not return booleans as the specification requires, and instead have no return value. When these sorts of tokens are cast to IERC20, their function signatures do not match and therefore the calls made, revert. Use OpenZeppelin’s SafeERC20's safeTransfer()/safeTransferFrom() instead

```
contracts/p1/RToken.sol::268 => _mint(recipient, amtRToken);
contracts/p1/RToken.sol::559 => _mint(recipient, amtRToken);
contracts/p1/RToken.sol::791 => _mint(account, amtRToken);
contracts/p1/StRSR.sol::230 => _mint(account, stakeAmount);
contracts/p1/StRSR.sol::694 => function _mint(address account, uint256 amount) internal virtual {
contracts/p1/StRSRVotes.sol::137 => function _mint(address account, uint256 amount) internal override {
contracts/p1/StRSRVotes.sol::138 => super._mint(account, amount);
contracts/plugins/aave/ERC20.sol::261 => function _mint(address account, uint256 amount) internal virtual {
contracts/plugins/aave/StaticATokenLM.sol::305 => _mint(recipient, amountToMint);
```

## [L-28] Upgradeable contract is missing a __gap[50] storage variable to allow for new storage variables in later versions

__gap is empty reserved space in storage that is recommended to be put in place in upgradeable contracts. It allows new state variables to be added in the future without compromising the storage compatibility with existing deployments

```
contracts/libraries/Permit.sol::4 => import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";
contracts/mixins/Auth.sol::4 => import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";
contracts/mixins/ComponentRegistry.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/p1/AssetRegistry.sol::182 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/BackingManager.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/BasketHandler.sol::736 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/Broker.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/Distributor.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/Furnace.sol::106 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/Main.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/p1/RToken.sol::5 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/RevenueTrader.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/StRSR.sol::4 => import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";
contracts/p1/StRSRVotes.sol::4 => import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";
contracts/p1/mixins/Component.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/p1/mixins/Trading.sol::4 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
contracts/plugins/trading/GnosisTrade.sol::6 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
```

## [L-28] Implementation contract may not be initialized

Implementation contract does not have a constructor with the initializer modifier therefore may be uninitialized. Implementation contracts should be initialized to avoid potential griefs or exploits.

```
contracts/interfaces/IMain.sol::4 => import "@openzeppelin/contracts-upgradeable/access/IAccessControlUpgradeable.sol";
contracts/interfaces/IRToken.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/IERC20MetadataUpgradeable.sol";
contracts/interfaces/IStRSR.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/IERC20MetadataUpgradeable.sol";
contracts/interfaces/IStRSRVotes.sol::4 => import "@openzeppelin/contracts-upgradeable/governance/utils/IVotesUpgradeable.sol";
contracts/libraries/Permit.sol::4 => import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";
contracts/mixins/Auth.sol::4 => import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";
contracts/mixins/ComponentRegistry.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/p1/AssetRegistry.sol::182 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/BackingManager.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/BasketHandler.sol::736 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/Broker.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/Distributor.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/Furnace.sol::106 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/RToken.sol::5 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/RevenueTrader.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/StRSR.sol::4 => import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";
contracts/p1/StRSRVotes.sol::4 => import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";
contracts/p1/mixins/Trading.sol::4 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
contracts/plugins/trading/GnosisTrade.sol::6 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
```

## [N-1] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
contracts/interfaces/IAsset.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IAssetRegistry.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IBackingManager.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IBasketHandler.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IBroker.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IComponent.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IDeployer.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IDeployerRegistry.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IDistributor.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IFacadeAct.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IFacadeRead.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IFacadeTest.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IFacadeWrite.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IFurnace.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IGnosis.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IMain.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IRToken.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IRevenueTrader.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IRewardable.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IStRSR.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IStRSRVotes.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/ITrade.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/ITrading.sol::2 => pragma solidity 0.8.9;
contracts/interfaces/IVersioned.sol::2 => pragma solidity 0.8.9;
contracts/libraries/Array.sol::2 => pragma solidity 0.8.9;
contracts/libraries/Fixed.sol::3 => pragma solidity ^0.8.9;
contracts/libraries/Permit.sol::2 => pragma solidity 0.8.9;
contracts/libraries/RedemptionBattery.sol::2 => pragma solidity 0.8.9;
contracts/libraries/String.sol::2 => pragma solidity 0.8.9;
contracts/mixins/Auth.sol::2 => pragma solidity 0.8.9;
contracts/mixins/ComponentRegistry.sol::2 => pragma solidity 0.8.9;
contracts/mixins/Versioned.sol::2 => pragma solidity 0.8.9;
contracts/p1/AssetRegistry.sol::2 => pragma solidity 0.8.9;
contracts/p1/BackingManager.sol::2 => pragma solidity 0.8.9;
contracts/p1/BasketHandler.sol::2 => pragma solidity 0.8.9;
contracts/p1/Broker.sol::2 => pragma solidity 0.8.9;
contracts/p1/Deployer.sol::2 => pragma solidity 0.8.9;
contracts/p1/Distributor.sol::2 => pragma solidity 0.8.9;
contracts/p1/Furnace.sol::2 => pragma solidity 0.8.9;
contracts/p1/Main.sol::2 => pragma solidity 0.8.9;
contracts/p1/RToken.sol::2 => pragma solidity 0.8.9;
contracts/p1/RevenueTrader.sol::2 => pragma solidity 0.8.9;
contracts/p1/StRSR.sol::2 => pragma solidity 0.8.9;
contracts/p1/StRSRVotes.sol::2 => pragma solidity 0.8.9;
contracts/p1/mixins/Component.sol::2 => pragma solidity 0.8.9;
contracts/p1/mixins/RecollateralizationLib.sol::2 => pragma solidity 0.8.9;
contracts/p1/mixins/RewardableLib.sol::2 => pragma solidity 0.8.9;
contracts/p1/mixins/TradeLib.sol::2 => pragma solidity 0.8.9;
contracts/p1/mixins/Trading.sol::2 => pragma solidity 0.8.9;
contracts/plugins/aave/ERC20.sol::3 => pragma solidity ^0.6.0;
contracts/plugins/aave/IAToken.sol::2 => pragma solidity 0.6.12;
contracts/plugins/aave/IAaveIncentivesController.sol::2 => pragma solidity 0.6.12;
contracts/plugins/aave/IStaticATokenLM.sol::2 => pragma solidity 0.6.12;
contracts/plugins/aave/RayMathNoRounding.sol::2 => pragma solidity 0.6.12;
contracts/plugins/aave/ReentrancyGuard.sol::3 => pragma solidity >=0.6.0 <0.8.0;
contracts/plugins/aave/StaticATokenErrors.sol::2 => pragma solidity 0.6.12;
contracts/plugins/aave/StaticATokenLM.sol::2 => pragma solidity 0.6.12;
contracts/plugins/assets/ATokenFiatCollateral.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/Asset.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/CTokenFiatCollateral.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/CTokenNonFiatCollateral.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/EURFiatCollateral.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/FiatCollateral.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/ICToken.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/NonFiatCollateral.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/OracleLib.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/RTokenAsset.sol::2 => pragma solidity 0.8.9;
contracts/plugins/assets/SelfReferentialCollateral.sol::2 => pragma solidity 0.8.9;
contracts/plugins/governance/Governance.sol::2 => pragma solidity 0.8.9;
contracts/plugins/trading/GnosisTrade.sol::2 => pragma solidity 0.8.9;
```

## [N-2] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
contracts/p1/Distributor.sol::26 => // all distribution-share values are <= 10000
contracts/p1/Distributor.sol::165 => require(share.rsrDist <= 10000, "RSR distribution too high");
contracts/p1/Distributor.sol::166 => require(share.rTokenDist <= 10000, "RToken distribution too high");
```

## [N-4] Use scientific notation (e.g. 1e18) rather than exponentiation (e.g. 10**18)

Scientific notation should be used for better code readability

```
contracts/libraries/Fixed.sol::101 => if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5
contracts/libraries/Fixed.sol::102 => if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57
```

## [N-5] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
contracts/interfaces/IBasketHandler.sol::21 => event PrimeBasketSet(IERC20[] erc20s, uint192[] targetAmts, bytes32[] targetNames);
contracts/interfaces/IDeployerRegistry.sol::7 => event DeploymentUnregistered(string version, IDeployer deployer);
contracts/interfaces/IDeployerRegistry.sol::8 => event DeploymentRegistered(string version, IDeployer deployer);
contracts/interfaces/IDeployerRegistry.sol::9 => event LatestChanged(string version, IDeployer deployer);
contracts/interfaces/IDistributor.sol::28 => event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);
contracts/interfaces/IMain.sol::154 => event MainInitialized();
contracts/interfaces/IRToken.sol::83 => event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);
contracts/interfaces/IRToken.sol::87 => event Melted(uint256 amount);
```

## [N-6] The visibility for constructor is ignored

No need to add public to constructor

```
contracts/plugins/aave/ERC20.sol::57 => constructor(string memory name, string memory symbol) public {
```

## [N-8] Missing NatSpec

Code should include NatSpec

```
contracts/interfaces/IVersioned.sol::1 => // SPDX-License-Identifier: BlueOak-1.0.0
contracts/libraries/Permit.sol::1 => // SPDX-License-Identifier: BlueOak-1.0.0
contracts/plugins/aave/RayMathNoRounding.sol::1 => // SPDX-License-Identifier: agpl-3.0
contracts/plugins/aave/StaticATokenErrors.sol::1 => // SPDX-License-Identifier: agpl-3.0
```

## [N-9] Missing event for critical parameter change

Emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following changes to the contract.

```
contracts/interfaces/IBackingManager.sol::54 => function setTradingDelay(uint48 val) external;
contracts/interfaces/IBackingManager.sol::56 => function setBackingBuffer(uint192 val) external;
contracts/interfaces/IBasketHandler.sol::44 => function setPrimeBasket(IERC20[] memory erc20s, uint192[] memory targetAmts) external;
contracts/interfaces/IBasketHandler.sol::52 => function setBackupConfig(
contracts/interfaces/IBroker.sol::50 => function setAuctionLength(uint48 newAuctionLength) external;
contracts/interfaces/IBroker.sol::52 => function setDisabled(bool disabled_) external;
contracts/interfaces/IDistributor.sol::40 => function setDistribution(address dest, RevenueShare memory share) external;
contracts/interfaces/IFacadeWrite.sol::87 => function setupGovernance(
contracts/interfaces/IFurnace.sol::27 => function setPeriod(uint48) external;
contracts/interfaces/IFurnace.sol::38 => function setRatio(uint192) external;
contracts/interfaces/IGnosis.sol::45 => function settleAuction(uint256 auctionId) external returns (bytes32 encodedOrder);
contracts/interfaces/IMain.sol::168 => function setShortFreeze(uint48) external;
contracts/interfaces/IMain.sol::171 => function setLongFreeze(uint48) external;
contracts/interfaces/IRToken.sol::162 => function setBasketsNeeded(uint192 basketsNeeded) external;
contracts/interfaces/IRToken.sol::173 => function setIssuanceRate(uint192) external;
contracts/interfaces/IRToken.sol::179 => function setScalingRedemptionRate(uint192 val) external;
contracts/interfaces/IRToken.sol::185 => function setRedemptionRateFloor(uint256 val) external;
contracts/interfaces/IStRSR.sol::126 => function setRewardPeriod(uint48) external;
contracts/interfaces/IStRSR.sol::130 => function setRewardRatio(uint192) external;
contracts/interfaces/IStRSR.sol::134 => function setUnstakingDelay(uint48) external;
contracts/interfaces/IStRSR.sol::136 => function setName(string calldata) external;
contracts/interfaces/IStRSR.sol::138 => function setSymbol(string calldata) external;
contracts/interfaces/ITrade.sol::26 => function settle() external returns (uint256 soldAmt, uint256 boughtAmt);
contracts/interfaces/ITrading.sol::49 => function settleTrade(IERC20 sell) external;
contracts/interfaces/ITrading.sol::70 => function setMaxTradeSlippage(uint192 val) external;
contracts/interfaces/ITrading.sol::73 => function setMinTradeVolume(uint192 val) external;
contracts/p1/BasketHandler.sol::75 => function setFrom(Basket storage self, Basket storage other) internal {
contracts/p1/Distributor.sol::61 => function setDistribution(address dest, RevenueShare memory share) external governance {
contracts/p1/StRSR.sol::803 => function setName(string calldata name_) external governance {
contracts/p1/StRSR.sol::807 => function setSymbol(string calldata symbol_) external governance {
contracts/plugins/aave/IAaveIncentivesController.sol::38 => function setClaimer(address user, address claimer) external;
contracts/plugins/trading/GnosisTrade.sol::169 => function settle()
```

## [N-10] Non-assembly method available

assembly{ id := chainid() } => uint256 id = block.chainid
assembly { size := extcodesize() } => uint256 size = address().code.length

```
contracts/plugins/aave/StaticATokenLM.sol::265 => chainId := chainid()
```

## [N-11] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
contracts/libraries/Fixed.sol::33 => bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
```