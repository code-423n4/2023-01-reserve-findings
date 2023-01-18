# Gas report

## Author: rotcivegaf

| G-N    |Issue|Instances|
|:------:|:----|:-------:|
| [G&#x2011;01] | State variables should be cached in stack variables rather than re-reading them from storage | 19 |
| [G&#x2011;02] | The result of function calls should be cached rather than re-calling the function | 5 |

## [G-01] State variables should be cached in stack variables rather than re-reading them from storage

The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

```solidity
File: protocol/contracts/p1/AssetRegistry.sol

/// @audit: `basketHandler`
 76:        uint192 quantity = basketHandler.quantity(asset.erc20());
 78:        if (quantity > 0) basketHandler.disableBasket();

/// @audit: `_erc20s`
 88:        require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");
 92:        _erc20s.remove(address(asset.erc20()));

/// @audit: `assets[asset.erc20()]`
 89:        require(assets[asset.erc20()] == asset, "asset not found");
 93:        assets[asset.erc20()] = IAsset(address(0));

/// @audit: `basketHandler`
 90:        uint192 quantity = basketHandler.quantity(asset.erc20());
 96:        if (quantity > 0) basketHandler.disableBasket();

/// @audit: `assets[erc20]`
112:        require(assets[erc20].isCollateral(), "erc20 is not collateral");
113:        return ICollateral(address(assets[erc20]));

/// @audit: `assets[erc20]`
165:            if (assets[erc20] == asset) return false;
166:            else emit AssetUnregistered(erc20, assets[erc20]);
```

```solidity
File: protocol/contracts/p1/BackingManager.sol

/// @audit: `main`
 75:        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
 76:        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);

/// @audit: `basketHandler`
111:        require(basketHandler.status() == CollateralStatus.SOUND, "basket not sound");
113:        uint48 basketTimestamp = basketHandler.timestamp();
116:        if (basketHandler.fullyCollateralized()) {

/// @audit: `rsr`
173:        if (rsr.balanceOf(address(this)) > 0) {
175:            IERC20Upgradeable(address(rsr)).safeTransfer(
177:                rsr.balanceOf(address(this))

/// @audit: `rsrTrader`
176:                address(rsrTrader),
241:            if (toRSR[i] > 0) erc20.safeTransfer(address(rsrTrader), toRSR[i]);

/// @audit: `rToken`
190:            needed = rToken.basketsNeeded(); // {BU}
194:                uint192 totalSupply = _safeWrap(rToken.totalSupply()); // {rTok}
203:                rToken.mint(address(this), uint256(rTok));
204:                rToken.setBasketsNeeded(held);

/// @audit: `basketHandler`
249:        assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
250:        rToken.setBasketsNeeded(basketHandler.basketsHeldBy(address(this)));
```

```solidity
File: protocol/contracts/p1/BasketHandler.sol

/// @audit: `other.erc20s[i]`
 79:            self.erc20s.push(other.erc20s[i]);
 80:            self.refAmts[other.erc20s[i]] = other.refAmts[other.erc20s[i]];

/// @audit: `config.erc20s[i]`
219:            delete config.targetAmts[config.erc20s[i]];
220:            delete config.targetNames[config.erc20s[i]];

/// @audit: `basket.erc20s`
280        uint256 size = basket.erc20s.length;
287            CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();

/// @audit: `basket.erc20s[i]`
338:            uint192 qty = quantity(basket.erc20s[i]);
342:                ? assetRegistry.toAsset(basket.erc20s[i]).lotPrice()
343:                : assetRegistry.toAsset(basket.erc20s[i]).price();

/// @audit: `basket.erc20s[i]`
417:            erc20s[i] = address(basket.erc20s[i]);
420:            quantities[i] = quantity(basket.erc20s[i]).mul(amount, rounding).shiftl_toUint(
421:                int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),

/// @audit: `basket.erc20s[i]`
438:            ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);
450:            uint192 q = basket.refAmts[basket.erc20s[i]].div(refPerTok, CEIL);
```

```solidity
File: protocol/contracts/p1/Deployer.sol

/// @audit: `implementations`
113:            address(new ERC1967Proxy(address(implementations.main), new bytes(0)))
118:            address(new ERC1967Proxy(address(implementations.components.rToken), new bytes(0)))
122:                address(new ERC1967Proxy(address(implementations.components.stRSR), new bytes(0)))
128:                        address(implementations.components.assetRegistry),
136:                        address(implementations.components.basketHandler),
144:                        address(implementations.components.backingManager),
151:                    new ERC1967Proxy(address(implementations.components.distributor), new bytes(0))
156:                    new ERC1967Proxy(address(implementations.components.rsrTrader), new bytes(0))
161:                    new ERC1967Proxy(address(implementations.components.rTokenTrader), new bytes(0))
165:                address(new ERC1967Proxy(address(implementations.components.furnace), new bytes(0)))
168:                address(new ERC1967Proxy(address(implementations.components.broker), new bytes(0)))
202:        components.broker.init(main, gnosis, implementations.trade, params.auctionLength);
```

## [G-02] The result of function calls should be cached rather than re-calling the function

```solidity
File: protocol/contracts/p1/AssetRegistry.sol

/// @audit: `asset.erc20()`
 74:        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");
 76:        uint192 quantity = basketHandler.quantity(asset.erc20());

/// @audit: `asset.erc20()`
 88:        require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");
 89:        require(assets[asset.erc20()] == asset, "asset not found");
 90:        uint192 quantity = basketHandler.quantity(asset.erc20());
 92:        _erc20s.remove(address(asset.erc20()));
 93:        assets[asset.erc20()] = IAsset(address(0));
 94:        emit AssetUnregistered(asset.erc20(), asset);

/// @audit: `asset.erc20()`
152:            !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,
```

```solidity
File: protocol/contracts/p1/BackingManager.sol

/// @audit: `main.rToken()`
75:        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
76:        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);

/// @audit: `rsr.balanceOf(address(this))`
173:        if (rsr.balanceOf(address(this)) > 0) {
177:                rsr.balanceOf(address(this))
```
