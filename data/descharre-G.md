# Summary
|ID     | Finding|  Instances |
|:----: | :---           |  :----:         |
|1       |Make for loops unchecked| * |
|2       |Save storage variable to memory when it's used more than once in a function| 8 |
|3       |Internal functions only called once can be inlined to save gas| 1|
|4       |Using double require instead of && consumes less gas| 11|
|5       |x = x + y is cheaper than x += y| 20|
|6       |Use calldata instead of memory for read only function parameters | 2|
|7       |Using solidity version 0.8.17 will provide an overall gas optimization|1|
|8       |Calling msg.sender is cheaper than accessing a memory variable|9|
|9       |Calling msg.sender directly is cheaper than the function _msgSender()|*|
|10      |Combine 2 for loops|2|
|11      |Useless checks|2|
|12      |Use an unchecked block when operands can't underflow/overflow|2|
|13      |Make own counter instead of using CountersUpgradeable|1|
|14      |Ternary operation is cheaper than if else statement|2|
|15      |Make up to 3 fields in an event indexed|2|
|16      |Miscellaneous| 2|


# Details
## 1 Make for loops unchecked
The risk of for loops getting overflowed is extremely low. Because it always increments by 1 and is limited to the arrays length. Even if the arrays are extremely long, it will take a massive amount of time and gas to let the for loop overflow.

Gas savings when you make all the for loops in RToken.sol unchecked. 

|Function     | Normal|  Gas Optimazion| Gas saved |
|:----: | :---           |              :----:    |  :----:         |
|cancel       |110657| 110356 | 301 |
|issue       |868581| 868108 | 473 |
|redeem       |554004| 553728 | 276 |
|vest       |522834| 522374 | 460 |

It can also be done on every other for loop in the project. The larger the array the more gas you will save.

Example:

