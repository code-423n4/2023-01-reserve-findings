# Interface Sanity Check

# Summary

When governance is involved with contract updates, extra work should be done to require that inputs pass sanity checks. In `AssetRegistryP1.sol`, both IAsset and erc20 are added or passed to other contracts without the verification that they are correctly associated. Governance protocols often time incorporate time locks, meaning if an invalid contract added it can take time to include the fix. 

# Code Snippet

https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/AssetRegistry.sol#L162-L178

# Recommendation

Consider adding requirements when adding new smart contracts.

```solidity
function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
    require(IERC165(asset).supportsInterface(type(IAsset).interfaceId), "AssetRegistryP1::_registerIgnoringCollisions: asset does not support IAsset");
        IERC20Metadata erc20 = asset.erc20();
        if (_erc20s.contains(address(erc20))) {
            if (assets[erc20] == asset) return false;
            else emit AssetUnregistered(erc20, assets[erc20]);
        } else {
            _erc20s.add(address(erc20));
        }

        assets[erc20] = asset;
        emit AssetRegistered(erc20, asset);

        // Refresh to ensure it does not revert, and to save a recent lastPrice
        asset.refresh();
        return true;
    }
```