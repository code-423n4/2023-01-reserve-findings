# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1 | Usage of `uint/int` smaller than 32 bytes (256 bits) cost more gas  |  5 |
| 2 | `storage` variable should be cached into `memory` variables instead of re-reading them  |  4 |
| 3 | Use `unchecked` blocks to save gas  |  3 |
| 4 | Remove redundant checks |  1 |
| 5 | `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables  |  9 |
| 6 | Splitting `require()` statements that uses && saves gas |10 |
| 7 | `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow (for & while loops) |  38  |


## Findings

### 1- Usage of `uint/int` smaller than 32 bytes (256 bits) cost more gas :

State variable declared as `uint/int` smaller than 32 bytes (256 bits) will cost more gas when used in the code logic as the compiler must first convert them back to `uint256` before using them, the only case where using smaller `uint/int` size matter is when you store many state variables in the same storage slot otherwise it's better to declare them as `uint256` to save gas in functions calls.

There are 5 instances of this issue :

File: contracts/p1/RToken.sol [Line 50](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L50)

```
uint192 public issuanceRate;
```

File: contracts/p1/RToken.sol [Line 67-76](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L67-L76)

```
uint192 public basketsNeeded; // D18{BU}

// ==== Slow Issuance State====

// When all pending issuances will have vested.
uint192 private allVestAt; // D18{fractional block number}

// Enforce a fixed issuanceRate throughout the entire block by caching it.
// Both of these MUST only be modified by whenFinished()
uint192 private lastIssRate; // D18{rTok/block}
```

File: contracts/p1/StRSR.sol [Line 147](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L147)

```
uint48 public payoutLastPaid;
```

### 2- `storage` variable should be cached into `memory` variables instead of re-reading them :

The instances below point to the second+ access of a state variable within a function, the caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read, thus saves **100gas** for each instance.

There are 4 instances of this issue :

File: contracts/p1/StRSR.sol [Line 270](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L270)

```
uint256 newStakeRSR = (FIX_ONE_256 * totalStakes + (stakeRate - 1)) / stakeRate; 
```

In the code linked above the value of `stakeRate` is read multiple times (2) from storage and it's respective values does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: contracts/p1/StRSR.sol [Line 324](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L324)

```
uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;
```

In the code linked above the value of `draftRate` is read multiple times (2) from storage and it's respective values does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: contracts/p1/StRSR.sol [Line 391-398](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L391-L398)

```
if (stakeRSR > 0) {
    // Downcast is safe: totalStakes is 1e38 at most so expression maximum value is 1e56
    stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);
}
if (stakeRSR == 0 || stakeRate > MAX_STAKE_RATE) {
    seizedRSR += stakeRSR;
    beginEra();
}
```

In the code linked above the value of `stakeRSR` is read multiple times (5) from storage and it's respective values does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.

File: contracts/p1/StRSR.sol [Line 406-414](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L406-L414)

```
if (draftRSR > 0) {
    // Downcast is safe: totalDrafts is 1e38 at most so expression maximum value is 1e56
    draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);
}

if (draftRSR == 0 || draftRate > MAX_DRAFT_RATE) {
    seizedRSR += draftRSR;
    beginDraftEra();
}
```

In the code linked above the value of `draftRSR` is read multiple times (5) from storage and it's respective values does not change so it should be cached into a memory variable in order to save gas by avoiding multiple reads from storage.


### 3- Use `unchecked` blocks to save gas :

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block.

There are 3 instances of this issue:

File: contracts/libraries/RedemptionBattery.sol [Line 47](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L47)

```
battery.lastCharge = charge - amount;
```

The operation in the code above cannot underflow because of the if check that precede it [Line 44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L44) so it should be marked as `unchecked`. 

File: contracts/p1/StRSR.sol [Line 698-699](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L698-L699)

```
stakes[era][account] += amount;
totalStakes += amount;
```

The operations in the code above cannot overflow because of the if check that precedes them [Line 696](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696) and because we always have `totalStakes >= stakes[era][account]` so they should be marked as `unchecked`. 

### 4- Remove redundant checks :

The following require check in the `unstake` function is redundant because the `_burn` function is also called by `unstake` and it already contains the same check on the account staking balance, so this check is redundant and should be removed to save gas.

