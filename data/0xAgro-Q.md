# QA Report
## Finding Summary
||Issue|Instances|
|-|-|-|
|[NC-01]|Contracts Missing `@title` NatSpec Tag|62|
|[NC-02]|Double Space In Function Definition|36|
|[NC-03]|Style Guide Voiding Whitespace (parenthesis/brackets/braces)|26|
|[NC-04]|Contract Layout Voids Solidity Docs|11|
|[NC-05]|Long Lines (> 120 Characters)|9|
|[NC-06]|Underscore Notation Not Used / Not Used Consistently|9|
|[NC-07]|Spelling Mistakes|8|
|[NC-08]|Events Not Indexed|6|
|[NC-09]|Error Messages Are Missing From Require Statement(s)|3|
|[NC-10]|NatSpec Comments Use `//` Instead of `///`|3|
|[NC-11]|Inconsistent Comment Initial Spacing|3|
|[NC-12]|Power of Ten Literal > `10e3` Not In Scientific Notation|2|
|[NC-13]|Non-Assembly Function Available (`address().code.length`)|1|
|[NC-14]|Non-Assembly Function Available (`chainid()`)|1|
|[NC-15]|No License Indication|1|
|[L-01]|`assert` Used Over `require`|20|
|[L-02]|Spelling Mistake In `require` Message|1|

### [NC-01] Contracts Missing `@title` NatSpec Tag

62 out of 110 of the contracts in scope are missing a `@title` tag. Given that 48 contracts all have a `@title` tag, consider adding one per the 62 remaining contracts.

[AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol), [Broker.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol), [Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol), [Component.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol), [Trading.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol), [RevenueTrader.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol), [RayMathNoRounding.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol), [ERC20.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol), [IAaveIncentivesController.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol), [StaticATokenErrors.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol), [IStaticATokenLM.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol), [IAToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol), [ReentrancyGuard.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol), [OracleLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol), [ICToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ICToken.sol), [RTokenAsset.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol), [Asset.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol), [SelfdestructTransferMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/SelfdestructTransferMock.sol), [BadERC20.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol), [AaveLendingPoolMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/AaveLendingPoolMock.sol), [UnpricedAssetPlugin.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/UnpricedAssetPlugin.sol), [GnosisMockReentrant.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol), [USDCMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/USDCMock.sol), [ERC20Mock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ERC20Mock.sol), [WETH.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol), [NontrivialPegCollateral.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol), [BrokerV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BrokerV2.sol), [AssetRegistryV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/AssetRegistryV2.sol), [FurnaceV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/FurnaceV2.sol), [DistributorV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/DistributorV2.sol), [RevenueTraderV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/RevenueTraderV2.sol), [BackingManagerV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BackingManagerV2.sol), [StRSRV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/StRSRV2.sol), [MainV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/MainV2.sol), [RTokenV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/RTokenV2.sol), [BasketHandlerV2.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BasketHandlerV2.sol), [InvalidFiatCollateral.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidFiatCollateral.sol), [BadCollateralPlugin.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadCollateralPlugin.sol), [ERC1271Mock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ERC1271Mock.sol), [GnosisMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol), [CTokenMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/CTokenMock.sol), [ComptrollerMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ComptrollerMock.sol), [EasyAuction.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol), [ATokenMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol), [WBTCMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WBTCMock.sol), [InvalidBrokerMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol), [ZeroDecimalMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ZeroDecimalMock.sol), [MockableCollateral.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/MockableCollateral.sol), [InvalidATokenFiatCollateralMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol), [GnosisTrade.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol), [Array.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol), [Permit.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol), [RedemptionBattery.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol), [String.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol), [StringCallerMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/StringCallerMock.sol), [ArrayCallerMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol), [FixedCallerMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol), [IDeployerRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol), [IGnosis.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol), [IStRSRVotes.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSRVotes.sol), [ITrade.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrade.sol), and [IVersioned.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IVersioned.sol) are missing a `@title` tag.

### [NC-02] Double Space In Function Definition

In [FixedCallerMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol) most function definitions have double spaces in the parameters: [L19](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L19), [L26](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L26), [L29](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L29), [L38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L38), [L41](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L41), [L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L44), [L47](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L47), [L50](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L50), [L53](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L53), [L56](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L56), [L59](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L59), [L62](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L62), [L65](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L65), [L68](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L68), [L71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L71), [L74](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L74), [L77](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L77), [L80](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L80), [L83](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L83), [L86](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L86), [L89](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L89), [L92](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L92), [L95](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L95), [L98](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L98), [L101](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L101), [L104](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L104), [L109](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L109), [L112](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L112), [L115](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L115), [L118](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L118), [L121](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L121), [L124](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L124), [L127](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L127), [L130](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L130), [L133](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L133), [L136](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L136). Consider using a single space for better cleanliness.

