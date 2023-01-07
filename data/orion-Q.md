The BackingManager.grantRTokenAllowance function makes unnecessary calls, the function gains the approval on the erc20 to the rToken contract to the maximum number type(uint256).max
The function makes unnecessary call before the approval gain which sets the approval to 0, after that directly it gains the approval

```
        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
```

The second call makes no effect and it just gains the gas consumption it's better to remove it