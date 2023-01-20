1. Protocol will break for double entrypoint address tokens. Many ERC20 tokens on Ethereum has double entry points. In those cases the P1 contracts which use `ArrayLib.allUnique()` function which will give false negative result.
    ```solidity
    require(ArrayLib.allUnique(erc20s), "duplicate tokens");
    ```

2. ComponentRegistry.sol contains unnecessary imports
     ```solidity
    import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
    import "../interfaces/IMain.sol";
    ```
    
3. In `AssetRegistryP1` contract the `swapRegistered()` function calls `disableBasket` before updating contract state while `unregister()` function calls `disableBasket` after updating contract state. A consistent flow should be followed in both the functions.
     ```solidity
    function swapRegistered(IAsset asset) external governance returns (bool swapped) {
        // ...
        uint192 quantity = basketHandler.quantity(asset.erc20());
        if (quantity > 0) basketHandler.disableBasket();
        swapped = _registerIgnoringCollisions(asset);
    }

    function unregister(IAsset asset) external governance {
        // ...
        uint192 quantity = basketHandler.quantity(asset.erc20());
        _erc20s.remove(address(asset.erc20()));
        assets[asset.erc20()] = IAsset(address(0));
        emit AssetUnregistered(asset.erc20(), asset);
        if (quantity > 0) basketHandler.disableBasket();
    }
    ```
