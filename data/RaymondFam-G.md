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