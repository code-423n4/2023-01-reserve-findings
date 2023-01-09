# Reserve

## Gas

### 1. `++i`  costs less gas than  `i++`, especially when it’s used in  `for`- loops (`--i`/`i--`) too.

Saves 5 gas per loop.

The following should be fixed:
```
protocol/contracts/plugins/mocks/EasyAuction.sol:277:            for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:288:        for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:316:        for (uint256 i = 0; i < _sellOrders.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:344:        for (uint256 i = 0; i < iterationSteps; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:513:        for (uint256 i = 0; i < orders.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:524:        for (uint256 i = 0; i < orders.length; i++) {
protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol:36:            i++;
protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol:55:            i++;
protocol/contracts/libraries/String.sol:14:        for (uint256 i = 0; i < bStr.length; i++) {
protocol/contracts/p1/BasketHandler.sol:653:        for (uint256 i = 0; i < erc20s.length; i++) {
protocol/contracts/p1/Distributor.sol:133:        for (uint256 i = 0; i < numTransfers; i++) {
```

Tool used: `grep -rn "i++" protocol/contracts` / `grep -rn "i--" protocol/contracts`

### 2. `++i`/`i++`  should be  `unchecked{++i)`/`unchecked{i++}`  when it is not possible for them to overflow, as it’s the case when used in  `for`- and  `while`- loops.

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas (per loop).

The following should be fixed:

```
protocol/contracts/plugins/mocks/EasyAuction.sol:277:            for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:288:        for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:316:        for (uint256 i = 0; i < _sellOrders.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:344:        for (uint256 i = 0; i < iterationSteps; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:513:        for (uint256 i = 0; i < orders.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol:524:        for (uint256 i = 0; i < orders.length; i++) {
protocol/contracts/libraries/Array.sol:11:        for (uint256 i = 1; i < arrLen; ++i) {
protocol/contracts/libraries/Array.sol:12:            for (uint256 j = 0; j < i; ++j) {
protocol/contracts/libraries/Array.sol:23:        for (uint256 i = 1; i < arrLen; ++i) {
protocol/contracts/libraries/String.sol:14:        for (uint256 i = 0; i < bStr.length; i++) {
protocol/contracts/p1/BackingManager.sol:221:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BackingManager.sol:238:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/mixins/RewardableLib.sol:27:        for (uint256 i = 0; i < registry.assets.length; ++i) {
protocol/contracts/p1/mixins/RewardableLib.sol:67:        for (uint256 i = 0; i < erc20sLen; ++i) {
protocol/contracts/p1/mixins/RewardableLib.sol:73:        for (uint256 i = 0; i < erc20sLen; ++i) {
protocol/contracts/p1/mixins/RecollateralizationLib.sol:242:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {
protocol/contracts/p1/mixins/RecollateralizationLib.sol:329:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {
protocol/contracts/p1/mixins/RecollateralizationLib.sol:437:        for (uint256 i = 0; i < len; ++i) {
protocol/contracts/p1/BasketHandler.sol:70:        for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
protocol/contracts/p1/BasketHandler.sol:78:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BasketHandler.sol:218:        for (uint256 i = 0; i < config.erc20s.length; ++i) {
protocol/contracts/p1/BasketHandler.sol:227:        for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/BasketHandler.sol:262:        for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/BasketHandler.sol:286:        for (uint256 i = 0; i < size; ++i) {
protocol/contracts/p1/BasketHandler.sol:337:        for (uint256 i = 0; i < len; ++i) {
protocol/contracts/p1/BasketHandler.sol:397:        for (uint256 i = 0; i < len; ++i) {
protocol/contracts/p1/BasketHandler.sol:416:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BasketHandler.sol:437:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BasketHandler.sol:530:        for (uint256 i = 0; i < basketLength; ++i) {
protocol/contracts/p1/BasketHandler.sol:548:        for (uint256 i = 0; i < basketLength; ++i) {
protocol/contracts/p1/BasketHandler.sol:553:            for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {
protocol/contracts/p1/BasketHandler.sol:586:        for (uint256 i = 0; i < targetsLength; ++i) {
protocol/contracts/p1/BasketHandler.sol:597:            for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
protocol/contracts/p1/BasketHandler.sol:611:            for (uint256 j = 0; j < backupLength && assigned < size; ++j) {
protocol/contracts/p1/BasketHandler.sol:643:        for (uint256 i = 0; i < basketLength; ++i) {
protocol/contracts/p1/BasketHandler.sol:653:        for (uint256 i = 0; i < erc20s.length; i++) {
protocol/contracts/p1/BasketHandler.sol:707:        for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/BasketHandler.sol:725:        for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/AssetRegistry.sol:38:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/AssetRegistry.sol:49:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/AssetRegistry.sol:127:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/AssetRegistry.sol:138:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/Distributor.sol:108:        for (uint256 i = 0; i < destinations.length(); ++i) {
protocol/contracts/p1/Distributor.sol:133:        for (uint256 i = 0; i < numTransfers; i++) {
protocol/contracts/p1/Distributor.sol:143:        for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/RToken.sol:270:            for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/RToken.sol:303:            for (uint256 i = 0; i < basketSize; ++i) {
protocol/contracts/p1/RToken.sol:329:        for (uint256 i = 0; i < basketSize; ++i) {
protocol/contracts/p1/RToken.sol:334:        for (uint256 i = 0; i < basketSize; ++i) {
protocol/contracts/p1/RToken.sol:478:        for (uint256 i = 0; i < erc20length; ++i) {
protocol/contracts/p1/RToken.sol:501:        for (uint256 i = 0; i < erc20length; ++i) {
protocol/contracts/p1/RToken.sol:674:            for (uint256 i = 0; i < tokensLen; ++i) {
protocol/contracts/p1/RToken.sol:683:            for (uint256 i = 0; i < tokensLen; ++i) {
protocol/contracts/p1/RToken.sol:711:        for (uint256 i = 0; i < queue.tokens.length; ++i) {
protocol/contracts/p1/RToken.sol:757:            for (uint256 i = 0; i < tokensLen; ++i) {
protocol/contracts/p1/RToken.sol:767:            for (uint256 i = 0; i < tokensLen; ++i) {
protocol/contracts/p1/RToken.sol:793:        for (uint256 i = 0; i < tokensLen; ++i) {
```

### 3. Use functions instead of modifiers

Modifiers should be refactored in functions to save gas.

This should be refactored:

```
protocol/contracts/plugins/mocks/EasyAuction.sol:18:    modifier atStageOrderPlacement(uint256 auctionId) {
protocol/contracts/plugins/mocks/EasyAuction.sol:26:    modifier atStageOrderPlacementAndCancelation(uint256 auctionId) {
protocol/contracts/plugins/mocks/EasyAuction.sol:34:    modifier atStageSolutionSubmission(uint256 auctionId) {
protocol/contracts/plugins/mocks/EasyAuction.sol:47:    modifier atStageFinished(uint256 auctionId) {
protocol/contracts/plugins/mocks/vendor/EasyAuction.sol:914:    modifier onlyOwner() {
protocol/contracts/plugins/aave/ReentrancyGuard.sol:49:    modifier nonReentrant() {
protocol/contracts/p1/mixins/Component.sol:41:    modifier notPausedOrFrozen() {
protocol/contracts/p1/mixins/Component.sol:46:    modifier notFrozen() {
protocol/contracts/p1/mixins/Component.sol:51:    modifier governance() {
```

### 4. Using Solidity version 0.8.17 will provide an overall gas optimization

Using at least 0.8.10 will save gas due to skipped extcodesize check if there is a return value. Currently the contracts are compiled using version 0.8.9. 