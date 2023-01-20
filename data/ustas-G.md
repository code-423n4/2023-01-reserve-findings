# Gas report
1. Use a memory variable to store a result instead of calling another contract multiple times.
```diff
diff --git a/AssetRegistry.sol.orig b/AssetRegistry.sol
index 18ef1eb1..f1a73e04 100644
--- a/AssetRegistry.sol.orig
+++ b/AssetRegistry.sol
@@ -71,9 +71,10 @@ contract AssetRegistryP1 is ComponentP1, IAssetRegistry {
     // effects: assets' = assets + {asset.erc20(): asset}
     // actions: if asset.erc20() is in basketHandler's basket then basketHandler.disableBasket()
     function swapRegistered(IAsset asset) external governance returns (bool swapped) {
-        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");
+        IERC20Metadata erc20 = asset.erc20();
+        require(_erc20s.contains(address(erc20)), "no ERC20 collision");

-        uint192 quantity = basketHandler.quantity(asset.erc20());
+        uint192 quantity = basketHandler.quantity(erc20);

         if (quantity > 0) basketHandler.disableBasket();

@@ -85,13 +86,14 @@ contract AssetRegistryP1 is ComponentP1, IAssetRegistry {
     // checks: assets[asset.erc20()] == asset
     // effects: assets' = assets - {asset.erc20():_} + {asset.erc20(), asset}
     function unregister(IAsset asset) external governance {
-        require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");
-        require(assets[asset.erc20()] == asset, "asset not found");
-        uint192 quantity = basketHandler.quantity(asset.erc20());
+        IERC20Metadata erc20 = asset.erc20();
+        require(_erc20s.contains(address(erc20)), "no asset to unregister");
+        require(assets[erc20] == asset, "asset not found");
+        uint192 quantity = basketHandler.quantity(erc20);
```
2. Use the saved peer contracts instead of calling `Main`.
```diff
diff --git a/./RToken.sol.orig b/./RToken.sol
index 2420c8d6..991aab7b 100644
--- a/./RToken.sol.orig
+++ b/./RToken.sol
@@ -377,7 +377,7 @@ contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {
     // Thin wrapper over refundSpan() and vestUpTo(); see those for correctness analysis
     function vest(address account, uint256 endId) external notPausedOrFrozen {
         // == Keepers ==
-        main.assetRegistry().refresh();
+        assetRegistry.refresh();

         // == Checks ==
         CollateralStatus status = basketHandler.status();
@@ -440,7 +440,7 @@ contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {
         require(amount > 0, "Cannot redeem zero");

         // == Refresh ==
-        main.assetRegistry().refresh();
+        assetRegistry.refresh();

         // == Checks and Effects ==
         address redeemer = _msgSender();
@@ -449,7 +449,7 @@ contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {

         // Failure to melt results in a lower redemption price, so we can allow it when paused
         // solhint-disable-next-line no-empty-blocks
-        try main.furnace().melt() {} catch {}
+        try furnace.melt() {} catch {}
```
3. 