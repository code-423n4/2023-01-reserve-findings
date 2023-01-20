## Summary
### Gas optimizations

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Avoid use of `&&` within a `require` function|15|
|2| Inline a function that is used only once|22|
|3| Avoid use of default "checked" behavior in a `for` loop|68|
|4| Avoid storage of uints or ints smaller than 32 bytes, if possible|16|
|| Total|121|
___

| No. | Description | 
|--| ----------- | 
|1|**Avoid use of `&&` within a `require` function**|
|  | Splitting into separate `require()` statements saves gas|	
___
[Broker.sol: L134-137](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Broker.sol#L134-L137)
```solidity
        require(
            newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
            "invalid auctionLength"
        );
```
Recommendation:
```solidity
        require(newAuctionLength > 0, "invalid auctionLength");
        require(newAuctionLength <= MAX_AUCTION_LENGTH, "invalid auctionLength");
```
___
[Deployer.sol: L48-65](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Deployer.sol#L48-L65)
```solidity
        require(
            address(rsr_) != address(0) &&
                address(gnosis_) != address(0) &&
                address(rsrAsset_) != address(0) &&
                address(implementations_.main) != address(0) &&
                address(implementations_.trade) != address(0) &&
                address(implementations_.components.assetRegistry) != address(0) &&
                address(implementations_.components.backingManager) != address(0) &&
                address(implementations_.components.basketHandler) != address(0) &&
                address(implementations_.components.broker) != address(0) &&
                address(implementations_.components.distributor) != address(0) &&
                address(implementations_.components.furnace) != address(0) &&
                address(implementations_.components.rsrTrader) != address(0) &&
                address(implementations_.components.rTokenTrader) != address(0) &&
                address(implementations_.components.rToken) != address(0) &&
                address(implementations_.components.stRSR) != address(0),
            "invalid address"
        );
```
Recommendation:
```solidity
        require(address(rsr_) != address(0), "invalid address");
        require(address(gnosis_) != address(0), "invalid address");
        require(address(rsrAsset_) != address(0), "invalid address");
        require(address(implementations_.main) != address(0), "invalid address");
        require(address(implementations_.trade) != address(0), "invalid address");
        require(address(implementations_.components.assetRegistry) != address(0), "invalid address");
        require(address(implementations_.components.backingManager) != address(0), "invalid address");
        require(address(implementations_.components.basketHandler) != address(0), "invalid address");
        require(address(implementations_.components.broker) != address(0), "invalid address");
        require(address(implementations_.components.distributor) != address(0), "invalid address");
        require(address(implementations_.components.furnace) != address(0), "invalid address");
        require(address(implementations_.components.furnace) != address(0), "invalid address");
        require(address(implementations_.components.rsrTrader) != address(0), "invalid address");
        require(address(implementations_.components.rTokenTrader) != address(0), "invalid address");
        require(address(implementations_.components.rToken) != address(0), "invalid address");
        require(address(implementations_.components.stRSR) != address(0), "invalid address");
```
___
[Deployer.sol: L109](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Deployer.sol#L109)
```solidity
        require(owner != address(0) && owner != address(this), "invalid owner");
```
Recommendation:
```solidity
        require(owner != address(0), "invalid owner");
        require(owner != address(this), "invalid owner");
```
___
[Furnace.sol: L89](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Furnace.sol#L89)
```solidity
        require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
```
Recommendation:
```solidity
        require(period_ > 0, "invalid period");
        require(period_ <= MAX_PERIOD, "invalid period");
```
___
[RToken.sol: L410](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L410)
```solidity
        require(queue.left <= endId && endId <= queue.right, "out of range");
```
Recommendation:
```solidity
        require(queue.left <= endId, "out of range");
        require(endId <= queue.right, "out of range");
```
___

Similary for the following `require` statements incorporating `&&`:

[RToken.sol: L590](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L590)

[RToken.sol: L623](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L623)

[RToken.sol: L741](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L741)

[RToken.sol: L813](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L813)

[RevenueTrader.sol: L72](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RevenueTrader.sol#L72)

[StRSR.sol: L813](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L813)

[StRSR.sol: L821](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L821)

[Auth.sol: L181](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181)

[Auth.sol: L188](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188)

[Asset.sol: L50](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/assets/Asset.sol#L50)
___
___

| No. | Description | 
|--| ----------- | 
|2|**Inline a function that is used only once**|
|  | It costs less gas to inline the code of functions that are only called once. Doing so saves the cost of allocating the function selector, and the cost of the jump when the function is called.|

___
[RToken.sol: L344](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L344)
```solidity
    function whenFinished(uint256 amtRToken) private returns (uint192 finished) {
```
`function whenFinished` above is called only once, as shown below:

[RToken.sol: L243](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L243)
```solidity
        uint192 vestingEnd = whenFinished(amtRToken); // D18{block number}
```
___

The following `functions` similarly are called only once and should be inlined:

**BackingManager.sol**

[L154](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BackingManager.sol#L154), [L248](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BackingManager.sol#L248)

**BasketHandler.sol**

[L75](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L75), [L519](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L519)

**RToken.sol**

[L737](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L737)

**StRSR.sol**

[L543-545](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L543-L545), [L739-743](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L739-L743)

**RecollateralizationLib.sol**

[L319-324](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L319-L324), [L428-431](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L428-L431), [L463-467](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L463-L467)

**ComponentRegistry.sol**

[L36](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L36), [L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L44), [L52](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L52), [L60](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L60), [L68](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L68)

[L76](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L76), [L84](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L84), [L92](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L92), [L100](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L100), [L108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L108)

**FiatCollateral.sol**

[L199-200](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/assets/FiatCollateral.sol#L199-L200)
___


| No. | Description | 
|--| ----------- | 
|3|**Avoid use of default "checked" behavior in a `for` loop**|
|  | Underflow/overflow checks are made every time `++i` (or `i++` or equivalent counter) is called. Such checks are unnecessary since `i` is already limited. Therefore, use `unchecked {++i}`/`unchecked {i++}` instead, as shown below:|	


[AssetRegistry.sol: L38-40](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/AssetRegistry.sol#L38-L40)
```solidity
        for (uint256 i = 0; i < length; ++i) {
            _register(assets_[i]);
        }
```
Suggestion:
```solidity
        for (uint256 i; i < length;) {
            _register(assets_[i]);

            unchecked {
              ++i;
          }
        }
```
___
Similarly, for the following `for` loops:

**AssetRegistry.sol**

[L49-51](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/AssetRegistry.sol#L49-L51), [L127-129](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/AssetRegistry.sol#L127-L129), [L138-141](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/AssetRegistry.sol#L138-L141)

**BackingManager.sol**

[L221](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BackingManager.sol#L221), [L238](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BackingManager.sol#L238)

**BasketHandler.sol**

[L70](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L70), [L78](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L78), [L218](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L218), [L262](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L262), [L286](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L286)

[L337](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L337), [L397](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L397), [L416](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L416), [L437](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L437), [L530](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L530)

[L548](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L548), [L553](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L553), [L586](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L586), [L597](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L597), [L611](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L611), 

[L643](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L643), [L653](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L653), [L707](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L707), [L725](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L725)

**Distributor.sol**

[L108](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Distributor.sol#L108), [L133](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Distributor.sol#L133), [L143](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Distributor.sol#L143)

**RToken.sol**

[L270](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L270), [L303](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L303), [L329](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L329), [L334](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L334)

[L478](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L478), [L501](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L501), [L674](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L674), [L683](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L683)

[L711](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L711), [L757](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L757), [L767](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L767), [L793](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L793)

**RecollateralizationLib.sol**

[L242](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L242), [L329](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L329), [L437](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L437)

**RewardableLib.sol**

[L27](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RewardableLib.sol#L27), [L67](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RewardableLib.sol#L67), [L73](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RewardableLib.sol#L73)

**FacadeAct.sol**

[L76](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeAct.sol#L76), [L106](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeAct.sol#L106), [L176](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeAct.sol#L176)

[L308](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeAct.sol#L308), [L317](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeAct.sol#L317), [L334](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeAct.sol#L334)

**FacadeRead.sol**

[L101](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeRead.sol#L101), [L114](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeRead.sol#L114), [L131](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeRead.sol#L131)

[L154](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeRead.sol#L154), [L273](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeRead.sol#L273)

**FacadeWrite.sol**

[L32](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeWrite.sol#L32), [L37](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeWrite.sol#L37), [L64](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeWrite.sol#L64), [L74](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeWrite.sol#L74)

[L88](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeWrite.sol#L88), [L93](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeWrite.sol#L93), [L110](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeWrite.sol#L110)

**Array.sol**

[L11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11), [L12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L12), [L23](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23)

**String.sol**

[L14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14)
___


| No. | Description | 
|--| ----------- | 
|4|**Avoid storage of uints or ints smaller than 32 bytes, if possible**|
|  | Storage of uints or ints smaller than 32 bytes incurs overhead. Instead, use size of at least 32, then downcast where needed|	

___
[Distributor.sol: L181](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Distributor.sol#L181)
```solidity
    function _ensureNonZeroDistribution(uint24 rTokenDist, uint24 rsrDist) internal pure {
```
___
[StRSRVotes.sol: L118-125](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSRVotes.sol#L118-L125)
```solidity
    function delegateBySig(
        address delegatee,
        uint256 nonce,
        uint256 expiry,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public {
```
___
[Permit.sol: L10-16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L10-L16)
```solidity
    function requireSignature(
        address owner,
        bytes32 hash,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external view {
```
___
[IDistributor.sol: L13-16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L13-L16)
```solidity
struct RevenueTotals {
    uint24 rTokenTotal; // {revShare}
    uint24 rsrTotal; // {revShare}
}
```
___
[IDistributor.sol: L28](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L28)
```solidity
    event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);
```
___
[IDistributor.sol: L63](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L63)
```solidity
    function distribution(address) external view returns (uint16 rTokenDist, uint16 rsrDist);
```
___
[IStaticATokenLM.sol: L10-14](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IStaticATokenLM.sol#L10-L14)
```solidity
    struct SignatureParams {
        uint8 v;
        bytes32 r;
        bytes32 s;
    }
```
___
[IStaticATokenLM.sol: L27-32](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IStaticATokenLM.sol#L27-L32)
```solidity
    function deposit(
        address recipient,
        uint256 amount,
        uint16 referralCode,
        bool fromUnderlying
    ) external returns (uint256);
```
___
[IStaticATokenLM.sol: L102-110](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IStaticATokenLM.sol#L102-L110)
```solidity
    function metaDeposit(
        address depositor,
        address recipient,
        uint256 value,
        uint16 referralCode,
        bool fromUnderlying,
        uint256 deadline,
        SignatureParams calldata sigParams
    ) external returns (uint256);
```
___
[StaticATokenLM.sol: L105-110](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L105-L110)
```solidity
    function deposit(
        address recipient,
        uint256 amount,
        uint16 referralCode,
        bool fromUnderlying
    ) external override nonReentrant returns (uint256) {
```
___
[StaticATokenLM.sol: L161-169](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L161-L169)
```solidity
    function metaDeposit(
        address depositor,
        address recipient,
        uint256 value,
        uint16 referralCode,
        bool fromUnderlying,
        uint256 deadline,
        SignatureParams calldata sigParams
    ) external override nonReentrant returns (uint256) {
```
___
[StaticATokenLM.sol: L287-293](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L287-L293)
```solidity
    function _deposit(
        address depositor,
        address recipient,
        uint256 amount,
        uint16 referralCode,
        bool fromUnderlying
    ) internal returns (uint256) {
```
___
___
