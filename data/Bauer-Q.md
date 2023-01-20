### Missing checks for address(0) when registering an asset


In the function call register(), there is no check for address(0)
Instances(1):
AssetRegistry.sol
```
61-63

  function register(IAsset asset) external governance returns (bool) {
        return _register(asset);
   }


```


### _register is not implemented correctly

```
    function _register(IAsset asset) internal returns (bool registered) {
        require(
            !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,
            "duplicate ERC20 detected"
        );

        registered = _registerIgnoringCollisions(asset);
    }

```
if assets[asset.erc20()] == asset, the internaly function call _registerIgnoringCollisions() will return

I think is should be
```
   require(
            !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] != asset,
            "duplicate ERC20 detected"
        );
```
