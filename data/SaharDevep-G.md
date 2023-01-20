# c4udit Report

## Summery
[G001] Don't Initialize Variables with Default Value
[G002] Cache Array Length Outside of Loop
[G003] Use != 0 instead of > 0 for Unsigned Integer Comparison
[G004] Use immutable for OpenZeppelin AccessControl's Roles Declarations
[G005] Long Revert Strings
[G006] Use Shift Right/Left instead of Division/Multiplication if possible
[G007] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
[G008] USE A RETURN STATEMENT INSTEAD OF BREAK
[G009] USE A MORE RECENT VERSION OF SOLIDITY
[G010] DON’T USE _MSGSENDER() IF NOT SUPPORTING EIP-2771
[G011] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
[G012] EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING
[G013] USING BOOLS FOR STORAGE INCURS OVERHEAD
[G014] USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS
[G015] STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE
[G016] MULTIPLE ACCESSES OF A MAPPING/ARRAY SHOULD USE A LOCAL VARIABLE CACHE
[G017] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
[G018] FUNCTIONS WITH ACCESS CONTROL CHEAPER IF PAYABLE
[G019] USE ABI.ENCODEWITHSELECTOR INSTEAD OF ABI.ENCODEWITHSIGNATURE

## Issues found

### [G001] Don't Initialize Variables with Default Value

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g001---dont-initialize-variables-with-default-value)

#### Findings:
```
\libraries\Array.sol::12 => for (uint256 j = 0; j < i; ++j) {
\libraries\String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
\p1\AssetRegistry.sol::38 => for (uint256 i = 0; i < length; ++i) {
\p1\AssetRegistry.sol::49 => for (uint256 i = 0; i < length; ++i) {
\p1\AssetRegistry.sol::127 => for (uint256 i = 0; i < length; ++i) {
\p1\AssetRegistry.sol::138 => for (uint256 i = 0; i < length; ++i) {
\p1\BackingManager.sol::221 => for (uint256 i = 0; i < length; ++i) {
\p1\BackingManager.sol::238 => for (uint256 i = 0; i < length; ++i) {
\p1\BasketHandler.sol::70 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
\p1\BasketHandler.sol::78 => for (uint256 i = 0; i < length; ++i) {
\p1\BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
\p1\BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\BasketHandler.sol::286 => for (uint256 i = 0; i < size; ++i) {
\p1\BasketHandler.sol::337 => for (uint256 i = 0; i < len; ++i) {
\p1\BasketHandler.sol::397 => for (uint256 i = 0; i < len; ++i) {
\p1\BasketHandler.sol::416 => for (uint256 i = 0; i < length; ++i) {
\p1\BasketHandler.sol::437 => for (uint256 i = 0; i < length; ++i) {
\p1\BasketHandler.sol::530 => for (uint256 i = 0; i < basketLength; ++i) {
\p1\BasketHandler.sol::548 => for (uint256 i = 0; i < basketLength; ++i) {
\p1\BasketHandler.sol::586 => for (uint256 i = 0; i < targetsLength; ++i) {
\p1\BasketHandler.sol::592 => uint256 size = 0; // backup basket size
\p1\BasketHandler.sol::597 => for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
\p1\BasketHandler.sol::606 => uint256 assigned = 0;
\p1\BasketHandler.sol::611 => for (uint256 j = 0; j < backupLength && assigned < size; ++j) {
\p1\BasketHandler.sol::643 => for (uint256 i = 0; i < basketLength; ++i) {
\p1\BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
\p1\BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\Distributor.sol::108 => for (uint256 i = 0; i < destinations.length(); ++i) {
\p1\Distributor.sol::133 => for (uint256 i = 0; i < numTransfers; i++) {
\p1\Distributor.sol::143 => for (uint256 i = 0; i < length; ++i) {
\p1\RToken.sol::270 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\RToken.sol::303 => for (uint256 i = 0; i < basketSize; ++i) {
\p1\RToken.sol::329 => for (uint256 i = 0; i < basketSize; ++i) {
\p1\RToken.sol::334 => for (uint256 i = 0; i < basketSize; ++i) {
\p1\RToken.sol::478 => for (uint256 i = 0; i < erc20length; ++i) {
\p1\RToken.sol::501 => for (uint256 i = 0; i < erc20length; ++i) {
\p1\RToken.sol::674 => for (uint256 i = 0; i < tokensLen; ++i) {
\p1\RToken.sol::683 => for (uint256 i = 0; i < tokensLen; ++i) {
\p1\RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
\p1\RToken.sol::757 => for (uint256 i = 0; i < tokensLen; ++i) {
\p1\RToken.sol::767 => for (uint256 i = 0; i < tokensLen; ++i) {
\p1\RToken.sol::793 => for (uint256 i = 0; i < tokensLen; ++i) {
\p1\StRSRVotes.sol::102 => uint256 low = 0;
\p1\mixins\RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
\p1\mixins\RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
\p1\mixins\RecollateralizationLib.sol::437 => for (uint256 i = 0; i < len; ++i) {
\p1\mixins\RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
\p1\mixins\RewardableLib.sol::67 => for (uint256 i = 0; i < erc20sLen; ++i) {
\p1\mixins\RewardableLib.sol::73 => for (uint256 i = 0; i < erc20sLen; ++i) {
\plugins\mocks\EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
\plugins\mocks\EasyAuction.sol::285 => uint256 sumOfSellAmounts = 0;
\plugins\mocks\EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
\plugins\mocks\EasyAuction.sol::315 => uint256 claimableAmount = 0;
\plugins\mocks\EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
\plugins\mocks\EasyAuction.sol::344 => for (uint256 i = 0; i < iterationSteps; i++) {
\plugins\mocks\EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) {
\plugins\mocks\EasyAuction.sol::524 => for (uint256 i = 0; i < orders.length; i++) {
\plugins\mocks\InvalidChainlinkMock.sol::51 => uint256 i = 0;
\plugins\mocks\InvalidFiatCollateral.sol::32 => uint256 i = 0;
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G002] Cache Array Length Outside of Loop

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)

#### Findings:
```
\libraries\String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
\p1\BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
\p1\BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
\p1\BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\Distributor.sol::108 => for (uint256 i = 0; i < destinations.length(); ++i) {
\p1\Distributor.sol::143 => for (uint256 i = 0; i < length; ++i) {
\p1\RToken.sol::270 => for (uint256 i = 0; i < erc20s.length; ++i) {
\p1\RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
\p1\mixins\RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
\p1\mixins\RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
\p1\mixins\RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
\plugins\mocks\EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
\plugins\mocks\EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
\plugins\mocks\EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
\plugins\mocks\EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) 
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G003] Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g003---use--0-instead-of--0-for-unsigned-integer-comparison)

