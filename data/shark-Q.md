## 0. Use time units for better readability

[Solidity has time units](https://docs.soliditylang.org/en/v0.8.14/units-and-global-variables.html) (seconds, minutes, hours, etc). As such, consider using time units instead of using literal numbers, this will increase readability and decrease the likelihood of mistakes being made.

For example:

File: `Furnace.sol` [Line 16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L16)

```solidity
    uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
```

Instead of the above (`31536000`), consider refactoring to:

```solidity
    uint48 public constant MAX_PERIOD = 1 year;
```

Other instances found:

- File: `Auth.sol` [Line 9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L9)
- File: `Auth.sol` [Line 10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L10)
- File: `BackingManager.sol` [Line 33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33)
- File: `StRSR.sol` [Line 37](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L37)
- File: `StRSR.sol` [Line 38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L38)
- File: `Broker.sol` [Line 24](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24)

## 1. Use delete to clear variables instead of zero assignment

Using the [`delete`](https://docs.soliditylang.org/en/v0.8.17/types.html#delete) keyword more clearly states your intention.

For instance:

File: `RToken.sol` [Line 693-694](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L693-L694)

```solidity
692:    // empty entire queue
693:    queue.left = 0;
694:    queue.right = 0;
```

Instead of above, consider using the `delete` keyword instead:

```solidity
692:    // empty entire queue
693:    delete queue.left;
694:    delete queue.right;
```

Instances also found:

- File: `RToken.sol` [Line 784](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L784)
- File: `RToken.sol` [Line 785](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L785)
- File: `StRSR.sol` [Line 575](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L575)
- File: `StRSR.sol` [Line 576](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L576)
- File: `StRSR.sol` [Line 587-588](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L587-L588)

## 2. Misleading comment

File: `Auth.sol` [Line 135-141](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L135-L141)

```solidity
135:    function freezeLong() external onlyRole(LONG_FREEZER) {
136:        longFreezes[_msgSender()] -= 1; // reverts on underflow
137:
138:        // Revoke on 0 charges as a cleanup step
139:        if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());
140:        freezeUntil(uint48(block.timestamp) + longFreeze);
141:    }
```

In the code above, there is a comment stating that [line 136](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L136) will "revert on underflow". However, it will never actually underflow. This is due to the `LONG_FREEZER` role getting revoked on 0 charges at [line 139](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L139). And, since the `LONG_FREEZER` role is revoked at 0 charges, it no longer has the necessary role to call `freezeLong()`, meaning line 136 will never underflow/revert.

Consider removing the misleading comment at line 136.

## 3. Typo mistakes

It is not recommended to spell words incorrectly as this will decreases readability. However, this is issue is present in many contracts. Consider fixing the following typos to increase readability:

File: `StRSR.sol` [Line 25](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L25), [Line 541](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L541)

```
    /// @audit zereod -> zeroed
25: *   If this happens, users balances are zereod out and StRSR is re-issued at a 1:1 exchange rate

      /// @audit appeneded -> appended
541:  //   r'.queue is r.queue with a new entry appeneded for (totalDrafts' - totalDraft) drafts
```

File: `Fixed.sol` [Line 288](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L288)

```
            /// @audit "Multiply-in" -> "Multiply in"
288:        // Multiply-in FIX_SCALE before dividing by y to preserve precision.
```

## 4. `chainlinkFeed.latestRoundData()` could give invalid price

In `OracleLib.sol`, the function `price()` does not check for a negative value being given from `latestRoundData()`.

Consider verifying that `int256 p` is a non-negative integer so that, if a negative integer were to be given, an underflow will not happen when casting `int256 p` into a `uint256()` (at line 30).

Here is the code affected:

File: `OracleLib.sol` [Line 14 - Line 31](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L14-L31)

```solidity
14:    function price(AggregatorV3Interface chainlinkFeed, uint48 timeout)
15:        internal
16:        view
17:        returns (uint192)
18:    {
19:        (uint80 roundId, int256 p, , uint256 updateTime, uint80 answeredInRound) = chainlinkFeed
20:            .latestRoundData();
21:
22:        if (updateTime == 0 || answeredInRound < roundId) {
23:            revert StalePrice();
24:        }
25:        // Downcast is safe: uint256(-) reverts on underflow; block.timestamp assumed < 2^48
26:        uint48 secondsSince = uint48(block.timestamp - updateTime);
27:        if (secondsSince > timeout) revert StalePrice();
28:
29:        // {UoA/tok}
30:        return shiftl_toFix(uint256(p), -int8(chainlinkFeed.decimals()));
31:    }
```

## 5. Use named imports rather than unnamed imports

Using named imports (`import {x} from "y.sol"`) will speed up compilation, and avoids polluting the namespace making flattened files smaller.

For example:

File: `BackingManager.sol` [Line 4-12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L4-L12)

```solidity
4: import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6: import "../interfaces/IAsset.sol";
7: import "../interfaces/IBackingManager.sol";
8: import "../interfaces/IMain.sol";
9: import "../libraries/Array.sol";
10: import "../libraries/Fixed.sol";
11: import "./mixins/Trading.sol";
12: import "./mixins/RecollateralizationLib.sol";
```

## 6. Avoid short variable names

Very short variable names like p, x, \_a can make code less readable and potentially less maintainable.

For example, the following variable name is not very descriptive:

File: `NonFiatCollateral.sol` [Line 46](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L46)

```solidity
        uint192 p = pricePerTarget.mul(pegPrice).mul(refPerTok());
```

## 7. `require()` should be used over `assert()`

Using `assert()` instead of `require()` prevents the use of error strings and causes a Panic error on failure. However, many contracts across the codebase are found to be doing this.

`assert()` creates a `Panic(uint256)` error instead of `Error(string)` created by `require()`. Nevertheless, Solidity’s documentation says:

> Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Language analysis tools can evaluate your contract to identify the conditions and function calls which will cause a Panic.

whereas

> The `require` function either creates an error without any data or an error of type `Error(string)`. It should be used to ensure valid conditions that cannot be detected until execution time. This includes conditions on inputs or return values from calls to external contracts.

Also, you can optionally provide a message string for require, but not for assert.

Source: [docs.soliditylang.org/en/v0.8.17/control-structures.html#panic-via-assert-and-error-via-require](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#panic-via-assert-and-error-via-require)

Consider using `require()` with informative error strings instead of `assert()`.

here are some examples:

- File: `TradeLib.sol` [Line 44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44)
- File: `TradeLib.sol` [Line 108-113](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L108-L113)
- File: `TradeLib.sol` [Line 168](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L168)
- File: `RecollateralizationLib.sol` [Line 110](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110)
