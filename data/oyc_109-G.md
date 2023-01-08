## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
contracts/libraries/Array.sol::12 => for (uint256 j = 0; j < i; ++j) {
contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
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
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
contracts/p1/BasketHandler.sol::207 => //   config'.targetAmts[erc20s[i]] = targetAmts[i], for i from 0 to erc20s.length-1
contracts/p1/BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p1/BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/Distributor.sol::108 => for (uint256 i = 0; i < destinations.length(); ++i) {
contracts/p1/RToken.sol::145 => // - for each item in queue.items: queue.tokens.length == item.deposits.length
contracts/p1/RToken.sol::270 => for (uint256 i = 0; i < erc20s.length; ++i) {
contracts/p1/RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
contracts/p1/RToken.sol::734 => //     for i in [0, iss.deposits.length):
contracts/p1/mixins/RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
contracts/p1/mixins/RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
contracts/p1/mixins/RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
```

## [G-03] Using > 0 costs more gas than != 0 when used on a uint in a require() statement

When dealing with unsigned integer types, comparisons with != 0 are cheaper then with > 0. This change saves 6 gas per instance

```
contracts/mixins/Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
contracts/mixins/Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
contracts/p1/BasketHandler.sol::213 => require(erc20s.length > 0, "cannot empty basket");
contracts/p1/Distributor.sol::98 => require(totalShares > 0, "nothing to distribute");
contracts/p1/Distributor.sol::182 => require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");
contracts/p1/Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
contracts/p1/RToken.sol::156 => require(bytes(name_).length > 0, "name empty");
contracts/p1/RToken.sol::157 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p1/RToken.sol::158 => require(bytes(mandate_).length > 0, "mandate empty");
contracts/p1/RToken.sol::191 => require(amtRToken > 0, "Cannot issue zero");
contracts/p1/RToken.sol::440 => require(amount > 0, "Cannot redeem zero");
contracts/p1/RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
contracts/p1/RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
contracts/p1/StRSR.sol::168 => require(bytes(name_).length > 0, "name empty");
contracts/p1/StRSR.sol::169 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p1/StRSR.sol::213 => require(rsrAmount > 0, "Cannot stake zero");
contracts/p1/StRSR.sol::259 => require(stakeAmount > 0, "Cannot withdraw zero");
contracts/p1/StRSR.sol::376 => require(rsrAmount > 0, "Amount cannot be zero");
contracts/p1/StRSR.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
contracts/p1/StRSR.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
contracts/plugins/assets/Asset.sol::48 => require(priceTimeout_ > 0, "price timeout zero");
contracts/plugins/assets/Asset.sol::50 => require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
contracts/plugins/assets/Asset.sol::52 => require(maxTradeVolume_ > 0, "invalid max trade volume");
contracts/plugins/assets/Asset.sol::53 => require(oracleTimeout_ > 0, "oracleTimeout zero");
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::29 => require(referenceERC20Decimals_ > 0, "referenceERC20Decimals missing");
contracts/plugins/assets/FiatCollateral.sol::75 => require(config.delayUntilDefault > 0, "delayUntilDefault zero");
contracts/plugins/assets/RTokenAsset.sol::29 => require(maxTradeVolume_ > 0, "invalid max trade volume");
contracts/plugins/trading/GnosisTrade.sol::128 => // Gnosis EasyAuction requires minBuyAmtPerOrder > 0
```

## [G-04] Long Revert Strings

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

```
contracts/p1/BasketHandler.sol::231 => require(0 < targetAmts[i], "invalid target amount; must be nonzero");
contracts/p1/BasketHandler.sol::657 => require(erc20s[i] != zero, "address zero is not valid collateral");
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
contracts/plugins/aave/ERC20.sol::242 => require(sender != address(0), "ERC20: transfer from the zero address");
contracts/plugins/aave/ERC20.sol::243 => require(recipient != address(0), "ERC20: transfer to the zero address");
contracts/plugins/aave/ERC20.sol::283 => require(account != address(0), "ERC20: burn from the zero address");
contracts/plugins/aave/ERC20.sol::310 => require(owner != address(0), "ERC20: approve from the zero address");
contracts/plugins/aave/ERC20.sol::311 => require(spender != address(0), "ERC20: approve to the zero address");
```

## [G-05] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
contracts/libraries/Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
contracts/libraries/Fixed.sol::323 => if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
contracts/libraries/Fixed.sol::326 => x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
contracts/p1/BasketHandler.sol::372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
contracts/p1/StRSR.sol::453 => test = (left + right) / 2; // left < test < right because left < right - 1
```

## [G-06] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
contracts/interfaces/IBroker.sol::37 => function openTrade(TradeRequest memory req) external returns (ITrade);
contracts/libraries/Array.sol::9 => function allUnique(IERC20[] memory arr) internal pure returns (bool) {
contracts/libraries/Array.sol::21 => function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {
contracts/libraries/String.sol::11 => function toLower(string memory str) internal pure returns (string memory) {
contracts/p1/Broker.sol::85 => function openTrade(TradeRequest memory req) external notPausedOrFrozen returns (ITrade) {
```

## [G-07] Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

```
contracts/p1/Deployer.sol::33 => IERC20Metadata public immutable rsr;
contracts/p1/Deployer.sol::34 => IGnosis public immutable gnosis;
contracts/p1/Deployer.sol::35 => IAsset public immutable rsrAsset;
contracts/plugins/assets/Asset.sol::13 => AggregatorV3Interface public immutable chainlinkFeed; // {UoA/tok}
contracts/plugins/assets/Asset.sol::15 => IERC20Metadata public immutable erc20;
contracts/plugins/assets/Asset.sol::17 => uint8 public immutable erc20Decimals;
contracts/plugins/assets/Asset.sol::19 => uint192 public immutable override maxTradeVolume; // {UoA}
contracts/plugins/assets/Asset.sol::21 => uint48 public immutable oracleTimeout; // {s} Seconds that an oracle value is considered valid
contracts/plugins/assets/Asset.sol::23 => uint192 public immutable oracleError; // {1} The max % deviation allowed by the oracle
contracts/plugins/assets/Asset.sol::27 => uint48 public immutable priceTimeout; // {s} The period over which `savedHighPrice` decays to 0
contracts/plugins/assets/CTokenFiatCollateral.sol::20 => uint8 public immutable referenceERC20Decimals;
contracts/plugins/assets/CTokenFiatCollateral.sol::22 => IComptroller public immutable comptroller;
contracts/plugins/assets/CTokenNonFiatCollateral.sol::18 => AggregatorV3Interface public immutable targetUnitChainlinkFeed; // {UoA/target}
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::18 => uint8 public immutable referenceERC20Decimals;
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::20 => IComptroller public immutable comptroller;
contracts/plugins/assets/EURFiatCollateral.sol::17 => AggregatorV3Interface public immutable uoaPerTargetFeed; // {UoA/target}
contracts/plugins/assets/FiatCollateral.sol::50 => uint48 public immutable delayUntilDefault; // {s} e.g 86400
contracts/plugins/assets/FiatCollateral.sol::53 => bytes32 public immutable targetName;
contracts/plugins/assets/FiatCollateral.sol::55 => uint192 public immutable pegBottom; // {target/ref} The bottom of the peg
contracts/plugins/assets/FiatCollateral.sol::57 => uint192 public immutable pegTop; // {target/ref} The top of the peg
contracts/plugins/assets/NonFiatCollateral.sol::17 => AggregatorV3Interface public immutable uoaPerTargetFeed; // {UoA/target}
contracts/plugins/assets/RTokenAsset.sol::16 => IBasketHandler public immutable basketHandler;
contracts/plugins/assets/RTokenAsset.sol::17 => IAssetRegistry public immutable assetRegistry;
contracts/plugins/assets/RTokenAsset.sol::18 => IBackingManager public immutable backingManager;
contracts/plugins/assets/RTokenAsset.sol::20 => IERC20Metadata public immutable erc20;
contracts/plugins/assets/RTokenAsset.sol::22 => uint8 public immutable erc20Decimals;
contracts/plugins/assets/RTokenAsset.sol::24 => uint192 public immutable override maxTradeVolume; // {UoA}
```

## [G-08] Functions guaranteed to revert when called by normal users can be marked payable

If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

```
contracts/mixins/Auth.sol::120 => function freezeShort() external onlyRole(SHORT_FREEZER) {
contracts/mixins/Auth.sol::135 => function freezeLong() external onlyRole(LONG_FREEZER) {
contracts/mixins/Auth.sol::148 => function freezeForever() external onlyRole(OWNER) {
contracts/mixins/Auth.sol::157 => function unfreeze() external onlyRole(OWNER) {
contracts/mixins/Auth.sol::165 => function pause() external onlyRole(PAUSER) {
contracts/mixins/Auth.sol::172 => function unpause() external onlyRole(PAUSER) {
contracts/mixins/Auth.sol::180 => function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
contracts/mixins/Auth.sol::187 => function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
```

## [G-09] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
contracts/interfaces/IDistributor.sol::8 => uint16 rTokenDist; // {revShare} A value between [0, 10,000]
contracts/interfaces/IDistributor.sol::9 => uint16 rsrDist; // {revShare} A value between [0, 10,000]
contracts/libraries/Fixed.sol::37 => uint8 constant FIX_DECIMALS = 18;
contracts/libraries/Fixed.sol::41 => uint64 constant FIX_SCALE = 1e18;
contracts/libraries/Fixed.sol::44 => uint128 constant FIX_SCALE_SQ = 1e36;
contracts/libraries/Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
contracts/p1/Distributor.sol::34 => uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;
contracts/p1/StRSR.sol::45 => uint8 public constant decimals = 18;
contracts/p1/StRSR.sol::78 => uint64 availableAt; // When the last of the drafts will become available
contracts/p1/StRSR.sol::560 => uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
contracts/plugins/aave/ERC20.sol::46 => uint8 private _decimals;
contracts/plugins/aave/IStaticATokenLM.sol::11 => uint8 v;
contracts/plugins/assets/Asset.sol::17 => uint8 public immutable erc20Decimals;
contracts/plugins/assets/CTokenFiatCollateral.sol::20 => uint8 public immutable referenceERC20Decimals;
contracts/plugins/assets/CTokenFiatCollateral.sol::46 => int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::18 => uint8 public immutable referenceERC20Decimals;
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::49 => int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;
contracts/plugins/assets/RTokenAsset.sol::22 => uint8 public immutable erc20Decimals;
```

## [G-10] Using bools for storage incurs overhead

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use uint256(1) and uint256(2) for true/false instead

```
contracts/mixins/Auth.sol::42 => bool public paused;
contracts/p1/BasketHandler.sol::139 => bool private disabled;
contracts/p1/Broker.sol::41 => bool public disabled;
contracts/p1/Broker.sol::44 => mapping(address => bool) private trades;
```

## [G-11] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
contracts/libraries/Array.sol::11 => for (uint256 i = 1; i < arrLen; ++i) {
contracts/libraries/Array.sol::12 => for (uint256 j = 0; j < i; ++j) {
contracts/libraries/Array.sol::23 => for (uint256 i = 1; i < arrLen; ++i) {
contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
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
contracts/p1/BasketHandler.sol::553 => for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {
contracts/p1/BasketHandler.sol::586 => for (uint256 i = 0; i < targetsLength; ++i) {
contracts/p1/BasketHandler.sol::597 => for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
contracts/p1/BasketHandler.sol::598 => if (goodCollateral(_targetNames.at(i), backup.erc20s[j])) size++;
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
contracts/p1/mixins/RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
contracts/p1/mixins/RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
contracts/p1/mixins/RecollateralizationLib.sol::437 => for (uint256 i = 0; i < len; ++i) {
contracts/p1/mixins/RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
contracts/p1/mixins/RewardableLib.sol::67 => for (uint256 i = 0; i < erc20sLen; ++i) {
contracts/p1/mixins/RewardableLib.sol::73 => for (uint256 i = 0; i < erc20sLen; ++i) {
```

## [G-12] <x> += <y> costs more gas than <x> = <x> + <y> for state variables

use <x> = <x> + <y> or <x> = <x> - <y> instead to save gas

```
contracts/libraries/Fixed.sol::104 => shiftLeft += 18;
contracts/libraries/Fixed.sol::392 => decimals -= 18; // shift so that toUint happens at the same time.
contracts/libraries/Fixed.sol::504 => if (mm > lo) hi -= 1;
contracts/libraries/Fixed.sol::505 => lo -= mm;
contracts/libraries/Fixed.sol::509 => lo += hi * ((0 - pow2) / pow2 + 1);
contracts/libraries/Fixed.sol::537 => if (mm > 0) result += 1;
contracts/libraries/Fixed.sol::539 => if (mm > ((z - 1) / 2)) result += 1; // z should be z-1
contracts/libraries/Fixed.sol::555 => if (mm < lo) hi -= 1;
contracts/mixins/Auth.sol::136 => longFreezes[_msgSender()] -= 1; // reverts on underflow
contracts/p1/BasketHandler.sol::345 => low256 += quantityMulPrice(qty, lowP);
contracts/p1/BasketHandler.sol::346 => high256 += quantityMulPrice(qty, highP);
contracts/p1/BasketHandler.sol::636 => nonce += 1;
contracts/p1/Distributor.sol::145 => revTotals.rTokenTotal += share.rTokenDist;
contracts/p1/Distributor.sol::146 => revTotals.rsrTotal += share.rsrDist;
contracts/p1/Furnace.sol::81 => lastPayout += numPeriods * period;
contracts/p1/RToken.sol::330 => liabilities[IERC20(erc20s[i])] += deposits[i];
contracts/p1/RToken.sol::678 => liabilities[IERC20(queue.tokens[i])] -= amt[i];
contracts/p1/RToken.sol::687 => liabilities[IERC20(queue.tokens[i])] -= amt[i];
contracts/p1/RToken.sol::761 => liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];
contracts/p1/RToken.sol::771 => liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];
contracts/p1/StRSR.sol::229 => stakeRSR += rsrAmount;
contracts/p1/StRSR.sol::387 => stakeRSR -= stakeRSRToTake;
contracts/p1/StRSR.sol::396 => seizedRSR += stakeRSR;
contracts/p1/StRSR.sol::402 => draftRSR -= draftRSRToTake;
contracts/p1/StRSR.sol::403 => seizedRSR += draftRSRToTake;
contracts/p1/StRSR.sol::412 => seizedRSR += draftRSR;
contracts/p1/StRSR.sol::417 => seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;
contracts/p1/StRSR.sol::513 => stakeRSR += payout;
contracts/p1/StRSR.sol::516 => payoutLastPaid += numPeriods * rewardPeriod;
contracts/p1/StRSR.sol::549 => draftRSR += rsrAmount;
contracts/p1/StRSR.sol::669 => // effects: bal[from] -= amount; bal[to] += amount;
contracts/p1/StRSR.sol::684 => eraStakes[to] += amount;
contracts/p1/StRSR.sol::692 => // effects: bal[account] += amount; totalStakes += amount
contracts/p1/StRSR.sol::698 => stakes[era][account] += amount;
contracts/p1/StRSR.sol::699 => totalStakes += amount;
contracts/p1/StRSR.sol::706 => // effects: bal[account] -= amount; totalStakes -= amount;
contracts/p1/StRSR.sol::721 => totalStakes -= amount;
contracts/p1/mixins/RecollateralizationLib.sol::264 => assetsLow += low.mul(bal, FLOOR);
contracts/p1/mixins/RecollateralizationLib.sol::271 => else assetsHigh += val;
```

## [G-13] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
contracts/p1/StRSR.sol::778 => abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)
contracts/p1/StRSRVotes.sol::128 => _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),
contracts/plugins/aave/StaticATokenLM.sol::151 => abi.encode(PERMIT_TYPEHASH, owner, spender, value, currentValidNonce, deadline)
contracts/plugins/aave/StaticATokenLM.sol::179 => abi.encode(
contracts/plugins/aave/StaticATokenLM.sol::219 => abi.encode(
contracts/plugins/aave/StaticATokenLM.sol::269 => abi.encode(
```

## [G-14] Use custom errors rather than revert()/require() strings to save gas

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

```
contracts/libraries/RedemptionBattery.sol::44 => require(amount <= charge, "redemption battery insufficient");
contracts/mixins/Auth.sol::94 => require(account != address(0), "cannot grant role to address 0");
contracts/mixins/Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
contracts/mixins/Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
contracts/mixins/Auth.sol::197 => require(newUnfreezeAt > unfreezeAt, "frozen");
contracts/mixins/ComponentRegistry.sol::37 => require(address(val) != address(0), "invalid RToken address");
contracts/mixins/ComponentRegistry.sol::45 => require(address(val) != address(0), "invalid StRSR address");
contracts/mixins/ComponentRegistry.sol::53 => require(address(val) != address(0), "invalid AssetRegistry address");
contracts/mixins/ComponentRegistry.sol::61 => require(address(val) != address(0), "invalid BasketHandler address");
contracts/mixins/ComponentRegistry.sol::69 => require(address(val) != address(0), "invalid BackingManager address");
contracts/mixins/ComponentRegistry.sol::77 => require(address(val) != address(0), "invalid Distributor address");
contracts/mixins/ComponentRegistry.sol::85 => require(address(val) != address(0), "invalid RSRTrader address");
contracts/mixins/ComponentRegistry.sol::93 => require(address(val) != address(0), "invalid RTokenTrader address");
contracts/mixins/ComponentRegistry.sol::101 => require(address(val) != address(0), "invalid Furnace address");
contracts/mixins/ComponentRegistry.sol::109 => require(address(val) != address(0), "invalid Broker address");
contracts/p1/AssetRegistry.sol::74 => require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");
contracts/p1/AssetRegistry.sol::88 => require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");
contracts/p1/AssetRegistry.sol::89 => require(assets[asset.erc20()] == asset, "asset not found");
contracts/p1/AssetRegistry.sol::103 => require(_erc20s.contains(address(erc20)), "erc20 unregistered");
contracts/p1/AssetRegistry.sol::111 => require(_erc20s.contains(address(erc20)), "erc20 unregistered");
contracts/p1/AssetRegistry.sol::112 => require(assets[erc20].isCollateral(), "erc20 is not collateral");
contracts/p1/BackingManager.sol::73 => require(assetRegistry.isRegistered(erc20), "erc20 unregistered");
contracts/p1/BackingManager.sol::85 => require(ArrayLib.allUnique(erc20s), "duplicate tokens");
contracts/p1/BackingManager.sol::97 => require(ArrayLib.sortedAndAllUnique(erc20s), "duplicate/unsorted tokens");
contracts/p1/BackingManager.sol::111 => require(basketHandler.status() == CollateralStatus.SOUND, "basket not sound");
contracts/p1/BackingManager.sol::257 => require(val <= MAX_TRADING_DELAY, "invalid tradingDelay");
contracts/p1/BackingManager.sol::264 => require(val <= MAX_BACKING_BUFFER, "invalid backingBuffer");
contracts/p1/BasketHandler.sol::168 => require(_msgSender() == address(assetRegistry), "asset registry only");
contracts/p1/BasketHandler.sol::213 => require(erc20s.length > 0, "cannot empty basket");
contracts/p1/BasketHandler.sol::214 => require(erc20s.length == targetAmts.length, "must be same length");
contracts/p1/BasketHandler.sol::230 => require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
contracts/p1/BasketHandler.sol::231 => require(0 < targetAmts[i], "invalid target amount; must be nonzero");
contracts/p1/BasketHandler.sol::232 => require(targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount; too large");
contracts/p1/BasketHandler.sol::265 => require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
contracts/p1/BasketHandler.sol::654 => require(erc20s[i] != rsr, "RSR is not valid collateral");
contracts/p1/BasketHandler.sol::655 => require(erc20s[i] != IERC20(address(rToken)), "RToken is not valid collateral");
contracts/p1/BasketHandler.sol::656 => require(erc20s[i] != IERC20(address(stRSR)), "stRSR is not valid collateral");
contracts/p1/BasketHandler.sol::657 => require(erc20s[i] != zero, "address zero is not valid collateral");
contracts/p1/BasketHandler.sol::660 => require(ArrayLib.allUnique(erc20s), "contains duplicates");
contracts/p1/Broker.sol::57 => require(address(gnosis_) != address(0), "invalid Gnosis address");
contracts/p1/Broker.sol::86 => require(!disabled, "broker disabled");
contracts/p1/Broker.sol::125 => require(trades[_msgSender()], "unrecognized trade contract");
contracts/p1/Deployer.sol::109 => require(owner != address(0) && owner != address(this), "invalid owner");
contracts/p1/Distributor.sol::92 => require(erc20 == rsr || erc20 == rToken, "RSR or RToken");
contracts/p1/Distributor.sol::98 => require(totalShares > 0, "nothing to distribute");
contracts/p1/Distributor.sol::162 => require(dest != address(0), "dest cannot be zero");
contracts/p1/Distributor.sol::163 => if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");
contracts/p1/Distributor.sol::164 => if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");
contracts/p1/Distributor.sol::165 => require(share.rsrDist <= 10000, "RSR distribution too high");
contracts/p1/Distributor.sol::166 => require(share.rTokenDist <= 10000, "RToken distribution too high");
contracts/p1/Distributor.sol::172 => require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");
contracts/p1/Distributor.sol::182 => require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");
contracts/p1/Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
contracts/p1/Furnace.sol::97 => require(ratio_ <= MAX_RATIO, "invalid ratio");
contracts/p1/Main.sol::32 => require(address(rsr_) != address(0), "invalid RSR address");
contracts/p1/Main.sol::48 => require(!pausedOrFrozen(), "paused or frozen");
contracts/p1/RToken.sol::156 => require(bytes(name_).length > 0, "name empty");
contracts/p1/RToken.sol::157 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p1/RToken.sol::158 => require(bytes(mandate_).length > 0, "mandate empty");
contracts/p1/RToken.sol::191 => require(amtRToken > 0, "Cannot issue zero");
contracts/p1/RToken.sol::217 => require(status == CollateralStatus.SOUND, "basket unsound");
contracts/p1/RToken.sol::384 => require(status == CollateralStatus.SOUND, "basket unsound");
contracts/p1/RToken.sol::410 => require(queue.left <= endId && endId <= queue.right, "out of range");
contracts/p1/RToken.sol::440 => require(amount > 0, "Cannot redeem zero");
contracts/p1/RToken.sol::448 => require(basketHandler.status() != CollateralStatus.DISABLED, "collateral default");
contracts/p1/RToken.sol::558 => require(_msgSender() == address(backingManager), "not backing manager");
contracts/p1/RToken.sol::581 => require(_msgSender() == address(backingManager), "not backing manager");
contracts/p1/RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
contracts/p1/RToken.sol::603 => require(val <= FIX_ONE, "invalid fraction");
contracts/p1/RToken.sol::623 => require(index >= item.left && index < item.right, "out of range");
contracts/p1/RToken.sol::741 => require(queue.left <= endId && endId <= queue.right, "out of range");
contracts/p1/RToken.sol::747 => require(rightItem.when <= FIX_ONE_256 * block.number, "issuance not ready");
contracts/p1/RToken.sol::813 => require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
contracts/p1/RToken.sol::833 => require(to != address(this), "RToken transfer to self");
contracts/p1/RevenueTrader.sol::29 => require(address(tokenToBuy_) != address(0), "invalid token address");
contracts/p1/RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
contracts/p1/StRSR.sol::168 => require(bytes(name_).length > 0, "name empty");
contracts/p1/StRSR.sol::169 => require(bytes(symbol_).length > 0, "symbol empty");
contracts/p1/StRSR.sol::213 => require(rsrAmount > 0, "Cannot stake zero");
contracts/p1/StRSR.sol::259 => require(stakeAmount > 0, "Cannot withdraw zero");
contracts/p1/StRSR.sol::260 => require(stakes[era][account] >= stakeAmount, "Not enough balance");
contracts/p1/StRSR.sol::305 => require(basketHandler.fullyCollateralized(), "RToken uncollateralized");
contracts/p1/StRSR.sol::306 => require(basketHandler.status() == CollateralStatus.SOUND, "basket defaulted");
contracts/p1/StRSR.sol::312 => require(endId <= queue.length, "index out-of-bounds");
contracts/p1/StRSR.sol::313 => require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");
contracts/p1/StRSR.sol::375 => require(_msgSender() == address(backingManager), "not backing manager");
contracts/p1/StRSR.sol::376 => require(rsrAmount > 0, "Amount cannot be zero");
contracts/p1/StRSR.sol::380 => require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");
contracts/p1/StRSR.sol::660 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
contracts/p1/StRSR.sol::675 => require(from != address(0), "ERC20: transfer from the zero address");
contracts/p1/StRSR.sol::676 => require(to != address(0), "ERC20: transfer to the zero address");
contracts/p1/StRSR.sol::680 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
contracts/p1/StRSR.sol::695 => require(account != address(0), "ERC20: mint to the zero address");
contracts/p1/StRSR.sol::711 => require(account != address(0), "ERC20: burn from the zero address");
contracts/p1/StRSR.sol::717 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
contracts/p1/StRSR.sol::732 => require(owner != address(0), "ERC20: approve from the zero address");
contracts/p1/StRSR.sol::733 => require(spender != address(0), "ERC20: approve to the zero address");
contracts/p1/StRSR.sol::746 => require(currentAllowance >= amount, "ERC20: insufficient allowance");
contracts/p1/StRSR.sol::760 => require(to != address(this), "StRSR transfer to self");
contracts/p1/StRSR.sol::775 => require(block.timestamp <= deadline, "ERC20Permit: expired deadline");
contracts/p1/StRSR.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
contracts/p1/StRSR.sol::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p1/StRSR.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
contracts/p1/StRSR.sol::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
contracts/p1/StRSR.sol::829 => require(val <= MAX_REWARD_RATIO, "invalid rewardRatio");
contracts/p1/StRSRVotes.sol::77 => require(blockNumber < block.number, "ERC20Votes: block not yet mined");
contracts/p1/StRSRVotes.sol::83 => require(blockNumber < block.number, "ERC20Votes: block not yet mined");
contracts/p1/StRSRVotes.sol::89 => require(blockNumber < block.number, "ERC20Votes: block not yet mined");
contracts/p1/StRSRVotes.sol::126 => require(block.timestamp <= expiry, "ERC20Votes: signature expired");
contracts/p1/StRSRVotes.sol::133 => require(nonce == _useNonce(signer), "ERC20Votes: invalid nonce");
contracts/p1/mixins/Component.sol::34 => require(address(main_) != address(0), "main is zero address");
contracts/p1/mixins/Component.sol::42 => require(!main.pausedOrFrozen(), "paused or frozen");
contracts/p1/mixins/Component.sol::47 => require(!main.frozen(), "frozen");
contracts/p1/mixins/Component.sol::52 => require(main.hasRole(OWNER, _msgSender()), "governance only");
contracts/p1/mixins/RewardableLib.sol::96 => require(reg.isRegistered(erc20), "erc20 unregistered");
contracts/p1/mixins/Trading.sol::69 => require(trade.canSettle(), "cannot settle yet");
contracts/p1/mixins/Trading.sol::129 => require(val < MAX_TRADE_SLIPPAGE, "invalid maxTradeSlippage");
contracts/p1/mixins/Trading.sol::136 => require(val <= MIN_TRADE_VOLUME, "invalid minTradeVolume");
contracts/plugins/aave/ERC20.sol::242 => require(sender != address(0), "ERC20: transfer from the zero address");
contracts/plugins/aave/ERC20.sol::243 => require(recipient != address(0), "ERC20: transfer to the zero address");
contracts/plugins/aave/ERC20.sol::262 => require(account != address(0), "ERC20: mint to the zero address");
contracts/plugins/aave/ERC20.sol::283 => require(account != address(0), "ERC20: burn from the zero address");
contracts/plugins/aave/ERC20.sol::310 => require(owner != address(0), "ERC20: approve from the zero address");
contracts/plugins/aave/ERC20.sol::311 => require(spender != address(0), "ERC20: approve to the zero address");
contracts/plugins/aave/ReentrancyGuard.sol::51 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
contracts/plugins/assets/Asset.sol::48 => require(priceTimeout_ > 0, "price timeout zero");
contracts/plugins/assets/Asset.sol::49 => require(address(chainlinkFeed_) != address(0), "missing chainlink feed");
contracts/plugins/assets/Asset.sol::50 => require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
contracts/plugins/assets/Asset.sol::51 => require(address(erc20_) != address(0), "missing erc20");
contracts/plugins/assets/Asset.sol::52 => require(maxTradeVolume_ > 0, "invalid max trade volume");
contracts/plugins/assets/Asset.sol::53 => require(oracleTimeout_ > 0, "oracleTimeout zero");
contracts/plugins/assets/CTokenFiatCollateral.sol::26 => require(address(comptroller_) != address(0), "comptroller missing");
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::29 => require(referenceERC20Decimals_ > 0, "referenceERC20Decimals missing");
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::30 => require(address(comptroller_) != address(0), "comptroller missing");
contracts/plugins/assets/EURFiatCollateral.sol::24 => require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");
contracts/plugins/assets/FiatCollateral.sol::73 => require(config.targetName != bytes32(0), "targetName missing");
contracts/plugins/assets/FiatCollateral.sol::75 => require(config.delayUntilDefault > 0, "delayUntilDefault zero");
contracts/plugins/assets/NonFiatCollateral.sol::24 => require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");
contracts/plugins/assets/RTokenAsset.sol::28 => require(address(erc20_) != address(0), "missing erc20");
contracts/plugins/assets/RTokenAsset.sol::29 => require(maxTradeVolume_ > 0, "invalid max trade volume");
contracts/plugins/assets/SelfReferentialCollateral.sol::19 => require(config.defaultThreshold == 0, "default threshold not supported");
contracts/plugins/governance/Governance.sol::102 => require(startedInSameEra(proposalId), "new era");
contracts/plugins/governance/Governance.sol::112 => require(!startedInSameEra(proposalId), "same era");
contracts/plugins/governance/Governance.sol::123 => require(startedInSameEra(proposalId), "new era");
contracts/plugins/trading/GnosisTrade.sol::60 => require(status == begin, "Invalid trade state");
contracts/plugins/trading/GnosisTrade.sol::88 => require(req.sellAmount <= type(uint96).max, "sellAmount too large");
contracts/plugins/trading/GnosisTrade.sol::89 => require(req.minBuyAmount <= type(uint96).max, "minBuyAmount too large");
contracts/plugins/trading/GnosisTrade.sol::95 => require(initBal <= type(uint96).max, "initBal too large");
contracts/plugins/trading/GnosisTrade.sol::96 => require(initBal >= req.sellAmount, "unfunded trade");
contracts/plugins/trading/GnosisTrade.sol::174 => require(msg.sender == origin, "only origin can settle");
contracts/plugins/trading/GnosisTrade.sol::216 => require(status == TradeStatus.CLOSED, "only after trade is closed");
```

## [G-15] Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.
Consequences: each usage of a constant costs more gas on each access. Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library)

```
contracts/libraries/Fixed.sol::48 => uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;
contracts/libraries/Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
contracts/p1/BasketHandler.sol::117 => uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight
contracts/p1/RToken.sol::15 => uint192 constant MIN_BLOCK_ISSUANCE_LIMIT = 10_000 * FIX_ONE;
contracts/p1/StRSR.sol::65 => uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}
contracts/p1/StRSR.sol::87 => uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}
contracts/plugins/trading/GnosisTrade.sol::34 => uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}
```

## [G-16] Use a more recent version of solidity

Use a solidity version of at least 0.8.0 to get overflow protection without SafeMath
Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

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

## [G-17] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
contracts/p1/Distributor.sol::133 => for (uint256 i = 0; i < numTransfers; i++) {
```

## [G-19] Use bytes32 instead of string

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

```
contracts/p1/Deployer.sol::31 => string public constant ENS = "reserveprotocol.eth";
contracts/plugins/aave/StaticATokenErrors.sol::5 => string public constant INVALID_OWNER = "1";
contracts/plugins/aave/StaticATokenErrors.sol::6 => string public constant INVALID_EXPIRATION = "2";
contracts/plugins/aave/StaticATokenErrors.sol::7 => string public constant INVALID_SIGNATURE = "3";
contracts/plugins/aave/StaticATokenErrors.sol::8 => string public constant INVALID_DEPOSITOR = "4";
contracts/plugins/aave/StaticATokenErrors.sol::9 => string public constant INVALID_RECIPIENT = "5";
contracts/plugins/aave/StaticATokenErrors.sol::10 => string public constant INVALID_CLAIMER = "6";
contracts/plugins/aave/StaticATokenErrors.sol::11 => string public constant ONLY_ONE_AMOUNT_FORMAT_ALLOWED = "7";
```

## [G-20] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
contracts/mixins/Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
contracts/mixins/Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
contracts/p1/Deployer.sol::109 => require(owner != address(0) && owner != address(this), "invalid owner");
contracts/p1/Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
contracts/p1/RToken.sol::410 => require(queue.left <= endId && endId <= queue.right, "out of range");
contracts/p1/RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
contracts/p1/RToken.sol::623 => require(index >= item.left && index < item.right, "out of range");
contracts/p1/RToken.sol::741 => require(queue.left <= endId && endId <= queue.right, "out of range");
contracts/p1/RToken.sol::813 => require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
contracts/p1/RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
contracts/p1/StRSR.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
contracts/p1/StRSR.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
contracts/plugins/assets/Asset.sol::50 => require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
```

## [G-21] Usage of assert() instead of require()

Between solc 0.4.10 and 0.8.0, require() used REVERT (0xfd) opcode which refunded remaining gas on failure while assert() used INVALID (0xfe) opcode which consumed all the supplied gas.
require() should be used for checking error conditions on inputs and return values while assert() should be used for invariant checking (properly functioning code should never reach a failing assert statement, unless there is a bug in your contract you should fix).

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

## [G-22] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
contracts/mixins/Auth.sol::107 => function pausedOrFrozen() public view returns (bool) {
contracts/mixins/Versioned.sol::11 => function version() public pure virtual override returns (string memory) {
contracts/p1/StRSR.sol::604 => function totalSupply() public view returns (uint256) {
contracts/p1/StRSR.sol::651 => function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {
contracts/p1/StRSR.sol::657 => function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
contracts/p1/StRSR.sol::786 => function nonces(address owner) public view returns (uint256) {
contracts/p1/StRSRVotes.sol::59 => function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {
contracts/p1/StRSRVotes.sol::63 => function numCheckpoints(address account) public view returns (uint48) {
contracts/p1/StRSRVotes.sol::71 => function getVotes(address account) public view returns (uint256) {
contracts/p1/StRSRVotes.sol::76 => function getPastVotes(address account, uint256 blockNumber) public view returns (uint256) {
contracts/p1/StRSRVotes.sol::82 => function getPastTotalSupply(uint256 blockNumber) public view returns (uint256) {
contracts/p1/StRSRVotes.sol::88 => function getPastEra(uint256 blockNumber) public view returns (uint256) {
contracts/plugins/aave/ERC20.sol::66 => function name() public view returns (string memory) {
contracts/plugins/aave/ERC20.sol::74 => function symbol() public view returns (string memory) {
contracts/plugins/aave/ERC20.sol::91 => function decimals() public view returns (uint8) {
contracts/plugins/aave/ERC20.sol::98 => function totalSupply() public view override returns (uint256) {
contracts/plugins/aave/ERC20.sol::105 => function balanceOf(address account) public view override returns (uint256) {
contracts/plugins/aave/ERC20.sol::188 => function increaseAllowance(address spender, uint256 addedValue) public virtual returns (bool) {
contracts/plugins/assets/ATokenFiatCollateral.sol::45 => function refPerTok() public view override returns (uint192) {
contracts/plugins/assets/Asset.sol::86 => function refresh() public virtual override {
contracts/plugins/assets/CTokenFiatCollateral.sol::44 => function refPerTok() public view override returns (uint192) {
contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::47 => function refPerTok() public view override returns (uint192) {
contracts/plugins/assets/RTokenAsset.sol::63 => function refresh() public virtual override {
```

## [G-23] Not using the named return variables when a function returns, wastes deployment gas

It is not necessary to have both a named return and a return statement.

```
contracts/libraries/Fixed.sol::499 => ) pure returns (uint256 result) {
contracts/p1/AssetRegistry.sol::162 => function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
contracts/p1/BasketHandler.sol::279 => function status() public view returns (CollateralStatus status_) {
contracts/p1/BasketHandler.sol::297 => try assetRegistry.toColl(erc20) returns (ICollateral coll) {
contracts/p1/BasketHandler.sol::316 => function price() external view returns (uint192 low, uint192 high) {
contracts/p1/BasketHandler.sol::325 => function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {
contracts/p1/BasketHandler.sol::432 => function basketsHeldBy(address account) public view returns (uint192 baskets) {
contracts/p1/BasketHandler.sol::675 => try assetRegistry.toColl(erc20) returns (ICollateral coll) {
contracts/p1/RToken.sol::634 => function queueBounds(address account) external view returns (uint256 left, uint256 right) {
contracts/p1/mixins/RecollateralizationLib.sol::65 => returns (bool doTrade, TradeRequest memory req)
contracts/p1/mixins/TradeLib.sol::41 => returns (bool notDust, TradeRequest memory req)
contracts/p1/mixins/TradeLib.sol::106 => returns (bool notDust, TradeRequest memory req)
contracts/p1/mixins/TradeLib.sol::160 => try trader.mulDivCeil(x, y, z) returns (uint192 result) {
contracts/plugins/aave/StaticATokenLM.sol::119 => ) external override nonReentrant returns (uint256, uint256) {
contracts/plugins/aave/StaticATokenLM.sol::128 => ) external override nonReentrant returns (uint256, uint256) {
contracts/plugins/aave/StaticATokenLM.sol::209 => ) external override nonReentrant returns (uint256, uint256) {
contracts/plugins/aave/StaticATokenLM.sol::316 => ) internal returns (uint256, uint256) {
contracts/plugins/assets/Asset.sol::110 => function price() public view virtual returns (uint192, uint192) {
contracts/plugins/assets/Asset.sol::111 => try this.tryPrice() returns (uint192 low, uint192 high, uint192) {
contracts/plugins/assets/Asset.sol::126 => function lotPrice() external view virtual returns (uint192 lotLow, uint192 lotHigh) {
contracts/plugins/assets/Asset.sol::127 => try this.tryPrice() returns (uint192 low, uint192 high, uint192) {
contracts/plugins/assets/RTokenAsset.sol::44 => function tryPrice() external view virtual returns (uint192 low, uint192 high) {
contracts/plugins/assets/RTokenAsset.sol::72 => function price() public view virtual returns (uint192, uint192) {
contracts/plugins/assets/RTokenAsset.sol::73 => try this.tryPrice() returns (uint192 low, uint192 high) {
contracts/plugins/assets/RTokenAsset.sol::87 => function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {
contracts/plugins/governance/Governance.sol::90 => ) public override(Governor, IGovernor) returns (uint256 proposalId) {
```

## [G-24] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
contracts/p1/StRSR.sol::60 => mapping(uint256 => mapping(address => uint256)) private stakes; // Stakes per account {qStRSR}
contracts/p1/StRSR.sol::68 => mapping(uint256 => mapping(address => mapping(address => uint256))) private _allowances;
contracts/p1/StRSR.sol::81 => mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {drafts}
contracts/p1/StRSR.sol::82 => mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index
contracts/p1/StRSR.sol::123 => mapping(address => CountersUpgradeable.Counter) private _nonces;
contracts/p1/StRSR.sol::678 => mapping(address => uint256) storage eraStakes = stakes[era];
contracts/p1/StRSR.sol::713 => mapping(address => uint256) storage eraStakes = stakes[era];
contracts/p1/StRSRVotes.sol::31 => mapping(address => address) private _delegates;
contracts/p1/StRSRVotes.sol::38 => mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}
contracts/plugins/aave/ERC20.sol::38 => mapping(address => uint256) private _balances;
contracts/plugins/aave/ERC20.sol::40 => mapping(address => mapping(address => uint256)) private _allowances;
contracts/plugins/aave/StaticATokenLM.sol::66 => mapping(address => uint256) public _nonces;
contracts/plugins/aave/StaticATokenLM.sol::74 => mapping(address => uint256) private _userSnapshotRewardsPerToken;
contracts/plugins/aave/StaticATokenLM.sol::76 => mapping(address => uint256) private _unclaimedRewards;
```

## [G-25] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
contracts/interfaces/IAsset.sol::66 => function worseThan(CollateralStatus a, CollateralStatus b) internal pure returns (bool) {
contracts/libraries/Array.sol::9 => function allUnique(IERC20[] memory arr) internal pure returns (bool) {
contracts/libraries/Array.sol::21 => function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {
contracts/libraries/Fixed.sol::222 => function plus(uint192 x, uint192 y) internal pure returns (uint192) {
contracts/libraries/Fixed.sol::229 => function plusu(uint192 x, uint256 y) internal pure returns (uint192) {
contracts/libraries/Fixed.sol::236 => function minus(uint192 x, uint192 y) internal pure returns (uint192) {
contracts/libraries/Fixed.sol::243 => function minusu(uint192 x, uint256 y) internal pure returns (uint192) {
contracts/libraries/Fixed.sol::269 => function mulu(uint192 x, uint256 y) internal pure returns (uint192) {
contracts/libraries/Fixed.sol::316 => function powu(uint192 x_, uint48 y) internal pure returns (uint192) {
contracts/libraries/Fixed.sol::332 => function lt(uint192 x, uint192 y) internal pure returns (bool) {
contracts/libraries/Fixed.sol::336 => function lte(uint192 x, uint192 y) internal pure returns (bool) {
contracts/libraries/Fixed.sol::340 => function gt(uint192 x, uint192 y) internal pure returns (bool) {
contracts/libraries/Fixed.sol::344 => function gte(uint192 x, uint192 y) internal pure returns (bool) {
contracts/libraries/Fixed.sol::348 => function eq(uint192 x, uint192 y) internal pure returns (bool) {
contracts/libraries/Fixed.sol::352 => function neq(uint192 x, uint192 y) internal pure returns (bool) {
contracts/libraries/Fixed.sol::401 => function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {
contracts/libraries/Fixed.sol::419 => function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {
contracts/libraries/String.sol::11 => function toLower(string memory str) internal pure returns (string memory) {
contracts/mixins/Auth.sol::65 => function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {
contracts/mixins/ComponentRegistry.sol::19 => function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {
contracts/p1/BasketHandler.sol::75 => function setFrom(Basket storage self, Basket storage other) internal {
contracts/p1/StRSR.sol::694 => function _mint(address account, uint256 amount) internal virtual {
contracts/p1/StRSR.sol::708 => function _burn(address account, uint256 amount) internal virtual {
contracts/p1/StRSR.sol::795 => function _useNonce(address owner) internal returns (uint256 current) {
contracts/p1/mixins/Component.sol::33 => function __Component_init(IMain main_) internal onlyInitializing {
contracts/p1/mixins/Trading.sol::111 => function tryTrade(TradeRequest memory req) internal nonReentrant {
contracts/plugins/aave/ERC20.sol::261 => function _mint(address account, uint256 amount) internal virtual {
contracts/plugins/aave/ERC20.sol::282 => function _burn(address account, uint256 amount) internal virtual {
contracts/plugins/aave/ERC20.sol::324 => function _setupDecimals(uint8 decimals_) internal {
contracts/plugins/aave/RayMathNoRounding.sol::11 => function rayMulNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {
contracts/plugins/aave/RayMathNoRounding.sol::19 => function rayDivNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {
contracts/plugins/aave/RayMathNoRounding.sol::25 => function rayToWadNoRounding(uint256 a) internal pure returns (uint256) {
contracts/plugins/assets/FiatCollateral.sol::199 => function alreadyDefaulted() internal view returns (bool) {
```

## [G-26] internal functions not called by the contract should be removed to save deployment gas

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword

```
contracts/mixins/Auth.sol::65 => function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {
contracts/mixins/ComponentRegistry.sol::19 => function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {
contracts/p1/mixins/Component.sol::33 => function __Component_init(IMain main_) internal onlyInitializing {
contracts/p1/mixins/Trading.sol::111 => function tryTrade(TradeRequest memory req) internal nonReentrant {
contracts/plugins/aave/ERC20.sol::261 => function _mint(address account, uint256 amount) internal virtual {
contracts/plugins/aave/ERC20.sol::282 => function _burn(address account, uint256 amount) internal virtual {
contracts/plugins/aave/ERC20.sol::324 => function _setupDecimals(uint8 decimals_) internal {
contracts/plugins/aave/RayMathNoRounding.sol::11 => function rayMulNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {
contracts/plugins/aave/RayMathNoRounding.sol::19 => function rayDivNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {
contracts/plugins/aave/RayMathNoRounding.sol::25 => function rayToWadNoRounding(uint256 a) internal pure returns (uint256) {
```