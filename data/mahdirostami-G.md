## 1. instead of two for loops, use mapping
```diff
--- a/contracts/libraries/Array.sol
+++ b/contracts/libraries/Array.sol
@@ -6,12 +6,18 @@ import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
 library ArrayLib {
     /// O(n^2)
     /// @return If the array contains all unique addresses
-    function allUnique(IERC20[] memory arr) internal pure returns (bool) {
+    function allUnique(IERC20[] memory arr) internal returns (bool) {
         uint256 arrLen = arr.length;
-        for (uint256 i = 1; i < arrLen; ++i) {
-            for (uint256 j = 0; j < i; ++j) {
-                if (arr[i] == arr[j]) return false;
-            }
+        mapping(uint160 => uint256) private exists;
+        for (uint256 i = 0; i < arrLen; ++i) {
+            if (exists[uint160(address(arr[i]))] == 1) return false;
+            exists[uint160(address(arr[i]))] = 1;
+            }   
         }
         return true;
```
affected code:

[Array.sol#L9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L9)

## 2. State variables can be packed into fewer storage slots

```diff
--- a/contracts/libraries/Fixed.sol
+++ b/contracts/libraries/Fixed.sol
@@ -34,20 +34,23 @@ bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOu

 // Used by P1 implementation for easier casting
 uint256 constant FIX_ONE_256 = 1e18;
-uint8 constant FIX_DECIMALS = 18;

 // If a particular uint192 is represented by the uint192 n, then the uint192 represents the
 // value n/FIX_SCALE.
 uint64 constant FIX_SCALE = 1e18;
+// State variables can be packed into fewer storage slots
+uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

+uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
+uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.

+uint8 constant FIX_DECIMALS = 18;
 // FIX_SCALE Squared:

 // Used by P1 implementation for easier casting
 uint256 constant FIX_ONE_256 = 1e18;
-uint8 constant FIX_DECIMALS = 18;

 // If a particular uint192 is represented by the uint192 n, then the uint192 represents the
 // value n/FIX_SCALE.
 uint64 constant FIX_SCALE = 1e18;
+// State variables can be packed into fewer storage slots
+uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

+uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
+uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.

+uint8 constant FIX_DECIMALS = 18;
 // FIX_SCALE Squared:
 uint128 constant FIX_SCALE_SQ = 1e36;

 // The largest integer that can be converted to uint192 .
 // This is a bit bigger than 3.1e39
-uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

-uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.
 uint192 constant FIX_ONE = FIX_SCALE; // The uint192 representation of one.
 uint192 constant FIX_MAX = type(uint192).max; // The largest uint192. (Not an integer!)
 uint192 constant FIX_MIN = 0; // The smallest uint192.
```

affected code:

[Fixed.sol#L36](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L36)
[Fixed.sol#L310](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L310)

```diff

     uint192 public constant MIN_TRADE_VOLUME = 1e29; // {UoA}
     uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}
+    // === Governance param ===
+    uint192 public maxTradeSlippage; // {%}

+    uint192 public minTradeVolume; // {UoA}

     // Peer contracts, immutable after init()
     IBroker private broker;
@@ -27,11 +31,6 @@ abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeabl
     mapping(IERC20 => ITrade) public trades;
     uint48 public tradesOpen;

-    // === Governance param ===
-    uint192 public maxTradeSlippage; // {%}

-    uint192 public minTradeVolume; // {UoA}
```

affected code:

[Trading.sol#L20-L33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L20-L33)

## 3.One if instead of two if
```diff
@@ -161,11 +164,7 @@ function _divrnd(
     if (rounding == FLOOR) return result;

     if (rounding == ROUND) {
-        if (numerator % divisor > (divisor - 1) / 2) {
-            result++;
-        }
-    } else {
-        if (numerator % divisor > 0) {
+        if ((numerator % divisor > (divisor - 1) / 2) || (numerator % divisor > 0)) {
             result++;
         }
     }
```

affected code:

[Fixed.sol#L163](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L163)

## 4.<x> += <y> costs more gas than <x> = <x> + <y> for state variables

```diff
@@ -388,9 +389,8 @@ library FixLib {
         if (x == 0) return 0; // always computable, no matter what decimals is
         if (decimals <= -42) return (rounding == CEIL ? 1 : 0);
         if (96 <= decimals) revert UIntOutOfBounds();
-        decimals -= 18; // shift so that toUint happens at the same time.
+        decimals = decimals - 18; // shift so that toUint happens at the same time.
```
affected code:

[Fixed.sol#L392](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L392)
[Fixed.sol#L505](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L505)
[Auth.sol#L136](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L136)
[RecollateralizationLib.sol#L264](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L264)
[RecollateralizationLib.sol#L271](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L271)

## 5. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loop

affected code:

[Array.sol#L11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11)
[Array.sol#L23](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23)
[String.sol#L14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14)
[RecollateralizationLib.sol#L329](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L329)
[RecollateralizationLib.sol#L437](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437)
