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