### [NC-03] Style Guide Voiding Whitespace (parenthesis/brackets/braces)

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#whitespace-in-expressions) recommends to *"[a]void extraneous whitespace [i]mmediately inside parenthesis, brackets or braces, with the exception of single line function declarations"*. There are many instances of a trailing space in the `returns` parameter.

*/contracts/libraries/test/FixedCallerMock.sol*
Links: [9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L9), [12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L12), [15](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L15), [19](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L19), [26](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L26), [29](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L29), [38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L38), [41](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L41), [44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L44), [47](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L47), [50](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L50), [53](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L53), [56](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L56), [59](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L59), [62](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L62), [65](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L65), [68](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L68), [71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L71), [74](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L74), [77](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L77), [80](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L80), [83](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L83), [127](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L127), [130](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L130), [133](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L133), [136](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L136).
```solidity
9:	function toFix_(uint256 x) public pure returns (uint192 ) {
12:	function shiftl_toFix_(uint256 x, int8 d) public pure returns (uint192 ) {
15:	function shiftl_toFix_Rnd(uint256 x, int8 d, RoundingMode rnd) public pure returns (uint192 ) {
19:	function divFix_(uint256 x, uint192  y) public pure returns (uint192 ) {
26:	function fixMin_(uint192  x, uint192  y) public pure returns (uint192 ) {
29:	function fixMax_(uint192  x, uint192  y) public pure returns (uint192 ) {
38:	function toUint(uint192  x) public pure returns (uint256 ) {
41:	function toUintRnd(uint192  x, RoundingMode rnd) public pure returns (uint256 ) {
44:	function shiftl(uint192  x, int8 decimals) public pure returns (uint192 ) {
47:	function shiftlRnd(uint192  x, int8 decimals, RoundingMode rnd) public pure returns (uint192 ) {
50:	function plus(uint192  x, uint192  y) public pure returns (uint192 ) {
53:	function plusu(uint192  x, uint256 y) public pure returns (uint192 ) {
56:	function minus(uint192  x, uint192  y) public pure returns (uint192 ) {
59:	function minusu(uint192  x, uint256 y) public pure returns (uint192 ) {
62:	function mul(uint192  x, uint192  y) public pure returns (uint192 ) {
65:	function mulRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {
68:	function mulu(uint192  x, uint256 y) public pure returns (uint192 ) {
71:	function div(uint192  x, uint192  y) public pure returns (uint192 ) {
74:	function divRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {
77:	function divu(uint192  x, uint256 y) public pure returns (uint192 ) {
80:	function divuRnd(uint192  x, uint256 y, RoundingMode rnd) public pure returns (uint192 ) {
83:	function powu(uint192  x, uint48 y) public pure returns (uint192 ) {
127:	function muluDivu(uint192  x, uint256 y, uint256 z) public pure returns (uint192 ) {
130:	function muluDivuRnd(uint192  x, uint256 y, uint256 z, RoundingMode rnd) public pure returns (uint192 ) {
133:	function mulDiv(uint192  x, uint192  y, uint192  z) public pure returns (uint192 ) {
136:	function mulDivRnd(uint192  x, uint192  y, uint192  z, RoundingMode rnd) public pure returns (uint192 ) {
```

### [NC-04] Contract Layout Voids Solidity Docs

The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout) suggests the following contract layout order: Type declarations, State variables, Events, Modifiers, Functions.

The following contracts are not compliant (examples are only to prove the layout are out of order NOT a full description): 

* [RecollateralizationLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol): State is positioned after Functions.
* [Component.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol): Modifiers are positioned after Functions.
* [IAToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol): Events are positioned after Functions.
* [ReentrancyGuard.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol): Modifiers are positioned after Functions.
* [WETH.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol): State is positioned after Events.
* [NontrivialPegCollateral.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol): State is positioned after Functions.
* [EasyAuction.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol): Events are Positioned after Events.
* [IFacadeRead.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeRead.sol): State is positioned after Functions.
* [IFurnace.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol): Events are positioned after Functions.
* [IMain.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol): Events are positioned after Functions.
* [ComponentRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol): State is positioned after Functions

### [NC-05] Long Lines (> 120 Characters)

