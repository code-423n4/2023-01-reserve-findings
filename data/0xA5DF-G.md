


## Cache in memory storage variable / view-calls that are read multiple times

Gas saving: ~700 gas units in total

Code: [/contracts/p0/RToken.sol#L466-L473](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L466-L473)

`blockIssuanceRates[block.number]` is read twice (and written once).
It can be cached to a memory variable to save gas (and the memory variable should be updated too if the storage variable is changed).
```solidity
        // Calculate the issuance rate if this is the first issue in the block
        if (blockIssuanceRates[block.number] == 0) {
            blockIssuanceRates[block.number] = Math.max(
                MIN_BLOCK_ISSUANCE_LIMIT,
                issuanceRate.mulu_toUint(totalSupply())
            );
        }
        uint256 perBlock = blockIssuanceRates[block.number];
```

Code: [/contracts/p1/AssetRegistry.sol#L165-L166](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L165-L166)

`assets[erc20]` is being read twice here:
```solidity
            if (assets[erc20] == asset) return false;
            else emit AssetUnregistered(erc20, assets[erc20]);
```


Code: [/contracts/p1/BackingManager.sol#L139-L142](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L139-L142)

`req.sell.erc20()` is used read here twice if the condition is true

```solidity
                if (req.sell.erc20() == rsr) {
                    uint256 bal = req.sell.erc20().balanceOf(address(this));
                    if (req.sellAmount > bal) stRSR.seizeRSR(req.sellAmount - bal);
```

Code: [/contracts/p1/StRSR.sol#L497-L498](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L497-L498)


`payoutLastPaid` and `rewardPeriod` are both storage variable that are read twice here
```solidity
        if (block.timestamp < payoutLastPaid + rewardPeriod) return;
        uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod; 
```

Code: [/contracts/plugins/trading/GnosisTrade.sol#L174-L192](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L174-L192)

Here `origin` is being read 3 times.
In the 2 last times `msg.sender` can be used instead, since we've verified they're equal (`msg.sender` costs 2 gas units, while reading a storage variable costs 100 gas units).
```solidity
        require(msg.sender == origin, "only origin can settle");

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
        uint256 sellBal = sell.balanceOf(address(this));
        boughtAmt = buy.balanceOf(address(this));

        if (sellBal > 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);
        if (boughtAmt > 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);
```


## Convert to fix and divide instead of using `divuu()`

Gas saving: ~100-200 gas units

Code: [/contracts/plugins/assets/Asset.sol#L141-L142](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L141-L142)


`divuu()` is a pretty expensive function, it uses `mulDiv256()` which uses about 40-50 math operations (should cost 100-200 gas units, for some reason the gas tests don't report it).
Since `priceTimeout - delta` is `unit48` anyways, it can be converted to a fixed and then divide using `divu()`.

```diff
-            uint192 lotMultiplier = divuu(priceTimeout - delta, priceTimeout);
+            uint192 lotMultiplier = toFix(priceTimeout - delta).divu(priceTimeout);
```