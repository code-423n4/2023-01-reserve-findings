# c4udit Report

## Files analyzed
- contracts/facade/DeployerRegistry.sol
- contracts/facade/FacadeAct.sol
- contracts/facade/FacadeRead.sol
- contracts/facade/FacadeTest.sol
- contracts/facade/FacadeWrite.sol
- contracts/facade/lib/FacadeWriteLib.sol
- contracts/interfaces/IAsset.sol
- contracts/interfaces/IAssetRegistry.sol
- contracts/interfaces/IBackingManager.sol
- contracts/interfaces/IBasketHandler.sol
- contracts/interfaces/IBroker.sol
- contracts/interfaces/IComponent.sol
- contracts/interfaces/IDeployer.sol
- contracts/interfaces/IDeployerRegistry.sol
- contracts/interfaces/IDistributor.sol
- contracts/interfaces/IFacadeAct.sol
- contracts/interfaces/IFacadeRead.sol
- contracts/interfaces/IFacadeTest.sol
- contracts/interfaces/IFacadeWrite.sol
- contracts/interfaces/IFurnace.sol
- contracts/interfaces/IGnosis.sol
- contracts/interfaces/IMain.sol
- contracts/interfaces/IRToken.sol
- contracts/interfaces/IRevenueTrader.sol
- contracts/interfaces/IRewardable.sol
- contracts/interfaces/IStRSR.sol
- contracts/interfaces/IStRSRVotes.sol
- contracts/interfaces/ITrade.sol
- contracts/interfaces/ITrading.sol
- contracts/interfaces/IVersioned.sol
- contracts/libraries/Array.sol
- contracts/libraries/Fixed.sol
- contracts/libraries/Permit.sol
- contracts/libraries/RedemptionBattery.sol
- contracts/libraries/String.sol
- contracts/libraries/test/ArrayCallerMock.sol
- contracts/libraries/test/FixedCallerMock.sol
- contracts/libraries/test/StringCallerMock.sol
- contracts/mixins/Auth.sol
- contracts/mixins/ComponentRegistry.sol
- contracts/mixins/Versioned.sol
- contracts/p0/AssetRegistry.sol
- contracts/p0/BackingManager.sol
- contracts/p0/BasketHandler.sol
- contracts/p0/Broker.sol
- contracts/p0/Deployer.sol
- contracts/p0/Distributor.sol
- contracts/p0/Furnace.sol
- contracts/p0/Main.sol
- contracts/p0/RToken.sol
- contracts/p0/RevenueTrader.sol
- contracts/p0/StRSR.sol
- contracts/p0/mixins/Component.sol
- contracts/p0/mixins/Rewardable.sol
- contracts/p0/mixins/Trading.sol
- contracts/p0/mixins/TradingLib.sol
- contracts/p1/AssetRegistry.sol
- contracts/p1/BackingManager.sol
- contracts/p1/BasketHandler.sol
- contracts/p1/Broker.sol
- contracts/p1/Deployer.sol
- contracts/p1/Distributor.sol
- contracts/p1/Furnace.sol
- contracts/p1/Main.sol
- contracts/p1/RToken.sol
- contracts/p1/RevenueTrader.sol
- contracts/p1/StRSR.sol
- contracts/p1/StRSRVotes.sol
- contracts/p1/mixins/Component.sol
- contracts/p1/mixins/RecollateralizationLib.sol
- contracts/p1/mixins/RewardableLib.sol
- contracts/p1/mixins/TradeLib.sol
- contracts/p1/mixins/Trading.sol
- contracts/plugins/aave/ERC20.sol
- contracts/plugins/aave/IAToken.sol
- contracts/plugins/aave/IAaveIncentivesController.sol
- contracts/plugins/aave/IStaticATokenLM.sol
- contracts/plugins/aave/RayMathNoRounding.sol
- contracts/plugins/aave/ReentrancyGuard.sol
- contracts/plugins/aave/StaticATokenErrors.sol
- contracts/plugins/aave/StaticATokenLM.sol
- contracts/plugins/assets/ATokenFiatCollateral.sol
- contracts/plugins/assets/Asset.sol
- contracts/plugins/assets/CTokenFiatCollateral.sol
- contracts/plugins/assets/CTokenNonFiatCollateral.sol
- contracts/plugins/assets/CTokenSelfReferentialCollateral.sol
- contracts/plugins/assets/EURFiatCollateral.sol
- contracts/plugins/assets/FiatCollateral.sol
- contracts/plugins/assets/ICToken.sol
- contracts/plugins/assets/NonFiatCollateral.sol
- contracts/plugins/assets/OracleLib.sol
- contracts/plugins/assets/RTokenAsset.sol
- contracts/plugins/assets/SelfReferentialCollateral.sol
- contracts/plugins/governance/Governance.sol
- contracts/plugins/mocks/ATokenMock.sol
- contracts/plugins/mocks/AaveLendingPoolMock.sol
- contracts/plugins/mocks/BadCollateralPlugin.sol
- contracts/plugins/mocks/BadERC20.sol
- contracts/plugins/mocks/CTokenMock.sol
- contracts/plugins/mocks/ChainlinkMock.sol
- contracts/plugins/mocks/ComptrollerMock.sol
- contracts/plugins/mocks/ERC1271Mock.sol
- contracts/plugins/mocks/ERC20Mock.sol
- contracts/plugins/mocks/EasyAuction.sol
- contracts/plugins/mocks/GnosisMock.sol
- contracts/plugins/mocks/GnosisMockReentrant.sol
- contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol
- contracts/plugins/mocks/InvalidBrokerMock.sol
- contracts/plugins/mocks/InvalidChainlinkMock.sol
- contracts/plugins/mocks/InvalidFiatCollateral.sol
- contracts/plugins/mocks/MockableCollateral.sol
- contracts/plugins/mocks/NontrivialPegCollateral.sol
- contracts/plugins/mocks/RTokenCollateral.sol
- contracts/plugins/mocks/SelfdestructTransferMock.sol
- contracts/plugins/mocks/USDCMock.sol
- contracts/plugins/mocks/UnpricedAssetPlugin.sol
- contracts/plugins/mocks/WBTCMock.sol
- contracts/plugins/mocks/WETH.sol
- contracts/plugins/mocks/ZeroDecimalMock.sol
- contracts/plugins/mocks/upgrades/AssetRegistryV2.sol
- contracts/plugins/mocks/upgrades/BackingManagerV2.sol
- contracts/plugins/mocks/upgrades/BasketHandlerV2.sol
- contracts/plugins/mocks/upgrades/BrokerV2.sol
- contracts/plugins/mocks/upgrades/DistributorV2.sol
- contracts/plugins/mocks/upgrades/FurnaceV2.sol
- contracts/plugins/mocks/upgrades/MainV2.sol
- contracts/plugins/mocks/upgrades/RTokenV2.sol
- contracts/plugins/mocks/upgrades/RevenueTraderV2.sol
- contracts/plugins/mocks/upgrades/StRSRV2.sol
- contracts/plugins/mocks/vendor/EasyAuction.sol
- contracts/plugins/trading/GnosisTrade.sol
- contracts/vendor/ERC20PermitUpgradeable.sol

## Issues found

### Don't Initialize Variables with Default Value

