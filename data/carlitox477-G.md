# Main.sol: rsr should be immutable
The RSR is the governance token of Reserve protocol, it is not expected to be changed at any time. It should be set in the constructor not in ```init``` function, and it should be immutable.

Change [Line 19](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L19) for ```IERC20 immutable internal rsr;``` and modify constructor to be
```solidity
constructor(IERC20 _rsr) initializer {
    require(address(rsr_) != address(0), ERROR_ADDRESS_ZERO());
    rsr = _rsr;
}
```

Then remoce rsr_ logic from init function:
```diff

function init(
    Components memory components,
-   IERC20 rsr_,
    uint48 shortFreeze_,
    uint48 longFreeze_
) public virtual initializer {
-   require(address(rsr_) != address(0), "invalid RSR address");
    __Auth_init(shortFreeze_, longFreeze_);
    __ComponentRegistry_init(components);
    __UUPSUpgradeable_init();

-   rsr = rsr_;
    emit MainInitialized();
    }
```

# ```Auth.setShortFreeze``` and ```Auth.setLongFreeze``` can split require to save gas
Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 3 GAS per &&:

```diff
// Auth.setShortFreeze
-   require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
+   require(shortFreeze_ > 0, ERROR_SHORT_FREEZE_OUT_OF_RANGE());
+   require(shortFreeze_ <= MAX_SHORT_FREEZE, ERROR_SHORT_FREEZE_OUT_OF_RANGE())

// Auth.setLongFreeze
-   require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
+   require(longFreeze_ > 0, ERROR_LONG_FREEZE_OUT_OF_RANGE());
+   require(longFreeze_ <= MAX_LONG_FREEZE, ERROR_LONG_FREEZE_OUT_OF_RANGE());
```

Total gas saving per function call: $3 \times 2 = 6$

