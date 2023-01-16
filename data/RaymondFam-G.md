## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: Component.sol#L41-L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L41-L44)

```diff
+    function _notPausedOrFrozen() private view {
+        require(!main.pausedOrFrozen(), "paused or frozen");
+    }

    modifier notPausedOrFrozen() {
-        require(!main.pausedOrFrozen(), "paused or frozen");
+        _notPausedOrFrozen();
        _;
    }
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

Here are some of the instances entailed:

[File: RecollateralizationLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
70:        ComponentCache memory components = ComponentCache({

78:        TradingRules memory rules = TradingRules({

83:        Registry memory reg = components.reg.getRegistry();

              [ ... ]
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the instance below may be refactored as follows:

[File: RecollateralizationLib.sol#L401](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L401)

```diff
-        if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {
+        if (!(address(trade.sell) != address(0) || address(trade.buy) == address(0))) {
```
## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

[File: RecollateralizationLib.sol#L437-L454](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437-L454)

```diff
-        for (uint256 i = 0; i < len; ++i) {
+        for (uint256 i = 0; i < len;) {
              ICollateral coll = components.reg.toColl(basketERC20s[i]);

              [ ... ]

+            unchecked {
+                ++i;
+            }
          }
```
## `uint256` over `uint192`
In RecollateralizationLib.sol and Trading.sol, the use of `uint192` in the [structs](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L24-L36) and declaring [state variables](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L20-L21) has no gas saving at all because each `uint192` variable is too sizable to accommodate its `uint192` counterpart, making the variables separately taking up 1 single slot on its own still. Neither will the use of `uint192` have any inherent gas benefit when dealing with function arguments or memory values. In fact, the contract’s gas usage may be higher because the EVM operates on 32 bytes at a time. If the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size in properly enforcing the limits of this smaller type.

As such, consider resorting to `uint256` instead of `uint192`, and using FixLib for `uint256` too.

## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, the following `>=` inequality instance may be refactored as follows:

[File: RewardableLib.sol#L78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78)

```diff
-                assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
+                assert(erc20s[i].balanceOf(address(this)) > liabilities[erc20s[i]] - 1);
```