Lines with greater length than 120 characters are used. The [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-lengthhttps://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length) suggests that all lines should be 120 characters or less in width.

The following lines are longer than 120 characters, it is suggested to shorten these lines:

*contracts/plugins/aave/IAaveIncentivesController.sol*
*  [90](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L90). 

*contracts/plugins/aave/StaticATokenLM.sol*
*  [51](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L51), [55](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L55), [520](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L520). 

*contracts/plugins/mocks/ChainlinkMock.sol*
*  [14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L14). 

*contracts/plugins/mocks/vendor/EasyAuction.sol*
*  [653](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L653), [715](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L715), [734](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L734), [873](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L873).

### [NC-06] Underscore Notation Not Used / Not Used Consistently

Consider using underscore notation to help with contract readability (Ex. `23453` -> `23_453`).

*/contracts/p1/BackingManager.sol*
Links: [33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33).
```solidity
33:	uint48 public constant MAX_TRADING_DELAY = 31536000;
```

*/contracts/p1/Broker.sol*
Links: [24](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24).
```solidity
24:	uint48 public constant MAX_AUCTION_LENGTH = 604800;
```

*/contracts/p1/Furnace.sol*
Links: [16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L16).
```solidity
16:	uint48 public constant MAX_PERIOD = 31536000;
```

*/contracts/p1/StRSR.sol*
Links: [37](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L37), [38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L38).
```solidity
37:	uint48 public constant MAX_UNSTAKING_DELAY = 31536000;
38:	uint48 public constant MAX_REWARD_PERIOD = 31536000;
```

*/contracts/plugins/mocks/EasyAuction.sol*
Links: [124](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L124).
```solidity
124:	uint256 public constant FEE_DENOMINATOR = 1000;
```

*/contracts/plugins/trading/GnosisTrade.sol*
Links: [27](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L27).
```solidity
27:	uint256 public constant FEE_DENOMINATOR = 1000;
```

*/contracts/mixins/Auth.sol*
Links: [9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L9), [10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L10).
```solidity
9:	uint48 constant MAX_SHORT_FREEZE = 2592000;
10:	uint48 constant MAX_LONG_FREEZE = 31536000;
```

### [NC-07] Spelling Mistakes / Grammar

There are some spelling mistakes throughout the codebase. Consider fixing all spelling mistakes.

*/contracts/p1/BasketHandler.sol*

* The word `interpreted` is misspelled as `interepreted` ([537](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L537)).

*/contracts/p1/StRSRVotes.sol*

* The word `amount` is misspelled as `amound` ([524](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L524)).

*/contracts/plugins/mocks/GnosisMockReentrant.sol*

* The word `attempts` is misspelled as `attemts` ([10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol#L10)).

*/contracts/plugins/aave/StaticATokenLM.sol*
* The word `account` is misspelled as `accountt` ([30](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L30)).

*/contracts/plugins/mocks/EasyAuction.sol*
* The word `initiate` is misspelled as `intiate` ([137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L137)).
* See [L-02]

*/contracts/interfaces/IAsset.sol*
* The word `implementation` is misspelled as `implemntation` ([86](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L86)).

*/contracts/interfaces/IFacadeWrite.sol*
* The word `interacting` is misspelled as `interactin` ([68](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeWrite.sol#L68)).

### [NC-08] Events Not Indexed

It is best practice to have 3 indexed fields in events. Indexed fields help off-chain tools analyze on-chain activity through filtering these indexed fields.

*/contracts/interfaces/IDeployerRegistry.sol*
Links: [7](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L7), [8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L8), [9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L9).
```solidity
7:	event DeploymentUnregistered(string version, IDeployer deployer);
8:	event DeploymentRegistered(string version, IDeployer deployer);
9:	event LatestChanged(string version, IDeployer deployer);
```

*/contracts/interfaces/IDistributor.sol*
Links: [28](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L28).
```solidity
28:	event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);
```

*/contracts/interfaces/IRToken.sol*
Links: [83](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L83), [87](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L87).
```solidity
83:	event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);
87:	event Melted(uint256 amount);
```

### [NC-09] Error Messages Are Missing From Require Statement(s)

It is good practice to return a message on failure. Error messages help with debugging.

*/contracts/plugins/mocks/WETH.sol*
Links: [43](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L43), [68](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L68), [71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L71).
```solidity
43:	require(balanceOf[msg.sender] >= wad);
68:	require(balanceOf[src] >= wad);
71:	require(allowance[src][msg.sender] >= wad);
```

### [NC-10] NatSpec Comments Use `//` Instead of `///`

The [NatSpec notation](https://docs.soliditylang.org/en/v0.8.17/natspec-format.html#documentation-example) requires the use of `///` or `/**` to differentiate from regular comments. There are instances in the codebase where a `//` is used instead of `///`.

*contracts/plugins/mocks/EasyAuction.sol*
 Links: [137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L137), [398](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L398). 
```solidity
137:    // @dev: function to intiate a new auction
398:    // @dev function settling the auction and calculating the price
```

*contracts/plugins/mocks/vendor/EasyAuction.sol*
 Links: [337](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L337). 
```solidity
337:    // @dev orders are ordered by
```

### [NC-11] Inconsistent Comment Initial Spacing

Some comments have an initial space after `//` or `///` while others do not. It is best for code clearity to keep a consistent style.

The following contracts have both: [StaticATokenLM.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol), [EasyAuction.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol), and [ATokenMock.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol).

All remaining contracts only have initial space comments (EX. `// foo`).

### [NC-12] Power of Ten Literal > `10e3` Not In Scientific Notation

Power of ten literals > `10e3` are easier to read when expressed in scientific notation. Consider expressing large powers of ten in scientific notation (Ex. 10e5).

*/contracts/p1/Distributor.sol*
Links: [165](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165), [166](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L166).
```solidity
165:	require(share.rsrDist <= 10000, "RSR distribution too high");
166:	require(share.rTokenDist <= 10000, "RToken distribution too high");
```

### [NC-13] Non-Assembly Function Available (`address().code.length`)

The complexity brought by assembly can be avoided by using built in functions when there is no need for assembly. `assembly { size := extcodesize(account) }` can be replaced by `uint256 size = address().code.length`.

*/contracts/plugins/mocks/vendor/EasyAuction.sol*
Links: [696](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L696).
```solidity
696:	size := extcodesize(account)
```

### [NC-14] Non-Assembly Function Available (`chainid()`) 

The complexity brought by assembly can be avoided by using built in functions when there is no need for assembly. `assembly { chainId := chainid() }` can be replaced by `uint256 chainId = block.chainid`.

*/contracts/plugins/aave/StaticATokenLM.sol*
Links: [265](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L265).
```solidity
265:	chainId := chainid()
```

### [NC-15] No License Indication

`WETH.sol` has a license pasted in full, but is missing an official license indication. If no license is used `SPDX-License-Identifier: UNLICENSED` should be at the top of a contract.

The following contracts are missing a formal license:

* [WETH.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol)

### [L-01] `assert` Used Over `require`

`assert` should only be used in tests. Consider changing all occurrences of `assert` to `require`. Prior to Solidity 0.8 `require` will refund all remaining gas whereas `assert` will not. Even after Solidity 0.8 [`assert` will result in a panic](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#panic-via-assert-and-error-via-require) which should not occur in production code. As stated in the Solidity Documentation: "[p]roperly functioning code should never create a Panic".


*/contracts/p1/BackingManager.sol*
Links: [249](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249).
```solidity
249:	assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
```

*/contracts/p1/BasketHandler.sol*
Links: [556](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556).
```solidity
556:	assert(targetIndex < targetsLength);
```

*/contracts/p1/mixins/TradeLib.sol*
Links: [44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44), [108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L108), [168](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L168), [170](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L170).
```solidity
44:	assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
108:	assert(
168:	assert(errorCode == 0x11 || errorCode == 0x12);
170:	assert(keccak256(reason) == UIntOutofBoundsHash);
```

*/contracts/p1/mixins/RecollateralizationLib.sol*
Links: [110](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110).
```solidity
110:	assert(doTrade);
```

*/contracts/p1/mixins/RewardableLib.sol*
Links: [78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78), [102](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L102).
```solidity
78:	assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
102:	assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
```

*/contracts/p1/mixins/Trading.sol*
Links: [114](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L114).
```solidity
114:	assert(address(trades[sell]) == address(0));
```

*/contracts/p1/StRSR.sol*
Links: [696](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696).
```solidity
696:	assert(totalStakes + amount < type(uint224).max);
```

*/contracts/plugins/aave/StaticATokenLM.sol*
Links: [345](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L345).
```solidity
345:	assert(amt == amountToWithdraw);
```

*/contracts/plugins/assets/FiatCollateral.sol*
Links: [137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L137).
```solidity
137:	assert(low == 0);
```

*/contracts/plugins/assets/RTokenAsset.sol*
Links: [74](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L74).
```solidity
74:	assert(low <= high);
```

*/contracts/plugins/assets/Asset.sol*
Links: [98](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L98), [112](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L112), [147](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L147).
```solidity
98:	assert(low == 0);
112:	assert(low <= high);
147:	assert(lotLow <= lotHigh);
```

*/contracts/plugins/trading/GnosisTrade.sol*
Links: [63](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L63), [98](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L98), [182](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L182).
```solidity
63:	assert(status == TradeStatus.PENDING);
98:	assert(origin_ != address(0));
182:	assert(isAuctionCleared());
```

### [L-02] Spelling Mistake In `require` Message

On [L281](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L281) of [EasyAuction.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol) `minimal` is misspelled as `mimimal`. Spelling mistakes in error messages are not good as they can cause confusion in critical circumstances.
