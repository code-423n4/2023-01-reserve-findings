# Audit Report

## Summery
[L001] Unsafe ERC20 Operation(s)
[L002] Unspecific Compiler Version Pragma
[L003] Do not use Deprecated Library Functions
[L004] PRAGMA EXPERIMENTAL ABIENCODERV2 IS DEPRECATED
[L005] REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()
[L006] SHADOWED VARIABLE
[L007] PREVENT DIV BY 0
[L008] UNSAFE CALLS TO OPTIONAL ERC20 FUNCTIONS 
[L009] SAFEAPPROVE() IS DEPRECATED
[N001] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’
[N002] NUMERIC VALUES HAVING TO DO WITH TIME SHOULD USE TIME UNITS FOR READABILITY
[N003] EVENT IS MISSING INDEXED FIELDS
[N004] THE NONREENTRANT MODIFIER SHOULD OCCUR BEFORE ALL OTHER MODIFIERS
[N005] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING
[N006] USE A MORE RECENT VERSION OF SOLIDITY
[N007] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT
[N008] STATE VARIABLE VISIBILITY IS NOT SET
[N009] REQUIRE MESSAGE IS UNCLEAR
[N010] CODE CONTAINS EMPTY BLOCKS
[N011] CONSTRUCTOR VISIBILITY
[N012] EVENT DOES NOT HAVE PARAMETERS

## Issues found

### [L001] Unsafe ERC20 Operation(s)

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
plugins\mocks\WETH.sol::45 => msg.sender.transfer(wad);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [L002] Unspecific Compiler Version Pragma

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
libraries\Fixed.sol::3 => pragma solidity ^0.8.9;
libraries\test\ArrayCallerMock.sol::2 => pragma solidity ^0.8.9;
libraries\test\FixedCallerMock.sol::2 => pragma solidity ^0.8.9;
libraries\test\StringCallerMock.sol::2 => pragma solidity ^0.8.9;
plugins\aave\ERC20.sol::3 => pragma solidity ^0.6.0;
plugins\aave\ReentrancyGuard.sol::3 => pragma solidity >=0.6.0 <0.8.0;
plugins\mocks\ChainlinkMock.sol::2 => pragma solidity ^0.8.0;
plugins\mocks\InvalidChainlinkMock.sol::2 => pragma solidity ^0.8.0;
plugins\mocks\WETH.sol::16 => pragma solidity >=0.4.22 <0.6;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [L003] Do not use Deprecated Library Functions

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions)

