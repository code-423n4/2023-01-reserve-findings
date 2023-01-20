# Report
## Gas Optimizations ##
### [G-1]: The increment in for loop post condition can be made unchecked
**Context:**

1. ```for (uint256 i = 0; i < length; ++i) {``` [L38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L38) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L49](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L49) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L127](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L127) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L138](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L138) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L221](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L221) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L238](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L238) 
1. ```for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;``` [L70](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L70) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L78) 
1. ```for (uint256 i = 0; i < config.erc20s.length; ++i) {``` [L218](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L218) 
1. ```for (uint256 i = 0; i < erc20s.length; ++i) {``` [L227](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L227) 
1. ```for (uint256 i = 0; i < erc20s.length; ++i) {``` [L262](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L262) 
1. ```for (uint256 i = 0; i < size; ++i) {``` [L286](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L286) 
1. ```for (uint256 i = 0; i < len; ++i) {``` [L337](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L337) 
1. ```for (uint256 i = 0; i < len; ++i) {``` [L397](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L397) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L416](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L416) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L437](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L437) 
1. ```for (uint256 i = 0; i < basketLength; ++i) {``` [L530](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L530) 
1. ```for (uint256 i = 0; i < basketLength; ++i) {``` [L548](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L548) 
1. ```for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {``` [L553](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L553) 
1. ```for (uint256 i = 0; i < targetsLength; ++i) {``` [L586](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L586) 
1. ```for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {``` [L597](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L597) 
1. ```for (uint256 j = 0; j < backupLength && assigned < size; ++j) {``` [L611](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L611) 
1. ```for (uint256 i = 0; i < basketLength; ++i) {``` [L643](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L643) 
1. ```for (uint256 i = 0; i < erc20s.length; i++) {``` [L653](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L653) 
1. ```for (uint256 i = 0; i < erc20s.length; ++i) {``` [L707](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L707) 
1. ```for (uint256 i = 0; i < erc20s.length; ++i) {``` [L725](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L725) 
1. ```for (uint256 i = 0; i < destinations.length(); ++i) {``` [L108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L108) 
1. ```for (uint256 i = 0; i < numTransfers; i++) {``` [L133](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L133) 
1. ```for (uint256 i = 0; i < length; ++i) {``` [L143](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L143) 
1. ```for (uint256 i = 0; i < basketSize; ++i) {``` [L303](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L303) 
1. ```for (uint256 i = 0; i < basketSize; ++i) {``` [L329](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L329) 
1. ```for (uint256 i = 0; i < basketSize; ++i) {``` [L334](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L334) 
1. ```for (uint256 i = 0; i < erc20length; ++i) {``` [L478](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L478) 
1. ```for (uint256 i = 0; i < erc20length; ++i) {``` [L501](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L501) 
1. ```for (uint256 i = 0; i < tokensLen; ++i) {``` [L674](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L674) 
1. ```for (uint256 i = 0; i < tokensLen; ++i) {``` [L683](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L683) 
1. ```for (uint256 i = 0; i < queue.tokens.length; ++i) {``` [L711](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L711) 
1. ```for (uint256 i = 0; i < tokensLen; ++i) {``` [L757](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L757) 
1. ```for (uint256 i = 0; i < tokensLen; ++i) {``` [L767](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L767) 
1. ```for (uint256 i = 0; i < tokensLen; ++i) {``` [L793](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L793) 
1. ```for (uint256 i = 1; i < arrLen; ++i) {``` [L11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11) 
1. ```for (uint256 j = 0; j < i; ++j) {``` [L12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L12) 
1. ```for (uint256 i = 1; i < arrLen; ++i) {``` [L23](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23) 
1. ```for (uint256 i = 0; i < bStr.length; i++) {``` [L14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14) 
1. ```for (uint256 i = 0; i < reg.erc20s.length; ++i) {``` [L242](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L242) 
1. ```for (uint256 i = 0; i < reg.erc20s.length; ++i) {``` [L329](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L329) 
1. ```for (uint256 i = 0; i < len; ++i) {``` [L437](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437) 
1. ```for (uint256 i = 0; i < registry.assets.length; ++i) {``` [L27](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27) 
1. ```for (uint256 i = 0; i < erc20sLen; ++i) {``` [L67](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L67) 
1. ```for (uint256 i = 0; i < erc20sLen; ++i) {``` [L73](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L73) 
1. ```for (uint256 i = 0; i < _minBuyAmounts.length; i++) {``` [L277](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L277) 
1. ```for (uint256 i = 0; i < _minBuyAmounts.length; i++) {``` [L288](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L288) 
1. ```for (uint256 i = 0; i < _sellOrders.length; i++) {``` [L316](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L316) 
1. ```for (uint256 i = 0; i < iterationSteps; i++) {``` [L344](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L344) 
1. ```for (uint256 i = 0; i < orders.length; i++) {``` [L513](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L513) 
1. ```for (uint256 i = 0; i < orders.length; i++) {``` [L524](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L524) 

**Description:**

[This](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked) can save 30-40 gas per loop iteration.

**Recommendation:**

Example how to fix. Change:
```
for (uint256 i = 0; i < orders.length; ++i) {
   // Do the thing
}
```

To:
```
for (uint256 i = 0; i < orders.length;) {
   // Do the thing
   unchecked { ++i; }
}
```

### [G-2]: Place subtractions where the operands can't underflow in unchecked {} block
**Context:**

1. ```IssueItem storage prev = queue.items[queue.right - 1];``` [L296](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L296) (queue.right - 1)
1. ```IssueItem storage leftItem = queue.items[left - 1];``` [L681](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L681) (left-1)
1. ```IssueItem storage leftItem = queue.items[queue.left - 1];``` [L764](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L764) (queue.right - 1)
1. ```uint256 high = (FIX_ONE_256 * basketsNeeded + (supply - 1)) / supply; // D18{BU/rTok}``` [L810](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L810) (supply-1)
1. ```draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);``` [L408](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L408) (draftRSR - 1)
1. ```if (uint160(address(arr[i])) <= uint160(address(arr[i - 1]))) return false;``` [L24](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L24) (i-1)
1. ```battery.lastCharge = charge - amount;``` [L48](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L48) 
1. ```uint256 c = a - b;``` [L495](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L495) 
1. ```soldAmt = initBal - sellBal;``` [L196](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L196) 
1. ```uint256 adjustedSoldAmt = Math.max(soldAmt - 1, 1);``` [L199](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L199) (soldAmt - 1)

**Description:**

Some gas can be saved by using an unchecked {} block if an underflow isn't possible because of a previous require() or if-statement.