#### Findings:
```
\libraries\Fixed.sol::168 => if (numerator % divisor > 0) {
\libraries\Fixed.sol::537 => if (mm > 0) result += 1;
\mixins\Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
\mixins\Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
\p1\AssetRegistry.sol::78 => if (quantity > 0) basketHandler.disableBasket();
\p1\AssetRegistry.sol::96 => if (quantity > 0) basketHandler.disableBasket();
\p1\BackingManager.sol::109 => if (tradesOpen > 0) return;
\p1\BackingManager.sol::173 => if (rsr.balanceOf(address(this)) > 0) {
\p1\BackingManager.sol::200 => uint192 rTok = (needed > 0) ? extraBUs.mulDiv(totalSupply, needed) : extraBUs;
\p1\BackingManager.sol::230 => // no div-by-0: Distributor guarantees (totals.rTokenTotal + totals.rsrTotal) > 0
\p1\BackingManager.sol::240 => if (toRToken[i] > 0) erc20.safeTransfer(address(rTokenTrader), toRToken[i]);
\p1\BackingManager.sol::241 => if (toRSR[i] > 0) erc20.safeTransfer(address(rsrTrader), toRSR[i]);
\p1\BasketHandler.sol::213 => require(erc20s.length > 0, "cannot empty basket");
\p1\BasketHandler.sol::301 => if (refPerTok > 0) {
\p1\BasketHandler.sol::486 => If unsoundPrimeWt(tgt) > 0 and len(backups(tgt)) == 0 for some tgt, then disabled' == true.
\p1\BasketHandler.sol::523 => while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
\p1\BasketHandler.sol::679 => coll.refPerTok() > 0 &&
\p1\BasketHandler.sol::680 => coll.targetPerRef() > 0;
\p1\Broker.sol::135 => newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
\p1\Distributor.sol::98 => require(totalShares > 0, "nothing to distribute");
\p1\Distributor.sol::182 => require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");
\p1\Furnace.sol::83 => if (amount > 0) rToken.melt(amount);
\p1\Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
\p1\RToken.sol::156 => require(bytes(name_).length > 0, "name empty");
\p1\RToken.sol::157 => require(bytes(symbol_).length > 0, "symbol empty");
\p1\RToken.sol::158 => require(bytes(mandate_).length > 0, "mandate empty");
\p1\RToken.sol::191 => require(amtRToken > 0, "Cannot issue zero");
\p1\RToken.sol::202 => if (queue.basketNonce > 0 && queue.basketNonce != basketNonce) {
\p1\RToken.sol::234 => totalSupply() > 0 ? mulDiv256(basketsNeeded, amtRToken, totalSupply()) : amtRToken
\p1\RToken.sol::295 => if (queue.right > 0) {
\p1\RToken.sol::440 => require(amount > 0, "Cannot redeem zero");
\p1\RToken.sol::483 => uint256 prorata = (prorate > 0)
\p1\RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
\p1\RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
\p1\StRSR.sol::168 => require(bytes(name_).length > 0, "name empty");
\p1\StRSR.sol::169 => require(bytes(symbol_).length > 0, "symbol empty");
\p1\StRSR.sol::213 => require(rsrAmount > 0, "Cannot stake zero");
\p1\StRSR.sol::259 => require(stakeAmount > 0, "Cannot withdraw zero");
\p1\StRSR.sol::315 => uint192 oldDrafts = firstId > 0 ? queue[firstId - 1].drafts : 0;
\p1\StRSR.sol::376 => require(rsrAmount > 0, "Amount cannot be zero");
\p1\StRSR.sol::391 => if (stakeRSR > 0) {
\p1\StRSR.sol::406 => if (draftRSR > 0) {
\p1\StRSR.sol::504 => if (totalStakes > 0) {
\p1\StRSR.sol::559 => uint192 oldDrafts = index > 0 ? queue[index - 1].drafts : 0;
\p1\StRSR.sol::560 => uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
\p1\StRSR.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
\p1\StRSR.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
\p1\StRSRVotes.sol::171 => if (src != dst && amount > 0) {
\p1\StRSRVotes.sol::202 => if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {
\p1\mixins\RecollateralizationLib.sol::411 => high > 0 &&
\p1\mixins\RewardableLib.sol::74 => if (deltas[i] > 0) {
\p1\mixins\RewardableLib.sol::98 => if (amt > 0) {
\p1\mixins\TradeLib.sol::44 => assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
\p1\mixins\TradeLib.sol::109 => trade.sellPrice > 0 &&
\p1\mixins\TradeLib.sol::111 => trade.buyPrice > 0 &&
\p1\mixins\TradeLib.sol::183 => return size > 0 ? size : 1;
\p1\mixins\TradeLib.sol::192 => return size > 0 ? size : 1;
\plugins\aave\StaticATokenLM.sol::330 => if (staticAmount > 0) {
\plugins\aave\StaticATokenLM.sol::422 => if (supply > 0 && rewardsAccrued > 0) {
\plugins\aave\StaticATokenLM.sol::428 => if (rewardsAccrued > 0) {
\plugins\aave\StaticATokenLM.sol::461 => if (reward > 0) {
\plugins\aave\StaticATokenLM.sol::512 => if (balance > 0) {
\plugins\assets\Asset.sol::48 => require(priceTimeout_ > 0, "price timeout zero");
\plugins\assets\Asset.sol::50 => require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
\plugins\assets\Asset.sol::52 => require(maxTradeVolume_ > 0, "invalid max trade volume");
\plugins\assets\Asset.sol::53 => require(oracleTimeout_ > 0, "oracleTimeout zero");
\plugins\assets\CTokenSelfReferentialCollateral.sol::29 => require(referenceERC20Decimals_ > 0, "referenceERC20Decimals missing");
\plugins\assets\FiatCollateral.sol::74 => if (config.defaultThreshold > 0) {
\plugins\assets\FiatCollateral.sol::75 => require(config.delayUntilDefault > 0, "delayUntilDefault zero");
\plugins\assets\RTokenAsset.sol::29 => require(maxTradeVolume_ > 0, "invalid max trade volume");
\plugins\mocks\ATokenMock.sol::81 => if (address(aaveToken) != address(0) && aaveBalances[msg.sender] > 0) {
\plugins\mocks\AaveLendingPoolMock.sol::36 => return _normalizedIncome[asset] > 0 ? _normalizedIncome[asset] : 1e27;
\plugins\mocks\EasyAuction.sol::164 => require(_auctionedSellAmount > 0, "cannot auction zero tokens");
\plugins\mocks\EasyAuction.sol::165 => require(_minBuyAmount > 0, "tokens cannot be auctioned for free");
\plugins\mocks\EasyAuction.sol::167 => minimumBiddingAmountPerOrder > 0,
\plugins\mocks\EasyAuction.sol::289 => require(_minBuyAmounts[i] > 0, "_minBuyAmounts must be greater than 0");
\plugins\mocks\EasyAuction.sol::427 => currentBidSum > 0 &&
\plugins\mocks\EasyAuction.sol::597 => if (auctioningTokenAmount > 0) {
\plugins\mocks\EasyAuction.sol::600 => if (biddingTokenAmount > 0) {
\plugins\mocks\GnosisMock.sol::69 => require(auctionedSellAmount > 0, "sell amount is zero");
\plugins\mocks\GnosisMock.sol::92 => require(bid.buyAmount > 0, "zero volume bid");
\plugins\mocks\GnosisMockReentrant.sol::33 => require(auctionedSellAmount > 0, "sell amount is zero");
\plugins\mocks\vendor\EasyAuction.sol::202 => if (returndata.length > 0) {
\plugins\mocks\vendor\EasyAuction.sol::557 => require(b > 0, errorMessage);
\plugins\mocks\vendor\EasyAuction.sol::698 => return size > 0;
\plugins\mocks\vendor\EasyAuction.sol::842 => if (returndata.length > 0) {
\plugins\trading\GnosisTrade.sol::191 => if (sellBal > 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);
\plugins\trading\GnosisTrade.sol::192 => if (boughtAmt > 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G004] Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information:(https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g006---use-immutable-for-openzeppelin-accesscontrols-roles-declarations)

#### Findings:
```
\libraries\Fixed.sol::33 => bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
\p1\StRSR.sol::127 => keccak256(
\p1\StRSR.sol::777 => bytes32 structHash = keccak256(
\p1\StRSRVotes.sol::28 => keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
\p1\StRSRVotes.sol::128 => _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),
\p1\mixins\TradeLib.sol::170 => assert(keccak256(reason) == UIntOutofBoundsHash);
\plugins\aave\StaticATokenLM.sol::42 => keccak256(
\plugins\aave\StaticATokenLM.sol::46 => keccak256(
\plugins\aave\StaticATokenLM.sol::50 => keccak256(
\plugins\aave\StaticATokenLM.sol::54 => keccak256(
\plugins\aave\StaticATokenLM.sol::146 => bytes32 digest = keccak256(
\plugins\aave\StaticATokenLM.sol::150 => keccak256(
\plugins\aave\StaticATokenLM.sol::174 => bytes32 digest = keccak256(
\plugins\aave\StaticATokenLM.sol::178 => keccak256(
\plugins\aave\StaticATokenLM.sol::214 => bytes32 digest = keccak256(
\plugins\aave\StaticATokenLM.sol::218 => keccak256(
\plugins\aave\StaticATokenLM.sol::268 => keccak256(
\plugins\aave\StaticATokenLM.sol::271 => keccak256(bytes(name())),
\plugins\aave\StaticATokenLM.sol::272 => keccak256(EIP712_REVISION),
\plugins\mocks\BadERC20.sol::32 => bytes memory data = abi.encodePacked((bytes4(keccak256("absentDecimalsFn()"))));
\vendor\ERC20PermitUpgradeable.sol::40 => keccak256(
\vendor\ERC20PermitUpgradeable.sol::83 => bytes32 structHash = keccak256(
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G005] Long Revert Strings

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g007---long-revert-strings)

#### Findings:
```
\p1\BasketHandler.sol::231 => require(0 < targetAmts[i], "invalid target amount; must be nonzero");
\p1\BasketHandler.sol::657 => require(erc20s[i] != zero, "address zero is not valid collateral");
\p1\Broker.sol::60 => "invalid Trade Implementation address"
\p1\StRSR.sol::380 => require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");
\p1\StRSR.sol::660 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
\p1\StRSR.sol::675 => require(from != address(0), "ERC20: transfer from the zero address");
\p1\StRSR.sol::676 => require(to != address(0), "ERC20: transfer to the zero address");
\p1\StRSR.sol::680 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
\p1\StRSR.sol::711 => require(account != address(0), "ERC20: burn from the zero address");
\p1\StRSR.sol::717 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
\p1\StRSR.sol::732 => require(owner != address(0), "ERC20: approve from the zero address");
\p1\StRSR.sol::733 => require(spender != address(0), "ERC20: approve to the zero address");
\p1\StRSR.sol::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
\p1\StRSR.sol::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
\plugins\aave\ERC20.sol::170 => "ERC20: transfer amount exceeds allowance"
\plugins\aave\ERC20.sol::217 => "ERC20: decreased allowance below zero"
\plugins\aave\ERC20.sol::242 => require(sender != address(0), "ERC20: transfer from the zero address");
\plugins\aave\ERC20.sol::243 => require(recipient != address(0), "ERC20: transfer to the zero address");
\plugins\aave\ERC20.sol::247 => _balances[sender] = _balances[sender].sub(amount, "ERC20: transfer amount exceeds balance");
\plugins\aave\ERC20.sol::283 => require(account != address(0), "ERC20: burn from the zero address");
\plugins\aave\ERC20.sol::287 => _balances[account] = _balances[account].sub(amount, "ERC20: burn amount exceeds balance");
\plugins\aave\ERC20.sol::310 => require(owner != address(0), "ERC20: approve from the zero address");
\plugins\aave\ERC20.sol::311 => require(spender != address(0), "ERC20: approve to the zero address");
\plugins\aave\StaticATokenLM.sol::32 => ERC20("STATIC_ATOKEN_IMPL", "STATIC_ATOKEN_IMPL"),
\plugins\assets\CTokenNonFiatCollateral.sol::30 => "missing target unit chainlink feed"
\plugins\mocks\EasyAuction.sol::21 => "no longer in order placement phase"
\plugins\mocks\EasyAuction.sol::29 => "no longer in order placement and cancelation phase"
\plugins\mocks\EasyAuction.sol::41 => "Auction not in solution submission phase"
\plugins\mocks\EasyAuction.sol::131 => require(newFeeNumerator <= 15, "Fee is not allowed to be set higher than 1.5%");
\plugins\mocks\EasyAuction.sol::165 => require(_minBuyAmount > 0, "tokens cannot be auctioned for free");
\plugins\mocks\EasyAuction.sol::168 => "minimumBiddingAmountPerOrder is not allowed to be zero"
\plugins\mocks\EasyAuction.sol::172 => "time periods are not configured correctly"
\plugins\mocks\EasyAuction.sol::174 => require(auctionEndDate > block.timestamp, "auction end date must be in the future");
\plugins\mocks\EasyAuction.sol::281 => "limit price not better than mimimal offer"
\plugins\mocks\EasyAuction.sol::289 => require(_minBuyAmounts[i] > 0, "_minBuyAmounts must be greater than 0");
\plugins\mocks\EasyAuction.sol::326 => require(userIdOfIter == userId, "Only the user can cancel his orders");
\plugins\mocks\EasyAuction.sol::374 => "not allowed to settle auction atomically"
\plugins\mocks\EasyAuction.sol::378 => "Only one order can be placed atomically"
\plugins\mocks\EasyAuction.sol::385 => "precalculateSellAmountSum is already too advanced"
\plugins\mocks\EasyAuction.sol::526 => require(userIdOrder == userId, "only allowed to claim for same user");
\plugins\mocks\vendor\EasyAuction.sol::155 => "SafeERC20: approve from non-zero to non-zero allowance"
\plugins\mocks\vendor\EasyAuction.sol::179 => "SafeERC20: decreased allowance below zero"
\plugins\mocks\vendor\EasyAuction.sol::205 => require(abi.decode(returndata, (bool)), "SafeERC20: ERC20 operation did not succeed");
\plugins\mocks\vendor\EasyAuction.sol::362 => require(userIdLeft != userIdRight, "user is not allowed to place same order twice");
\plugins\mocks\vendor\EasyAuction.sol::370 => require(!isEmpty(self), "Trying to get first from empty set");
\plugins\mocks\vendor\EasyAuction.sol::375 => require(value != QUEUE_END, "Trying to get next of last element");
\plugins\mocks\vendor\EasyAuction.sol::377 => require(nextElement != bytes32(0), "Trying to get next of non-existent element");
\plugins\mocks\vendor\EasyAuction.sol::519 => require(c / a == b, "SafeMath: multiplication overflow");
\plugins\mocks\vendor\EasyAuction.sol::657 => require(value < 2**96, "SafeCast: value doesn't fit in 96 bits");
\plugins\mocks\vendor\EasyAuction.sol::662 => require(value < 2**64, "SafeCast: value doesn't fit in 64 bits");
\plugins\mocks\vendor\EasyAuction.sol::722 => require(success, "Address: unable to send value, recipient may have reverted");
\plugins\mocks\vendor\EasyAuction.sol::778 => functionCallWithValue(target, data, value, "Address: low-level call with value failed");
\plugins\mocks\vendor\EasyAuction.sol::793 => require(address(this).balance >= value, "Address: insufficient balance for call");
\plugins\mocks\vendor\EasyAuction.sol::812 => return functionStaticCall(target, data, "Address: low-level static call failed");
\plugins\mocks\vendor\EasyAuction.sol::826 => require(isContract(target), "Address: static call to non-contract");
\plugins\mocks\vendor\EasyAuction.sol::936 => require(newOwner != address(0), "Ownable: new owner is the zero address");
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G006] Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: (https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

#### Findings:
```
\libraries\Fixed.sol::164 => if (numerator % divisor > (divisor - 1) / 2) {
\libraries\Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
\libraries\Fixed.sol::323 => if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
\libraries\Fixed.sol::326 => x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
\libraries\Fixed.sol::539 => if (mm > ((z - 1) / 2)) result += 1; // z should be z-1
\p1\BasketHandler.sol::372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
\p1\StRSR.sol::427 => return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
\p1\StRSR.sol::453 => test = (left + right) / 2; // left < test < right because left < right - 1
\p1\StRSR.sol::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
\p1\StRSR.sol::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
```
#### Tools used
[c4udit](https://github.com/byterocket/c4udit)

### [G007] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

#### Findings:
```
\mixins\Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
\mixins\Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
\p1\Broker.sol::135 => newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
\p1\Deployer.sol:: 48 => require(
\p1\Deployer.sol:: 109 => require(owner != address(0) && owner != address(this), "invalid owner");
\p1\Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
\p1\RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
\p1\RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
\p1\RToken.sol::741 => require(queue.left <= endId && endId <= queue.right, "out of range");
\p1\RToken.sol::813 => require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
\p1\StRSRVotes.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
\p1\StRSRVotes.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
\plugins\mocks\EasyAuction.sol::37 => require(
\plugins\mocks\EasyAuction.sol::376 => require(
```
#### Tools used
Manual

### [G008] USE A RETURN STATEMENT INSTEAD OF BREAK

#### Findings:
```
\libraries\Fixed.sol::324 => if (y <= 1) break;
\p1\BasketHandler.sol::554 => if (_targetNames.at(targetIndex) == config.targetNames[erc20]) break;
\plugins\mocks\EasyAuction.sol::419 => break;
```

#### Tools used
Manual

### [G009] USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

#### Tools used
Manual

### [G010] DON’T USE _MSGSENDER() IF NOT SUPPORTING EIP-2771
Use msg.sender if the code does not implement EIP-2771 trusted forwarder support

### Findings:
```
\mixins\Auth.sol::74 => address msgSender = _msgSender();
\mixins\Auth.sol::122 => _revokeRole(SHORT_FREEZER, _msgSender());
\mixins\Auth.sol::122 => if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());
\p1\BasketHandler.sol::168 => require(_msgSender() == address(assetRegistry), "asset registry only");
\p1\BasketHandler.sol::187 => main.hasRole(OWNER, _msgSender()) ||
\p1\Broker.sol::88 => address caller = _msgSender();
\p1\Broker.sol::125 => require(trades[_msgSender()], "unrecognized trade contract");
\p1\RToken.sol::178 => issue(_msgSender(), amtRToken);
\p1\RToken.sol::196 => address issuer = _msgSender(); // OK to save: it can't be changed in reentrant runs
\p1\RToken.sol::407 => address account = _msgSender();
\p1\RToken.sol::446 => address redeemer = _msgSender();
\p1\RToken.sol::558 => require(_msgSender() == address(backingManager), "not backing manager");
\p1\RToken.sol::570 => _burn(_msgSender(), amtRToken);
\p1\RToken.sol::581 => require(_msgSender() == address(backingManager), "not backing manager");
\p1\StRSR.sol::228 => address account = _msgSender();
\p1\StRSR.sol::258 => address account = _msgSender();
\p1\StRSR.sol::375 => require(_msgSender() == address(backingManager), "not backing manager");
\p1\StRSR.sol::421 => IERC20Upgradeable(address(rsr)).safeTransfer(_msgSender(), seizedRSR);
\p1\StRSR.sol::623 => address owner = _msgSender();
\p1\StRSR.sol::633 => _approve(_msgSender(), spender, amount);
\p1\StRSR.sol::646 => _spendAllowance(from, _msgSender(), amount);
\p1\StRSR.sol::652 => address owner = _msgSender();
\p1\StRSR.sol::658 => address owner = _msgSender();
\p1\StRSRVotes.sol::115 => _delegate(_msgSender(), delegatee);
\p1\mixins\Component.sol::52 => require(main.hasRole(OWNER, _msgSender()), "governance only");
\plugins\aave\ERC20.sol::118 => _transfer(_msgSender(), recipient, amount);
\plugins\aave\ERC20.sol::143 => _approve(_msgSender(), spender, amount);
\plugins\aave\ERC20.sol::167 => _msgSender(),
\plugins\aave\ERC20.sol::168 => _allowances[sender][_msgSender()].sub(
\plugins\aave\ERC20.sol::89 => _approve(_msgSender(), spender, _allowances[_msgSender()][spender].add(addedValue));
\plugins\aave\ERC20.sol::213 => _msgSender(),
\plugins\aave\ERC20.sol::215 => _allowances[_msgSender()][spender].sub(
```

#### Tools used
Manual

### [G011] ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()

#### Findings:
```
\p1\StRSR.sol::778 => abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)
\p1\StRSRVotes.sol::128 => _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),
\plugins\aave\StaticATokenLM.sol::151 => abi.encode(PERMIT_TYPEHASH, owner, spender, value, currentValidNonce, deadline)
\plugins\aave\StaticATokenLM.sol::179 => abi.encode(
\plugins\aave\StaticATokenLM.sol::219 => abi.encode(
\plugins\aave\StaticATokenLM.sol::269 => abi.encode(
```

#### Tools used
Manual

### [G012] EMPTY BLOCKS SHOULD BE REMOVED OR EMIT SOMETHING

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

### [G013] USING BOOLS FOR STORAGE INCURS OVERHEAD
Booleans are more expensive compared to uint256 or any other type that takes up a full word. This is because the write operation emits an extra SLOAD that first reads the slot contents and then replaces the bits the boolean has taken up, it then writes this back. This is how the compiler defends against contract upgrades and pointer aliasing.
Use uint256(1) and uint256(2) for true/false

#### Findings:
```
\mixins\Auth.sol::42 => bool public paused;
\p1\BasketHandler.sol::139 => bool private disabled;
\p1\Broker.sol::41 => bool public disabled;
```
#### Tools used
Manual

### [G014] USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

#### Findings:
```
\p1\BasketHandler.sol::231 => require(0 < targetAmts[i], "invalid target amount; must be nonzero");
\p1\BasketHandler.sol::657 => require(erc20s[i] != zero, "address zero is not valid collateral");
\p1\Broker.sol::60 => "invalid Trade Implementation address"
\p1\StRSR.sol::380 => require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");
\p1\StRSR.sol::660 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
\p1\StRSR.sol::675 => require(from != address(0), "ERC20: transfer from the zero address");
\p1\StRSR.sol::676 => require(to != address(0), "ERC20: transfer to the zero address");
\p1\StRSR.sol::680 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
\p1\StRSR.sol::711 => require(account != address(0), "ERC20: burn from the zero address");
\p1\StRSR.sol::717 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
\p1\StRSR.sol::732 => require(owner != address(0), "ERC20: approve from the zero address");
\p1\StRSR.sol::733 => require(spender != address(0), "ERC20: approve to the zero address");
\p1\StRSR.sol::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
\p1\StRSR.sol::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
\plugins\aave\ERC20.sol::170 => "ERC20: transfer amount exceeds allowance"
\plugins\aave\ERC20.sol::217 => "ERC20: decreased allowance below zero"
\plugins\aave\ERC20.sol::242 => require(sender != address(0), "ERC20: transfer from the zero address");
\plugins\aave\ERC20.sol::243 => require(recipient != address(0), "ERC20: transfer to the zero address");
\plugins\aave\ERC20.sol::247 => _balances[sender] = _balances[sender].sub(amount, "ERC20: transfer amount exceeds balance");
\plugins\aave\ERC20.sol::283 => require(account != address(0), "ERC20: burn from the zero address");
\plugins\aave\ERC20.sol::287 => _balances[account] = _balances[account].sub(amount, "ERC20: burn amount exceeds balance");
\plugins\aave\ERC20.sol::310 => require(owner != address(0), "ERC20: approve from the zero address");
\plugins\aave\ERC20.sol::311 => require(spender != address(0), "ERC20: approve to the zero address");
\plugins\aave\StaticATokenLM.sol::32 => ERC20("STATIC_ATOKEN_IMPL", "STATIC_ATOKEN_IMPL"),
\plugins\assets\CTokenNonFiatCollateral.sol::30 => "missing target unit chainlink feed"
\plugins\mocks\EasyAuction.sol::21 => "no longer in order placement phase"
\plugins\mocks\EasyAuction.sol::29 => "no longer in order placement and cancelation phase"
\plugins\mocks\EasyAuction.sol::41 => "Auction not in solution submission phase"
\plugins\mocks\EasyAuction.sol::131 => require(newFeeNumerator <= 15, "Fee is not allowed to be set higher than 1.5%");
\plugins\mocks\EasyAuction.sol::165 => require(_minBuyAmount > 0, "tokens cannot be auctioned for free");
\plugins\mocks\EasyAuction.sol::168 => "minimumBiddingAmountPerOrder is not allowed to be zero"
\plugins\mocks\EasyAuction.sol::172 => "time periods are not configured correctly"
\plugins\mocks\EasyAuction.sol::174 => require(auctionEndDate > block.timestamp, "auction end date must be in the future");
\plugins\mocks\EasyAuction.sol::281 => "limit price not better than mimimal offer"
\plugins\mocks\EasyAuction.sol::289 => require(_minBuyAmounts[i] > 0, "_minBuyAmounts must be greater than 0");
\plugins\mocks\EasyAuction.sol::326 => require(userIdOfIter == userId, "Only the user can cancel his orders");
\plugins\mocks\EasyAuction.sol::374 => "not allowed to settle auction atomically"
\plugins\mocks\EasyAuction.sol::378 => "Only one order can be placed atomically"
\plugins\mocks\EasyAuction.sol::385 => "precalculateSellAmountSum is already too advanced"
\plugins\mocks\EasyAuction.sol::526 => require(userIdOrder == userId, "only allowed to claim for same user");
\plugins\mocks\vendor\EasyAuction.sol::155 => "SafeERC20: approve from non-zero to non-zero allowance"
\plugins\mocks\vendor\EasyAuction.sol::179 => "SafeERC20: decreased allowance below zero"
\plugins\mocks\vendor\EasyAuction.sol::205 => require(abi.decode(returndata, (bool)), "SafeERC20: ERC20 operation did not succeed");
\plugins\mocks\vendor\EasyAuction.sol::362 => require(userIdLeft != userIdRight, "user is not allowed to place same order twice");
\plugins\mocks\vendor\EasyAuction.sol::370 => require(!isEmpty(self), "Trying to get first from empty set");
\plugins\mocks\vendor\EasyAuction.sol::375 => require(value != QUEUE_END, "Trying to get next of last element");
\plugins\mocks\vendor\EasyAuction.sol::377 => require(nextElement != bytes32(0), "Trying to get next of non-existent element");
\plugins\mocks\vendor\EasyAuction.sol::519 => require(c / a == b, "SafeMath: multiplication overflow");
\plugins\mocks\vendor\EasyAuction.sol::657 => require(value < 2**96, "SafeCast: value doesn't fit in 96 bits");
\plugins\mocks\vendor\EasyAuction.sol::662 => require(value < 2**64, "SafeCast: value doesn't fit in 64 bits");
\plugins\mocks\vendor\EasyAuction.sol::722 => require(success, "Address: unable to send value, recipient may have reverted");
\plugins\mocks\vendor\EasyAuction.sol::778 => functionCallWithValue(target, data, value, "Address: low-level call with value failed");
\plugins\mocks\vendor\EasyAuction.sol::793 => require(address(this).balance >= value, "Address: insufficient balance for call");
\plugins\mocks\vendor\EasyAuction.sol::812 => return functionStaticCall(target, data, "Address: low-level static call failed");
\plugins\mocks\vendor\EasyAuction.sol::826 => require(isContract(target), "Address: static call to non-contract");
\plugins\mocks\vendor\EasyAuction.sol::936 => require(newOwner != address(0), "Ownable: new owner is the zero address");
etc...
```

#### Tools used
Manual

### [G015] STATE VARIABLES ONLY SET IN THE CONSTRUCTOR SHOULD BE DECLARED IMMUTABLE

#### Findings:
```
\p1\Deployer.sol:: 38 => Implementations public implementations;
\plugins\aave\ERC20.sol::44 => string internal _name;
\plugins\aave\ERC20.sol::45 => string internal _symbol;
\plugins\aave\ERC20.sol::46 => uint8 private _decimals;
\plugins\aave\ReentrancyGuard.sol::36 => uint256 private _status;
\plugins\aave\StaticATokenLM.sol::60-64 => ILendingPool public override LENDING_POOL;

```
#### Tools used
Manual

### [G016] MULTIPLE ACCESSES OF A MAPPING/ARRAY SHOULD USE A LOCAL VARIABLE CACHE

#### Findings: 
```
\interfaces\IDeployerRegistry.sol::27 => function register(
\interfaces\IDeployerRegistry.sol::46 =>  function unregister(string calldata version) external onlyOwner {
\mixins\Auth.sol::135 => function freezeLong() external onlyRole(LONG_FREEZER) {
\p1\AssetRegistry.sol::87 => function unregister(IAsset asset) external governance {
\p1\AssetRegistry.sol::110 => function toColl(IERC20 erc20) external view returns (ICollateral) {
\p1\AssetRegistry.sol::134 => function getRegistry() external view returns (Registry memory reg) {
\p1\AssetRegistry.sol::164 => function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
```

#### Tools used
Manual

### [G017] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

#### Findings: 
```
\mixins\Auth.sol::36-38 => uint48 public unfreezeAt;
\p1\mixins\RecollateralizationLib.sol::236 => uint192 potentialDustLoss; // {UoA}
\p1\mixins\Trading.sol::20 => uint192 public constant MIN_TRADE_VOLUME = 1e29; // {UoA}
\p1\mixins\Trading.sol::21 => uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}
\p1\mixins\Trading.sol::28 => uint48 public tradesOpen;
\p1\mixins\Trading.sol::31 => uint192 public maxTradeSlippage; // {%}
\p1\mixins\Trading.sol::33 => uint192 public minTradeVolume; // {UoA}
\p1\BackingManager.sol::33 => uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
\p1\BackingManager.sol::34 => uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%
\p1\BackingManager.sol::36 => uint48 public tradingDelay; // {s} how long to wait until resuming trading after switching
\p1\BackingManager.sol::37 => uint192 public backingBuffer; // {%} how much extra backing collateral to keep
\p1\BasketHandler.sol::117 => uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight
\p1\BasketHandler.sol::134 => uint48 public override nonce; // A unique identifier for this basket instance
\p1\BasketHandler.sol::135 => uint48 public override timestamp; // The timestamp when this basket was last set
\p1\Broker.sol::24 => uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
\p1\Broker.sol::37 => uint48 public auctionLength;
\p1\Distributor.sol::34 => uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;
\p1\Furnace.sol::15 => uint192 public constant MAX_RATIO = FIX_ONE; // {1} 100%
\p1\Furnace.sol::16 => uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
\p1\Furnace.sol::18 => uint192 public ratio; // {1} What fraction of balance to melt each period
\p1\Furnace.sol::19 => uint48 public period; // {seconds} How often to melt
\p1\Furnace.sol::21 => uint48 public lastPayout; // {seconds} The last time we did a payout
\p1\RToken.sol::50 => uint192 public issuanceRate;
\p1\RToken.sol::67 => uint192 public basketsNeeded; // D18{BU}
\p1\RToken.sol::72 => uint192 private allVestAt; // D18{fractional block number}
\p1\RToken.sol::76 => uint192 private lastIssRate; // D18{rTok/block}
\p1\RToken.sol::77 => uint256 private lastIssRateBlock; // {block number}
\p1\StRSR.sol::37-39 => uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
\p1\StRSR.sol::45 => uint8 public constant decimals = 18;
\p1\StRSR.sol::63 => uint192 public stakeRate; // The exchange rate between stakes and RSR. D18{qStRSR/qRSR}
\p1\StRSR.sol::65 => uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}
\p1\StRSR.sol::85 => uint192 public draftRate; // The exchange rate between drafts and RSR. D18{qDrafts/qRSR}
\p1\StRSR.sol::87 => uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}
\p1\StRSR.sol::134-136 => uint48 public unstakingDelay; // {s} The minimum length of time spent in the draft queue
\p1\StRSR.sol::147 => uint48 public payoutLastPaid;
```

#### Tools used
Manual

### [G018] FUNCTIONS WITH ACCESS CONTROL CHEAPER IF PAYABLE

#### Findings: 
```
\mixins\Auth.sol::89 => function grantRole(bytes32 role, address account)
\mixins\Auth.sol::120 => function freezeShort() external onlyRole(SHORT_FREEZER) {
\mixins\Auth.sol::135 => function freezeLong() external onlyRole(LONG_FREEZER) {
\mixins\Auth.sol::148 => function freezeForever() external onlyRole(OWNER) {
\mixins\Auth.sol::157 => function unfreeze() external onlyRole(OWNER) {
\mixins\Auth.sol::165 => function pause() external onlyRole(PAUSER) {
\mixins\Auth.sol::172 => function unpause() external onlyRole(PAUSER) {
\mixins\Auth.sol::180 => function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
\mixins\Auth.sol::187 => function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
```
#### Tools used
Manual

### [G019] USE ABI.ENCODEWITHSELECTOR INSTEAD OF ABI.ENCODEWITHSIGNATURE

#### Findings: 
```
\libraries\Fixed.sol::33 => bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
\p1\mixins\RewardableLib.sol::30 => abi.encodeWithSignature("claimRewards()"),
\p1\mixins\RewardableLib.sol::43 => abi.encodeWithSignature("claimRewards()"),
```
#### Tools used
Manual