#### Findings:
```
\p1\BackingManager.sol::75 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
\p1\BackingManager.sol::76 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
\p1\RevenueTrader.sol::61 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), 0);
\p1\RevenueTrader.sol::62 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), bal);
\p1\mixins\Trading.sol::116 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);
\p1\mixins\Trading.sol::117 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);
\plugins\aave\StaticATokenLM.sol::101 => ASSET.safeApprove(address(pool), type(uint256).max);
\plugins\mocks\vendor\EasyAuction.sol::144 => function safeApprove(
\plugins\trading\GnosisTrade.sol::136 => IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), 0);
\plugins\trading\GnosisTrade.sol::137 => IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), initBal);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [L004] PRAGMA EXPERIMENTAL ABIENCODERV2 IS DEPRECATED

#### Findings:
```
\plugins\aave\IAaveIncentivesController.sol::3 => pragma experimental ABIEncoderV2;
\plugins\aave\IStaticATokenLM.sol::3 => pragma experimental ABIEncoderV2;
\plugins\aave\RayMathNoRounding.sol::3 => pragma experimental ABIEncoderV2;
\plugins\aave\StaticATokenLM.sol::3 => pragma experimental ABIEncoderV2;
```

#### Tools used
Manual

### [L005] REQUIRE() SHOULD BE USED INSTEAD OF ASSERT()

#### Findings:
```
\p1\BackingManager.sol::249 => assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
\p1\BasketHandler.sol::556 => assert(targetIndex < targetsLength);
\p1\StRSR.sol::696 => assert(totalStakes + amount < type(uint224).max);
\p1\mixins\RecollateralizationLib.sol::110 => assert(doTrade);
\p1\mixins\RewardableLib.sol::78 => assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
\p1\mixins\RewardableLib.sol::102 => assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
\p1\mixins\TradeLib.sol::44 => assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
\p1\mixins\TradeLib.sol::108 => assert(
\p1\mixins\TradeLib.sol::168 => assert(errorCode == 0x11 || errorCode == 0x12);
\p1\mixins\TradeLib.sol::170 => assert(keccak256(reason) == UIntOutofBoundsHash);
\p1\mixins\Trading.sol::114 => assert(address(trades[sell]) == address(0));
\plugins\aave\StaticATokenLM.sol::345 => assert(amt == amountToWithdraw);
\plugins\assets\Asset.sol::98 => assert(low == 0);
\plugins\assets\Asset.sol::112 => assert(low <= high);
\plugins\assets\Asset.sol::147 => assert(lotLow <= lotHigh);
\plugins\mocks\InvalidFiatCollateral.sol::137 => assert(low == 0);
\plugins\assets\RTokenAsset.sol::74 => assert(low <= high);
```

#### Tools used
Manual

### [L006] SHADOWED VARIABLE

#### Findings:
```
\plugins\assets\CTokenFiatCollateral.sol::27 => ICToken erc20 = ICToken(address(config.erc20));
\plugins\assets\CTokenFiatCollateral.sol::28 => referenceERC20Decimals = IERC20Metadata(erc20.underlying()).decimals();
```
#### Tools used
Manual

### [L007] PREVENT DIV BY 0

#### Findings:
```
\libraries\Fixed.sol::159 => uint256 result = numerator / divisor;
\p1\BasketHandler.sol::369 => if (rawDelta / p != qty) return FIX_MAX;
\p1\StRSRVotes.sol::270 => uint256 newStakeRSR = (FIX_ONE_256 * totalStakes + (stakeRate - 1)) / stakeRate;
\p1\StRSRVotes.sol::427 => return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
```

#### Tools used
Manual

### [L008] UNSAFE CALLS TO OPTIONAL ERC20 FUNCTIONS 
decimals(), name() and symbol() are optional parts of the ERC20 specification, so there are tokens that do not implement them. It’s not safe to cast arbitrary token addresses in order to call these functions. If IERC20Metadata is to be relied on, that should be the variable type of the token variable, rather than it being address, so the compiler can verify that types correctly match, rather than this being a runtime failure.

#### Findings:
```
\plugins\assets\Asset.sol::58 => erc20Decimals = erc20.decimals();
\p1\BackingManager.sol::228 => int8(IERC20Metadata(address(erc20s[i])).decimals())
\p1\BasketHandler.sol::421 => int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),
\plugins\aave\StaticATokenLM.sol::89 => _setupDecimals(IERC20Detailed(aToken).decimals());
\plugins\assets\CTokenFiatCollateral.sol::28 => referenceERC20Decimals = IERC20Metadata(erc20.underlying()).decimals();
\plugins\assets\OracleLib.sol::30 => return shiftl_toFix(uint256(p), -int8(chainlinkFeed.decimals()));
\plugins\assets\RTokenAsset.sol::37 => erc20Decimals = erc20_.decimals();
\plugins\trading\GnosisTrade.sol::106-107 => worstCasePrice = shiftl_toFix(req.minBuyAmount, -int8(buy.decimals())).div(
\plugins\trading\GnosisTrade.sol::125 => DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))
\plugins\trading\GnosisTrade.sol::202-203 => uint192 clearingPrice = shiftl_toFix(boughtAmt, -int8(buy.decimals())).div(
```
#### Tools used
Manual

### [L009] SAFEAPPROVE() IS DEPRECATED
Safeapprove() is deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance(). If only setting the initial allowance to the value that means infinite, safeIncreaseAllowance() can be used instead.

#### Findings:
```
\p1\BackingManager.sol::75 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
\p1\BackingManager.sol::76 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
\p1\RevenueTrader.sol::61 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), 0);
\p1\RevenueTrader.sol::62 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), bal);
\p1\mixins\Trading.sol::116 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);
\p1\mixins\Trading.sol::117 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);
\plugins\aave\StaticATokenLM.sol::101 => ASSET.safeApprove(address(pool), type(uint256).max);
```

#### Tools used
Manual

### [N001] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’
In all files use named import instead for better readabality

#### Tools used
Manual

### [N002] NUMERIC VALUES HAVING TO DO WITH TIME SHOULD USE TIME UNITS FOR READABILITY

#### Findings:
```
mixins\Auth.sol::9 => uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month
mixins\Auth.sol::10 => uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year
p1\BackingManager.sol::33 => uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
p1\Furnace.sol::16 => uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
p1\StRSR.sol::37 => uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
p1\StRSR.sol::38 => uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
```
#### Tools used
Manual

### [N003] EVENT IS MISSING INDEXED FIELDS

#### Findings:
```
interfaces\IDeployerRegistry.sol::7 => event DeploymentUnregistered(string version, IDeployer deployer);
interfaces\IDeployerRegistry.sol::8 => event DeploymentRegistered(string version, IDeployer deployer);
interfaces\IDeployerRegistry.sol::9 => event LatestChanged(string version, IDeployer deployer);
interfaces\IDistributor.sol::28 => event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);
interfaces\IRToken.sol::83 => event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);
```
#### Tools used
Manual

### [N004] THE NONREENTRANT MODIFIER SHOULD OCCUR BEFORE ALL OTHER MODIFIERS

#### Findings:
```
p1\mixins\Trading.sol::66 => function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {
```
#### Tools used
Manual

### [N005] NOT USING THE NAMED RETURN VARIABLES ANYWHERE IN THE FUNCTION IS CONFUSING

#### Findings:
```
\p1\AssetRegistry.sol::102 => function toAsset(IERC20 erc20) external view returns (IAsset) {
\p1\AssetRegistry.sol::162 => function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
\p1\BasketHandler.sol::316 => function price() external view returns (uint192 low, uint192 high) {
\p1\BasketHandler.sol::325 => function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {  
\p1\Broker.sol::85 => function openTrade(TradeRequest memory req) external notPausedOrFrozen returns (ITrade) {
\p1\mixins\TradeLib.sol::41 => returns (bool notDust, TradeRequest memory req)
\p1\mixins\TradeLib.sol::106 => returns (bool notDust, TradeRequest memory req)
```

#### Tools used
Manual

### [N006] USE A MORE RECENT VERSION OF SOLIDITY
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions.
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked()

#### Tools used
Manual

### [N007] EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

#### Findings:
```
\libraries\Fixed.sol::33 => bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
\p1\StRSR.sol::126 => bytes32 private constant _PERMIT_TYPEHASH =
\p1\StRSR.sol::777 => bytes32 structHash = keccak256(
\p1\StRSRVotes.sol::27 => bytes32 private constant _DELEGATE_TYPEHASH =
```
#### Tools used
Manual

### [N008] STATE VARIABLE VISIBILITY IS NOT SET

#### Findings:
```
\libraries\Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
```
#### Tools used
Manual

### [N009] REQUIRE MESSAGE IS UNCLEAR

#### Findings:
```
\libraries\Fixed.sol::317 => require(x_ <= FIX_ONE);
```

#### Tools used
Manual

### [N010] CODE CONTAINS EMPTY BLOCKS

#### Findings:
```
\p1\Main.sol::23 => constructor() initializer {}
\p1\Main.sol::64 => function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
\p1\mixins\Component.sol:: 25 => constructor() initializer {}
\p1\mixins\Component.sol:: 57 => function _authorizeUpgrade(address newImplementation) internal view override governance {}
\p1\RToken.sol::838 => function requireNotPausedOrFrozen() private notPausedOrFrozen {}
\plugins\aave\ERC20.sol::346 => ) internal virtual {}
\plugins\assets\Asset.sol::164 => function claimRewards() external virtual {}
```

#### Tools used
Manual

### [N011] CONSTRUCTOR VISIBILITY

#### Findings:
```
\plugins\aave\StaticATokenLM.sol::83 => ) public { 
```

#### Tools used
Manual

### [N012] EVENT DOES NOT HAVE PARAMETERS

#### Findings:
```
\interfaces\IMain.sol::154 => event MainInitialized();
```

#### Tools used
Manual

