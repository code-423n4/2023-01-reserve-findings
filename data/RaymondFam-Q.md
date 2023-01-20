## Typo mistakes
[File: IMain.sol#L32](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L32)

```diff
- *   - usually (but not always) contain sizeable state that require a proxy
+ *   - usually (but not always) contain sizable state that requires a proxy
```
[File: Auth.sol#L22](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L22)

```diff
-     * Typically freezing thaws on its own in a predetemined number of blocks.
+     * Typically freezing thaws on its own in a predetermined number of blocks.
```
[File: Furnace.sol#L66](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L66)

```diff
-    //   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay leriod)
+    //   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay period)
```
[File: IAsset.sol#L92](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L92)

```diff
-    /// @return The status of this collateral asset. (Is it defaulting? Might it soon?)
+    /// @return The status of this collateral asset. (Is it defaulting? Might it be soon?)
```
## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For instance, the import instances below could be refactored as follows:

[File: Component.sol#L4-L9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L4-L9)

```diff
- import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
+ import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
- import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
+ import {UUPSUpgradeable.sol} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
    [ ... ]
```
## Interfaces and libraries should be separately saved and imported
Some contracts have interface(s) or library showing up in its/their entirety at the top/bottom of the contract facilitating an ease of references on the same file page. This has, in certain instances, made the already large contract size to house an excessive code base. Additionally, it might create difficulty locating them when attempting to cross reference the specific interfaces embedded elsewhere but not saved into a particular .sol file.  

Consider saving the interfaces and libraries entailed respectively, and having them imported like all other files.

Here are some of the instances entailed:

[File: IAsset.sol#L75](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L75)
[File: IMain.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol)
[File: BasketHandler.sol#L61-L104](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L61-L104)
[File: ATokenFiatCollateral.sol#L10-L27](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L10-L27)

## Inconsistency in interface naming
Some interfaces in the code bases are named without the prefix `I` that could cause confusion to developers and readers referencing or interacting with the protocol. Consider conforming to Solidity's naming conventions by having the specific instance below refactored as follows:

[File: IMain.sol#L166](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L166)

```diff
- interface TestIMain is IMain {
+ interface ITestIMain is IMain {
```
## Contracts should have their names exactly matched to their filenames 
Contracts, interfaces and libraries with their names differing with the filenames could sometimes create confusion and difficulty in cross referencing.

For instance, the abstract contract below has been named `ComponetP1` where as the file has been saved as `Component.sol`. Consider sticking to the same name where possible:

[File: Component.sol#L14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L14)

```solidity
abstract contract ComponentP1 is
```
## Camel/snake casing function names
Function names should also be conventionally camel cased where possible. If snake case is preferred/adopted, consider lower casing them.

Here is one of the numerous instances entailed:

[File: ATokenFiatCollateral.sol#L26](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L26)

```solidity
    function REWARD_TOKEN() external view returns (IERC20);
```
## `require()` over `assert()`
As documented by [Solidity Documentations](https://docs.soliditylang.org/en/v0.8.17/control-structures.html):

"Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Language analysis tools can evaluate your contract to identify the conditions and function calls which will cause a Panic."

The protocol uses `assert()` in multiple places of the code bases. Despite the strip of gas saving benefits for Solidity version ^0.8.0, `require()` should still be used for checking error conditions on inputs and return values.

Here are some of the instances entailed:

[File: RecollateralizationLib.sol#L110](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110)

```solidity
        assert(doTrade); // @ audit Associated with the return value, `doTrade`
```
[File: TradeLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol)

```solidity
            // @ audit Associated with the input parameter, `trade`
44:        assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX); 

            // @ audit Associated with the input parameter, `trade`
108:        assert( 
109:            trade.sellPrice > 0 &&
110:                trade.sellPrice < FIX_MAX && 
111:                trade.buyPrice > 0 &&
112:                trade.buyPrice < FIX_MAX
113:        );

168:            assert(errorCode == 0x11 || errorCode == 0x12); // @ audit Associated with try catch

170:            assert(keccak256(reason) == UIntOutofBoundsHash); // audit Associated with try catch
```
## Descriptive and Verbose Options
Consider making the naming of local variables more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability.

Here are some of the instances entailed:

[File: TradeLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol)

```solidity
55:         uint192 s = trade.sellAmount > maxSell ? maxSell : trade.sellAmount; // {sellTok} @ audit s

59:        uint192 b = safeMulDivCeil( // @ audit b
```
## Time Units
According to:

https://docs.soliditylang.org/en/v0.8.14/units-and-global-variables.html

suffixes like `seconds`, `minutes`, `hours`, `days` and `weeks` after literal numbers can be used to specify units of time where seconds are the base unit and units are considered naively in the following way:

1 == 1 seconds
1 minutes == 60 seconds
1 hours == 60 minutes
1 days == 24 hours
1 weeks == 7 days

As an example, to minimize human error while making the assignment more verbose, the constant declarations below may respectively be rewritten as follows:

[File: BackingManager.sol#L33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33)

```diff
-    uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
+    uint48 public constant MAX_TRADING_DELAY = 365 days; // {s} 1 year
```
[File: Broker.sol#L24](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24)

```diff
-    uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
+    uint48 public constant MAX_AUCTION_LENGTH = 1 weeks; // {s} max valid duration - 1 week
```
## Use `delete` to clear variables
`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address or a boolean to false. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at x.

The delete key better conveys the intention and is also more idiomatic.

For instance, the mapping instance below may be refactored as follows:

[File: AssetRegistry.sol#L93](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L93)

```diff
-        assets[asset.erc20()] = IAsset(address(0));
+        delete assets[asset.erc20()];
```
##Minimization of truncation
The number of divisions in an equation should be reduced to minimize truncation frequency, serving to achieve higher precision. And, where deemed fit, comment the code line with the original multiple division arithmetic operation for clarity reason.

For instance, the equation below with three division may be refactored as follows:

[File: StRSR.sol#L427](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L427)

```diff
-        return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
+        return ((FIX_SCALE_SQ * 2) + stakeRate) / (stakeRate * 2); // ROUND method
```
## Events associated with setter functions
Consider having events associated with setter functions emit both the new and old values instead of just the new value.

Here are some of the instances entailed:

[File: StRSR.sol#L803-L809](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L803-L809)

```solidity
    function setName(string calldata name_) external governance {
        name = name_;
    }

    function setSymbol(string calldata symbol_) external governance {
        symbol = symbol_;
    }
```
## Modifier on repeated code
Code line repeatedly used may be grouped into a modifier.

For instance, a modifier for the identical require statement in `setUnstakingDelay()` and `setRewardPeriod()` of StRSR.sol may be refactored as follows:

[File: StRSR.sol#L812-L825](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812-L825)

```diff
+    modifier ratioCompatible() {
+        _;
+        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
+    }

-    function setUnstakingDelay(uint48 val) public governance {
+    function setUnstakingDelay(uint48 val) public ratioCompatible governance {
        require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
        emit UnstakingDelaySet(unstakingDelay, val);
        unstakingDelay = val;
-        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
    }

    /// @custom:governance
-    function setRewardPeriod(uint48 val) public governance {
+    function setRewardPeriod(uint48 val) public ratioCompatible governance {
        require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
        emit RewardPeriodSet(rewardPeriod, val);
        rewardPeriod = val;
-        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
    }
```
## Missing check on negative price
`price()` in OracleLib.sol should have a check for negative price. The revert will also prevent an underflow in the return statement when casting a negative integer to `uint256()`. 

[File: OracleLib.sol#L14-L31](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L14-L31) 

```diff
+    error NegativePrice();    

    function price(AggregatorV3Interface chainlinkFeed, uint48 timeout)
        internal
        view
        returns (uint192)
    {
        (uint80 roundId, int256 p, , uint256 updateTime, uint80 answeredInRound) = chainlinkFeed
            .latestRoundData();

        if (updateTime == 0 || answeredInRound < roundId) {
            revert StalePrice();
        }
        // Downcast is safe: uint256(-) reverts on underflow; block.timestamp assumed < 2^48
        uint48 secondsSince = uint48(block.timestamp - updateTime);
        if (secondsSince > timeout) revert StalePrice();

+        if (p < 0) revert NegativePrice(); //

        // {UoA/tok}
        return shiftl_toFix(uint256(p), -int8(chainlinkFeed.decimals()));
    }
```
## Workaround for solhint-disable-next-line no-empty-blocks
The constructor in Main.sol can be rewritten as follows to silence any warning flag without the need for the commented "solhint-disable-next-line no-empty-blocks".

Note: This feature is readily incorporated in the Solidity Wizard since the UUPS vulnerability discovery. You would just need to [check UPGRADEABILITY](https://wizard.openzeppelin.com/) to have the following constructor code block added to the contract:

[File: Main.sol#L21-L23](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L21-L23)

```diff
    /// @custom:oz-upgrades-unsafe-allow constructor
-    // solhint-disable-next-line no-empty-blocks
-    constructor() initializer {}
+    constructor() {
+        _disableInitializers();
+    }
```
## Inappropriate comments
Consider rewriting the first line of comments on `freezeForever()` as follows:

Note: `permanent` means lasting or intended to last or remain unchanged indefinitely. But quite the opposite, the permanent freeze can always be thawed via `unfreeze()` and refrozen via  `freeUntil()`.

[File: Auth.sol#L143-L150](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L143-L150)

```diff
-    /// Enter a permanent freeze
+    /// Enter an indefinite freeze
    // checks:
    // - caller has the OWNER role
    // - unfreezeAt != type(uint48).max
    // effects: unfreezeAt' = type(uint48).max
    function freezeForever() external onlyRole(OWNER) {
        freezeUntil(MAX_UNFREEZE_AT);
    }
```
## Unneeded comment
Underflow due to decrementing `longFreezes[_msgSender()]` in `freezeLong()` of Auth.sol will never happen because `_msgSender()`  has already got its `LONG-FREEZER` role revoked when `longFreezes[_msgSender()] == 0`. This makes it impossible to reach the first line of the function logic.

[File: Auth.sol#L135-L141](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L135-L141) 

```diff
    function freezeLong() external onlyRole(LONG_FREEZER) {
-        longFreezes[_msgSender()] -= 1; // reverts on underflow
+        longFreezes[_msgSender()] -= 1; // No underflow would transpire. Apply unchecked { ... } if need be.

        // Revoke on 0 charges as a cleanup step
        if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());
        freezeUntil(uint48(block.timestamp) + longFreeze);
    }
```
## Inadequate boundary check
`longFreeze` should not only be greater than zero but also greater than `MAX_SHORT_FREEZE` or `shortfreeze` to be feasibly practical. Otherwise, it could likely end up having `shortFreeze > longFreeze`, making `freezeLong()` to readily revert (due to [`newUnfreezeAt < unfreezeAt`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L197)) if `freezeShort()` had been invoked not too long ago. 

Consider having `setLongFreeze()` refactored as follows:

```diff
    function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
-        require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
+        require(longFreeze_ > shortFreeze && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
        emit LongFreezeDurationSet(longFreeze, longFreeze_);
        longFreeze = longFreeze_;
    }
```
Better yet, set a reasonable threshold so that `longFreeze_ > shortFreeze + threshold` to further minimize edge cases.

## Empty Event
The following event has no parameter in it to emit anything. Although the standard global variables like block.number and block.timestamp will implicitly be tagged along, consider adding some relevant parameters to the make the best out of this emitted event for better support of off-chain logging API.

[File: IMain.sol#L154](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L154)

```solidity
    event MainInitialized();
```
[File: Main.sol#L38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L38)

```solidity
        emit MainInitialized();
```
## Useful sort function
In [`manageTokensSortedOrder()`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L95-L99) of BackingManager.sol, `erc20s` must already be in sorted ascending order before it can call [`ArrayLib.sortedAndAllUnique(erc20s)`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L21-L27).

Here is a useful and quick `sortAscending()` that may be added to Array.sol on top of the off chain method the protocol might already adopt. (Note: `IERC20[] memory arr` will have to be cast into `uint160[] memory arr` prior to calling this function.)

```solidity
    function sortAscending(
        uint160[] memory arr,
        int160 left,
        int160 right
    ) internal pure {
        int160 i = left;
        int160 j = right;
        if (i == j) return;
        uint160 pivot = arr[uint160(left + (right - left) / 2)];
        while (i <= j) {
            while (arr[uint160(i)] < pivot) i++;
            while (pivot < arr[uint256(j)]) j--;
            if (i <= j) {
                (arr[uint160(i)], arr[uint160(j)]) = (
                    arr[uint160(j)],
                    arr[uint160(i)]
                );
                i++;
                j--;
            }
        }
        if (left < j) _quickSort(arr, left, j);
        if (i < right) _quickSort(arr, i, right);
    }
```
## `allUnique()` in Array.sol could have a remove logic embedded
Consider having [`allUnique()`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L9-L17) refactored such that it will remove one of the two identical elements, e.g. replace the would be removed element with the last element and then calling arr.pop().

Alternatively, here is another useful `removePosition()` that can be invoked without affecting the order of the elements in the array:

```solidity
  function _removePosition(IERC20[] memory arr, uint8 position)
    internal
    returns (IERC20[] memory newArr)
  {
    uint256 length = arr.length;
    require(position < length);
    newArr = new arr[](length - 1);
    uint256 i;
    for (; i < position; ) {
      newArr[i] = arr[i];
      unchecked {
        ++i;
      }
    }
    for (; i < length - 1; ) {
      unchecked {
        newArr[i] = arr[i + 1];
        ++i;
      }
    }
  }
```