#### Impact
Issue Information: [G001](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
contracts/facade/FacadeAct.sol::76 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeAct.sol::106 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeAct.sol::176 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeAct.sol::308 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeAct.sol::317 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeAct.sol::334 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeRead.sol::101 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeRead.sol::114 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeRead.sol::131 => for (uint256 i = 0; i < right - left; i++) {
contracts/facade/FacadeRead.sol::154 => for (uint256 i = 0; i < right - left; i++) {
contracts/facade/FacadeRead.sol::273 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeTest.sol::33 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeTest.sol::54 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeTest.sol::82 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeWrite.sol::32 => for (uint256 i = 0; i < setup.backups.length; ++i) {
contracts/facade/FacadeWrite.sol::37 => for (uint256 i = 0; i < setup.beneficiaries.length; ++i) {
contracts/facade/FacadeWrite.sol::64 => for (uint256 i = 0; i < setup.assets.length; ++i) {
contracts/facade/FacadeWrite.sol::74 => for (uint256 i = 0; i < setup.primaryBasket.length; ++i) {
contracts/facade/FacadeWrite.sol::88 => for (uint256 i = 0; i < setup.backups.length; ++i) {
contracts/facade/FacadeWrite.sol::93 => for (uint256 j = 0; j < setup.backups[i].backupCollateral.length; ++j) {
contracts/facade/FacadeWrite.sol::110 => for (uint256 i = 0; i < setup.beneficiaries.length; ++i) {
contracts/libraries/Array.sol::12 => for (uint256 j = 0; j < i; ++j) {
contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
contracts/p0/AssetRegistry.sol::23 => for (uint256 i = 0; i < assets_.length; i++) {
contracts/p0/AssetRegistry.sol::32 => for (uint256 i = 0; i < _erc20s.length(); i++) {
contracts/p0/AssetRegistry.sol::93 => for (uint256 i = 0; i < _erc20s.length(); i++) {
contracts/p0/AssetRegistry.sol::102 => for (uint256 i = 0; i < length; ++i) {
contracts/p0/BackingManager.sol::155 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/BasketHandler.sol::69 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
contracts/p0/BasketHandler.sol::77 => for (uint256 i = 0; i < length; ++i) {
contracts/p0/BasketHandler.sol::199 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::209 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::245 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::267 => for (uint256 i = 0; i < size; ++i) {
contracts/p0/BasketHandler.sol::316 => for (uint256 i = 0; i < basket.erc20s.length; i++) {
contracts/p0/BasketHandler.sol::360 => for (uint256 i = 0; i < len; ++i) {
contracts/p0/BasketHandler.sol::379 => for (uint256 i = 0; i < length; ++i) {
contracts/p0/BasketHandler.sol::400 => for (uint256 i = 0; i < length; ++i) {
contracts/p0/BasketHandler.sol::489 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::506 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::540 => for (uint256 i = 0; i < targetNames.length(); ++i) {
contracts/p0/BasketHandler.sol::546 => uint256 size = 0; // backup basket size
contracts/p0/BasketHandler.sol::550 => for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {
contracts/p0/BasketHandler.sol::559 => uint256 assigned = 0;
contracts/p0/BasketHandler.sol::564 => for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {
contracts/p0/BasketHandler.sol::593 => for (uint256 i = 0; i < basket.erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::606 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/Distributor.sol::64 => for (uint256 i = 0; i < destinations.length(); i++) {
contracts/p0/Distributor.sol::85 => for (uint256 i = 0; i < destinations.length(); i++) {
contracts/p0/RToken.sol::171 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/RToken.sol::247 => for (uint256 i = 0; i < iss.erc20s.length; i++) {
contracts/p0/RToken.sol::288 => for (uint256 i = 0; i < endId && i < queue.length; i++) {
contracts/p0/RToken.sol::350 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/RToken.sol::379 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/RToken.sol::446 => for (uint256 i = 0; i < iss.erc20s.length; i++) {
contracts/p0/RToken.sol::480 => bool someProcessed = false;
contracts/p0/RToken.sol::485 => for (uint256 i = 0; i < issuances[account].length; i++) {
contracts/p0/RToken.sol::493 => for (uint256 j = 0; j < iss.erc20s.length; j++) {
contracts/p0/RToken.sol::535 => for (uint256 i = 0; i < queue.length; i++) {
contracts/p0/StRSR.sol::206 => uint256 start = 0;
contracts/p0/StRSR.sol::211 => uint256 total = 0;
contracts/p0/StRSR.sol::227 => uint256 i = 0;
contracts/p0/StRSR.sol::268 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::270 => for (uint256 j = 0; j < queue.length; j++) {
contracts/p0/StRSR.sol::296 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::305 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::459 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::460 => for (uint256 j = 0; j < withdrawals[accounts.at(i)].length; j++) {
contracts/p0/StRSR.sol::468 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::469 => for (uint256 j = 0; j < withdrawals[accounts.at(i)].length; j++) {
contracts/p0/mixins/Rewardable.sol::21 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/mixins/TradingLib.sol::292 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/mixins/TradingLib.sol::375 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/mixins/TradingLib.sol::472 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/AssetRegistry.sol::38 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/AssetRegistry.sol::49 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/AssetRegistry.sol::127 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/AssetRegistry.sol::138 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BackingManager.sol::221 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BackingManager.sol::238 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BasketHandler.sol::70 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
contracts/p1/BasketHandler.sol::78 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::286 => for (uint256 i = 0; i < size; ++i) {
contracts/p1/BasketHandler.sol::337 => for (uint256 i = 0; i < len; ++i) {
contracts/p1/BasketHandler.sol::397 => for (uint256 i = 0; i < len; ++i) {
contracts/p1/BasketHandler.sol::416 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BasketHandler.sol::437 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BasketHandler.sol::530 => for (uint256 i = 0; i < basketLength; ++i) {
contracts/p1/BasketHandler.sol::548 => for (uint256 i = 0; i < basketLength; ++i) {
contracts/p1/BasketHandler.sol::586 => for (uint256 i = 0; i < targetsLength; ++i) {
contracts/p1/BasketHandler.sol::592 => uint256 size = 0; // backup basket size
contracts/p1/BasketHandler.sol::597 => for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
contracts/p1/BasketHandler.sol::606 => uint256 assigned = 0;
contracts/p1/BasketHandler.sol::611 => for (uint256 j = 0; j < backupLength && assigned < size; ++j) {
contracts/p1/BasketHandler.sol::643 => for (uint256 i = 0; i < basketLength; ++i) {
contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p1/BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/Distributor.sol::108 => for (uint256 i = 0; i < destinations.length(); ++i) {
contracts/p1/Distributor.sol::133 => for (uint256 i = 0; i < numTransfers; i++) {
contracts/p1/Distributor.sol::143 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/RToken.sol::270 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/RToken.sol::303 => for (uint256 i = 0; i < basketSize; ++i) {
contracts/p1/RToken.sol::329 => for (uint256 i = 0; i < basketSize; ++i) {
contracts/p1/RToken.sol::334 => for (uint256 i = 0; i < basketSize; ++i) {
contracts/p1/RToken.sol::478 => for (uint256 i = 0; i < erc20length; ++i) {
contracts/p1/RToken.sol::501 => for (uint256 i = 0; i < erc20length; ++i) {
contracts/p1/RToken.sol::674 => for (uint256 i = 0; i < tokensLen; ++i) {
contracts/p1/RToken.sol::683 => for (uint256 i = 0; i < tokensLen; ++i) {
contracts/p1/RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
contracts/p1/RToken.sol::757 => for (uint256 i = 0; i < tokensLen; ++i) {
contracts/p1/RToken.sol::767 => for (uint256 i = 0; i < tokensLen; ++i) {
contracts/p1/RToken.sol::793 => for (uint256 i = 0; i < tokensLen; ++i) {
contracts/p1/StRSRVotes.sol::102 => uint256 low = 0;
contracts/p1/mixins/RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
contracts/p1/mixins/RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
contracts/p1/mixins/RecollateralizationLib.sol::437 => for (uint256 i = 0; i < len; ++i) {
contracts/p1/mixins/RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
contracts/p1/mixins/RewardableLib.sol::67 => for (uint256 i = 0; i < erc20sLen; ++i) {
contracts/p1/mixins/RewardableLib.sol::73 => for (uint256 i = 0; i < erc20sLen; ++i) {
contracts/plugins/mocks/EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
contracts/plugins/mocks/EasyAuction.sol::285 => uint256 sumOfSellAmounts = 0;
contracts/plugins/mocks/EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
contracts/plugins/mocks/EasyAuction.sol::315 => uint256 claimableAmount = 0;
contracts/plugins/mocks/EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
contracts/plugins/mocks/EasyAuction.sol::344 => for (uint256 i = 0; i < iterationSteps; i++) {
contracts/plugins/mocks/EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) {
contracts/plugins/mocks/EasyAuction.sol::524 => for (uint256 i = 0; i < orders.length; i++) {
contracts/plugins/mocks/InvalidChainlinkMock.sol::51 => uint256 i = 0;
contracts/plugins/mocks/InvalidFiatCollateral.sol::32 => uint256 i = 0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Cache Array Length Outside of Loop

#### Impact
Issue Information: [G002](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
contracts/facade/FacadeAct.sol::76 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeAct.sol::106 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeAct.sol::176 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeAct.sol::307 => uint256[] memory initialBals = new uint256[](erc20s.length);
contracts/facade/FacadeAct.sol::308 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeAct.sol::317 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeAct.sol::334 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeRead.sol::99 => uint192[] memory uoaAmts = new uint192[](erc20s.length);
contracts/facade/FacadeRead.sol::100 => targets = new bytes32[](erc20s.length);
contracts/facade/FacadeRead.sol::101 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeRead.sol::113 => uoaShares = new uint192[](erc20s.length);
contracts/facade/FacadeRead.sol::114 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/facade/FacadeRead.sol::273 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeTest.sol::33 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeTest.sol::54 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeTest.sol::82 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/facade/FacadeWrite.sol::28 => require(setup.primaryBasket.length > 0, "no collateral");
contracts/facade/FacadeWrite.sol::29 => require(setup.primaryBasket.length == setup.weights.length, "invalid length");
contracts/facade/FacadeWrite.sol::32 => for (uint256 i = 0; i < setup.backups.length; ++i) {
contracts/facade/FacadeWrite.sol::33 => require(setup.backups[i].backupCollateral.length > 0, "no backup collateral");
contracts/facade/FacadeWrite.sol::37 => for (uint256 i = 0; i < setup.beneficiaries.length; ++i) {
contracts/facade/FacadeWrite.sol::64 => for (uint256 i = 0; i < setup.assets.length; ++i) {
contracts/facade/FacadeWrite.sol::71 => IERC20[] memory basketERC20s = new IERC20[](setup.primaryBasket.length);
contracts/facade/FacadeWrite.sol::74 => for (uint256 i = 0; i < setup.primaryBasket.length; ++i) {
contracts/facade/FacadeWrite.sol::88 => for (uint256 i = 0; i < setup.backups.length; ++i) {
contracts/facade/FacadeWrite.sol::90 => setup.backups[i].backupCollateral.length
contracts/facade/FacadeWrite.sol::93 => for (uint256 j = 0; j < setup.backups[i].backupCollateral.length; ++j) {
contracts/facade/FacadeWrite.sol::110 => for (uint256 i = 0; i < setup.beneficiaries.length; ++i) {
contracts/interfaces/IDeployer.sol::40 => uint48 auctionLength; // {s} the length of an auction
contracts/interfaces/IRToken.sol::5 => // solhint-disable-next-line max-line-length
contracts/interfaces/IStRSR.sol::5 => // solhint-disable-next-line max-line-length
contracts/libraries/Array.sol::10 => uint256 arrLen = arr.length;
contracts/libraries/Array.sol::22 => uint256 arrLen = arr.length;
contracts/libraries/String.sol::13 => bytes memory bLower = new bytes(bStr.length);
contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
contracts/mixins/Auth.sol::37 => uint48 public shortFreeze; // {s} length of an initial freeze
contracts/mixins/Auth.sol::38 => uint48 public longFreeze; // {s} length of a freeze extension
contracts/p0/AssetRegistry.sol::23 => for (uint256 i = 0; i < assets_.length; i++) {
contracts/p0/AssetRegistry.sol::32 => for (uint256 i = 0; i < _erc20s.length(); i++) {
contracts/p0/AssetRegistry.sol::92 => erc20s_ = new IERC20[](_erc20s.length());
contracts/p0/AssetRegistry.sol::93 => for (uint256 i = 0; i < _erc20s.length(); i++) {
contracts/p0/AssetRegistry.sol::99 => uint256 length = _erc20s.length();
contracts/p0/AssetRegistry.sol::100 => reg.erc20s = new IERC20[](length);
contracts/p0/AssetRegistry.sol::101 => reg.assets = new IAsset[](length);
contracts/p0/AssetRegistry.sol::102 => for (uint256 i = 0; i < length; ++i) {
contracts/p0/AssetRegistry.sol::108 => assert(reg.erc20s.length == reg.assets.length);
contracts/p0/BackingManager.sol::155 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/BasketHandler.sol::68 => uint256 length = self.erc20s.length;
contracts/p0/BasketHandler.sol::69 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
contracts/p0/BasketHandler.sol::76 => uint256 length = other.erc20s.length;
contracts/p0/BasketHandler.sol::77 => for (uint256 i = 0; i < length; ++i) {
contracts/p0/BasketHandler.sol::150 => uint192[] memory refAmts = new uint192[](basket.erc20s.length);
contracts/p0/BasketHandler.sol::188 => //   config'.targetAmts[erc20s[i]] = targetAmts[i], for i from 0 to erc20s.length-1
contracts/p0/BasketHandler.sol::194 => require(erc20s.length > 0, "cannot empty basket");
contracts/p0/BasketHandler.sol::195 => require(erc20s.length == targetAmts.length, "must be same length");
contracts/p0/BasketHandler.sol::199 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::207 => bytes32[] memory names = new bytes32[](erc20s.length);
contracts/p0/BasketHandler.sol::209 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::245 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::263 => uint256 size = basket.erc20s.length;
contracts/p0/BasketHandler.sol::316 => for (uint256 i = 0; i < basket.erc20s.length; i++) {
contracts/p0/BasketHandler.sol::358 => uint256 len = basket.erc20s.length;
contracts/p0/BasketHandler.sol::375 => uint256 length = basket.erc20s.length;
contracts/p0/BasketHandler.sol::376 => erc20s = new address[](length);
contracts/p0/BasketHandler.sol::377 => quantities = new uint256[](length);
contracts/p0/BasketHandler.sol::379 => for (uint256 i = 0; i < length; ++i) {
contracts/p0/BasketHandler.sol::396 => uint256 length = basket.erc20s.length;
contracts/p0/BasketHandler.sol::397 => if (length == 0 || disabled) return FIX_ZERO;
contracts/p0/BasketHandler.sol::400 => for (uint256 i = 0; i < length; ++i) {
contracts/p0/BasketHandler.sol::483 => while (targetNames.length() > 0) targetNames.remove(targetNames.at(0));
contracts/p0/BasketHandler.sol::489 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::499 => uint192[] memory goodWeights = new uint192[](targetNames.length());
contracts/p0/BasketHandler.sol::503 => uint192[] memory totalWeights = new uint192[](targetNames.length());
contracts/p0/BasketHandler.sol::506 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::511 => for (targetIndex = 0; targetIndex < targetNames.length(); ++targetIndex) {
contracts/p0/BasketHandler.sol::514 => assert(targetIndex < targetNames.length());
contracts/p0/BasketHandler.sol::540 => for (uint256 i = 0; i < targetNames.length(); ++i) {
contracts/p0/BasketHandler.sol::550 => for (uint256 j = 0; j < backup.erc20s.length && size < backup.max; ++j) {
contracts/p0/BasketHandler.sol::564 => for (uint256 j = 0; j < backup.erc20s.length && assigned < size; ++j) {
contracts/p0/BasketHandler.sol::582 => if (newBasket.erc20s.length == 0) disabled = true;
contracts/p0/BasketHandler.sol::592 => uint192[] memory refAmts = new uint192[](basket.erc20s.length);
contracts/p0/BasketHandler.sol::593 => for (uint256 i = 0; i < basket.erc20s.length; ++i) {
contracts/p0/BasketHandler.sol::606 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/Broker.sol::31 => uint48 public auctionLength; // {s} the length of an auction
contracts/p0/Distributor.sol::64 => for (uint256 i = 0; i < destinations.length(); i++) {
contracts/p0/Distributor.sol::85 => for (uint256 i = 0; i < destinations.length(); i++) {
contracts/p0/Distributor.sol::104 => require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");
contracts/p0/RToken.sol::4 => // solhint-disable-next-line max-line-length
contracts/p0/RToken.sol::52 => // List of accounts. If issuances[user].length > 0 then (user is in accounts)
contracts/p0/RToken.sol::95 => require(bytes(name_).length > 0, "name empty");
contracts/p0/RToken.sol::96 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p0/RToken.sol::97 => require(bytes(mandate_).length > 0, "mandate empty");
contracts/p0/RToken.sol::171 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/RToken.sol::191 => uint256 index = issuances[recipient].length - 1;
contracts/p0/RToken.sol::241 => (uint256 first, uint256 last) = earliest ? (0, endId) : (endId, queue.length);
contracts/p0/RToken.sol::244 => for (uint256 n = first; n < last && n < queue.length; n++) {
contracts/p0/RToken.sol::247 => for (uint256 i = 0; i < iss.erc20s.length; i++) {
contracts/p0/RToken.sol::262 => for (int256 i = int256(queue.length) - 1; i >= 0; i--) {
contracts/p0/RToken.sol::288 => for (uint256 i = 0; i < endId && i < queue.length; i++) {
contracts/p0/RToken.sol::296 => if (endId == queue.length) {
contracts/p0/RToken.sol::297 => for (int256 i = int256(queue.length) - 1; i >= 0; i--) {
contracts/p0/RToken.sol::311 => while (i < queue.length && queue[i].blockAvailableAt.lte(currBlock)) i++;
contracts/p0/RToken.sol::350 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/RToken.sol::379 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/RToken.sol::446 => for (uint256 i = 0; i < iss.erc20s.length; i++) {
contracts/p0/RToken.sol::485 => for (uint256 i = 0; i < issuances[account].length; i++) {
contracts/p0/RToken.sol::493 => for (uint256 j = 0; j < iss.erc20s.length; j++) {
contracts/p0/RToken.sol::504 => for (int256 i = int256(issuances[account].length) - 1; i >= 0; i--) {
contracts/p0/RToken.sol::535 => for (uint256 i = 0; i < queue.length; i++) {
contracts/p0/RToken.sol::550 => for (uint256 i = _right; i < queue.length; i++) {
contracts/p0/StRSR.sol::109 => require(bytes(name_).length > 0, "name empty");
contracts/p0/StRSR.sol::110 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p0/StRSR.sol::183 => uint256 index = withdrawals[account].length;
contracts/p0/StRSR.sol::202 => require(endId <= queue.length, "index out-of-bounds");
contracts/p0/StRSR.sol::228 => while (i < queue.length && queue[i].availableAt <= block.timestamp) i++;
contracts/p0/StRSR.sol::268 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::270 => for (uint256 j = 0; j < queue.length; j++) {
contracts/p0/StRSR.sol::296 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::305 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::459 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::460 => for (uint256 j = 0; j < withdrawals[accounts.at(i)].length; j++) {
contracts/p0/StRSR.sol::468 => for (uint256 i = 0; i < accounts.length(); i++) {
contracts/p0/StRSR.sol::469 => for (uint256 j = 0; j < withdrawals[accounts.at(i)].length; j++) {
contracts/p0/mixins/Rewardable.sol::21 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p0/mixins/TradingLib.sol::292 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/mixins/TradingLib.sol::374 => // No space on the stack to cache erc20s.length
contracts/p0/mixins/TradingLib.sol::375 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p0/mixins/TradingLib.sol::472 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/AssetRegistry.sol::37 => uint256 length = assets_.length;
contracts/p1/AssetRegistry.sol::38 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/AssetRegistry.sol::48 => uint256 length = _erc20s.length();
contracts/p1/AssetRegistry.sol::49 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/AssetRegistry.sol::125 => uint256 length = _erc20s.length();
contracts/p1/AssetRegistry.sol::126 => erc20s_ = new IERC20[](length);
contracts/p1/AssetRegistry.sol::127 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/AssetRegistry.sol::135 => uint256 length = _erc20s.length();
contracts/p1/AssetRegistry.sol::136 => reg.erc20s = new IERC20[](length);
contracts/p1/AssetRegistry.sol::137 => reg.assets = new IAsset[](length);
contracts/p1/AssetRegistry.sol::138 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BackingManager.sol::217 => uint256 length = erc20s.length;
contracts/p1/BackingManager.sol::219 => uint256[] memory toRSR = new uint256[](length);
contracts/p1/BackingManager.sol::220 => uint256[] memory toRToken = new uint256[](length);
contracts/p1/BackingManager.sol::221 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BackingManager.sol::238 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BasketHandler.sol::69 => uint256 length = self.erc20s.length;
contracts/p1/BasketHandler.sol::70 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
contracts/p1/BasketHandler.sol::77 => uint256 length = other.erc20s.length;
contracts/p1/BasketHandler.sol::78 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BasketHandler.sol::169 => uint192[] memory refAmts = new uint192[](basket.erc20s.length);
contracts/p1/BasketHandler.sol::207 => //   config'.targetAmts[erc20s[i]] = targetAmts[i], for i from 0 to erc20s.length-1
contracts/p1/BasketHandler.sol::213 => require(erc20s.length > 0, "cannot empty basket");
contracts/p1/BasketHandler.sol::214 => require(erc20s.length == targetAmts.length, "must be same length");
contracts/p1/BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::225 => bytes32[] memory names = new bytes32[](erc20s.length);
contracts/p1/BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::280 => uint256 size = basket.erc20s.length;
contracts/p1/BasketHandler.sol::336 => uint256 len = basket.erc20s.length;
contracts/p1/BasketHandler.sol::395 => uint256 len = basket.erc20s.length;
contracts/p1/BasketHandler.sol::412 => uint256 length = basket.erc20s.length;
contracts/p1/BasketHandler.sol::413 => erc20s = new address[](length);
contracts/p1/BasketHandler.sol::414 => quantities = new uint256[](length);
contracts/p1/BasketHandler.sol::416 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BasketHandler.sol::433 => uint256 length = basket.erc20s.length;
contracts/p1/BasketHandler.sol::434 => if (length == 0 || disabled) return FIX_ZERO;
contracts/p1/BasketHandler.sol::437 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/BasketHandler.sol::523 => while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
contracts/p1/BasketHandler.sol::529 => uint256 basketLength = config.erc20s.length;
contracts/p1/BasketHandler.sol::533 => uint256 targetsLength = _targetNames.length();
contracts/p1/BasketHandler.sol::596 => uint256 backupLength = backup.erc20s.length;
contracts/p1/BasketHandler.sol::630 => uint256 newBasketLength = _newBasket.erc20s.length;
contracts/p1/BasketHandler.sol::641 => basketLength = basket.erc20s.length;
contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p1/BasketHandler.sol::703 => erc20s = new IERC20[](basket.erc20s.length);
contracts/p1/BasketHandler.sol::704 => targetNames = new bytes32[](erc20s.length);
contracts/p1/BasketHandler.sol::705 => targetAmts = new uint192[](erc20s.length);
contracts/p1/BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::724 => erc20s = new IERC20[](backup.erc20s.length);
contracts/p1/BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/Broker.sol::36 => // {s} the length of an auction. Governance parameter.
contracts/p1/Distributor.sol::105 => Transfer[] memory transfers = new Transfer[](destinations.length());
contracts/p1/Distributor.sol::108 => for (uint256 i = 0; i < destinations.length(); ++i) {
contracts/p1/Distributor.sol::142 => uint256 length = destinations.length();
contracts/p1/Distributor.sol::143 => for (uint256 i = 0; i < length; ++i) {
contracts/p1/Distributor.sol::172 => require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");
contracts/p1/RToken.sol::4 => // solhint-disable-next-line max-line-length
contracts/p1/RToken.sol::145 => // - for each item in queue.items: queue.tokens.length == item.deposits.length
contracts/p1/RToken.sol::156 => require(bytes(name_).length > 0, "name empty");
contracts/p1/RToken.sol::157 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p1/RToken.sol::158 => require(bytes(mandate_).length > 0, "mandate empty");
contracts/p1/RToken.sol::270 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/RToken.sol::287 => IssueItem storage curr = (queue.right < queue.items.length)
contracts/p1/RToken.sol::292 => uint256 basketSize = erc20s.length; // gas optimization
contracts/p1/RToken.sol::302 => curr.deposits = new uint256[](deposits.length);
contracts/p1/RToken.sol::471 => uint256 erc20length = erc20s.length;
contracts/p1/RToken.sol::478 => for (uint256 i = 0; i < erc20length; ++i) {
contracts/p1/RToken.sol::501 => for (uint256 i = 0; i < erc20length; ++i) {
contracts/p1/RToken.sol::653 => //   (action) REFUND DEPOSITS: For i in [0, iss.deposits.length):
contracts/p1/RToken.sol::665 => uint256 tokensLen = queue.tokens.length;
contracts/p1/RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
contracts/p1/RToken.sol::734 => //     for i in [0, iss.deposits.length):
contracts/p1/RToken.sol::749 => uint256 tokensLen = queue.tokens.length;
contracts/p1/StRSR.sol::96 => //   Let `r.right` be the value draftsQueues[era][acct].length
contracts/p1/StRSR.sol::134 => uint48 public unstakingDelay; // {s} The minimum length of time spent in the draft queue
contracts/p1/StRSR.sol::168 => require(bytes(name_).length > 0, "name empty");
contracts/p1/StRSR.sol::169 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p1/StRSR.sol::312 => require(endId <= queue.length, "index out-of-bounds");
contracts/p1/StRSR.sol::440 => (uint256 left, uint256 right) = (firstRemainingDraft[draftEra][account], queue.length);
contracts/p1/StRSR.sol::452 => // - either right == queue.length or queue[right].availableAt > time
contracts/p1/StRSR.sol::461 => /// @return The length of the draft queue for an account in an era
contracts/p1/StRSR.sol::463 => return draftQueues[era_][account].length;
contracts/p1/StRSR.sol::557 => index = queue.length;
contracts/p1/StRSRVotes.sol::64 => return SafeCastUpgradeable.toUint48(_checkpoints[era][account].length);
contracts/p1/StRSRVotes.sol::72 => uint256 pos = _checkpoints[era][account].length;
contracts/p1/StRSRVotes.sol::100 => // taken after blockNumber, or ckpts.length if no checkpoint was taken after blockNumber
contracts/p1/StRSRVotes.sol::101 => uint256 high = ckpts.length;
contracts/p1/StRSRVotes.sol::198 => uint256 pos = ckpts.length;
contracts/p1/mixins/RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
contracts/p1/mixins/RecollateralizationLib.sol::328 => // No space on the stack to cache erc20s.length
contracts/p1/mixins/RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
contracts/p1/mixins/RecollateralizationLib.sol::434 => uint256 len = basketERC20s.length;
contracts/p1/mixins/RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
contracts/p1/mixins/RewardableLib.sol::63 => uint256 erc20sLen = erc20s.length;
contracts/plugins/assets/Asset.sol::102 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
contracts/plugins/assets/Asset.sol::116 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
contracts/plugins/assets/Asset.sol::133 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
contracts/plugins/assets/FiatCollateral.sol::149 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
contracts/plugins/assets/RTokenAsset.sol::78 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
contracts/plugins/mocks/BadCollateralPlugin.sol::51 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
contracts/plugins/mocks/EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
contracts/plugins/mocks/EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
contracts/plugins/mocks/EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
contracts/plugins/mocks/EasyAuction.sol::377 => _minBuyAmount.length == 1 && _sellAmount.length == 1,
contracts/plugins/mocks/EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) {
contracts/plugins/mocks/EasyAuction.sol::524 => for (uint256 i = 0; i < orders.length; i++) {
contracts/plugins/mocks/GnosisMock.sol::70 => auctionId = auctions.length;
contracts/plugins/mocks/GnosisMock.sol::132 => return auctions.length;
contracts/plugins/mocks/GnosisMockReentrant.sol::34 => auctionId = auctions.length;
contracts/plugins/mocks/InvalidBrokerMock.sol::22 => uint48 public auctionLength; // {s} the length of an auction
contracts/plugins/mocks/vendor/EasyAuction.sol::152 => // solhint-disable-next-line max-line-length
contracts/plugins/mocks/vendor/EasyAuction.sol::202 => if (returndata.length > 0) {
contracts/plugins/mocks/vendor/EasyAuction.sol::204 => // solhint-disable-next-line max-line-length
contracts/plugins/mocks/vendor/EasyAuction.sol::842 => if (returndata.length > 0) {
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: [G003](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
contracts/facade/FacadeAct.sol::75 => if (cache.bm.tradesOpen() > 0) {
contracts/facade/FacadeAct.sol::142 => if (cache.rTokenTrader.tradesOpen() - tradesOpen > 0) {
contracts/facade/FacadeAct.sol::157 => if (cache.rsrTrader.tradesOpen() - tradesOpen > 0) {
contracts/facade/FacadeAct.sol::185 => if (cache.rTokenTrader.tradesOpen() - tradesOpen > 0) {
contracts/facade/FacadeAct.sol::209 => if (cache.rsrTrader.tradesOpen() - tradesOpen > 0) {
contracts/facade/FacadeAct.sol::368 => return size > 0 ? size : 1;
contracts/facade/FacadeRead.sol::60 => uint192 baskets = (rTok.totalSupply() > 0) // {BU}
contracts/facade/FacadeRead.sol::90 => rToken.totalSupply() > 0
contracts/facade/FacadeRead.sol::105 => uint192 midPrice = lowPrice > 0 ? lowPrice.plus(highPrice).div(2) : lowPrice;
contracts/facade/FacadeRead.sol::158 => if (i + left > 0) {
contracts/facade/FacadeRead.sol::257 => uint192 basketMidPrice = buPriceLow > 0 ? buPriceLow.plus(buPriceHigh).div(2) : buPriceLow;
contracts/facade/FacadeRead.sol::278 => uint192 midPrice = lowPrice > 0 ? lowPrice.plus(highPrice).div(2) : lowPrice;
contracts/facade/FacadeRead.sol::299 => uint192 midPrice = lowPrice > 0 ? lowPrice.plus(highPrice).div(2) : lowPrice;
contracts/facade/FacadeWrite.sol::28 => require(setup.primaryBasket.length > 0, "no collateral");
contracts/facade/FacadeWrite.sol::33 => require(setup.backups[i].backupCollateral.length > 0, "no backup collateral");
contracts/facade/FacadeWrite.sol::40 => (setup.beneficiaries[i].revShare.rTokenDist > 0 ||
contracts/facade/FacadeWrite.sol::41 => setup.beneficiaries[i].revShare.rsrDist > 0),
contracts/libraries/Fixed.sol::168 => if (numerator % divisor > 0) {
contracts/libraries/Fixed.sol::537 => if (mm > 0) result += 1;
contracts/mixins/Auth.sol::129 => // - longFreezes[caller] > 0
contracts/mixins/Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
contracts/mixins/Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
contracts/p0/BackingManager.sol::73 => if (tradesOpen > 0) return;
contracts/p0/BackingManager.sol::125 => if (rsrBal > 0) {
contracts/p0/BackingManager.sol::143 => uint192 rTok = (needed > 0) ? extraBUs.mulDiv(totalSupply, needed) : extraBUs;
contracts/p0/BackingManager.sol::168 => if (toRSR > 0) erc20s[i].safeTransfer(address(main.rsrTrader()), toRSR);
contracts/p0/BackingManager.sol::172 => if (toRToken > 0)
contracts/p0/BasketHandler.sol::194 => require(erc20s.length > 0, "cannot empty basket");
contracts/p0/BasketHandler.sol::394 => //    min(e.balanceOf(account) / quantity(e) for e in basket.erc20s if quantity(e) > 0),
contracts/p0/BasketHandler.sol::411 => // {BU} = either {BU} or {tok} / {tok/BU}; q > 0 because q = (n).div(_, CEIL) and n > 0
contracts/p0/BasketHandler.sol::445 => If unsoundPrimeWt(tgt) > 0 and len(backups(tgt)) == 0 for some tgt, then disabled' == true.
contracts/p0/BasketHandler.sol::483 => while (targetNames.length() > 0) targetNames.remove(targetNames.at(0));
contracts/p0/BasketHandler.sol::544 => // Now, unsoundPrimeWt(tgt) > 0
contracts/p0/BasketHandler.sol::628 => coll.refPerTok() > 0 &&
contracts/p0/BasketHandler.sol::629 => coll.targetPerRef() > 0;
contracts/p0/Broker.sol::56 => assert(req.sellAmount > 0);
contracts/p0/Broker.sol::112 => newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
contracts/p0/Distributor.sol::57 => require(totalShares > 0, "nothing to distribute");
contracts/p0/Distributor.sol::113 => require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");
contracts/p0/Furnace.sol::50 => if (amount > 0) rToken.melt(amount);
contracts/p0/Furnace.sol::57 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
contracts/p0/RToken.sol::52 => // List of accounts. If issuances[user].length > 0 then (user is in accounts)
contracts/p0/RToken.sol::95 => require(bytes(name_).length > 0, "name empty");
contracts/p0/RToken.sol::96 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p0/RToken.sol::97 => require(bytes(mandate_).length > 0, "mandate empty");
contracts/p0/RToken.sol::109 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
contracts/p0/RToken.sol::154 => require(amount > 0, "Cannot issue zero");
contracts/p0/RToken.sol::165 => uint192 baskets = (totalSupply() > 0) // {BU}
contracts/p0/RToken.sol::259 => if (numCanceled > 0) emit IssuancesCanceled(account, left, last, amtRToken);
contracts/p0/RToken.sol::291 => if (first == 0 && vestedAmount > 0) first = i;
contracts/p0/RToken.sol::293 => if (totalVested > 0) emit IssuancesCompleted(account, first, endId, totalVested);
contracts/p0/RToken.sol::319 => require(amount > 0, "Cannot redeem zero");
contracts/p0/RToken.sol::358 => if (amounts[i] > 0) {
contracts/p0/RToken.sol::381 => if (delta > 0) IERC20(address(erc20s[i])).safeTransfer(address(bm), delta);
contracts/p0/RToken.sol::390 => if (amt > 0) {
contracts/p0/RevenueTrader.sol::53 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
contracts/p0/StRSR.sol::60 => // List of accounts. If balances[user] > 0 then (user is in accounts)
contracts/p0/StRSR.sol::109 => require(bytes(name_).length > 0, "name empty");
contracts/p0/StRSR.sol::110 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p0/StRSR.sol::136 => require(rsrAmount > 0, "Cannot stake zero");
contracts/p0/StRSR.sol::144 => if (totalStaked > 0) stakeAmount = (rsrAmount * totalStaked) / rsrBacking;
contracts/p0/StRSR.sol::163 => require(stakeAmount > 0, "Cannot withdraw zero");
contracts/p0/StRSR.sol::184 => uint256 lastAvailableAt = index > 0 ? withdrawals[account][index - 1].availableAt : 0;
contracts/p0/StRSR.sol::238 => require(rsrAmount > 0, "Amount cannot be zero");
contracts/p0/StRSR.sol::442 => if (totalStaked > 0) {
contracts/p0/StRSR.sol::524 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
contracts/p0/StRSR.sol::531 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
contracts/p0/mixins/TradingLib.sol::30 => ///   buy != 0, buyAmount (unused) {buyTok}, buyPrice > 0 {UoA/buyTok}
contracts/p0/mixins/TradingLib.sol::48 => assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
contracts/p0/mixins/TradingLib.sol::450 => if (high > 0 && isEnoughToSell(rsrAsset, rsrAvailable, lotLow, rules.minTradeVolume)) {
contracts/p0/mixins/TradingLib.sol::523 => ///   sellPrice > 0 {UoA/sellTok}
contracts/p0/mixins/TradingLib.sol::524 => ///   buyPrice > 0 {UoA/buyTok}
contracts/p0/mixins/TradingLib.sol::549 => trade.sellPrice > 0 &&
contracts/p0/mixins/TradingLib.sol::551 => trade.buyPrice > 0 &&
contracts/p0/mixins/TradingLib.sol::620 => return size > 0 ? size : 1;
contracts/p0/mixins/TradingLib.sol::627 => return size > 0 ? size : 1;
contracts/p1/AssetRegistry.sol::78 => if (quantity > 0) basketHandler.disableBasket();
contracts/p1/AssetRegistry.sol::96 => if (quantity > 0) basketHandler.disableBasket();
contracts/p1/BackingManager.sol::109 => if (tradesOpen > 0) return;
contracts/p1/BackingManager.sol::173 => if (rsr.balanceOf(address(this)) > 0) {
contracts/p1/BackingManager.sol::200 => uint192 rTok = (needed > 0) ? extraBUs.mulDiv(totalSupply, needed) : extraBUs;
contracts/p1/BackingManager.sol::230 => // no div-by-0: Distributor guarantees (totals.rTokenTotal + totals.rsrTotal) > 0
contracts/p1/BackingManager.sol::240 => if (toRToken[i] > 0) erc20.safeTransfer(address(rTokenTrader), toRToken[i]);
contracts/p1/BackingManager.sol::241 => if (toRSR[i] > 0) erc20.safeTransfer(address(rsrTrader), toRSR[i]);
contracts/p1/BasketHandler.sol::95 => //      Additionally, setPrimeBasket() enforces prime-basket tokens must have a weight > 0.
contracts/p1/BasketHandler.sol::213 => require(erc20s.length > 0, "cannot empty basket");
contracts/p1/BasketHandler.sol::283 => //      disabled is only set in _switchBasket, and only if size > 0.
contracts/p1/BasketHandler.sol::301 => if (refPerTok > 0) {
contracts/p1/BasketHandler.sol::431 => //    min(e.balanceOf(account) / quantity(e) for e in basket.erc20s if quantity(e) > 0),
contracts/p1/BasketHandler.sol::443 => // We know that basket.refAmts[basket.erc20s[i]] > 0, so we have no baskets.
contracts/p1/BasketHandler.sol::452 => // {BU} = {tok} / {tok/BU}.  q > 0 because q = (n).div(_, CEIL) and n > 0
contracts/p1/BasketHandler.sol::486 => If unsoundPrimeWt(tgt) > 0 and len(backups(tgt)) == 0 for some tgt, then disabled' == true.
contracts/p1/BasketHandler.sol::523 => while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
contracts/p1/BasketHandler.sol::570 => // this div is safe: targetPerRef() > 0: goodCollateral check
contracts/p1/BasketHandler.sol::590 => // Now, unsoundPrimeWt(tgt) > 0
contracts/p1/BasketHandler.sol::619 => // this div is safe: targetPerRef > 0: goodCollateral check
contracts/p1/BasketHandler.sol::679 => coll.refPerTok() > 0 &&
contracts/p1/BasketHandler.sol::680 => coll.targetPerRef() > 0;
contracts/p1/Broker.sol::135 => newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
contracts/p1/Distributor.sol::83 => //   sum(values(w)) > 0
contracts/p1/Distributor.sol::98 => require(totalShares > 0, "nothing to distribute");
contracts/p1/Distributor.sol::182 => require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");
contracts/p1/Furnace.sol::83 => if (amount > 0) rToken.melt(amount);
contracts/p1/Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
contracts/p1/RToken.sol::156 => require(bytes(name_).length > 0, "name empty");
contracts/p1/RToken.sol::157 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p1/RToken.sol::158 => require(bytes(mandate_).length > 0, "mandate empty");
contracts/p1/RToken.sol::191 => require(amtRToken > 0, "Cannot issue zero");
contracts/p1/RToken.sol::202 => if (queue.basketNonce > 0 && queue.basketNonce != basketNonce) {
contracts/p1/RToken.sol::234 => totalSupply() > 0 ? mulDiv256(basketsNeeded, amtRToken, totalSupply()) : amtRToken
contracts/p1/RToken.sol::295 => if (queue.right > 0) {
contracts/p1/RToken.sol::440 => require(amount > 0, "Cannot redeem zero");
contracts/p1/RToken.sol::483 => uint256 prorata = (prorate > 0)
contracts/p1/RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
contracts/p1/RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
contracts/p1/StRSR.sol::168 => require(bytes(name_).length > 0, "name empty");
contracts/p1/StRSR.sol::169 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p1/StRSR.sol::213 => require(rsrAmount > 0, "Cannot stake zero");
contracts/p1/StRSR.sol::259 => require(stakeAmount > 0, "Cannot withdraw zero");
contracts/p1/StRSR.sol::315 => uint192 oldDrafts = firstId > 0 ? queue[firstId - 1].drafts : 0;
contracts/p1/StRSR.sol::376 => require(rsrAmount > 0, "Amount cannot be zero");
contracts/p1/StRSR.sol::391 => if (stakeRSR > 0) {
contracts/p1/StRSR.sol::406 => if (draftRSR > 0) {
contracts/p1/StRSR.sol::504 => if (totalStakes > 0) {
contracts/p1/StRSR.sol::559 => uint192 oldDrafts = index > 0 ? queue[index - 1].drafts : 0;
contracts/p1/StRSR.sol::560 => uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
contracts/p1/StRSR.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
contracts/p1/StRSR.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
contracts/p1/StRSRVotes.sol::171 => if (src != dst && amount > 0) {
contracts/p1/StRSRVotes.sol::202 => if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {
contracts/p1/mixins/RecollateralizationLib.sol::411 => high > 0 &&
contracts/p1/mixins/RewardableLib.sol::74 => if (deltas[i] > 0) {
contracts/p1/mixins/RewardableLib.sol::98 => if (amt > 0) {
contracts/p1/mixins/TradeLib.sol::26 => ///   buy != 0, buyAmount (unused) {buyTok}, buyPrice > 0 {UoA/buyTok}
contracts/p1/mixins/TradeLib.sol::44 => assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
contracts/p1/mixins/TradeLib.sol::83 => ///   sellPrice > 0 {UoA/sellTok}
contracts/p1/mixins/TradeLib.sol::84 => ///   buyPrice > 0 {UoA/buyTok}
contracts/p1/mixins/TradeLib.sol::109 => trade.sellPrice > 0 &&
contracts/p1/mixins/TradeLib.sol::111 => trade.buyPrice > 0 &&
contracts/p1/mixins/TradeLib.sol::183 => return size > 0 ? size : 1;
contracts/p1/mixins/TradeLib.sol::192 => return size > 0 ? size : 1;
contracts/plugins/aave/IStaticATokenLM.sol::117 => * @param staticAmount The amount of staticAToken to withdraw. If > 0, `dynamicAmount` needs to be 0
contracts/plugins/aave/IStaticATokenLM.sol::118 => * @param dynamicAmount The amount of underlying/aToken to withdraw. If > 0, `staticAmount` needs to be 0
contracts/plugins/aave/StaticATokenLM.sol::330 => if (staticAmount > 0) {
contracts/plugins/aave/StaticATokenLM.sol::422 => if (supply > 0 && rewardsAccrued > 0) {
contracts/plugins/aave/StaticATokenLM.sol::428 => if (rewardsAccrued > 0) {
contracts/plugins/aave/StaticATokenLM.sol::461 => if (reward > 0) {
contracts/plugins/aave/StaticATokenLM.sol::512 => if (balance > 0) {
contracts/plugins/assets/Asset.sol::48 => require(priceTimeout_ > 0, "price timeout zero");
contracts/plugins/assets/Asset.sol::50 => require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
contracts/plugins/assets/Asset.sol::52 => require(maxTradeVolume_ > 0, "invalid max trade volume");
contracts/plugins/assets/Asset.sol::53 => require(oracleTimeout_ > 0, "oracleTimeout zero");
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::29 => require(referenceERC20Decimals_ > 0, "referenceERC20Decimals missing");
contracts/plugins/assets/FiatCollateral.sol::74 => if (config.defaultThreshold > 0) {
contracts/plugins/assets/FiatCollateral.sol::75 => require(config.delayUntilDefault > 0, "delayUntilDefault zero");
contracts/plugins/assets/RTokenAsset.sol::29 => require(maxTradeVolume_ > 0, "invalid max trade volume");
contracts/plugins/mocks/ATokenMock.sol::81 => if (address(aaveToken) != address(0) && aaveBalances[msg.sender] > 0) {
contracts/plugins/mocks/AaveLendingPoolMock.sol::36 => return _normalizedIncome[asset] > 0 ? _normalizedIncome[asset] : 1e27;
contracts/plugins/mocks/ERC1271Mock.sol::28 => * MUST NOT modify state (using STATICCALL for solc < 0.5, view modifier for solc > 0.5)
contracts/plugins/mocks/EasyAuction.sol::164 => require(_auctionedSellAmount > 0, "cannot auction zero tokens");
contracts/plugins/mocks/EasyAuction.sol::165 => require(_minBuyAmount > 0, "tokens cannot be auctioned for free");
contracts/plugins/mocks/EasyAuction.sol::167 => minimumBiddingAmountPerOrder > 0,
contracts/plugins/mocks/EasyAuction.sol::289 => require(_minBuyAmounts[i] > 0, "_minBuyAmounts must be greater than 0");
contracts/plugins/mocks/EasyAuction.sol::427 => currentBidSum > 0 &&
contracts/plugins/mocks/EasyAuction.sol::597 => if (auctioningTokenAmount > 0) {
contracts/plugins/mocks/EasyAuction.sol::600 => if (biddingTokenAmount > 0) {
contracts/plugins/mocks/GnosisMock.sol::69 => require(auctionedSellAmount > 0, "sell amount is zero");
contracts/plugins/mocks/GnosisMock.sol::92 => require(bid.buyAmount > 0, "zero volume bid");
contracts/plugins/mocks/GnosisMockReentrant.sol::33 => require(auctionedSellAmount > 0, "sell amount is zero");
contracts/plugins/mocks/vendor/EasyAuction.sol::202 => if (returndata.length > 0) {
contracts/plugins/mocks/vendor/EasyAuction.sol::557 => require(b > 0, errorMessage);
contracts/plugins/mocks/vendor/EasyAuction.sol::698 => return size > 0;
contracts/plugins/mocks/vendor/EasyAuction.sol::842 => if (returndata.length > 0) {
contracts/plugins/trading/GnosisTrade.sol::128 => // Gnosis EasyAuction requires minBuyAmtPerOrder > 0
contracts/plugins/trading/GnosisTrade.sol::191 => if (sellBal > 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);
contracts/plugins/trading/GnosisTrade.sol::192 => if (boughtAmt > 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: [G006](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
contracts/libraries/Fixed.sol::33 => bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
contracts/p0/StRSR.sol::46 => keccak256(
contracts/p0/StRSR.sol::497 => bytes32 structHash = keccak256(
contracts/p0/mixins/TradingLib.sol::607 => assert(keccak256(reason) == UIntOutofBoundsHash);
contracts/p1/StRSR.sol::127 => keccak256(
contracts/p1/StRSR.sol::777 => bytes32 structHash = keccak256(
contracts/p1/StRSRVotes.sol::28 => keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
contracts/p1/StRSRVotes.sol::128 => _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),
contracts/p1/mixins/TradeLib.sol::170 => assert(keccak256(reason) == UIntOutofBoundsHash);
contracts/plugins/aave/StaticATokenLM.sol::42 => keccak256(
contracts/plugins/aave/StaticATokenLM.sol::46 => keccak256(
contracts/plugins/aave/StaticATokenLM.sol::50 => keccak256(
contracts/plugins/aave/StaticATokenLM.sol::54 => keccak256(
contracts/plugins/aave/StaticATokenLM.sol::146 => bytes32 digest = keccak256(
contracts/plugins/aave/StaticATokenLM.sol::150 => keccak256(
contracts/plugins/aave/StaticATokenLM.sol::174 => bytes32 digest = keccak256(
contracts/plugins/aave/StaticATokenLM.sol::178 => keccak256(
contracts/plugins/aave/StaticATokenLM.sol::214 => bytes32 digest = keccak256(
contracts/plugins/aave/StaticATokenLM.sol::218 => keccak256(
contracts/plugins/aave/StaticATokenLM.sol::268 => keccak256(
contracts/plugins/aave/StaticATokenLM.sol::271 => keccak256(bytes(name())),
contracts/plugins/aave/StaticATokenLM.sol::272 => keccak256(EIP712_REVISION),
contracts/plugins/mocks/BadERC20.sol::32 => bytes memory data = abi.encodePacked((bytes4(keccak256("absentDecimalsFn()"))));
contracts/plugins/mocks/ERC1271Mock.sol::11 => // bytes4(keccak256("isValidSignature(bytes32,bytes)")
contracts/plugins/mocks/vendor/EasyAuction.sol::9 => /// bytes4(keccak256("isAllowed(address,uint256,bytes)"))
contracts/vendor/ERC20PermitUpgradeable.sol::40 => keccak256(
contracts/vendor/ERC20PermitUpgradeable.sol::83 => bytes32 structHash = keccak256(
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Long Revert Strings

#### Impact
Issue Information: [G007](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
contracts/facade/DeployerRegistry.sol::4 => import "@openzeppelin/contracts/access/Ownable.sol";
contracts/facade/DeployerRegistry.sol::5 => import "../interfaces/IDeployerRegistry.sol";
contracts/facade/FacadeAct.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/facade/FacadeRead.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/facade/FacadeTest.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/facade/lib/FacadeWriteLib.sol::4 => import "../../plugins/governance/Governance.sol";
contracts/interfaces/IAsset.sol::4 => import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
contracts/interfaces/IAsset.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/interfaces/IAssetRegistry.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/interfaces/IBackingManager.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/interfaces/IBasketHandler.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/interfaces/IDeployer.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/interfaces/IDistributor.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/interfaces/IGnosis.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/interfaces/IMain.sol::4 => import "@openzeppelin/contracts-upgradeable/access/IAccessControlUpgradeable.sol";
contracts/interfaces/IMain.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/interfaces/IRToken.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/IERC20MetadataUpgradeable.sol";
contracts/interfaces/IRToken.sol::6 => import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";
contracts/interfaces/IRewardable.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/interfaces/IStRSR.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/IERC20MetadataUpgradeable.sol";
contracts/interfaces/IStRSR.sol::6 => import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";
contracts/interfaces/IStRSRVotes.sol::4 => import "@openzeppelin/contracts-upgradeable/governance/utils/IVotesUpgradeable.sol";
contracts/interfaces/ITrade.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/interfaces/ITrading.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/libraries/Array.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/libraries/Fixed.sol::26 => //   - @return is the value expressed  in "value space", where uint192(1e18) "is" 1.0
contracts/libraries/Fixed.sol::27 => //   - as-ints: is the value expressed in "implementation space", where uint192(1e18) "is" 1e18
contracts/libraries/Permit.sol::4 => import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";
contracts/libraries/Permit.sol::5 => import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";
contracts/mixins/Auth.sol::4 => import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";
contracts/mixins/ComponentRegistry.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/mixins/ComponentRegistry.sol::5 => import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
contracts/mixins/ComponentRegistry.sol::6 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/AssetRegistry.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/AssetRegistry.sol::5 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p0/BackingManager.sol::4 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/p0/BackingManager.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/BasketHandler.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/p0/BasketHandler.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/BasketHandler.sol::6 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p0/BasketHandler.sol::213 => require(0 < targetAmts[i], "invalid target amount; must be nonzero");
contracts/p0/BasketHandler.sol::610 => require(erc20s[i] != zero, "address zero is not valid collateral");
contracts/p0/Broker.sol::4 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p0/Broker.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/p0/Broker.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/p0/Broker.sol::7 => import "../plugins/trading/GnosisTrade.sol";
contracts/p0/Broker.sol::44 => "invalid Trade Implementation address"
contracts/p0/Deployer.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/p0/Deployer.sol::6 => import "../plugins/assets/RTokenAsset.sol";
contracts/p0/Distributor.sol::4 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/p0/Distributor.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/Distributor.sol::6 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p0/Main.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/p0/Main.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/RToken.sol::5 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/p0/RToken.sol::6 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/RToken.sol::7 => import "@openzeppelin/contracts/utils/math/Math.sol";
contracts/p0/RToken.sol::8 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p0/RToken.sol::9 => import "@openzeppelin/contracts/access/Ownable.sol";
contracts/p0/RToken.sol::14 => import "../libraries/RedemptionBattery.sol";
contracts/p0/RToken.sol::17 => import "../vendor/ERC20PermitUpgradeable.sol";
contracts/p0/RevenueTrader.sol::4 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/p0/RevenueTrader.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/StRSR.sol::4 => import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";
contracts/p0/StRSR.sol::5 => import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";
contracts/p0/StRSR.sol::6 => import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";
contracts/p0/StRSR.sol::7 => import "@openzeppelin/contracts/utils/math/Math.sol";
contracts/p0/StRSR.sol::8 => import "@openzeppelin/contracts/utils/Counters.sol";
contracts/p0/StRSR.sol::9 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p0/StRSR.sol::10 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/StRSR.sol::11 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/p0/StRSR.sol::47 => "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
contracts/p0/StRSR.sol::242 => require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");
contracts/p0/StRSR.sol::346 => require(from != address(0), "ERC20: transfer from the zero address");
contracts/p0/StRSR.sol::347 => require(to != address(0), "ERC20: transfer to the zero address");
contracts/p0/StRSR.sol::352 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
contracts/p0/StRSR.sol::390 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
contracts/p0/StRSR.sol::403 => require(owner != address(0), "ERC20: approve from the zero address");
contracts/p0/StRSR.sol::404 => require(spender != address(0), "ERC20: approve to the zero address");
contracts/p0/StRSR.sol::527 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p0/StRSR.sol::534 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p0/mixins/Component.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/p0/mixins/Component.sol::5 => import "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";
contracts/p0/mixins/Rewardable.sol::4 => import "@openzeppelin/contracts/utils/Address.sol";
contracts/p0/mixins/Trading.sol::4 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/p0/mixins/Trading.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/mixins/TradingLib.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p0/mixins/TradingLib.sol::6 => import "../../interfaces/IAssetRegistry.sol";
contracts/p0/mixins/TradingLib.sol::7 => import "../../interfaces/IBackingManager.sol";
contracts/p1/AssetRegistry.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/AssetRegistry.sol::5 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p1/BackingManager.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/BackingManager.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/BackingManager.sol::7 => import "../interfaces/IBackingManager.sol";
contracts/p1/BackingManager.sol::12 => import "./mixins/RecollateralizationLib.sol";
contracts/p1/BackingManager.sol::185 => //   (>== is "no less than, and nearly equal to")
contracts/p1/BasketHandler.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/p1/BasketHandler.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/BasketHandler.sol::6 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p1/BasketHandler.sol::231 => require(0 < targetAmts[i], "invalid target amount; must be nonzero");
contracts/p1/BasketHandler.sol::657 => require(erc20s[i] != zero, "address zero is not valid collateral");
contracts/p1/Broker.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/Broker.sol::5 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p1/Broker.sol::6 => import "@openzeppelin/contracts/proxy/Clones.sol";
contracts/p1/Broker.sol::12 => import "../plugins/trading/GnosisTrade.sol";
contracts/p1/Broker.sol::60 => "invalid Trade Implementation address"
contracts/p1/Deployer.sol::4 => import "@openzeppelin/contracts/proxy/Clones.sol";
contracts/p1/Deployer.sol::5 => import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
contracts/p1/Deployer.sol::6 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/p1/Deployer.sol::9 => import "../interfaces/IBackingManager.sol";
contracts/p1/Deployer.sol::20 => import "../plugins/assets/RTokenAsset.sol";
contracts/p1/Distributor.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/Distributor.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/Distributor.sol::6 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/p1/Main.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/p1/Main.sol::5 => import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
contracts/p1/Main.sol::6 => import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
contracts/p1/Main.sol::7 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/RToken.sol::5 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/RToken.sol::9 => import "../libraries/RedemptionBattery.sol";
contracts/p1/RToken.sol::12 => import "../vendor/ERC20PermitUpgradeable.sol";
contracts/p1/RevenueTrader.sol::4 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/RevenueTrader.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/StRSR.sol::4 => import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";
contracts/p1/StRSR.sol::5 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/StRSR.sol::6 => import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";
contracts/p1/StRSR.sol::7 => import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";
contracts/p1/StRSR.sol::8 => import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";
contracts/p1/StRSR.sol::9 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/StRSR.sol::128 => "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
contracts/p1/StRSR.sol::380 => require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");
contracts/p1/StRSR.sol::660 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
contracts/p1/StRSR.sol::675 => require(from != address(0), "ERC20: transfer from the zero address");
contracts/p1/StRSR.sol::676 => require(to != address(0), "ERC20: transfer to the zero address");
contracts/p1/StRSR.sol::680 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
contracts/p1/StRSR.sol::711 => require(account != address(0), "ERC20: burn from the zero address");
contracts/p1/StRSR.sol::717 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
contracts/p1/StRSR.sol::732 => require(owner != address(0), "ERC20: approve from the zero address");
contracts/p1/StRSR.sol::733 => require(spender != address(0), "ERC20: approve to the zero address");
contracts/p1/StRSR.sol::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p1/StRSR.sol::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p1/StRSRVotes.sol::4 => import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";
contracts/p1/StRSRVotes.sol::5 => import "@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol";
contracts/p1/StRSRVotes.sol::28 => keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
contracts/p1/mixins/Component.sol::4 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/p1/mixins/Component.sol::5 => import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
contracts/p1/mixins/Component.sol::6 => import "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";
contracts/p1/mixins/RecollateralizationLib.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/mixins/RecollateralizationLib.sol::6 => import "../../interfaces/IAssetRegistry.sol";
contracts/p1/mixins/RecollateralizationLib.sol::7 => import "../../interfaces/IBackingManager.sol";
contracts/p1/mixins/RewardableLib.sol::4 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/p1/mixins/RewardableLib.sol::5 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/mixins/RewardableLib.sol::6 => import "@openzeppelin/contracts/utils/Address.sol";
contracts/p1/mixins/RewardableLib.sol::7 => import "../../interfaces/IAssetRegistry.sol";
contracts/p1/mixins/RewardableLib.sol::8 => import "../../interfaces/IBackingManager.sol";
contracts/p1/mixins/TradeLib.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/mixins/TradeLib.sol::6 => import "../../interfaces/IAssetRegistry.sol";
contracts/p1/mixins/Trading.sol::4 => import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";
contracts/p1/mixins/Trading.sol::5 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/p1/mixins/Trading.sol::6 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/p1/mixins/Trading.sol::7 => import "@openzeppelin/contracts/utils/Multicall.sol";
contracts/plugins/aave/ERC20.sol::5 => import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/Context.sol";
contracts/plugins/aave/ERC20.sol::6 => import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";
contracts/plugins/aave/ERC20.sol::7 => import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/SafeMath.sol";
contracts/plugins/aave/ERC20.sol::8 => import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/Address.sol";
contracts/plugins/aave/ERC20.sol::170 => "ERC20: transfer amount exceeds allowance"
contracts/plugins/aave/ERC20.sol::217 => "ERC20: decreased allowance below zero"
contracts/plugins/aave/ERC20.sol::242 => require(sender != address(0), "ERC20: transfer from the zero address");
contracts/plugins/aave/ERC20.sol::243 => require(recipient != address(0), "ERC20: transfer to the zero address");
contracts/plugins/aave/ERC20.sol::247 => _balances[sender] = _balances[sender].sub(amount, "ERC20: transfer amount exceeds balance");
contracts/plugins/aave/ERC20.sol::283 => require(account != address(0), "ERC20: burn from the zero address");
contracts/plugins/aave/ERC20.sol::287 => _balances[account] = _balances[account].sub(amount, "ERC20: burn amount exceeds balance");
contracts/plugins/aave/ERC20.sol::310 => require(owner != address(0), "ERC20: approve from the zero address");
contracts/plugins/aave/ERC20.sol::311 => require(spender != address(0), "ERC20: approve to the zero address");
contracts/plugins/aave/IAToken.sol::4 => import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";
contracts/plugins/aave/IAToken.sol::5 => import "@aave/protocol-v2/contracts/interfaces/IScaledBalanceToken.sol";
contracts/plugins/aave/IStaticATokenLM.sol::5 => import { IERC20 } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";
contracts/plugins/aave/IStaticATokenLM.sol::6 => import { ILendingPool } from "@aave/protocol-v2/contracts/interfaces/ILendingPool.sol";
contracts/plugins/aave/RayMathNoRounding.sol::5 => import { Errors } from "@aave/protocol-v2/contracts/protocol/libraries/helpers/Errors.sol";
contracts/plugins/aave/StaticATokenLM.sol::5 => import { ILendingPool } from "@aave/protocol-v2/contracts/interfaces/ILendingPool.sol";
contracts/plugins/aave/StaticATokenLM.sol::6 => import { IERC20 } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";
contracts/plugins/aave/StaticATokenLM.sol::8 => import { IERC20Detailed } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20Detailed.sol";
contracts/plugins/aave/StaticATokenLM.sol::18 => import { SafeERC20 } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/SafeERC20.sol";
contracts/plugins/aave/StaticATokenLM.sol::19 => import { WadRayMath } from "@aave/protocol-v2/contracts/protocol/libraries/math/WadRayMath.sol";
contracts/plugins/aave/StaticATokenLM.sol::21 => import { SafeMath } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/SafeMath.sol";
contracts/plugins/aave/StaticATokenLM.sol::32 => ERC20("STATIC_ATOKEN_IMPL", "STATIC_ATOKEN_IMPL"),
contracts/plugins/aave/StaticATokenLM.sol::43 => "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
contracts/plugins/aave/StaticATokenLM.sol::47 => "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
contracts/plugins/aave/StaticATokenLM.sol::51 => "Deposit(address depositor,address recipient,uint256 value,uint16 referralCode,bool fromUnderlying,uint256 nonce,uint256 deadline)"
contracts/plugins/aave/StaticATokenLM.sol::55 => "Withdraw(address owner,address recipient,uint256 staticAmount,uint256 dynamicAmount,bool toUnderlying,uint256 nonce,uint256 deadline)"
contracts/plugins/assets/ATokenFiatCollateral.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/assets/Asset.sol::4 => import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
contracts/plugins/assets/Asset.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/assets/CTokenFiatCollateral.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/assets/CTokenNonFiatCollateral.sol::30 => "missing target unit chainlink feed"
contracts/plugins/assets/EURFiatCollateral.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/assets/FiatCollateral.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/assets/ICToken.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/assets/NonFiatCollateral.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/assets/OracleLib.sol::4 => import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
contracts/plugins/assets/RTokenAsset.sol::4 => import "../../p1/mixins/RecollateralizationLib.sol";
contracts/plugins/assets/SelfReferentialCollateral.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/governance/Governance.sol::4 => import "@openzeppelin/contracts/governance/Governor.sol";
contracts/plugins/governance/Governance.sol::5 => import "@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol";
contracts/plugins/governance/Governance.sol::6 => import "@openzeppelin/contracts/governance/extensions/GovernorSettings.sol";
contracts/plugins/governance/Governance.sol::7 => import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";
contracts/plugins/governance/Governance.sol::8 => import "@openzeppelin/contracts/governance/extensions/GovernorVotes.sol";
contracts/plugins/governance/Governance.sol::9 => import "@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol";
contracts/plugins/mocks/ATokenMock.sol::4 => import "../assets/ATokenFiatCollateral.sol";
contracts/plugins/mocks/BadCollateralPlugin.sol::4 => import "../assets/ATokenFiatCollateral.sol";
contracts/plugins/mocks/BadERC20.sol::4 => import "@openzeppelin/contracts/utils/Address.sol";
contracts/plugins/mocks/CTokenMock.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/mocks/ChainlinkMock.sol::4 => import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
contracts/plugins/mocks/ComptrollerMock.sol::4 => import "contracts/plugins/assets/ICToken.sol";
contracts/plugins/mocks/ERC1271Mock.sol::4 => import "@openzeppelin/contracts/utils/Address.sol";
contracts/plugins/mocks/ERC20Mock.sol::4 => import "@openzeppelin/contracts/token/ERC20/ERC20.sol";
contracts/plugins/mocks/EasyAuction.sol::21 => "no longer in order placement phase"
contracts/plugins/mocks/EasyAuction.sol::29 => "no longer in order placement and cancelation phase"
contracts/plugins/mocks/EasyAuction.sol::41 => "Auction not in solution submission phase"
contracts/plugins/mocks/EasyAuction.sol::131 => require(newFeeNumerator <= 15, "Fee is not allowed to be set higher than 1.5%");
contracts/plugins/mocks/EasyAuction.sol::165 => require(_minBuyAmount > 0, "tokens cannot be auctioned for free");
contracts/plugins/mocks/EasyAuction.sol::168 => "minimumBiddingAmountPerOrder is not allowed to be zero"
contracts/plugins/mocks/EasyAuction.sol::172 => "time periods are not configured correctly"
contracts/plugins/mocks/EasyAuction.sol::174 => require(auctionEndDate > block.timestamp, "auction end date must be in the future");
contracts/plugins/mocks/EasyAuction.sol::281 => "limit price not better than mimimal offer"
contracts/plugins/mocks/EasyAuction.sol::289 => require(_minBuyAmounts[i] > 0, "_minBuyAmounts must be greater than 0");
contracts/plugins/mocks/EasyAuction.sol::326 => require(userIdOfIter == userId, "Only the user can cancel his orders");
contracts/plugins/mocks/EasyAuction.sol::374 => "not allowed to settle auction atomically"
contracts/plugins/mocks/EasyAuction.sol::378 => "Only one order can be placed atomically"
contracts/plugins/mocks/EasyAuction.sol::385 => "precalculateSellAmountSum is already too advanced"
contracts/plugins/mocks/EasyAuction.sol::526 => require(userIdOrder == userId, "only allowed to claim for same user");
contracts/plugins/mocks/GnosisMock.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/plugins/mocks/GnosisMock.sol::5 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/plugins/mocks/GnosisMock.sol::6 => import "@openzeppelin/contracts/utils/math/Math.sol";
contracts/plugins/mocks/GnosisMockReentrant.sol::4 => import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
contracts/plugins/mocks/GnosisMockReentrant.sol::5 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol::4 => import "../assets/ATokenFiatCollateral.sol";
contracts/plugins/mocks/InvalidBrokerMock.sol::4 => import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
contracts/plugins/mocks/InvalidBrokerMock.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/mocks/InvalidBrokerMock.sol::6 => import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
contracts/plugins/mocks/MockableCollateral.sol::4 => import "../assets/ATokenFiatCollateral.sol";
contracts/plugins/mocks/RTokenCollateral.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/mocks/UnpricedAssetPlugin.sol::4 => import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";
contracts/plugins/mocks/UnpricedAssetPlugin.sol::5 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/mocks/WETH.sol::326 => A "User Product" is either (1) a "consumer product", which means any
contracts/plugins/mocks/vendor/EasyAuction.sol::155 => "SafeERC20: approve from non-zero to non-zero allowance"
contracts/plugins/mocks/vendor/EasyAuction.sol::179 => "SafeERC20: decreased allowance below zero"
contracts/plugins/mocks/vendor/EasyAuction.sol::205 => require(abi.decode(returndata, (bool)), "SafeERC20: ERC20 operation did not succeed");
contracts/plugins/mocks/vendor/EasyAuction.sol::362 => require(userIdLeft != userIdRight, "user is not allowed to place same order twice");
contracts/plugins/mocks/vendor/EasyAuction.sol::370 => require(!isEmpty(self), "Trying to get first from empty set");
contracts/plugins/mocks/vendor/EasyAuction.sol::375 => require(value != QUEUE_END, "Trying to get next of last element");
contracts/plugins/mocks/vendor/EasyAuction.sol::377 => require(nextElement != bytes32(0), "Trying to get next of non-existent element");
contracts/plugins/mocks/vendor/EasyAuction.sol::519 => require(c / a == b, "SafeMath: multiplication overflow");
contracts/plugins/mocks/vendor/EasyAuction.sol::657 => require(value < 2**96, "SafeCast: value doesn't fit in 96 bits");
contracts/plugins/mocks/vendor/EasyAuction.sol::662 => require(value < 2**64, "SafeCast: value doesn't fit in 64 bits");
contracts/plugins/mocks/vendor/EasyAuction.sol::722 => require(success, "Address: unable to send value, recipient may have reverted");
contracts/plugins/mocks/vendor/EasyAuction.sol::778 => functionCallWithValue(target, data, value, "Address: low-level call with value failed");
contracts/plugins/mocks/vendor/EasyAuction.sol::793 => require(address(this).balance >= value, "Address: insufficient balance for call");
contracts/plugins/mocks/vendor/EasyAuction.sol::812 => return functionStaticCall(target, data, "Address: low-level static call failed");
contracts/plugins/mocks/vendor/EasyAuction.sol::826 => require(isContract(target), "Address: static call to non-contract");
contracts/plugins/mocks/vendor/EasyAuction.sol::936 => require(newOwner != address(0), "Ownable: new owner is the zero address");
contracts/plugins/trading/GnosisTrade.sol::4 => import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
contracts/plugins/trading/GnosisTrade.sol::5 => import "@openzeppelin/contracts/utils/math/Math.sol";
contracts/plugins/trading/GnosisTrade.sol::6 => import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
contracts/vendor/ERC20PermitUpgradeable.sol::7 => import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";
contracts/vendor/ERC20PermitUpgradeable.sol::8 => import "@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol";
contracts/vendor/ERC20PermitUpgradeable.sol::9 => import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";
contracts/vendor/ERC20PermitUpgradeable.sol::10 => import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";
contracts/vendor/ERC20PermitUpgradeable.sol::11 => import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
contracts/vendor/ERC20PermitUpgradeable.sol::41 => "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: [G008](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
contracts/facade/FacadeRead.sol::191 => uint256 test = (left + right) / 2;
contracts/facade/FacadeTest.sol::12 => uint192 constant FIX_TWO = FIX_ONE * 2;
contracts/libraries/Fixed.sol::164 => if (numerator % divisor > (divisor - 1) / 2) {
contracts/libraries/Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
contracts/libraries/Fixed.sol::323 => if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
contracts/libraries/Fixed.sol::326 => x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
contracts/libraries/Fixed.sol::491 => //    https://medium.com/coinmonks/4db014e080b1, https://medium.com/wicketh/afa55870a65
contracts/libraries/Fixed.sol::539 => if (mm > ((z - 1) / 2)) result += 1; // z should be z-1
contracts/libraries/Fixed.sol::544 => /// Return (x*y) as a "virtual uint512" (lo, hi), representing (hi*2**256 + lo)
contracts/libraries/Fixed.sol::546 => ///   https://medium.com/wicketh/27650fec525d, https://medium.com/coinmonks/4db014e080b1
contracts/libraries/Fixed.sol::548 => /// @return hi (hi, lo) satisfies  hi*(2**256) + lo == x * y
contracts/mixins/Auth.sol::205 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/mixins/ComponentRegistry.sol::117 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p0/BasketHandler.sol::343 => // if we overflowed *, then return FIX_MAX
contracts/p0/BasketHandler.sol::347 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
contracts/p0/BasketHandler.sol::638 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p0/StRSR.sol::527 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p0/StRSR.sol::534 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p1/AssetRegistry.sol::182 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/BackingManager.sol::103 => // only called internally, from manageTokens*, so erc20s has no duplicates unique
contracts/p1/BackingManager.sol::272 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/BasketHandler.sol::368 => // if we overflowed *, then return FIX_MAX
contracts/p1/BasketHandler.sol::372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
contracts/p1/BasketHandler.sol::374 => //          A)  shiftDelta = rawDelta + (FIX_ONE / 2)
contracts/p1/BasketHandler.sol::376 => //          B)  shiftDelta = MAX_UINT256 - FIX_ONE/2 + 1
contracts/p1/BasketHandler.sol::377 => //              rawDelta + (FIX_ONE/2) = MAX_UINT256 - FIX_ONE/2 + 1
contracts/p1/BasketHandler.sol::736 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/Broker.sol::151 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/Distributor.sol::188 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/Furnace.sol::106 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/Main.sol::69 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/RToken.sol::843 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/RevenueTrader.sol::99 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/StRSR.sol::133 => // Invariant: rewardPeriod * 2 <= unstakingDelay
contracts/p1/StRSR.sol::427 => return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
contracts/p1/StRSR.sol::453 => test = (left + right) / 2; // left < test < right because left < right - 1
contracts/p1/StRSR.sol::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p1/StRSR.sol::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p1/StRSR.sol::837 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/StRSRVotes.sol::225 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/mixins/Component.sol::62 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/p1/mixins/Trading.sol::155 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
contracts/plugins/aave/ERC20.sol::18 => * https://forum.zeppelin.solutions/t/how-to-implement-erc20-supply-mechanisms/226[How
contracts/plugins/aave/ERC20.sol::81 => * be displayed to a user as `5,05` (`505 / 10 ** 2`).
contracts/plugins/aave/IStaticATokenLM.sol::68 => * https://github.com/ethereum/EIPs/blob/8a34d644aacf0f9f8f00815307fd7dd5da07655f/EIPS/eip-2612.md
contracts/plugins/aave/IStaticATokenLM.sol::89 => * https://github.com/ethereum/EIPs/blob/8a34d644aacf0f9f8f00815307fd7dd5da07655f/EIPS/eip-2612.md
contracts/plugins/aave/IStaticATokenLM.sol::114 => * https://github.com/ethereum/EIPs/blob/8a34d644aacf0f9f8f00815307fd7dd5da07655f/EIPS/eip-2612.md
contracts/plugins/mocks/EasyAuction.sol::139 => // 2^96 units of the biddingToken, don't start the auction, as
contracts/plugins/mocks/vendor/EasyAuction.sol::68 => * https://github.com/ethereum/EIPs/issues/20#issuecomment-263524729
contracts/plugins/mocks/vendor/EasyAuction.sol::339 => // 2. by the sellAmount
contracts/plugins/mocks/vendor/EasyAuction.sol::429 => // (a + b) / 2 can overflow, so we distribute
contracts/plugins/mocks/vendor/EasyAuction.sol::430 => return (a / 2) + (b / 2) + (((a % 2) + (b % 2)) / 2);
contracts/plugins/mocks/vendor/EasyAuction.sol::710 => * https://diligence.consensys.net/posts/2019/09/stop-using-soliditys-transfer-now/[Learn more].
contracts/plugins/mocks/vendor/EasyAuction.sol::873 => this; // silence state mutability warning without generating bytecode - see https://github.com/ethereum/solidity/issues/2691
contracts/vendor/ERC20PermitUpgradeable.sol::125 => * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unsafe ERC20 Operation(s)

#### Impact
Issue Information: [L001](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l001---unsafe-erc20-operations)

#### Findings:
```
contracts/p1/Distributor.sol::86 => //     erc20.transferFrom(from, addrOf(dest), tokensPerShare * w[dest])
contracts/p1/RToken.sol::250 => //   for each token index i, erc20s[i].transferFrom(issuer, backingManager, iss.deposits[i])
contracts/p1/RToken.sol::285 => //   for each token index i, erc20s[i].transferFrom(issuer, this, iss.deposits[i])
contracts/p1/RToken.sol::438 => //     do token.transferFrom(backingManager, caller, min(tokenAmt, prorataAmt))
contracts/p1/RToken.sol::654 => //     issueQueues[account].erc20s[i].transfer(account, iss.deposits[i])
contracts/p1/RToken.sol::735 => //       issueQueues[account].erc20s[i].transfer(backingManager, iss.deposits[i]
contracts/p1/StRSR.sol::211 => //   rsr.transferFrom(account, this, rsrAmount)
contracts/p1/StRSR.sol::299 => //   rsr.transfer(account, rsrOut)
contracts/p1/StRSR.sol::368 => //   as (this), rsr.transfer(backingManager, seized)
contracts/plugins/mocks/WETH.sol::45 => msg.sender.transfer(wad);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
contracts/libraries/Fixed.sol::3 => pragma solidity ^0.8.9;
contracts/libraries/test/ArrayCallerMock.sol::2 => pragma solidity ^0.8.9;
contracts/libraries/test/FixedCallerMock.sol::2 => pragma solidity ^0.8.9;
contracts/libraries/test/StringCallerMock.sol::2 => pragma solidity ^0.8.9;
contracts/plugins/aave/ERC20.sol::3 => pragma solidity ^0.6.0;
contracts/plugins/aave/ReentrancyGuard.sol::3 => pragma solidity >=0.6.0 <0.8.0;
contracts/plugins/mocks/ChainlinkMock.sol::2 => pragma solidity ^0.8.0;
contracts/plugins/mocks/InvalidChainlinkMock.sol::2 => pragma solidity ^0.8.0;
contracts/plugins/mocks/WETH.sol::16 => pragma solidity >=0.4.22 <0.6;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### Do not use Deprecated Library Functions

#### Impact
Issue Information: [L005](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l005---do-not-use-deprecated-library-functions)

#### Findings:
```
contracts/p0/BackingManager.sol::47 => erc20.safeApprove(address(main.rToken()), 0);
contracts/p0/BackingManager.sol::48 => erc20.safeApprove(address(main.rToken()), type(uint256).max);
contracts/p0/RevenueTrader.sol::41 => erc20.safeApprove(address(main.distributor()), 0);
contracts/p0/RevenueTrader.sol::42 => erc20.safeApprove(address(main.distributor()), bal);
contracts/p0/mixins/Trading.sol::61 => req.sell.erc20().safeApprove(address(broker), 0);
contracts/p0/mixins/Trading.sol::62 => req.sell.erc20().safeApprove(address(broker), req.sellAmount);
contracts/p1/BackingManager.sol::75 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
contracts/p1/BackingManager.sol::76 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
contracts/p1/RevenueTrader.sol::61 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), 0);
contracts/p1/RevenueTrader.sol::62 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), bal);
contracts/p1/mixins/Trading.sol::116 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);
contracts/p1/mixins/Trading.sol::117 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);
contracts/plugins/aave/StaticATokenLM.sol::101 => ASSET.safeApprove(address(pool), type(uint256).max);
contracts/plugins/mocks/vendor/EasyAuction.sol::144 => function safeApprove(
contracts/plugins/trading/GnosisTrade.sol::136 => IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), 0);
contracts/plugins/trading/GnosisTrade.sol::137 => IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), initBal);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

