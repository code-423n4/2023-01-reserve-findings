# [01] Lack of check for zero amount when making erc20 transfers

Some ERC20s revert on transfer of zero amount. Whenever making more than one transfer in the same function, it's recommended to check for zero amount.

Otherwise, if a token with such behavior in used for one of the transfers, the entire transaction will fail including the transfer of tokens with standard behavior.

Consider making the following change in `RToken.sol`

```solidity
diff --git a/RToken.sol.orig b/RToken.sol
--- a/RToken.sol.orig
+++ b/RToken.sol
@@ -791,10 +791,12 @@ contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {
         _mint(account, amtRToken);

         for (uint256 i = 0; i < tokensLen; ++i) {
-            IERC20Upgradeable(queue.tokens[i]).safeTransfer(
-                address(backingManager),
-                amtDeposits[i]
-            );
+            if (amtDeposits[i] != 0) {
+                IERC20Upgradeable(queue.tokens[i]).safeTransfer(
+                    address(backingManager),
+                    amtDeposits[i]
+                );
+            }
         }
     }
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L793-L798

# [02] Lack of access control in `Distributor.distribute()`

The distribute function will send funds (calculated from `tokensPerShare * numberOfShares`) from the user controlled input `from`. This can be called by a griefer at unwanted times.

Consider adding access control into this function so that it can only be called by trusted addresses. 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L87-L137

# [03] Safe downcasting

The project downcast integers on multiple locations throughout the codebase. In many places, there's a comment explaining why downcasting without a check is safe, e.g.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L232

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L350

However, some operations are downcasting and don't contain any comment contextualizing why it's safe, e.g.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L112-L118

Consider adding a commend on all direct downcasting operations explaining why it's safe. For any place considered not safe, consider using a safe cast function that reverts if the number being downcasted causes an overflow, e.g. OpenZeppelin SafeCast, similarly to how it's implemented on [StRSRVotes](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L4).

This way, the project can: use safe cast wherever necessary, and have a comment for any place that uses direct downcasting without a safe wrapper.

# [04] Use the latest solidity version

The majority of contracts are using v0.8.9. Consider using the latest version of solidity to ensure the compiler contains the latest security fixes and the latest features, e.g. v.0.8.10 contains some new optimizations with regards to external function calls

# [05] Add address(0) checks for init functions

Consider checking against address(0) during address initializations.

If a variable gets configured with address zero, failure to immediately reset the value can result in unexpected behavior for the project.

E.g. the following checks can be added in `Distributor.sol`:

```solidity
diff --git a/Distributor.sol.orig b/Distributor.sol
--- a/Distributor.sol.orig
+++ b/Distributor.sol
@@ -42,10 +42,15 @@ contract DistributorP1 is ComponentP1, IDistributor {
         __Component_init(main_);

         rsr = main_.rsr();
+
+        require(address(main_.rToken()) != address(0), "invalid rToken address");
+        require(address(main_.furnace()) != address(0), "invalid furnace address");
+        require(address(main_.stRSR()) != address(0), "invalid stRSR address");
+
         rToken = IERC20(address(main_.rToken()));
         furnace = address(main_.furnace());
         stRSR = address(main_.stRSR());
```

# [06] Prevent state modifications when the contract is paused 

When possible, prevent state changes when the system is in pause mode.

E.g. `BackingManager.sol` won't execute the following functions if the contract is paused: `grantRTokenAllowance()`, `manageTokens()` and `manageTokensSortedOrder()`. However, state variables like `tradingDelay` and `backingBuffer` can be modified. Consider adding the modifier `notPausedOrFrozen` into `setTradingDelay()` and `setBackingBuffer()`.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256-L260

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L263-L267

# [07] Add a check for the price value returned by chainlink

Consider adding a check for the price returned by chainlink to ensure it's positive. The impact of not using this type of check is letting an incorrect price value being processed.

## Recommendation

Add the following change on `OracleLib.sol`:

```solidity
diff --git a/OracleLib.sol.orig b/OracleLib.sol
--- a/OracleLib.sol.orig
+++ b/OracleLib.sol
@@ -26,6 +26,8 @@ library OracleLib {
         (uint80 roundId, int256 p, , uint256 updateTime, uint80 answeredInRound) = chainlinkFeed
             .latestRoundData();
 
         if (updateTime == 0 || answeredInRound < roundId) {
             revert StalePrice();
         }
         // Downcast is safe: uint256(-) reverts on underflow; block.timestamp assumed < 2^48
         uint48 secondsSince = uint48(block.timestamp - updateTime);
         if (secondsSince > timeout) revert StalePrice();

+        if (p <= 0) revert CustomError();
+
         // {UoA/tok}
         return shiftl_toFix(uint256(p), -int8(chainlinkFeed.decimals()));
     }
```

# [08] Allow `oracleError` to be modified

Currently `Asset.oracleError` is immutable. Consider adding a setter function to allow this value to be modified after deployment. Otherwise, there might be slippage issues locking the system during market turbulance, preveting users from interacting with the asset.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L23

# [09] Unbounded loop

The loop in `BasketHandler._switchBasket()` can run out of gas if there are too many ERC20 items into one `config` state variable. Impact can be a DOS condition when calling `refreshBasket()`.

Consider adding new functions to allow switching a basket through multiple calls, by saving intermediare values and passing a range of indexes to be iterated for `config`, e.g. adding a slice functionality.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L548-L572

# [10] Emit the event `BasketNeededChanged` after the external call `battery.discarge()` in `RToken.redeem()`

Consider emitting the event after the external call. Since the function doesn't contain a nonReentrant modifier, reentrant calls could spam the event and disturbe frontends or monitoring tools.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L491

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L494

# [11] Avoid receiving stale values on setter functions

Check if the new value is different from the current value. This can help prevent emitting unnecessary events.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L128-L131

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L135-L139

# [12] Usage of abi.encodePacked

abi.encode is generally preferred over abi.encodePacked to prevent hash collisions. If there's a compelling reason to use abi.encodePacked, consider adding a comment.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L206-L207

# [13] Repeated validation statements

Repeated validation statements can be converted into a function modifier to improve code reusability.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L37

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L45

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L53

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L61

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L69

# [14] `__gap` can be declared into a separate contract and reused across the project

A statement used for the `__gap` variable, e.g. `uint256[47] private __gap`, can be declared into a separate contract and reused wherever necessary, instead of being declared directly on multiple contracts. See [reference implementation](https://github.com/code-423n4/2022-08-foundation/blob/main/contracts/mixins/shared/Gap10000.sol).

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L103-L108

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L179-L184

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L96-L101

# [15] Critical parameters changes should use two-step procedure

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L180-L184

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L187-L191

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L589-L593

# [16] OpenZeppelin new recommendation is to use `_disableInitializers()` for upgradeable contracts

Instead of using an empty constructor with the `initializer` modifier, OpenZeppelin recommendation is to use `_disableInitializers()` inside the constructor.

See this [post](https://forum.openzeppelin.com/t/what-does-disableinitializers-function-mean/28730) for more information.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L23

# [17] Draft openzeppelin dependencies

OpenZeppelin contracts may be considered draft contracts if they have not received adequate security auditing or are liable to change with future development.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L7

Consider waiting until the contract is finalized. Otherwise, make sure that development team is aware of the risks of using a draft OpenZeppelin contract and accept the risk-benefit trade-off.

# [18] Move require/validation statements to the top of the function

For `StRSR.setUnstakingDelay()` the statement `require(rewardPeriod * 2 <= unstakingDelay)` can be rewritten as `require(rewardPeriod * 2 <= val)` and can be moved to top of the function, after the first require and before emitting the event.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813-L816

# [19] Order of functions

The solidity [documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) recommends the following layout order for functions:

constructor
external
public
internal
private

The instance bellow shows external, then private, then public.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L95

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L105

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256

# [20] Fix typo

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L89

# [21] Fix inconsistent indentation

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L187-L189

BasketHandler line 188 should have the same indentation level as line 187 and line 189.

# [22] Can use named imports

Consider naming the imports to improve code readability. E.g. `import "@openzeppelin/contracts/token/ERC20/IERC20.sol";` can be rewritten as `import {IERC20} from “import “@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol”;`

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L7

# [23] Save state variables into a local variable

State variables can be saved into a local variable when reading the value more than one on the same function. 

E.g. `basketsNeeded` can be cached in `RToken.issue()`.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L259-L260

Similarly, `queue.left` can be cached on `RToken.vestUpTo()`:

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L739

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L741

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L754

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L764

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L780

# [24] Remove unused comment

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L112

# [25] Misleading comments

The comment on [L45-46](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L45-L46) on `Auth.sol` seems to be referring to `longFreezes` instead of `longFreeze`, since `longFreeze` is an integer and `longFreezes` is a mapping.

Also, the comment on [L174](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L174) for `BasketHandler.sol` indicates that `refreshBasket()` is only callable directly by governance. However, the condition on [L188-189](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L187-L188) allows to be called by anyone as long as the collateral status is disabled and the status is not paused/fronzen.

Furthermore, on `RToken.requireValidBUExchangeRate()` the comment on [L801](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L801) indicates the range is between [1e-9, 1e9]. However the checked range at [L813](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813) is [1e9, 1e27]. Consider changing the code to improve the readability of the code.

# [26] Misleading name `minTradeVolume`

The function `Trading.setMinTradeVolume()` implements a check to test if the received value is smaller than the minimum. A more consistent approach would be replace `min` with `max` for `setMinTradeVolume`, `MIN_TRADE_VOLUME` and `MinTradeVolumeSet`, since the new value has to be smaller than the constant, similarly to how it's implemented on the [Trading.setMaxTradeSlippage](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L128-L131).

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L135-L138

# [27] `>` can be replaced with `!=` for unsigned integer checks

When checking if a uint is not zero, a more declarative check can be `!= 0` instead of `> 0`.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188

# [28] Usage of return named variables and explicit values

Some functions return named variables, others return explicit values.

Following function returns an explicit value.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L61-L63

Following function return a named variable.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L150-L157

Consider adopting the same approach throughout the codebase to improve the explicitness and readability of the code.

# [29] Incomplete NATSPEC

Consider adding NATSPEC documenting all function arguments/return values when present. 

E.g. `RToken.vest()` doesn't contain `endId` on it's NATSPEC.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L372-L378

# [30] Public functions not called by the contract should be declared external

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L786-L788

# [31] Use time units

Consider using time units e.g. (replace 31536000 with 365 days).

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L9-L10

# [32] Replace assert with require or custom error

As described on the solidity [documentation](https://docs.soliditylang.org/en/v0.8.15/control-structures.html#panic-via-assert-and-error-via-require): 
"The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696

# [33] Replace exponentiation with type(uint<n>).max for multiples of 2

`value < 2**96` can be rewritten as `value <= type(uint96).max`

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L657

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L662

# [34] Multiples of 10 should use scientific notation

Using scientific notation for multiples of 10 can improve code readability.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165-L166
