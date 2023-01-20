1. In `Auth.__Auth_init` there is no need to invoke `__AccessControl_init` as the function is a no-op.
2. In `Auth.unpause` `delete` should be used instead of setting the boolean to false.
    ```solidity
    function unpause() external onlyRole(PAUSER) {
        emit PausedSet(paused, false);
        paused = false;
    }
    ```
3. In `AssetRegistryP1.unregister` `delete` should be used instead of setting the mapping to 0.
    ```solidity
    assets[asset.erc20()] = IAsset(address(0));
    ```
4. In `BasketLibP1.empty` `delete` should be used instead of setting the mapping to 0.
    ```solidity
    for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
    ```
5. The `RToken.issue(uint256)` function should be removed as it itself calls the `issue(address, uint256)` function. The signature of `issue(uint256)` function is `cc872b66` which comes before the signatures of many other functions in the method lookup table like `redeem`, `scalingRedemptionRate` & `requireNotPausedOrFrozen`. By removing the `issue(uint256)` function the execution cost of other mentioned functions can be reduced.
6. In `BackingManagerP1.grantRTokenAllowance` cached value stored in contracts storage should be used for `rToken`.
    ```solidity
    IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
    IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
    ```
7. In `BackingManagerP1.handoutExcessAssets` `rsr.balanceOf` result should be cached
    ```solidity
    if (rsr.balanceOf(address(this)) > 0) {
        IERC20Upgradeable(address(rsr)).safeTransfer(
            address(rsrTrader),
            rsr.balanceOf(address(this))
        );
    }
    ```
