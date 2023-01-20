### Gas Optimization

| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[G-01]| Avoid compound assignment operator in state variables | 18 |
|[G-02]| Remove require in `poke()` | 1 |
|[G-03]| Integer overflow/underflow | - |
|[G-04]| Replace `memory` to `calldata` for function parameters | - |


---

# [G-01] Avoid compound assignment operator in state variables

Using compound assignment operators for state variables (like State += X or State -= X ...) it's more expensive than using operator assignment (like State = State + X or State = State - X ...). In short, `<X> += <Y>` costs more gas than `<X> = <X> + <Y>` for x is state variable, (same with -= )

Using the addition operator instead of plus-equals saves gas:

```solidity
File: BasketHandler.sol
636:             nonce += 1;

File: Furnace.sol
81:         lastPayout += numPeriods * period;

File: RToken.sol
329:         for (uint256 i = 0; i < basketSize; ++i) {
330:             liabilities[IERC20(erc20s[i])] += deposits[i];
331:         }

678:                 liabilities[IERC20(queue.tokens[i])] -= amt[i];

687:                 liabilities[IERC20(queue.tokens[i])] -= amt[i];

761:                 liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];

771:                 liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];

File: StRSR.sol
229:         stakeRSR += rsrAmount;

387:         stakeRSR -= stakeRSRToTake;

402:         draftRSR -= draftRSRToTake;

513:             stakeRSR += payout;

516:         payoutLastPaid += numPeriods * rewardPeriod;

549:         draftRSR += rsrAmount;

684:         eraStakes[to] += amount;

698:         stakes[era][account] += amount;
699:         totalStakes += amount;

721:         totalStakes -= amount;

File: Auth.sol
136:         longFreezes[_msgSender()] -= 1; // reverts on underflow

```

# [G-02] Remove require in `poke()`

```solidity
File: Main.sol
43:     function poke() external {
44:         // == Refresher ==
45:         assetRegistry.refresh();
46:
47:         // == CE block ==
48:         require(!pausedOrFrozen(), "paused or frozen");
49:         furnace.melt();
50:         stRSR.payoutRewards();
51:     }
```

The `require(!pausedOrFrozen(), "paused or frozen");` can be omitted since the `melt()` and `payoutRewards()` function contains `notPausedOrFrozen` modifier, so this will save gas.

# [G-03] Integer overflow/underflow

Prior to Solidity 0.8.0 version, interger overflow and underflow checks were performed by using the SafeMath library. From Solidity 0.8.0 upward, the compiler does that check for us.

This extra check cost gas. If we know that the mathematical operations we will perform inside the contract will not overflow or underflow, we can tell the compiler not to check for overflow or underflow in the operation.

If we know that our mathematics will be safe we could safe a little gas by using unchecked.

```solidity
File: Trading.sol
72:         tradesOpen--;
121:         tradesOpen++;

File: StRSR.sol
578:         era++;

590:         draftEra++;

File: RToken.sol
316:         queue.right++;

File: Distributor.sol
128:             numTransfers++;

File: BasketHandler.sol
598:                 if (goodCollateral(_targetNames.at(i), backup.erc20s[j])) size++;

621:                     assigned++;

```

## ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

Most of the for-loops in the project is using the standard for loop, for example this code snippet:

```solidity
for (uint256 i = 0; i < erc20s.length; ++i) {
    require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
    conf.erc20s.push(erc20s[i]);
}
```

the gas-saving pattern for the for-loops by using `unchecked`:

```solidity
uint256 i;
for (; i < erc20s.length; ) {
  require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
  conf.erc20s.push(erc20s[i]);
  unchecked {
    ++i;
  }
}
```

# [G-04] Replace `memory` to `calldata` for function parameters

When running a function we could pass the function parameters as `calldata` or `memory` for variables such as strings, structs, arrays etc. If we are not modifying the passed parameter we should pass it as `calldata` because `calldata` is more gas efficient than `memory`. Can only use `calldata` when we are not going to modify the value of the variable passed as `calldata` inside the function.

for example:

```solidity
File: RecollateralizationLib.sol
460:     function isBetterSurplus(
461:         MaxSurplusDeficit memory curr,
462:         CollateralStatus other,
463:         uint192 surplusAmt
464:     ) private pure returns (bool) {
```

this function is using `memory` meanwhile the passed parameter is not being modified inside the function. thus, better to replace `memory` as `calldata` to safe gas.

other instances:

```solidity
File: Deployer.sol
42:     constructor(
43:         IERC20Metadata rsr_,
44:         IGnosis gnosis_,
45:         IAsset rsrAsset_,
46:         Implementations memory implementations_
47:     ) {

File: TradeLib.sol
38:     function prepareTradeSell(TradeInfo memory trade, TradingRules memory rules)

File: Trading.sol
111:     function tryTrade(TradeRequest memory req) internal nonReentrant {

```