[Source](https://yos.io/2021/05/17/gas-efficient-solidity/#tip-11-splitting-require-statements-that-use--saves-gas)

# ```Auth.freezeUntil``` should cache unfreezeAt
One access can be avoided by changing the current function to:
```solidity
function freezeUntil(uint48 newUnfreezeAt) private {
    uint48 _unfreezeAt = unfreezeAt;
    require(newUnfreezeAt > _unfreezeAt, "frozen");
    emit UnfreezeAtSet(_unfreezeAt, newUnfreezeAt);
    unfreezeAt = newUnfreezeAt;
}
```

# ```ArrayLib.allUnique``` and ```ArrayLib.sortedAndAllUnique``` can use unchecked blocks in iterations to save gas
This would avoid unnecesary overflow checks

```diff
    // ArrayLib.allUnique
-   for (uint256 i = 1; i < arrLen; ++i) {
+   for (uint256 i = 1; i < arrLen; unchecked{++i}) {
-       for (uint256 j = 0; j < i; ++j) {
+       for (uint256 j = 0; j < i; unchecked{++j}) {

// ArrayLib.sortedAndAllUnique
-   for (uint256 i = 1; i < arrLen; ++i) {
+   for (uint256 i = 1; i < arrLen; unchecked{++i}) {
```

# ```ArrayLib.allUnique``` can be refactor to reduce it complexity to O(n) in order to reduce gas consumption
This can be easily done by using a mapping to check if the address has already been analized:
```solidity
function allUnique(IERC20[] memory arr) internal pure returns (bool) {
    uint256 arrLen = arr.length;
    for (uint256 i = 0; i < arrLen; uncheckef{++i}) {
        if(checker[arr[i]] == 1){
            return false;
        } else{
            checker[arr[i]] = 1
        }
    }
    return true;
}
```


# Change ```RedemptionBatteryLib.currentCharge(Battery storage, uint256)``` for ```RedemptionBatteryLib.currentCharge(Battery memory, uint256)```
This function does not write any of the batery values. Therefore, it should be change to ```Battery memory battery``` in function parameters in order to save gas

# Change ```RedemptionBatteryLib.currentCharge``` can save gas by caching ```battery.redemptionRateFloor```
```diff
// In RedemptionBatteryLib.currentCharge
-   if (battery.redemptionRateFloor > amtPerHour) amtPerHour = battery.redemptionRateFloor;
+   uint256 _redemptionRateFloor = battery.redemptionRateFloor;
+   if (_redemptionRateFloor> amtPerHour) amtPerHour = _redemptionRateFloor;
```

# Use of assert instead of require is discoraged giving it consumes all remaining gas
All ```assert``` in the code should be change for ```require``` giving it consumes all remaing gas

[More about it here](https://stackoverflow.com/questions/48847407/assert-vs-require-within-solidity)

```solidity
// p1/mixins/RewardableLib.sol
78:     assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);

// p1/mixins/TradeLib.sol
44:     assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
108:    assert( 

// plugins/trading/GnosisTrade.sol
63:     assert(status == TradeStatus.PENDING);
98:     assert(origin_ != address(0));

// plugins/assets/Asset.sol
98:     assert(low == 0);
112:    assert(low <= high);

// p1/mixins/Trading.sol
114:    assert(address(trades[sell]) == address(0));

// plugins/assets/RTokenAsset.sol
74:     assert(low <= high);

// plugins/assets/FiatCollateral.sol
137:    assert(low == 0);

// p1/BackingManager.sol
249:    assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());

// plugins/aave/StaticATokenLM.sol
345:    assert(amt == amountToWithdraw);

// p1/mixins/RecollateralizationLib.sol
110:    assert(doTrade);

// p1/BasketHandler.sol
556:    assert(targetIndex < targetsLength);

// p1/StRSR.sol
696:    assert(totalStakes + amount < type(uint224).max);
```

# ```AssetRegistry._registerIgnoringCollisions(IAsset)``` can cache ```assets[erc20]``` to save gas
```diff
function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {
    IERC20Metadata erc20 = asset.erc20();
    if (_erc20s.contains(address(erc20))) {
+       IAsset _asset = assets[erc20]
-       if (assets[erc20] == asset) return false;
+       if (_asset== asset) return false;
-       else emit AssetUnregistered(erc20, assets[erc20]);
        else emit AssetUnregistered(erc20, _asset);
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

# ```AssetRegistry.getRegistry()``` and ```AssetRegistry.erc20s()``` can use unchecked block to save gas
```diff
// AssetRegistry.getRegistry
-   for (uint256 i = 0; i < length; ++i) {
+   for (uint256 i = 0; i < length; unchecked{ ++i}) {

// AssetRegistry.erc20s
-   for (uint256 i = 0; i < length; ++i) {
+   for (uint256 i = 0; i < length; unchecked{ ++i } ) {
```

# ```AssetRegistry.getRegistry()``` can use storage pointers to save gas
```diff
function getRegistry() external view returns (Registry memory reg) {
+   EnumerableSet.AddressSet storage __erc20s = _erc20s; 
+   IAsset[] storage _assets = assets;
-   uint256 length = _erc20s.length();
+   uint256 length = __erc20s.length();
    reg.erc20s = new IERC20[](length);
    reg.assets = new IAsset[](length);
    for (uint256 i = 0; i < length; ++i) {
-       reg.erc20s[i] = IERC20(_erc20s.at(i));
+       reg.erc20s[i] = IERC20(__erc20s.at(i));
-       reg.assets[i] = assets[IERC20(_erc20s.at(i))];
+       reg.assets[i] = _assets[IERC20(_erc20s.at(i))];
    }
}
```

# ```AssetRegistry.erc20s()``` can use storage pointer to save gas
```diff
function erc20s() external view returns (IERC20[] memory erc20s_) {
+   EnumerableSet.AddressSet storage __erc20s = _erc20s; 
-   uint256 length = _erc20s.length();
+   uint256 length = __erc20s.length();
    erc20s_ = new IERC20[](length);
    for (uint256 i = 0; i < length; ++i) {
        erc20s_[i] = IERC20(__erc20s.at(i));
    }
}
```

# ```AssetRegistry.toColl()``` can cache ```assets[erc20]``` to save gas
```diff
function toColl(IERC20 erc20) external view returns (ICollateral) {
    require(_erc20s.contains(address(erc20)), "erc20 unregistered");
+   IAsset _asset = assets[erc20];
-   require(assets[erc20].isCollateral(), "erc20 is not collateral");
+   require(_asset.isCollateral(), ERROR_NO_COLLATERAL());
-   return ICollateral(address(assets[erc20]));
+   return ICollateral(address(_asset));
}
```

# ```AssetRegistry.unregister(IAsset)``` can cache ```basketHandler``` to save gas
```diff
function unregister(IAsset asset) external governance {
    require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");
    require(assets[asset.erc20()] == asset, "asset not found");
+   IBasketHandler _basketHandler = basketHandler;
-   uint192 quantity = basketHandler.quantity(asset.erc20());
+   uint192 quantity = _basketHandler.quantity(asset.erc20());

    _erc20s.remove(address(asset.erc20()));
    assets[asset.erc20()] = IAsset(address(0));
    emit AssetUnregistered(asset.erc20(), asset);

-   if (quantity > 0) basketHandler.disableBasket();
+   if (quantity > 0) _basketHandler.disableBasket();
}
```

# ```AssetRegistry.refresh()``` can use storage pointers for ```_erc20s``` and ```assets``` to save gas, as well as unchecked block for ```++i```
This means that the current function should be refactor to:
```solidity
function refresh() public {
    // It's a waste of gas to require notPausedOrFrozen because assets can be updated directly
    EnumerableSet.AddressSet storage __erc20s = _erc20s;
    mapping(IERC20 => IAsset) storage _assets = assets;

    uint256 length = __erc20s.length();
    for (uint256 i ; i < length; unchecked {++i}) {
        _assets[IERC20(__erc20s.at(i))].refresh();
    }
}
```

# ```AssetRegistry.init``` can use unchecked block to save gas
// AssetRegistry.init(IMain, IAsset[] calldata)
```diff
-   for (uint256 i = 0; i < length; ++i) {
+   for (uint256 i = 0; i < length; unchecked {++i}) {
        _register(assets_[i]);
    }
```

# ```BackingManager.grantRTokenAllowance(IERC20)``` can cache ```main.rToken()``` to save gas
```diff
    function grantRTokenAllowance(IERC20 erc20) external notPausedOrFrozen {
        require(assetRegistry.isRegistered(erc20), "erc20 unregistered");
        // == Interaction ==
-       IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
-       IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
+       address rTokenAddress = address(main.rToken());
+       IERC20Upgradeable(address(erc20)).safeApprove(address(rTokenAddress), 0);
+       IERC20Upgradeable(address(erc20)).safeApprove(address(rTokenAddress), type(uint256).max);
    }
```

# ```BackingManager._manageTokens(IERC20[] calldata)``` can cache ```basketHandler``` to save gas
This would mean 2 less storage access
```diff
// In BackingManager._manageTokens(IERC20[] calldata)
+   IBasketHandler _basketHandler = basketHandler;
-   require(basketHandler.status() == CollateralStatus.SOUND, "basket not sound");
+   require(_basketHandler.status() == CollateralStatus.SOUND, "basket not sound");


-   uint48 basketTimestamp = basketHandler.timestamp();
+   uint48 basketTimestamp = _basketHandler.timestamp();
    if (block.timestamp < basketTimestamp + tradingDelay) return;

-   if (basketHandler.fullyCollateralized()) {
+   if (_basketHandler.fullyCollateralized()) {
        // == Interaction (then return) ==
        handoutExcessAssets(erc20s);
```

# ```BackingManager.handoutExcessAssets(IERC20[] calldata)``` cache ```rsr```, ```rToken``` and ```address(rTokenTrader)``` to avoid redundant storage access.
```diff
// In BackingManager.handoutExcessAssets(IERC20[] calldata)
+   IERC20 _rsr = rsr;
+   uint256 _rsrBalance = _rsr.balanceOf(address(this));
+   address _rsrTraderAddress = address(rsrTrader)
-   if (rsr.balanceOf(address(this)) > 0) {
+   if (_rsrBalance) > 0) {
        // For CEI, this is an interaction "within our system" even though RSR is already live
-       IERC20Upgradeable(address(rsr)).safeTransfer(
-           address(rsrTrader),
-           rsr.balanceOf(address(this))
-       );
+       IERC20Upgradeable(address(_rsr)).safeTransfer(
+           _rsrTraderAddress,
+           _rsrBalance
+       );
    // More lines
+   address _rTokenTraderAddress = address(rTokenTrader);
    for (uint256 i = 0; i < length; ++i) {
        IERC20Upgradeable erc20 = IERC20Upgradeable(address(erc20s[i]));
-       if (toRToken[i] > 0) erc20.safeTransfer(address(rTokenTrader), toRToken[i]);
-       if (toRSR[i] > 0) erc20.safeTransfer(address(rsrTrader), toRSR[i]);
+       if (toRToken[i] > 0) erc20.safeTransfer(_rTokenTraderAddress, toRToken[i]);
+       if (toRSR[i] > 0) erc20.safeTransfer(_rsrTraderAddress, toRSR[i]);
    }
}
```

# ```BackingManager.handoutExcessAssets(IERC20[] calldata)``` use storage pointer, avoid repeating same match operation and use unchecked block to save gas in for loop
```diff
    RevenueTotals memory totals = distributor.totals();
    uint256[] memory toRSR = new uint256[](length);
    uint256[] memory toRToken = new uint256[](length);
+   uint256 totalsSum = totals.rTokenTotal + totals.rsrTotal
+   IAssetRegistry storage _assetRegistry = assetRegistry;
+   IBasketHandler storage _basketHandler = basketHandler;
-   for (uint256 i = 0; i < length; ++i) {
+   for (uint256 i; i < length; unchecked{ ++i }) {
-       IAsset asset = assetRegistry.toAsset(erc20s[i]);
        IAsset asset = _assetRegistry.toAsset(erc20s[i]);

-       uint192 req = needed.mul(basketHandler.quantity(erc20s[i]), CEIL);
+       uint192 req = needed.mul(_basketHandler.quantity(erc20s[i]), CEIL);
        if (asset.bal(address(this)).gt(req)) {
            // delta: {qTok}, the excess quantity of this asset that we hold
            uint256 delta = asset.bal(address(this)).minus(req).shiftl_toUint(
                int8(IERC20Metadata(address(erc20s[i])).decimals())
            );
            // no div-by-0: Distributor guarantees (totals.rTokenTotal + totals.rsrTotal) > 0
            // initial division is intentional here! We'd rather save the dust than be unfair
-           toRSR[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rsrTotal;
-           toRToken[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rTokenTotal;
+           uint256 firstFactor = (delta / totalsSum);
+           toRSR[i] = firstFactor * totals.rsrTotal;
+           toRToken[i] = firstFactor * totals.rTokenTotal;
        }
    }
```



# ```BasketHandler.status``` can use a storage pointer for ```basket``` and ```assetRegistry``` and unchecked block to save gas
```diff
    function status() public view returns (CollateralStatus status_) {
-       uint256 size = basket.erc20s.length;
+       IERC20[] storage _erc20s = basket.erc20s;
+       uint256 size = _erc20s.length;

        // untestable:
        //      disabled is only set in _switchBasket, and only if size > 0.
        if (disabled || size == 0) return CollateralStatus.DISABLED;

+       IAssetRegistry storage _assetRegistry = assetRegistry;
-       for (uint256 i = 0; i < size; ++i) {
-           CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();
+       for (uint256 i; i < size; unchecked{ ++i} ) {
+           CollateralStatus s = _assetRegistry.toColl(_erc20s[i]).status();
            if (s.worseThan(status_)) status_ = s;
        }
    }
```


# ```BasketHandler._switchBasket``` can refactor while loop in order to save gas
Two impovement can be done:
1. Cache ```_targetNames.length()``` and change the while loop for a for loop
1. Use storage pointer to reduce gas consumption.
The final code would be:
```diff
-   while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
+   EnumerableSet.Bytes32Set storage __targetNames = _targetNames
+   uint256 targetNamesLen = __targetNames.length()
+   for(uint i; i < targetNamesLen; unchecked{++i}){
+       __targetNames.remove(__targetNames.at(0))
+   }
```

[Line to replace](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L523)

# ```BasketHandler._switchBasket``` can use a storage pointer for ```_targetNames```,  ```config```, ```_newBasket``` and ```assetRegistry```, and unchecked block to save gas in for loops
```diff
// First for loop
+   BasketConfig storage _config = config;
+   EnumerableSet.Bytes32Set storage __targetNames = _targetNames
-   for (uint256 i = 0; i < basketLength; ++i) {
-       _targetNames.add(config.targetNames[config.erc20s[i]]);
+   for (uint256 i; i < basketLength; unchecked{++i}) {
+       __targetNames.add(_config.targetNames[config.erc20s[i]]);
    }


    //...
    // Second for llop
+   IAssetRegistry _assetRegistry = assetRegistry;
+   Basket storage __newBasket = _newBasket;
-   for (uint256 i = 0; i < basketLength; ++i) {
-       IERC20 erc20 = config.erc20s[i];
+   for (uint256 i = 0; i < basketLength; unchecked{++i}) {
+       IERC20 erc20 = _config.erc20s[i];

        // Find collateral's targetName index
        uint256 targetIndex;
-       for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {
-           if (_targetNames.at(targetIndex) == config.targetNames[erc20]) break;
+       for (targetIndex; targetIndex < targetsLength; unchecked {++targetIndex}) {
+           if (__targetNames.at(targetIndex) == _config.targetNames[erc20]) break;
        }
        assert(targetIndex < targetsLength);
        // now, _targetNames[targetIndex] == config.targetNames[config.erc20s[i]]

        // Set basket weights for good, prime collateral,
        // and accumulate the values of goodWeights and targetWeights
-       uint192 targetWeight = config.targetAmts[erc20];
+       uint192 targetWeight = _config.targetAmts[erc20];
        totalWeights[targetIndex] = totalWeights[targetIndex].plus(targetWeight);

-       if (goodCollateral(config.targetNames[erc20], erc20) && targetWeight.gt(FIX_ZERO)) {
+       if (goodCollateral(_config.targetNames[erc20], erc20) && targetWeight.gt(FIX_ZERO)) {
            goodWeights[targetIndex] = goodWeights[targetIndex].plus(targetWeight);
-           _newBasket.add(
+           __newBasket.add(
                erc20,
-               targetWeight.div(assetRegistry.toColl(erc20).targetPerRef(), CEIL)
+               targetWeight.div(_assetRegistry.toColl(erc20).targetPerRef(), CEIL)
            );
            // this div is safe: targetPerRef() > 0: goodCollateral check
        }

// 3° for loop
-   for (uint256 i = 0; i < targetsLength; ++i) {
+   for (uint256 i; i < targetsLength; unchecked{++i}) {
        if (totalWeights[i].lte(goodWeights[i])) continue; // Don't need any backup weight

        // "tgt" = _targetNames[i]
        // Now, unsoundPrimeWt(tgt) > 0

        uint256 size = 0; // backup basket size
-       BackupConfig storage backup = config.backups[_targetNames.at(i)];
+       BackupConfig storage backup = _config.backups[__targetNames.at(i)];

        // Find the backup basket size: min(backup.max, # of good backup collateral)
        uint256 backupLength = backup.erc20s.length;
+       // @audit Cache backup.max
+       uint256 backupMax = backup.max;
-       for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
-           if (goodCollateral(_targetNames.at(i), backup.erc20s[j])) size++;
-       }
+       for (uint256 j; j < backupLength && size < backupMax; unchecked { ++j} ) {
+           if (goodCollateral(__targetNames.at(i), backup.erc20s[j])) ++size;
+       }

        // Now, size = len(backups(tgt)). Do the disable check:
        // Remove bad collateral and mark basket disabled. Pause most protocol functions
        if (size == 0) disabled = true;

        // Set backup basket weights...
        uint256 assigned = 0;
        // needed = unsoundPrimeWt(tgt)
        uint192 needed = totalWeights[i].minus(goodWeights[i]);

        // Loop: for erc20 in backups(tgt)...
-       for (uint256 j = 0; j < backupLength && assigned < size; ++j) {
+       for (uint256 j; j < backupLength && assigned < size; unchecked{++j}) {
            IERC20 erc20 = backup.erc20s[j];
-           if (goodCollateral(_targetNames.at(i), erc20)) {
+           if (goodCollateral(__targetNames.at(i), erc20)) {
                // Across this .add(), targetWeight(_newBasket',erc20)
                // = targetWeight(_newBasket,erc20) + unsoundPrimeWt(tgt) / len(backups(tgt))
-               _newBasket.add(
+               __newBasket.add(
                    erc20,
-                   needed.div(assetRegistry.toColl(erc20).targetPerRef().mulu(size), CEIL)
+                   needed.div(_assetRegistry.toColl(erc20).targetPerRef().mulu(size), CEIL)
                    // this div is safe: targetPerRef > 0: goodCollateral check
                );
                assigned++;
            }
        }
        // Here, targetWeight(_newBasket, e) = primeWt(e) + backupWt(e) for all e targeting tgt
    }

    // More lines
    // 4° for loop
+   Basket storage _basket = basket;
-   for (uint256 i = 0; i < basketLength; ++i) {
-       refAmts[i] = basket.refAmts[basket.erc20s[i]];
+   for (uint256 i; i < basketLength; unchecked{ ++i }) {
+       refAmts[i] = _basket.refAmts[_basket.erc20s[i]];
    }
```

# ```BasketHandler.requireValidCollArray(IERC20[] calldata)``` can cache ```address(rToken)``` and ```address(stRSR)```
```diff
+   IERC20 _rsr = rsr;
+   IERC20 _rToken = IERC20(address(rToken));
+   IERC20 _stRSR = IERC20(address(stRSR));
    for (uint256 i = 0; i < erc20s.length; i++) {
-       require(erc20s[i] != rsr, "RSR is not valid collateral");
-       require(erc20s[i] != IERC20(address(rToken)), "RToken is not valid collateral");
-       require(erc20s[i] != IERC20(address(stRSR)), "stRSR is not valid collateral");
+       require(erc20s[i] != _rsr, "RSR is not valid collateral");
+       require(erc20s[i] != _rToken, "RToken is not valid collateral");
+       require(erc20s[i] != _stRSR, "stRSR is not valid collateral");
+       require(erc20s[i] != zero, "address zero is not valid collateral");
    }
```





# ```BasketHandler.status``` should check if ```s = CollateralStatus.DISABLED``` to break sooner from for loop
If at least one collateral status is desable, it is impossible to find a status worst than this. Then ```if (s.worseThan(status_)) status_ = s;``` should be replaced for
```solidity
if (s.worseThan(status_)){
    status_ = s;
    if( status_ == CollateralStatus.DISABLED) return status_;
} 
```
In order to avoid meaningless iterations.


# ```Basket.refreshBasket()```  can cache ```main``` to save gas
This would reduce one access
```diff
    // In Basket.refreshBasket()
+   IMain _main = main;
    require(
-       main.hasRole(OWNER, _msgSender()) ||
+       _main.hasRole(OWNER, _msgSender()) ||
-           (status() == CollateralStatus.DISABLED && !main.pausedOrFrozen()),
+           (status() == CollateralStatus.DISABLED && !_main.pausedOrFrozen()),
        "basket unrefreshable"
    );
```

# ```Trading.tryTrade(TradeRequest memory)``` can cache ```broker``` to save gas
This would save 2 storage access:
```diff
// In Trading.tryTrade(TradeRequest memory)
+   IBroker _broker = broker;
-   IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);
+   IERC20Upgradeable(address(sell)).safeApprove(address(_broker), 0);
-   IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);
+   IERC20Upgradeable(address(sell)).safeApprove(address(_broker), req.sellAmount);
-   ITrade trade = broker.openTrade(req);
+   ITrade trade = _broker.openTrade(req);
```

# ```GnosisTrade.settle``` can cache state variables ```origin```, ```sell```, ```buy``` and ```initBal```
This would avoid X storage access

```diff

    function settle()
        external
        stateTransition(TradeStatus.OPEN, TradeStatus.CLOSED)
        returns (uint256 soldAmt, uint256 boughtAmt)
    {
+       address _origin = origin;
-       require(msg.sender == origin, "only origin can settle");
+       require(msg.sender == _origin, "only origin can settle");

        // Optionally process settlement of the auction in Gnosis
        if (!isAuctionCleared()) {
            // By design, we don't rely on this return value at all, just the
            // "cleared" state of the auction, and the token balances this contract owns.
            // slither-disable-next-line unused-return

            gnosis.settleAuction(auctionId);
            assert(isAuctionCleared());
        }

        // At this point we know the auction has cleared

        // Transfer balances to origin
+       IERC20Metadata _sell = sell;
+       IERC20Metadata _buy = buy;
-       uint256 sellBal = sell.balanceOf(address(this));
+       uint256 sellBal = _sell.balanceOf(address(this));
-       boughtAmt = buy.balanceOf(address(this));
+       boughtAmt = _buy.balanceOf(address(this));

-       if (sellBal > 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);
+       if (sellBal > 0) IERC20Upgradeable(address(_sell)).safeTransfer(_origin, sellBal);

-       if (boughtAmt > 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);
+       if (boughtAmt > 0) IERC20Upgradeable(address(_buy)).safeTransfer(_origin, boughtAmt);

        // Check clearing prices
+       uint256 _initBal = initBal;
-       if (sellBal < initBal) {
+       if (sellBal < _initBal) {
-           soldAmt = initBal - sellBal;
+           soldAmt = _initBal - sellBal;

            // Gnosis rounds defensively, so it's possible to get 1 fewer attoTokens returned
            uint256 adjustedSoldAmt = Math.max(soldAmt - 1, 1);

            // {buyTok/sellTok}
-           uint192 clearingPrice = shiftl_toFix(boughtAmt, -int8(buy.decimals())).div(
+           uint192 clearingPrice = shiftl_toFix(boughtAmt, -int8(_buy.decimals())).div(
-               shiftl_toFix(adjustedSoldAmt, -int8(sell.decimals()))
+               shiftl_toFix(adjustedSoldAmt, -int8(_sell.decimals()))
            );

            if (clearingPrice.lt(worstCasePrice)) {
                broker.reportViolation();
            }
        }
    }
```


# ```GnosisTrade.init``` can reduce state variable access
```diff
    function init(
        IBroker broker_,
        address origin_,
        IGnosis gnosis_,
        uint48 auctionLength,
        TradeRequest calldata req
    ) external stateTransition(TradeStatus.NOT_STARTED, TradeStatus.OPEN) {
        require(req.sellAmount <= type(uint96).max, "sellAmount too large");
        require(req.minBuyAmount <= type(uint96).max, "minBuyAmount too large");



-       sell = req.sell.erc20();
-       buy = req.buy.erc20();
-       initBal = sell.balanceOf(address(this));
+       IERC20Metadata _sell = req.sell.erc20();
+       IERC20Metadata _buy = req.buy.erc20();
+       uint256 _initBal = sell.balanceOf(address(this));
+       sell = _sell;
+       buy = _buy;
+       initBal = _initBal;

-       require(initBal <= type(uint96).max, "initBal too large");
-       require(initBal >= req.sellAmount, "unfunded trade");
+       require(_initBal <= type(uint96).max, "initBal too large");
+       require(_initBal >= req.sellAmount, "unfunded trade");

        assert(origin_ != address(0));

        broker = broker_;
        origin = origin_;
        gnosis = gnosis_;
+       uint48 _endTime = uint48(block.timestamp) + auctionLength;
-       endTime = uint48(block.timestamp) + auctionLength;
+       endTime = _endTime;


        // {buyTok/sellTok}
-       worstCasePrice = shiftl_toFix(req.minBuyAmount, -int8(buy.decimals())).div(
-           shiftl_toFix(req.sellAmount, -int8(sell.decimals()))
-       );
+       worstCasePrice = shiftl_toFix(req.minBuyAmount, -int8(_buy.decimals())).div(
+           shiftl_toFix(req.sellAmount, -int8(_sell.decimals()))
+       );

        // Downsize our sell amount to adjust for fee
        // {qTok} = {qTok} * {1} / {1}
        uint96 sellAmount = uint96(
            _divrnd(
                req.sellAmount * FEE_DENOMINATOR,
-               FEE_DENOMINATOR + gnosis.feeNumerator(),
+               FEE_DENOMINATOR + gnosis_.feeNumerator(),
                FLOOR
            )
        );

        // Don't decrease minBuyAmount even if fees are in effect. The fee is part of the slippage
        uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd

        uint256 minBuyAmtPerOrder = Math.max(
            minBuyAmount / MAX_ORDERS,
-           DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))
+           DEFAULT_MIN_BID.shiftl_toUint(int8(_buy.decimals()))
        );

        // Gnosis EasyAuction requires minBuyAmtPerOrder > 0
        // untestable:
        //      Value will always be at least 1. Handled previously in the calling contracts.
        if (minBuyAmtPerOrder == 0) minBuyAmtPerOrder = 1;

        // == Interactions ==

        // Set allowance (two safeApprove calls to support USDT)
-       IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), 0);
-       IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), initBal);
+       IERC20Upgradeable(address(_sell)).safeApprove(address(gnosis_), 0);
+       IERC20Upgradeable(address(_sell)).safeApprove(address(gnosis_), _initBal);

-       auctionId = gnosis.initiateAuction(
-           sell,
-           buy,
-           endTime,
-           endTime,
+       auctionId = gnosis_.initiateAuction(
+           _sell,
+           _buy,
+           _endTime,
+           _endTime,
            sellAmount,
            minBuyAmount,
            minBuyAmtPerOrder,
            0,
            false,
            address(0),
            new bytes(0)
        );
    }
```