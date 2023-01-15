## [L-01] Compiler version Pragma is non-specific

### Description

For non-library contracts, floating pragmas may be a security risk for application implementations, since a known vulnerable compiler version may accidentally be selected or security tools might fallback to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

### Findings

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L3 => pragma solidity ^0.6.0;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L2 => pragma solidity ^0.8.0;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L2 => pragma solidity ^0.8.0;
```

### Resources

- [L003 - Unspecific Compiler Version Pragma](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)
- [Version Pragma | Solidity documents](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
- [4.6 Unspecific compiler version pragma | Consensys Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)


## [L-02] Unsafe ERC20 Operation: _mint

### Description

`_SafeMint` is a better option than the `_mint` function, as it helps to prevent unauthorized token minting.

### Findings

- [contracts/p1/StRSRVotes.sol#L138](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L138) => `super._mint(account, amount);`

### Resources

- [_mint](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_mint-address-uint256-) vs [_safeMint](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_safeMint-address-uint256-bytes-) | OpenZeppelin Docs



## [L-03] require()/revert() statements should have descriptive reason strings

### Description

To increase overall code clarity and aid in debugging whenever a need is not met, think about adding precise, informative error messages to all `require` and `revert` statements.

### Findings

```Solidity
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol
::102 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
::116 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
::133 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol
::149 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol
::78 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadCollateralPlugin.sol
::51 =>                 if (errData.length == 0) revert(); // solhint-disable-line reason-string
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol
::32 =>             revert(); // Revert with no reason
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol
::43 =>         require(balanceOf[msg.sender] >= wad);
::68 =>         require(balanceOf[src] >= wad);
::71 =>             require(allowance[src][msg.sender] >= wad);
```

### Resources

- [Error handling: Assert, Require, Revert and Exceptions](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#error-handling-assert-require-revert-and-exceptions)
- [Missing error messages in require statements | Opyn Bull Strategy Contracts Audit](https://blog.openzeppelin.com/opyn-bull-strategy-contracts-audit/#missing-error-messages-in-require-statements)



## [L-04] Unnamed return parameters

### Description

To increase explicitness and readability, take into account introducing and utilizing named return parameters.

### Findings

```Solidity
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol
::66 =>     function worseThan(CollateralStatus a, CollateralStatus b) internal pure returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol
::9 =>     function allUnique(IERC20[] memory arr) internal pure returns (bool) {
::21 =>     function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol
::72 => function _safeWrap(uint256 x) pure returns (uint192) {
::80 => function toFix(uint256 x) pure returns (uint192) {
::88 => function shiftl_toFix(uint256 x, int8 shiftLeft) pure returns (uint192) {
::116 => function divFix(uint256 x, uint192 y) pure returns (uint192) {
::129 => function divuu(uint256 x, uint256 y) pure returns (uint192) {
::135 => function fixMin(uint192 x, uint192 y) pure returns (uint192) {
::141 => function fixMax(uint192 x, uint192 y) pure returns (uint192) {
::147 => function abs(int256 x) pure returns (uint256) {
::182 =>     function toUint(uint192 x) internal pure returns (uint136) {
::189 =>     function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {
::197 =>     function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {
::222 =>     function plus(uint192 x, uint192 y) internal pure returns (uint192) {
::229 =>     function plusu(uint192 x, uint256 y) internal pure returns (uint192) {
::236 =>     function minus(uint192 x, uint192 y) internal pure returns (uint192) {
::243 =>     function minusu(uint192 x, uint256 y) internal pure returns (uint192) {
::251 =>     function mul(uint192 x, uint192 y) internal pure returns (uint192) {
::269 =>     function mulu(uint192 x, uint256 y) internal pure returns (uint192) {
::276 =>     function div(uint192 x, uint192 y) internal pure returns (uint192) {
::295 =>     function divu(uint192 x, uint256 y) internal pure returns (uint192) {
::316 =>     function powu(uint192 x_, uint48 y) internal pure returns (uint192) {
::332 =>     function lt(uint192 x, uint192 y) internal pure returns (bool) {
::336 =>     function lte(uint192 x, uint192 y) internal pure returns (bool) {
::340 =>     function gt(uint192 x, uint192 y) internal pure returns (bool) {
::344 =>     function gte(uint192 x, uint192 y) internal pure returns (bool) {
::348 =>     function eq(uint192 x, uint192 y) internal pure returns (bool) {
::352 =>     function neq(uint192 x, uint192 y) internal pure returns (bool) {
::375 =>     function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {
::401 =>     function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {
::419 =>     function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol
::11 =>     function toLower(string memory str) internal pure returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol
::8 =>     function allUnique(IERC20[] memory arr) public pure returns (bool) {
::12 =>     function sortedAndAllUnique(IERC20[] memory arr) public pure returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol
::9 =>     function toFix_(uint256 x) public pure returns (uint192 ) {
::12 =>     function shiftl_toFix_(uint256 x, int8 d) public pure returns (uint192 ) {
::15 =>     function shiftl_toFix_Rnd(uint256 x, int8 d, RoundingMode rnd) public pure returns (uint192 ) {
::19 =>     function divFix_(uint256 x, uint192  y) public pure returns (uint192 ) {
::22 =>     function divuu_(uint256 x, uint256 y) public pure returns (uint256) {
::26 =>     function fixMin_(uint192  x, uint192  y) public pure returns (uint192 ) {
::29 =>     function fixMax_(uint192  x, uint192  y) public pure returns (uint192 ) {
::32 =>     function abs_(int256 x) public pure returns (uint256) {
::35 =>     function divrnd_(uint256 n, uint256 d, RoundingMode rnd) public pure returns (uint256) {
::38 =>     function toUint(uint192  x) public pure returns (uint256 ) {
::41 =>     function toUintRnd(uint192  x, RoundingMode rnd) public pure returns (uint256 ) {
::44 =>     function shiftl(uint192  x, int8 decimals) public pure returns (uint192 ) {
::47 =>     function shiftlRnd(uint192  x, int8 decimals, RoundingMode rnd) public pure returns (uint192 ) {
::50 =>     function plus(uint192  x, uint192  y) public pure returns (uint192 ) {
::53 =>     function plusu(uint192  x, uint256 y) public pure returns (uint192 ) {
::56 =>     function minus(uint192  x, uint192  y) public pure returns (uint192 ) {
::59 =>     function minusu(uint192  x, uint256 y) public pure returns (uint192 ) {
::62 =>     function mul(uint192  x, uint192  y) public pure returns (uint192 ) {
::65 =>     function mulRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {
::68 =>     function mulu(uint192  x, uint256 y) public pure returns (uint192 ) {
::71 =>     function div(uint192  x, uint192  y) public pure returns (uint192 ) {
::74 =>     function divRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {
::77 =>     function divu(uint192  x, uint256 y) public pure returns (uint192 ) {
::80 =>     function divuRnd(uint192  x, uint256 y, RoundingMode rnd) public pure returns (uint192 ) {
::83 =>     function powu(uint192  x, uint48 y) public pure returns (uint192 ) {
::86 =>     function lt(uint192  x, uint192  y) public pure returns (bool) {
::89 =>     function lte(uint192  x, uint192  y) public pure returns (bool) {
::92 =>     function gt(uint192  x, uint192  y) public pure returns (bool) {
::95 =>     function gte(uint192  x, uint192  y) public pure returns (bool) {
::98 =>     function eq(uint192  x, uint192  y) public pure returns (bool) {
::101 =>     function neq(uint192  x, uint192  y) public pure returns (bool) {
::104 =>     function near(uint192  x, uint192  y, uint192  epsilon) public pure returns (bool) {
::109 =>     function shiftl_toUint(uint192  x, int8 d) public pure returns (uint256) {
::112 =>     function shiftl_toUintRnd(uint192  x, int8 d, RoundingMode rnd) public pure returns (uint256) {
::115 =>     function mulu_toUint(uint192  x, uint256 y) public pure returns (uint256) {
::118 =>     function mulu_toUintRnd(uint192  x, uint256 y, RoundingMode rnd) public pure returns (uint256) {
::121 =>     function mul_toUint(uint192  x, uint192  y) public pure returns (uint256) {
::124 =>     function mul_toUintRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint256) {
::127 =>     function muluDivu(uint192  x, uint256 y, uint256 z) public pure returns (uint192 ) {
::130 =>     function muluDivuRnd(uint192  x, uint256 y, uint256 z, RoundingMode rnd) public pure returns (uint192 ) {
::133 =>     function mulDiv(uint192  x, uint192  y, uint192  z) public pure returns (uint192 ) {
::136 =>     function mulDivRnd(uint192  x, uint192  y, uint192  z, RoundingMode rnd) public pure returns (uint192 ) {
::141 =>     function mulDiv256_(uint256 x, uint256 y, uint256 z) public pure returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/StringCallerMock.sol
::9 =>     function toLower(string memory str) external pure returns (string memory){
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol
::101 =>     function frozen() external view returns (bool) {
::107 =>     function pausedOrFrozen() public view returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Versioned.sol
::11 =>     function version() public pure virtual override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol
::61 =>     function register(IAsset asset) external governance returns (bool) {
::118 =>     function isRegistered(IERC20 erc20) external view returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
::273 =>     function fullyCollateralized() external view returns (bool) {
::296 =>     function quantity(IERC20 erc20) public view returns (uint192) {
::356 =>     function quantityMulPrice(uint192 qty, uint192 p) internal pure returns (uint192) {
::665 =>     function goodCollateral(bytes32 targetName, IERC20 erc20) private view returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol
::596 =>     function scalingRedemptionRate() external view returns (uint192) {
::609 =>     function redemptionRateFloor() external view returns (uint256) {
::621 =>     function issueItem(address account, uint256 index) external view returns (IssueItem memory) {
::628 =>     function redemptionLimit() external view returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
::425 =>     function exchangeRate() public view returns (uint192) {
::435 =>     function endIdForWithdraw(address account) external view returns (uint256) {
::462 =>     function draftQueueLen(uint256 era_, address account) external view returns (uint256) {
::596 =>     function rsrRewards() internal view returns (uint256) {
::604 =>     function totalSupply() public view returns (uint256) {
::608 =>     function balanceOf(address account) public view returns (uint256) {
::622 =>     function transfer(address to, uint256 amount) public returns (bool) {
::632 =>     function approve(address spender, uint256 amount) public returns (bool) {
::651 =>     function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {
::657 =>     function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
::786 =>     function nonces(address owner) public view returns (uint256) {
::791 =>     function DOMAIN_SEPARATOR() external view returns (bytes32) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol
::55 =>     function currentEra() external view returns (uint256) {
::59 =>     function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {
::63 =>     function numCheckpoints(address account) public view returns (uint48) {
::67 =>     function delegates(address account) public view returns (address) {
::71 =>     function getVotes(address account) public view returns (uint256) {
::76 =>     function getPastVotes(address account, uint256 blockNumber) public view returns (uint256) {
::82 =>     function getPastTotalSupply(uint256 blockNumber) public view returns (uint256) {
::88 =>     function getPastEra(uint256 blockNumber) public view returns (uint256) {
::195 =>         function(uint256, uint256) view returns (uint256) op,
::214 =>     function _add(uint256 a, uint256 b) private pure returns (uint256) {
::218 =>     function _subtract(uint256 a, uint256 b) private pure returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol
::180 =>     function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {
::188 =>     function maxTradeSize(IAsset asset, uint192 price) private view returns (uint192) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol
::66 =>     function name() public view returns (string memory) {
::74 =>     function symbol() public view returns (string memory) {
::91 =>     function decimals() public view returns (uint8) {
::98 =>     function totalSupply() public view override returns (uint256) {
::105 =>     function balanceOf(address account) public view override returns (uint256) {
::117 =>     function transfer(address recipient, uint256 amount) public virtual override returns (bool) {
::142 =>     function approve(address spender, uint256 amount) public virtual override returns (bool) {
::188 =>     function increaseAllowance(address spender, uint256 addedValue) public virtual returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol
::11 =>     function rayMulNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {
::19 =>     function rayDivNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {
::25 =>     function rayToWadNoRounding(uint256 a) internal pure returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol
::242 =>     function dynamicBalanceOf(address account) external view override returns (uint256) {
::247 =>     function staticToDynamicAmount(uint256 amount) external view override returns (uint256) {
::252 =>     function dynamicToStaticAmount(uint256 amount) external view override returns (uint256) {
::257 =>     function rate() public view override returns (uint256) {
::262 =>     function getDomainSeparator() public view override returns (bytes32) {
::279 =>     function _dynamicToStaticAmount(uint256 amount, uint256 rate_) internal pure returns (uint256) {
::283 =>     function _staticToDynamicAmount(uint256 amount, uint256 rate_) internal pure returns (uint256) {
::577 =>     function getTotalClaimableRewards() external view override returns (uint256) {
::589 =>     function getClaimableRewards(address user) external view override returns (uint256) {
::594 =>     function getUnclaimedRewards(address user) external view override returns (uint256) {
::598 =>     function getAccRewardsPerToken() external view override returns (uint256) {
::602 =>     function getLifetimeRewardsClaimed() external view override returns (uint256) {
::606 =>     function getLifetimeRewards() external view override returns (uint256) {
::610 =>     function getLastRewardBlock() external view override returns (uint256) {
::618 =>     function UNDERLYING_ASSET_ADDRESS() external view override returns (address) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol
::45 =>     function refPerTok() public view override returns (uint192) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol
::110 =>     function price() public view virtual returns (uint192, uint192) {
::151 =>     function bal(address account) external view returns (uint192) {
::156 =>     function isCollateral() external pure virtual returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol
::44 =>     function refPerTok() public view override returns (uint192) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol
::47 =>     function refPerTok() public view override returns (uint192) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol
::199 =>     function alreadyDefaulted() internal view returns (bool) {
::203 =>     function whenDefault() external view returns (uint256) {
::210 =>     function refPerTok() public view virtual returns (uint192) {
::215 =>     function targetPerRef() public view virtual returns (uint192) {
::220 =>     function isCollateral() external pure virtual override(Asset, IAsset) returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol
::104 =>     function bal(address account) external view returns (uint192) {
::111 =>     function isCollateral() external pure virtual returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol
::48 =>     function votingDelay() public view override(IGovernor, GovernorSettings) returns (uint256) {
::52 =>     function votingPeriod() public view override(IGovernor, GovernorSettings) returns (uint256) {
::170 =>     function startedInSameEra(uint256 proposalId) private view returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol
::21 =>     function UNDERLYING_ASSET_ADDRESS() external view returns (address) {
::50 =>     function decimals() public pure override returns (uint8) {
::54 =>     function rate() external view returns (uint256) {
::87 =>     function getClaimableRewards(address user) external view returns (uint256) {
::91 =>     function _toExchangeRate(uint192 fiatcoinRedemptionRate) internal pure returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/AaveLendingPoolMock.sol
::35 =>     function getReserveNormalizedIncome(address asset) external view returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol
::31 =>     function decimals() public view override returns (uint8) {
::39 =>     function transfer(address to, uint256 amount) public virtual override returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/CTokenMock.sol
::23 =>     function decimals() public pure override returns (uint8) {
::27 =>     function exchangeRateCurrent() external returns (uint256) {
::32 =>     function exchangeRateStored() external view returns (uint256) {
::41 =>     function underlying() external view returns (address) {
::45 =>     function _toExchangeRate(uint192 fiatcoinRedemptionRate) internal view returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol
::114 =>     function description() external pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ComptrollerMock.sol
::31 =>     function getCompAddress() external view returns (address) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol
::621 =>     function getSecondsRemainingInBatch(uint256 auctionId) public view returns (uint256) {
::628 =>     function containsOrder(uint256 auctionId, bytes32 order) public view returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol
::131 =>     function numAuctions() external view returns (uint256) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol
::38 =>     function openTrade(TradeRequest memory req) external view notPausedOrFrozen returns (ITrade) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidFiatCollateral.sol
::15 =>     function price() public view virtual returns (uint192, uint192) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/MockableCollateral.sol
::17 =>     function targetPerRef() public view override returns (uint192) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol
::12 =>     function targetPerRef() public view virtual override returns (uint192) {
::23 =>     function targetPerRef() public view virtual override returns (uint192) {
::34 =>     function targetPerRef() public view virtual override returns (uint192) {
::45 =>     function targetPerRef() public view virtual override returns (uint192) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol
::51 =>     function price() public view override(RTokenAsset, IAsset) returns (uint192 low, uint192 high) {
::69 =>     function status() public view virtual override returns (CollateralStatus) {
::80 =>     function isCollateral() external pure virtual override(RTokenAsset, IAsset) returns (bool) {
::85 =>     function refPerTok() public view virtual returns (uint192) {
::92 =>     function targetPerRef() public view virtual returns (uint192) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/USDCMock.sol
::10 =>     function decimals() public pure override returns (uint8) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WBTCMock.sol
::10 =>     function decimals() public pure override returns (uint8) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol
::49 =>     function totalSupply() public view returns (uint256) {
::53 =>     function approve(address guy, uint256 wad) public returns (bool) {
::59 =>     function transfer(address dst, uint256 wad) public returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ZeroDecimalMock.sol
::10 =>     function decimals() public pure override returns (uint8) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/AssetRegistryV2.sol
::13 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BackingManagerV2.sol
::14 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BasketHandlerV2.sol
::13 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BrokerV2.sol
::13 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/DistributorV2.sol
::13 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/FurnaceV2.sol
::13 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/MainV2.sol
::14 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/RTokenV2.sol
::14 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/RevenueTraderV2.sol
::14 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/StRSRV2.sol
::13 =>     function version() public pure override returns (string memory) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol
::248 =>     function isEmpty(Data storage self) internal view returns (bool) {
::304 =>     function removeKeepHistory(Data storage self, bytes32 elementToRemove) internal returns (bool) {
::320 =>     function remove(Data storage self, bytes32 elementToRemove) internal returns (bool) {
::328 =>     function contains(Data storage self, bytes32 value) internal view returns (bool) {
::341 =>     function smallerThan(bytes32 orderLeft, bytes32 orderRight) internal pure returns (bool) {
::369 =>     function first(Data storage self) internal view returns (bytes32) {
::374 =>     function next(Data storage self, bytes32 value) internal view returns (bytes32) {
::413 =>     function max(uint256 a, uint256 b) internal pure returns (uint256) {
::420 =>     function min(uint256 a, uint256 b) internal pure returns (uint256) {
::428 =>     function average(uint256 a, uint256 b) internal pure returns (uint256) {
::458 =>     function add(uint256 a, uint256 b) internal pure returns (uint256) {
::475 =>     function sub(uint256 a, uint256 b) internal pure returns (uint256) {
::510 =>     function mul(uint256 a, uint256 b) internal pure returns (uint256) {
::536 =>     function div(uint256 a, uint256 b) internal pure returns (uint256) {
::576 =>     function mod(uint256 a, uint256 b) internal pure returns (uint256) {
::614 =>     function hasId(Data storage self, uint64 id) internal view returns (bool) {
::618 =>     function hasAddress(Data storage self, address addr) internal view returns (bool) {
::622 =>     function getAddressAt(Data storage self, uint64 id) internal view returns (address) {
::627 =>     function getId(Data storage self, address addr) internal view returns (uint64) {
::656 =>     function toUint96(uint256 value) internal pure returns (uint96) {
::661 =>     function toUint64(uint256 value) internal pure returns (uint64) {
::688 =>     function isContract(address account) internal view returns (bool) {
::743 =>     function functionCall(address target, bytes memory data) internal returns (bytes memory) {
::868 =>     function _msgSender() internal view virtual returns (address payable) {
::872 =>     function _msgData() internal view virtual returns (bytes memory) {
::907 =>     function owner() public view returns (address) {
::957 =>     function insert(bytes32 value) public returns (bool) {
::961 =>     function insertAt(bytes32 value, bytes32 at) public returns (bool) {
::965 =>     function remove(bytes32 value) public returns (bool) {
::969 =>     function removeKeepHistory(bytes32 value) public returns (bool) {
::973 =>     function contains(bytes32 value) public view returns (bool) {
::977 =>     function isEmpty() public view returns (bool) {
::981 =>     function first() public view returns (bytes32) {
::985 =>     function next(bytes32 value) public view returns (bytes32) {
::989 =>     function nextMap(bytes32 value) public view returns (bytes32) {
::993 =>     function prevMap(bytes32 value) public view returns (bytes32) {
::1017 =>     function smallerThan(bytes32 orderLeft, bytes32 orderRight) public pure returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol
::222 =>     function canSettle() external view returns (bool) {
::228 =>     function isAuctionCleared() private view returns (bool) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol
::99 =>     function nonces(address owner) public view virtual override returns (uint256) {
::107 =>     function DOMAIN_SEPARATOR() external view override returns (bytes32) {
```

### Resource

- [Unnamed return parameters | Opyn Bull Strategy Contracts Audit](https://blog.openzeppelin.com/opyn-bull-strategy-contracts-audit/#unnamed-return-parameters)