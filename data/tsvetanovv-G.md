### GAS Optimisation List
| Number |Issues Details
|:--:|:-------|:--:|
|[GAS-01]| Unchecking arithmetics operations that can’t `underflow/overflow`
|[GAS-02]| Use `require` instead of `assert`
|[GAS-03]| `x = x - y` costs less gas than `x -= y`, same for addition
|[GAS-04]|Shift right or left instead of dividing or multiply by 2
|[GAS-05]|Splitting `require()` statements that use `&&` saves gas
***

## [GAS-01] UNCHECKING ARITHMETICS OPERATIONS THAT CAN’T UNDERFLOW/OVERFLOW
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.
[Reference](https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic)
```
AssetRegistry.sol:
38:  for (uint256 i = 0; i < length; ++i) {
49:  for (uint256 i = 0; i < length; ++i) {
127: for (uint256 i = 0; i < length; ++i) {
138: for (uint256 i = 0; i < length; ++i) {

BackingManager.sol:
221: for (uint256 i = 0; i < length; ++i) {
238: for (uint256 i = 0; i < length; ++i) {

BasketHandler.sol:
70:  for (uint256 i = 0; i < length; ++i)
78:  for (uint256 i = 0; i < length; ++i) {
218: for (uint256 i = 0; i < config.erc20s.length; ++i) {
227: for (uint256 i = 0; i < erc20s.length; ++i) {
262: for (uint256 i = 0; i < erc20s.length; ++i) {
286: for (uint256 i = 0; i < size; ++i) {
337: for (uint256 i = 0; i < len; ++i) {
397: for (uint256 i = 0; i < len; ++i) {
416: for (uint256 i = 0; i < length; ++i) {
530: for (uint256 i = 0; i < basketLength; ++i) {
548: for (uint256 i = 0; i < basketLength; ++i) {
586: for (uint256 i = 0; i < targetsLength; ++i) {
597: for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
611: for (uint256 j = 0; j < backupLength && assigned < size; ++j) {
643: for (uint256 i = 0; i < basketLength; ++i) {
653: for (uint256 i = 0; i < erc20s.length; i++) {
707: for (uint256 i = 0; i < erc20s.length; ++i) {
725: for (uint256 i = 0; i < erc20s.length; ++i) {

Distributor.sol:
108: for (uint256 i = 0; i < destinations.length(); ++i) {
133: for (uint256 i = 0; i < numTransfers; i++) {
143: for (uint256 i = 0; i < length; ++i) {

RToken.sol:
270: for (uint256 i = 0; i < erc20s.length; ++i) {
303: for (uint256 i = 0; i < basketSize; ++i) {
329: for (uint256 i = 0; i < basketSize; ++i) {
334: for (uint256 i = 0; i < basketSize; ++i) {
478: for (uint256 i = 0; i < erc20length; ++i) {
501: for (uint256 i = 0; i < erc20length; ++i) {
674: for (uint256 i = 0; i < tokensLen; ++i) {
683: for (uint256 i = 0; i < tokensLen; ++i) {
711: for (uint256 i = 0; i < queue.tokens.length; ++i) {
757: for (uint256 i = 0; i < tokensLen; ++i) {
767: for (uint256 i = 0; i < tokensLen; ++i) {
793: for (uint256 i = 0; i < tokensLen; ++i) {

RecollateralizationLib.sol:
242: for (uint256 i = 0; i < reg.erc20s.length; ++i) {
329: for (uint256 i = 0; i < reg.erc20s.length; ++i) {
437: for (uint256 i = 0; i < len; ++i) {

RewardableLib.sol:
27: for (uint256 i = 0; i < registry.assets.length; ++i) {
67: for (uint256 i = 0; i < erc20sLen; ++i) {
73: for (uint256 i = 0; i < erc20sLen; ++i) {
```
***

## [GAS-02] USE REQUIRE INSTEAD OF ASSERT
The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.
```
BackingManager.sol:
249: assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());

TradeLib.sol:
44: assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
108: assert(
            trade.sellPrice > 0 &&
                trade.sellPrice < FIX_MAX &&
                trade.buyPrice > 0 &&
                trade.buyPrice < FIX_MAX
        );
168: assert(errorCode == 0x11 || errorCode == 0x12);
170: assert(keccak256(reason) == UIntOutofBoundsHash);
```
***

## [GAS-03] `x = x - y` costs less gas than `x -= y`, same for addition
```
BasketHandler.sol
345: low256 += quantityMulPrice(qty, lowP);
346: high256 += quantityMulPrice(qty, highP);
636: nonce += 1;

Distributor.sol:
145: revTotals.rTokenTotal += share.rTokenDist;
146: revTotals.rsrTotal += share.rsrDist;

Furnace.sol:
81: lastPayout += numPeriods * period;

RToken.sol:
330: liabilities[IERC20(erc20s[i])] += deposits[i];
678: liabilities[IERC20(queue.tokens[i])] -= amt[i];
687: liabilities[IERC20(queue.tokens[i])] -= amt[i];
771: liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];

StRSR.sol:
229: stakeRSR += rsrAmount;
387: stakeRSR -= stakeRSRToTake;
402: draftRSR -= draftRSRToTake;
403: seizedRSR += draftRSRToTake;
412: seizedRSR += draftRSR;
417: seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;
549: draftRSR += rsrAmount;
684: eraStakes[to] += amount;
698: stakes[era][account] += amount;
699: totalStakes += amount;
721: totalStakes -= amount;

RecollateralizationLib.sol:
264: assetsLow += low.mul(bal, FLOOR);
271: else assetsHigh += val;
```
You can replace all `-=` and `+=` occurrences to save gas
***
##  [GAS-04] SHIFT RIGHT OR LEFT INSTEAD OF DIVIDING OR MULTIPLY BY 2

Shifting one to the right will calculate a division by two.

`SHR` opcode only requires 3 gas, compared to the `DIV` opcode’s consumption of 5. Additionally, shifting is used to get around Solidity’s division operation’s division-by-0 prohibition.

```
StRSR.sol
427: return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
453: test = (left + right) / 2;
```
***
## [GAS-05] SPLITTING REQUIRE() STATEMENTS THAT USE `&&` SAVES GAS
```
Broker.sol:
134: require(
            newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
            "invalid auctionLength"
        );

Deployer.sol:
48: require(
            address(rsr_) != address(0) &&
                address(gnosis_) != address(0) &&
                address(rsrAsset_) != address(0) &&
                address(implementations_.main) != address(0) &&
                address(implementations_.trade) != address(0) &&
                address(implementations_.components.assetRegistry) != address(0) &&
                address(implementations_.components.backingManager) != address(0) &&
                address(implementations_.components.basketHandler) != address(0) &&
                address(implementations_.components.broker) != address(0) &&
                address(implementations_.components.distributor) != address(0) &&
                address(implementations_.components.furnace) != address(0) &&
                address(implementations_.components.rsrTrader) != address(0) &&
                address(implementations_.components.rTokenTrader) != address(0) &&
                address(implementations_.components.rToken) != address(0) &&
                address(implementations_.components.stRSR) != address(0),
            "invalid address"
        );
109: require(owner != address(0) && owner != address(this), "invalid owner");

Furnace.sol:
89: require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");

RevenueTrader.sol:
72: require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");

RToken.sol:
410: require(queue.left <= endId && endId <= queue.right, "out of range");
590: require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
623: require(index >= item.left && index < item.right, "out of range");
741: require(queue.left <= endId && endId <= queue.right, "out of range");
813: require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");

StRSR.sol:
821: require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
```