[RToken.sol#L270-L275](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270-L275)
```diff
+   function unchecked_inc(uint256 x) private pure returns (uint256) {
+       unchecked {
+           return x + 1;
+       }
+   }

L270:
-   for (uint256 i = 0; i < erc20s.length; ++i) {
+   for (uint256 i = 0; i < erc20s.length; i = unchecked_inc(i)) {
        IERC20Upgradeable(erc20s[i]).safeTransferFrom(
            issuer,
            address(backingManager),
            deposits[i]
        );
    }
```
Or another way to accomplish this is
```diff
L270: 
+   uint256 i;
-   for (uint256 i = 0; i < erc20s.length; ++i) {
+   for (; i < erc20s.length;) {
        IERC20Upgradeable(erc20s[i]).safeTransferFrom(
            issuer,
            address(backingManager),
            deposits[i]
        );
+       unchecked{
+           i++;
+       }
    }
```

## 2 Save storage variable to memory when it's used more than once in a function
When a storage variable is read for the second time from storage it costs 100 gas. When it's read from memory it only costs 3 gas. Make sure when you use this technique you don't use the memory  when you change it's state.   

`swapRegistered()` avg gas saved: 164
[AssetRegistry.sol#L73-L81](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L73-L81)
```diff
L73: 
    function swapRegistered(IAsset asset) external governance returns (bool swapped) {
        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");
+       IBasketHandler handler =basketHandler;
-       uint192 quantity = basketHandler.quantity(asset.erc20());
+       uint192 quantity = handler.quantity(asset.erc20());

-       if (quantity > 0) basketHandler.disableBasket();
+       if (quantity > 0) handler.disableBasket();
        swapped = _registerIgnoringCollisions(asset);
    }
```

Same thing can be done for: 
- [AssetRegistry.sol#L87-L97](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L87-L97): variable basketHandler; `unregister()` avg gas saved: 122
- [AssetRegistry.sol#L162-L177](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L162-L177): variable basketHandler; `swapRegistered()` avg gas saved: 122
- [BackingManager.sol#L105-L150](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L105-L150): variable basketHandler; `manageTokens()` avg gas saved: 247; `manageTokensSortedOrder()` avg gas saved: 267
- [BackingManager.sol#L154-L245)](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L154-L245): variable basketHandler; `manageTokens()` avg gas saved: 152; `manageTokensSortedOrder()` avg gas saved: 316
- [Furnace.sol#L70-L84](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L70-L84): variable lastpayout and period; `melt()` avg gas saved: 331
- [RToken.sol#L186-L339](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L186-L339): variable basketHandler: `issue()` avg gas saved: 211
- [StRSR.sol#L257-L277](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L257-L277): variable stakeRate: `unstake()` avg gas saved
## 3 Internal functions only called once can be inlined to save gas
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.
- `compromiseBasketsNeeded()` [BackingManager.sol#L248-L251](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L248-L251)

## 4 Using double require instead of && consumes less gas
[Auth.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181)

Gas saved: 8
```solidity
L181:  require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");

L188:  require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
```
```diff
-   require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
+   require(shortFreeze_ > 0, "short freeze out of range");
+   require(shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
```
- [Broker.sol#L134-L137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L134-L137)
- [RevenueTrader.sol#L72](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72)
- [RToken.sol#L410](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410)
- [RToken.sol#L590](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590)
- [RToken.sol#L741](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L741)
- [RToken.sol#L813](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813)
- [StRSR.sol#L813](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813)
- [StRSR.sol#L821](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L821)
- [StRSR.sol#L829](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L829)
- [Asset.sol#L50](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L50)

## 5 x = x + y is cheaper than x += y
Saves around 20 gas per optimization. This doesn't apply for incrementing an array.

[BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L345)
```solidity
L345:    low256 += quantityMulPrice(qty, lowP);
L346:    high256 += quantityMulPrice(qty, highP);
```
[Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165)

```solidity
L165:    revTotals.rTokenTotal += share.rTokenDist;
L166:    revTotals.rsrTotal += share.rsrDist;
```

[Furnace.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L81)
```solidity
L81:    lastPayout += numPeriods * period;

L166:    revTotals.rsrTotal += share.rsrDist;
```
[StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L229)
```solidity
L229:    stakeRSR += rsrAmount;

L387:    stakeRSR -= stakeRSRToTake;

L396:    seizedRSR += stakeRSR;

L402:    draftRSR -= draftRSRToTake;
L403:    seizedRSR += draftRSRToTake;

L412:    seizedRSR += draftRSR;

L417:    seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;

L513:    stakeRSR += payout;

L516:    payoutLastPaid += numPeriods * rewardPeriod;

L549:    draftRSR += rsrAmount;

L699:    totalStakes += amount;

L721:    totalStakes -= amount;
```
[RecollateralizationLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L264)
```solidity
L264:    assetsLow += low.mul(bal, FLOOR);

L271:    else assetsHigh += val;
```
## 6 Use calldata instead of memory for read only function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory.

[Deployer.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L103-L104): deploy function: avg gas saved: 395
```solidity
L103:    string memory name,
L104:    string memory symbol,
```
## 7 Using solidity version 0.8.17 will provide an overall gas optimization
Using at least 0.8.10 will save gas due to skipped extcodesize check if there is a return value and it makes overflow checks on multiplication more efficient.

## 8 Calling msg.sender is cheaper than accessing a memory variable
Instead of writing the msg.sender to a memory variable it's better to call it direclty. Reading from memory costs 3 gas, calling msg.sender directly is only 2 gas. Additionally you also save gas for not writing to a memory variable

The following changes save 26 gas:

[Broker.sol#L85-L118](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L85-L118)
```diff
L85:
    function openTrade(TradeRequest memory req) external notPausedOrFrozen returns (ITrade) {
        require(!disabled, "broker disabled");

-       address caller = _msgSender();
        require(
-           caller == address(backingManager) ||
-               caller == address(rsrTrader) ||
-               caller == address(rTokenTrader),
+           msg.sender == address(backingManager) ||
+               msg.sender == address(rsrTrader) ||
+               msg.sender == address(rTokenTrader),
            "only traders"
        );

L109:
        // == Interactions ==
        IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(
-           caller,
+           msg.sender,
            address(trade),
            req.sellAmount
        );

-       trade.init(this, caller, gnosis, auctionLength, req);
+       trade.init(this, msg.sender, gnosis, auctionLength, req);
        return trade;
    }
```

Can also be done at:
- [RToken.sol#L196](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L196)
- [RToken.sol#L407](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L407)
- [RToken.sol#L446](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L446)
- [StRSR.sol#L228](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L228)
- [StRSR.sol#L258](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L258)
- [StRSR.sol#L623](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L623)
- [StRSR.sol#L652](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L652)
- [StRSR.sol#L658](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L658)

Same applies for block.timestamp
- [StRSR.sol#L436](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L436)
## 9 Calling msg.sender directly is cheaper than the function _msgSender()
If you don't want to do the optimization above you can also instead of calling the function _msgSender() call msg.sender direclty. This keeps the same readability and does exaclty the same.

[RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L178)
```diff
-   issue(_msgSender(), amtRToken);
+   issue(msg.sender, amtRToken);
```
This can be done for every _msgSender function in the code. Most of them are in [RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol) and [StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol)
## 10 Combine 2 for loops
When 2 for loops always have the exact same length they can be combined.

In the following code making the struct is pretty useless. This optimization saves 923 gas

[Distributor.sol#L105-L136](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L105-L136)
```diff
L105: 
-   Transfer[] memory transfers = new Transfer[](destinations.length());
    uint256 numTransfers;

    for (uint256 i = 0; i < destinations.length(); ++i) {

L123:
-       transfers[numTransfers] = Transfer({
-           erc20: erc20,
-           addrTo: addrTo,
-           amount: transferAmt
-       });

+       IERC20Upgradeable(address(erc20)).safeTransferFrom(from, addrTo, transferAmt);
        numTransfers++;
    }
    emit RevenueDistributed(erc20, from, amount);

    // == Interactions ==
-   for (uint256 i = 0; i < numTransfers; i++) {
-       Transfer memory t = transfers[i];
-       IERC20Upgradeable(address(t.erc20)).safeTransferFrom(from, t.addrTo, t.amount);
-   }
```
Same can be applied to:
- [RToken.sol#L329-L336)](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L329-L336): they use exactly the same variable for the length; `issue()` avg gas saved: 244
## 11 Useless checks
- [BasketHandler.sol#L556](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556): TargetIndex will 100% of the time be smaller than targetsLength because it's specified like that in the for loop.
- [RToken.sol#L255](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L255): status == CollateralStatus.Sound is already checked on L217

## 12 Use an unchecked block when operands can't underflow/overflow
When operands can't underflow/overflow because of a previous require() or if statement, you can use an unchecked block.

In the assert statement is checked that totalStakes + amount is smaller than max of uint224. So there is no possibility of overflowing

[StRSR.sol#L699](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L699): `stake()` avg gas saved: 186
```diff
    assert(totalStakes + amount < type(uint224).max);
+   unchecked{
        stakes[era][account] += amount;
        totalStakes += amount;
+   }
```
If stakes per account can't be underflowed, the same applies for the totalStakes

[StRSR.sol#L721](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L721): `unstake()` avg gas saved: 91
```diff
        require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
        unchecked {
            eraStakes[account] = accountBalance - amount;
-       }
        totalStakes -= amount;
+       }
```
## 13 Make own counter instead of using CountersUpgradeable
[StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L786-L788): `permit()` avg gas saved: 30
```diff
+   mapping(address => uint256) noncesCounter;
L786:
    function nonces(address owner) public view returns (uint256) {
-       return _nonces[owner].current();
+       return noncesCounter[owner];
    }

L795:
    function _useNonce(address owner) internal returns (uint256 current) {
-       CountersUpgradeable.Counter storage nonce = _nonces[owner];
-       current = nonce.current();
-       nonce.increment();
+       unchecked{
+           current = noncesCounter[owner]++;
+       }
    }
```
## 14 Ternary operation is cheaper than if else statement
Really small optimization so it's also a choice to keep the if-else statement for readability
- [BasketHandler.sol#L301-L305](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L301-L305)
- [RecollateralizationLib.sol#L270-L271](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L270-L271)
```diff
-   if (uint256(assetsHigh) + val >= FIX_MAX) assetsHigh = FIX_MAX;
-   else assetsHigh += val;
+   assetsHigh = (uint256(assetsHigh) + val >= FIX_MAX ? FIX_MAX: assetsHigh + val);
```
## 15 Make up to 3 fields in an event indexed
Indexing all fields in an event saves gas. If the event has more than 3 fiels, only index 3.
- [IDeployerRegistry.sol#L7-L9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L7-L9)
- [IDistributor.sol#L28](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L28)
- [IRToken.sol#L83](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L83)
## Miscellaneous
### Use ++x or x++ instead of x+=1
Saves around 30 gas
- [BasketHandler.sol#L636](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L636)
### For simple calculations and comparisons it's cheaper to do it directly instead of using FixLib
For things like minus, plus, gt and other simple comparisons or calculations. It's better to not use the FixLib to save gas due to having less jumps. It keeps the same readability. For more complicated calculations you can keep using FixLib to keep the readability.
Example:
```diff
-    if (bal.gt(needed)) {
+    if (bal>needed) {    

```
