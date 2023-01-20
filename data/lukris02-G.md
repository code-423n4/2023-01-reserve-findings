# Gas Optimizations Report for Reserve contest
## Overview
During the audit, 4 gas issues were found.  
Total savings >3000.

№ | Title | Instance Count | Saved
--- | --- | --- | ---
G-1 | Use unchecked blocks for incrementing i | 60 | 2100
G-2 | Use unchecked blocks for subtractions where underflow is impossible | 30 | 1050
G-3 | Elements that are smaller than 32 bytes (256 bits) may increase gas usage |  | 
G-4 | Using ```storage``` pointer to ```struct``` is cheaper than using ```memory``` | 1 | 

## Gas Optimizations Findings(4)
### G-1. Use unchecked blocks for incrementing i
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. In the loops, "i" will not overflow because the loop will run out of gas before that.
##### Instances
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L38) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L49) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L127) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L138) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L221) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L238) 
- [```for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L70) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L78) 
- [```for (uint256 i = 0; i < config.erc20s.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L218) 
- [```for (uint256 i = 0; i < erc20s.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L227) 
- [```for (uint256 i = 0; i < erc20s.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L262) 
- [```for (uint256 i = 0; i < size; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L286) 
- [```for (uint256 i = 0; i < len; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L337) 
- [```for (uint256 i = 0; i < len; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L397) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L416) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L437) 
- [```for (uint256 i = 0; i < basketLength; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L530) 
- [```for (uint256 i = 0; i < basketLength; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L548) 
- [```for (uint256 i = 0; i < targetsLength; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L586) 
- [```for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L597) 
- [```if (goodCollateral(_targetNames.at(i), backup.erc20s[j])) size++;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L598) 
- [```for (uint256 j = 0; j < backupLength && assigned < size; ++j) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L611) 
- [```assigned++;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L621) 
- [```nonce += 1;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L636) 
- [```for (uint256 i = 0; i < basketLength; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L643) 
- [```for (uint256 i = 0; i < erc20s.length; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L653) 
- [```for (uint256 i = 0; i < erc20s.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L707) 
- [```for (uint256 i = 0; i < erc20s.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L725) 
- [```for (uint256 i = 0; i < destinations.length(); ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L108) 
- [```for (uint256 i = 0; i < numTransfers; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L133) 
- [```for (uint256 i = 0; i < length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L143) 
- [```for (uint256 i = 0; i < erc20s.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270) 
- [```for (uint256 i = 0; i < basketSize; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L303) 
- [```for (uint256 i = 0; i < basketSize; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L329) 
- [```for (uint256 i = 0; i < basketSize; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L334) 
- [```for (uint256 i = 0; i < erc20length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L478) 
- [```for (uint256 i = 0; i < erc20length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L501) 
- [```for (uint256 i = 0; i < tokensLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L674) 
- [```for (uint256 i = 0; i < tokensLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L683) 
- [```for (uint256 i = 0; i < queue.tokens.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L711) 
- [```for (uint256 i = 0; i < tokensLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L757) 
- [```for (uint256 i = 0; i < tokensLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L767) 
- [```for (uint256 i = 0; i < tokensLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L793) 
- [```for (uint256 i = 1; i < arrLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11) 
- [```for (uint256 j = 0; j < i; ++j) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L12) 
- [```for (uint256 i = 1; i < arrLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23) 
- [```for (uint256 i = 0; i < bStr.length; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14) 
- [```for (uint256 i = 0; i < reg.erc20s.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L242) 
- [```for (uint256 i = 0; i < reg.erc20s.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L329) 
- [```for (uint256 i = 0; i < len; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437) 
- [```for (uint256 i = 0; i < registry.assets.length; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27) 
- [```for (uint256 i = 0; i < erc20sLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L67) 
- [```for (uint256 i = 0; i < erc20sLen; ++i) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L73) 
- [```for (uint256 i = 0; i < _minBuyAmounts.length; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L277) 
- [```for (uint256 i = 0; i < _minBuyAmounts.length; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L288) 
- [```for (uint256 i = 0; i < _sellOrders.length; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L316) 
- [```for (uint256 i = 0; i < iterationSteps; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L344) 
- [```for (uint256 i = 0; i < orders.length; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L513) 
- [```for (uint256 i = 0; i < orders.length; i++) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L524) 

##### Recommendation
Change:
```
for (uint256 i; i < n; ++i) {
 // ...
}
```
to:
```
for (uint256 i; i < n;) { 
 // ...
 unchecked { ++i; }
}
```

##### Saved
This saves ~30-40 gas per iteration.  
So, ~35*60 = 2100
#
### G-2. Use unchecked blocks for subtractions where underflow is impossible
##### Description
In Solidity 0.8+, there’s a default overflow and underflow check on unsigned integers. When an overflow or underflow isn’t possible (after require or if-statement), some gas can be saved by using [unchecked blocks](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#checked-or-unchecked-arithmetic).
##### Instances
- [```if (req.sellAmount > bal) stRSR.seizeRSR(req.sellAmount - bal);```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L141) 
- [```IssueItem storage prev = queue.items[queue.right - 1];```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L296) 
- [```queue.right - 1,```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L320) 
- [```IssueItem storage leftItem = queue.items[left - 1];```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L681) 
- [```IssueItem storage leftItem = queue.items[queue.left - 1];```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L764) 
- [```uint256 high = (FIX_ONE_256 * basketsNeeded + (supply - 1)) / supply; // D18{BU/rTok}```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L810) (supply - 1)
- [```uint256 newStakeRSR = (FIX_ONE_256 * totalStakes + (stakeRate - 1)) / stakeRate;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L270) (stakeRate - 1)
- [```require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L313) 
- [```uint192 oldDrafts = firstId > 0 ? queue[firstId - 1].drafts : 0;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L315) 
- [```uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L316) (endId - 1)
- [```stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L393) (stakeRSR - 1)
- [```draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L408) (draftRSR - 1)
- [```: uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L526) (stakeRSR - 1)
- [```uint192 oldDrafts = index > 0 ? queue[index - 1].drafts : 0;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L559) 
- [```uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L560) 
- [```return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L73) 
- [```return high == 0 ? 0 : ckpts[high - 1].val;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L111) 
- [```oldWeight = pos == 0 ? 0 : ckpts[pos - 1].val;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L199) 
- [```if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L202) 
- [```ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L203) 
- [```if (uint160(address(arr[i])) <= uint160(address(arr[i - 1]))) return false;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L24) 
- [```uint192 diff = x <= y ? y - x : x - y;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L364) 
- [```battery.lastCharge = charge - amount;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L48) 
- [```uint192 lotMultiplier = divuu(priceTimeout - delta, priceTimeout);```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L141) 
- [```auction.sell.safeTransfer(auction.origin, auction.sellAmount - bid.sellAmount);```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L118) 
- [```auction.sell.safeTransfer(auction.origin, auction.sellAmount - bid.sellAmount);```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol#L84) 
- [```balanceOf[msg.sender] -= wad;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L44) 
- [```allowance[src][msg.sender] -= wad;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L72) 
- [```balanceOf[src] -= wad;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L75) 
- [```soldAmt = initBal - sellBal;```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L196) 

##### Saved
This saves ~35.  
So, ~35*30 = 1050
#
### G-3. Elements that are smaller than 32 bytes (256 bits) may increase gas usage
##### Description
According to [docs](https://docs.soliditylang.org/en/v0.8.16/internals/layout_in_storage.html#layout-of-state-variables-in-storage), when using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

##### Recommendation
Consider using a larger size where needed.

#
### G-4. Using ```storage``` pointer to ```struct``` is cheaper than using ```memory```
##### Description
When you copy a storage ```struct```/```array```/```mapping``` to a memory variable, you are copying each member by reading it from the storage, which is expensive, but when you use the ```storage``` keyword, you are just storing a pointer to the storage, which is much cheaper.
##### Instances
- [```AuctionData memory auction = auctionData[auctionId];```](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L518)

##### Recommendation
For example, change: 
```
AuctionData memory auction = auctionData[auctionId];
```  
to:  
```
AuctionData storage auction = auctionData[auctionId];
```