File: contracts/p1/StRSR.sol [Line 260](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L260)
```
require(stakes[era][account] >= stakeAmount, "Not enough balance");
```

### 5- `x += y/x -= y` costs more gas than `x = x + y/x = x - y` for state variables :

Using the addition operator instead of plus-equals saves **113 gas** as explained [here](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)

There are 9 instances of this issue:

```
File: contracts/p1/Furnace.sol

81      lastPayout += numPeriods * period;

File: contracts/p1/StRSR.sol

229     stakeRSR += rsrAmount;
387     stakeRSR -= stakeRSRToTake;
513     stakeRSR += payout;
516     payoutLastPaid += numPeriods * rewardPeriod;
549     draftRSR += rsrAmount;
698     stakes[era][account] += amount;
699     totalStakes += amount;
721     totalStakes -= amount;
```

### 6- Splitting `require()` statements that uses && saves gas :

There are 10 instances of this issue :

```
File: contracts/p1/Broker.sol

134     require(
            newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
            "invalid auctionLength"
        );

File: contracts/p1/Furnace.sol

89      require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");

File: contracts/p1/RevenueTrader.sol

72      require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");

File: contracts/p1/RToken.sol

410     require(queue.left <= endId && endId <= queue.right, "out of range");
590     require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
623     require(index >= item.left && index < item.right, "out of range");     
741     require(queue.left <= endId && endId <= queue.right, "out of range");
813     require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");

File: contracts/p1/StRSR.sol

813     require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
821     require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
```

### 7- `++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 38 instances of this issue:
 
```
File: contracts/p1/AssetRegistry.sol

38      for (uint256 i = 0; i < length; ++i)
49      for (uint256 i = 0; i < length; ++i)
127     for (uint256 i = 0; i < length; ++i)
138     for (uint256 i = 0; i < length; ++i)

File: contracts/p1/BackingManager.sol

221     for (uint256 i = 0; i < length; ++i)
238     for (uint256 i = 0; i < length; ++i)

File: contracts/p1/BasketHandler.sol

70      for (uint256 i = 0; i < length; ++i)
78      for (uint256 i = 0; i < length; ++i)
218     for (uint256 i = 0; i < config.erc20s.length; ++i)
227     for (uint256 i = 0; i < erc20s.length; ++i)
262     for (uint256 i = 0; i < erc20s.length; ++i)
286     for (uint256 i = 0; i < size; ++i)
337     for (uint256 i = 0; i < len; ++i)
397     for (uint256 i = 0; i < len; ++i)
416     for (uint256 i = 0; i < length; ++i) 
437     for (uint256 i = 0; i < length; ++i)
530     for (uint256 i = 0; i < basketLength; ++i)
548     for (uint256 i = 0; i < basketLength; ++i)
586     for (uint256 i = 0; i < targetsLength; ++i)
643     for (uint256 i = 0; i < basketLength; ++i)
653     for (uint256 i = 0; i < erc20s.length; i++)
707     for (uint256 i = 0; i < erc20s.length; i++)
725     for (uint256 i = 0; i < erc20s.length; i++)

File: contracts/p1/Distributor.sol

108     for (uint256 i = 0; i < destinations.length(); ++i) 
133     for (uint256 i = 0; i < numTransfers; i++) 
143     for (uint256 i = 0; i < length; ++i)

File: contracts/p1/RToken.sol

270     for (uint256 i = 0; i < erc20s.length; ++i)
303     for (uint256 i = 0; i < basketSize; ++i)
329     for (uint256 i = 0; i < basketSize; ++i)
334     for (uint256 i = 0; i < basketSize; ++i)
478     for (uint256 i = 0; i < erc20length; ++i)
501     for (uint256 i = 0; i < erc20length; ++i)
647     for (uint256 i = 0; i < tokensLen; ++i)
683     for (uint256 i = 0; i < tokensLen; ++i)
711     for (uint256 i = 0; i < queue.tokens.length; ++i)
757     for (uint256 i = 0; i < tokensLen; ++i)
767     for (uint256 i = 0; i < tokensLen; ++i)
793     for (uint256 i = 0; i < tokensLen; ++i)
```