## 1. State variables can be packed into fewer storage slots

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

 // The largest integer that can be converted to uint192.
 // This is a bit bigger than 3.1e39
-uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

-uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.
 uint192 constant FIX_ONE = FIX_SCALE; // The uint192 representation of one.
 uint192 constant FIX_MAX = type(uint192).max; // The largest uint192. (Not an integer!)
 uint192 constant FIX_MIN = 0; // The smallest uint192.


@@ -310
-uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;  
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

## 2.One if instead of two if
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

## 3.<x> += <y> costs more gas than <x> = <x> + <y> for state variables

```diff
@@ -133,7 +133,10 @@ abstract contract Auth is AccessControlUpgradeable, IAuth {
     // - longFreezes'[caller] = longFreezes[caller] - 1
     // - if longFreezes'[caller] == 0 then caller loses the LONG_FREEZER role
     function freezeLong() external onlyRole(LONG_FREEZER) {
-        longFreezes[_msgSender()] -= 1; // reverts on underflow
+        longFreezes[_msgSender()] = longFreezes[_msgSender()] - 1; // reverts on underflow
```
affected code:

[Auth.sol#L136](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L136)


## 4. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loop

affected code:

[Array.sol#L11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11)
[Array.sol#L23](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23)
[String.sol#L14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14)
[RecollateralizationLib.sol#L329](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L329)
[RecollateralizationLib.sol#L437](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437)
