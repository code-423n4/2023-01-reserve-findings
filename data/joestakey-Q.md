## Summary
### Low Risk
|      | Issue                                                                            |
|------|----------------------------------------------------------------------------------|
| L-01 | Wrong comment                                                                    |
| L-02 | Add a descriptive string for function likely to revert in some cases             |
| L-03 | Use `require` instead of `assert`                                                |
| L-04 | `furnace` and `stRsr` can be added twice in `destinations`                       |
| L-05 | `lastPayout` description is incorrect                                            |
| L-06 | Avoid using draft dependencies                                                   |
| L-07 | `StRSR.stake()` breaks a `@custom:interaction` requirement                       |
| L-08 | Avoid division by 0                                                              |

### Non-Critical
|      | Issue                                                                            |
|------|----------------------------------------------------------------------------------|
| N-01 | Use a more recent version of Solidity                                            |
| N-02 | Scientific notation can be used                                                  |
| N-03 | Typos                                                                            |
| N-04 | Naming conventions                                                               |
| N-05 | Block time assumption may be false                                               |
| N-06 | Native time should be used                                                       |
| N-07 | Avoid floating pragmas                                                           |
| N-08 | Avoid shadowing variables                                                        |


## Low
### [L‑01] Wrong comment 

`handoutExcessAssets()` forwards assets to the `RSRTrader`, not to the StRSR pool

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L173-L180
```solidity
173: // Forward any RSR held to StRSR pool; RSR should never be sold for RToken yield //@audit L: wrong comment
174:         if (rsr.balanceOf(address(this)) > 0) {
175:             // For CEI, this is an interaction "within our system" even though RSR is already live
176:             IERC20Upgradeable(address(rsr)).safeTransfer(
177:                 address(rsrTrader),
178:                 rsr.balanceOf(address(this))
179:             );
180:         }
```

### [L‑02] Add a descriptive string for function likely to revert in some cases

If the Aave lending pool has a liquidity shortage at the time of withdrawal,full withdrawal of the requested underlying token amount will not be possible.
Make sure an appropriate error is used to let the caller know why it reverted

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L345
```solidity
345: assert(amt == amountToWithdraw);
```


### [L‑03] Use `require` instead of `assert`

As per Solidity's [documentation](https://docs.soliditylang.org/en/v0.8.17/control-structures.html?highlight=assert#panic-via-assert-and-error-via-require):

```
The assert function creates an error of type Panic(uint256). The same error is created by the compiler in certain situations as listed below.

Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L104
```solidity
104: assert(low == 0);
```

### [L‑04] `furnace` and `stRsr` can be added twice in `destinations`

There is a check in the `add()` function of `EnumerableSet` to prevent the same value to be added twice.

The issue is that is possible to bypass this check because of the `FURNACE` and `ST_RSR` values, meaning both can be added twice (for instance,`FURNACE` and `furnace`, by calling `setDistribution()` with `dest ==  furnace`)

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L171
```solidity
170:         } else {
171:             destinations.add(dest);
172:             require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");
173:         }
```

Prevent it by adding an extra check in `setDistribution`

```diff
61: function setDistribution(address dest, RevenueShare memory share) external governance {
+         if((dest == furnace) || (dest == stRSR)) revert();
62:         _setDistribution(dest, share);
63:         RevenueTotals memory revTotals = totals();
64:         _ensureNonZeroDistribution(revTotals.rTokenTotal, revTotals.rsrTotal);
65:     }
```

### [L‑05] `lastPayout` description is incorrect

`lastPayout` is said to return the last payout time.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L21
```solidity
21: uint48 public lastPayout; // {seconds} The last time we did a payout
```

This is incorrect, as the rounding of `numPeriods` in `melt()` means most of the time `lastPayout` will be less than the `block.timestamp` at which the call was made

```solidity
73: uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;
74:         uint192 payoutRatio = FIX_ONE.minus(FIX_ONE.minus(ratio).powu(numPeriods));
75: 
76:         uint256 amount = payoutRatio.mulu_toUint(lastPayoutBal);
77: 
78:         lastPayout += numPeriods * period;
```

### [L‑06] Avoid using draft dependencies

`StRSR` is using `draft-EIP712Upgradeable.sol`, an OpenZeppelin contract.
This contract is still a draft and is not considered ready for mainnet use: it may not have received adequate security auditing and may be amended in the future.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L8
```solidity
8: import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";
```

### [L‑07] `StRSR.stake()` breaks a `@custom:interaction` requirement

It is available during paused/frozen state. This breaks a requirement of `@custom:interaction` [functions](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/docs/solidity-style.md#function-annotations):

```
1.@custom:interaction - An action. Disallowed while paused. Per-contract reentrancy-safety is needed.
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L200
```solidity
200: /// @dev Staking continues while paused/frozen, without reward handouts
```

Add this exception to the documentation, or add the modifier if the function should not be callable during paused/frozen state

### [L‑08] Avoid division by 0

`basketPriceLow` can be 0. Add a check before performing the following computation

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L173
```solidity
173: uint192 basketsHigh = components.bm.safeMulDivCeil(FIX_ONE, assetsHigh, basketPriceLow);
```




## Non-critical
### [N-01] Use a more recent version of Solidity


`pragma solidity 0.8.9;` is used across the contracts. Consider upgrading to a more recent version: at least `0.8.12` to get `string.concat()` to be used instead of `abi.encodePacked()`

### [N-02] Scientific notation can be used 


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L15
```solidity
15: uint192 constant MIN_BLOCK_ISSUANCE_LIMIT = 10_000 * FIX_ONE; //@audit 1e4
```

### [N-03] Typos

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L361
```solidity
361: //      qty = FIX_MAX iff p = 0 //@audit NC: typo iff -> if
```

### [N-04] Naming conventions

Internal and private functions should have an underscore

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L68
```solidity
68: function empty(Basket storage self) internal {
```

### [N-05] Block time assumption may be false

Avoid using fixed block time variables, as block time can be greater than expected if a [slot is left empty](https://ethereum.org/en/developers/docs/blocks/#block-time)
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L11
```solidity
11: uint48 constant BLOCKS_PER_HOUR = 300;
```

### [N-06] Native time should be used

use Native time units instead of a number of seconds

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L34
```solidity
34: uint48 public constant MAX_TRADING_DELAY = 31536000; //@audit 1 years
```

### [N-07] Avoid floating pragmas

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L3
```solidity
3: pragma solidity ^0.8.9;
```

### [N-08] Avoid shadowing variables

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L27
```solidity
27: ICToken erc20 = ICToken(address(config.erc20)); //@audit erc20 is shadowing
```