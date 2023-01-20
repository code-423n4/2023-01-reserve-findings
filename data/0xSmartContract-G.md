### Gas Optimizations List
| Number | Optimization Details | Context |
|:--:|:-------| :-----:|
| [G-01] | Remove the `initializer` modifier |10 |
| [G-02] |Use hardcode address instead ``address(this)``| 52 |
| [G-03] |Duplicated require()/if() checks should be refactored to a modifier or function |5 |
| [G-04] | Structs can be packed into fewer storage slots  |1 |
| [G-05] |Cache storage values in memory to minimize SLOADs |1|
| [G-06] |Using state variable in `BasketsNeededChanged` emit' wastes gas  |1 |
| [G-07] |Using ``delete``  instead of setting struct/mapping/state variable ``0`` saves gas | 9 |
| [G-08] | Using `storage` instead of `memory` for `structs/arrays` saves gas | 43 |
| [G-09] |Superfluous event fields  | 1 |
| [G-10] | Avoid indexing unnecessary event parameters  |25 |
| [G-11] | Use ``require`` instead of ``assert``  |20 |
| [G-12] |External visibility can be used in public functions   |15 |
| [G-13] |Don't use _msgSender() if not supporting EIP-2771  | 35 |
| [G-14] |Avoid contract existence checks by using solidity version 0.8.10 or later  | 43 |
| [G-15] |Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement | 2 |
| [G-16] |Empty blocks should be removed or emit something |11 |
| [G-17] |Use ``assembly`` to write _address storage values_ |30 |
| [G-18] |Setting the _constructor_ to `payable`|17 |
| [G-19] |Use ``double require`` instead of using ``&&``  | 14 |
| [G-20] |Use nested if and, avoid multiple check combinations  |16 |
| [G-21] |Sort Solidity operations using short-circuit mode  | 16 |
| [G-22] |`x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables  | 8 |
| [G-23] |Optimize names to save gas  | All contracts |
| [G-24] |Use a more recent version of solidity  | All contracts |
| [G-25] |Upgrade Solidity's optimizer  | |
| [G-26] |``>=`` costs less gas than ``>``  |13 |

Total 26 issues

### [G-01] Remove the `initializer` modifier

if we can just ensure that the `initialize()` function could only be called from within the constructor, we shouldn't need to worry about it getting called again. 

10 results - 10 files:
```solidity
protocol\contracts\p1\AssetRegistry.sol:
  34:     function init(IMain main_, IAsset[] calldata assets_) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L34

```solidity
protocol\contracts\p1\BackingManager.sol:
  44:     function init(
  50     ) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L44-L50

```solidity
protocol\contracts\p1\BasketHandler.sol:
  151:     function init(IMain main_) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L151

```solidity
protocol\contracts\p1\Broker.sol:
  51:     function init(
  56     ) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L51-L56

```solidity
protocol\contracts\p1\Distributor.sol:
  41:     function init(IMain main_, RevenueShare calldata dist) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L41

```solidity
protocol\contracts\p1\Furnace.sol:
  33:     function init(
  37:     ) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L33-L37

```solidity
protocol\contracts\p1\Main.sol:
  26:     function init(
  31:     ) public virtual initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L26-L31

```solidity
protocol\contracts\p1\RevenueTrader.sol:
  23:     function init(
  28:     ) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L23-L28

```solidity
protocol\contracts\p1\RToken.sol:
  147:     function init(
  155:     ) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L147-L155

```solidity
protocol\contracts\p1\StRSR.sol:
  160:     function init(
  167:     ) external initializer {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L160-L167

```diff
protocol\contracts\p1\StRSR.sol#L160-L167
  160:     function init(
- 167:     ) external initializer {
+                 require(address(this).code.length == 0, 'not in constructor’);
```

Now the Proxy contract's constructor can still delegatecall initialize(), but if anyone attempts to call it again (after deployment) through the Proxy instance, or tries to call it directly on the above instance, it will revert because address(this).code.length will be nonzero. 

Also, because we no longer need to write to any state to track whether initialize() has been called, we can avoid the 20k storage gas cost. In fact, the cost for checking our own code size is only 2 gas, which means we have a 10,000x gas savings over the standard version. Pretty neat!

### [G-02] Use hardcode address instead ``address(this)``

Instead of ``address(this)``, it is more gas-efficient to pre-calculate and use the address with a hardcode. Foundry's ``script.sol`` and solmate````LibRlp.sol`` contracts can do this.

Reference:
https://book.getfoundry.sh/reference/forge-std/compute-create-address
https://twitter.com/transmissions11/status/1518507047943245824

52 results - 13 files:
```solidity
protocol\contracts\p1\BackingManager.sol:
  140:    uint256 bal = req.sell.erc20().balanceOf(address(this));
  173:    if (rsr.balanceOf(address(this)) > 0) {
  177:    rsr.balanceOf(address(this))
  191:    uint192 held = basketHandler.basketsHeldBy(address(this)); // {BU}
  203:    rToken.mint(address(this), uint256(rTok));
  225:    if (asset.bal(address(this)).gt(req)) {
  227:    uint256 delta = asset.bal(address(this)).minus(req).shiftl_toUint(
  250:    rToken.setBasketsNeeded(basketHandler.basketsHeldBy(address(this)));
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L140

```solidity
protocol\contracts\p1\Deployer.sol:
  109:    require(owner != address(0) && owner != address(this), "invalid owner");
  242:    main.renounceRole(OWNER, address(this));
  243:    main.renounceRole(SHORT_FREEZER, address(this));
  244:    main.renounceRole(LONG_FREEZER, address(this));
  245:    main.renounceRole(PAUSER, address(this));
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109

```solidity
protocol\contracts\p1\Furnace.sol:
  42      lastPayout = uint48(block.timestamp);
  43:     lastPayoutBal = rToken.balanceOf(address(this));
  82:     lastPayoutBal = rToken.balanceOf(address(this)) - amount;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L42

```solidity
protocol\contracts\p1\RevenueTrader.sol:
  56:     uint256 bal = erc20.balanceOf(address(this));
  63:     distributor.distribute(erc20, address(this), bal);
  77:     sellAmount: sell.bal(address(this)),
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L56

```solidity
protocol\contracts\p1\RToken.sol:
  335:    IERC20Upgradeable(erc20s[i]).safeTransferFrom(issuer, address(this), deposits[i]);
  833:    require(to != address(this), "RToken transfer to self");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L335

```solidity
protocol\contracts\p1\StRSR.sol:
  181:    rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));
  236:    IERC20Upgradeable(address(rsr)).safeTransferFrom(account, address(this), rsrAmount);
  379:    uint256 rsrBalance = rsr.balanceOf(address(this));
  597:    return rsr.balanceOf(address(this)) - stakeRSR - draftRSR;
  760:    require(to != address(this), "StRSR transfer to self");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L181

```solidity
protocol\contracts\p1\mixins\RewardableLib.sol:
   68:    deltas[i] = erc20s[i].balanceOf(address(this)) - liabilities[erc20s[i]]; // {qTok}
   78:    assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
   97:    uint256 amt = erc20.balanceOf(address(this)) - liabilities[erc20];
  102:    assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L68

```solidity
protocol\contracts\p1\mixins\TradeLib.sol:
  60:     ITrading(address(this)),
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L60

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  274:    address(this)
  298:    ASSET.safeTransferFrom(depositor, address(this), amount);
  299:    LENDING_POOL.deposit(address(ASSET), amount, address(this), referralCode);
  301:    ATOKEN.safeTransferFrom(depositor, address(this), amount);
  392:    uint256 freshRewards = INCENTIVES_CONTROLLER.getRewardsBalance(assets, address(this));
  417:    address(this)
  457:    uint256 totBal = REWARD_TOKEN.balanceOf(address(this));
  548:    uint256 freshReward = INCENTIVES_CONTROLLER.getRewardsBalance(assets, address(this));
  584:    uint256 freshRewards = INCENTIVES_CONTROLLER.getRewardsBalance(assets, address(this));
  585:    return REWARD_TOKEN.balanceOf(address(this)).add(freshRewards);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L274

```solidity
protocol\contracts\plugins\assets\ATokenFiatCollateral.sol:
  54:     uint256 oldBal = stkAAVE.balanceOf(address(this));
  56:     emit RewardsClaimed(stkAAVE, stkAAVE.balanceOf(address(this)) - oldBal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L54

```solidity
protocol\contracts\plugins\assets\CTokenFiatCollateral.sol:
  54:     uint256 oldBal = comp.balanceOf(address(this));
  55:     comptroller.claimComp(address(this));
  56:     emit RewardsClaimed(comp, comp.balanceOf(address(this)) - oldBal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L54

```solidity
protocol\contracts\plugins\assets\CTokenSelfReferentialCollateral.sol:
  57:     uint256 oldBal = comp.balanceOf(address(this));
  58:     comptroller.claimComp(address(this));
  59:     emit RewardsClaimed(comp, comp.balanceOf(address(this)) - oldBal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L57

```solidity
protocol\contracts\plugins\trading\GnosisTrade.sol:
   93:    initBal = sell.balanceOf(address(this));
  188:    uint256 sellBal = sell.balanceOf(address(this));
  189:    boughtAmt = buy.balanceOf(address(this));
  217:    IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L93


### [G-03] Duplicated require()/if() checks should be refactored to a modifier or function

5 results 2 files:
```solidity
protocol\contracts\p1\RToken.sol:
  217:         require(status == CollateralStatus.SOUND, "basket unsound");
  384:         require(status == CollateralStatus.SOUND, "basket unsound");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L217

```solidity
protocol\contracts\p1\StRSRVotes.sol:
  77:         require(blockNumber < block.number, "ERC20Votes: block not yet mined");
  83:         require(blockNumber < block.number, "ERC20Votes: block not yet mined");
  89:         require(blockNumber < block.number, "ERC20Votes: block not yet mined");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L77


**Recommendation:**

You can consider adding a modifier like below.

```solidity
 modifer checkBlockNumber () {
        require(blockNumber < block.number, "ERC20Votes: block not yet mined");
        _;
 }
```


Here are the data available in the covered contracts. The use of this situation in contracts that are not covered will also provide gas optimization.

### [G-04] Structs can be packed into fewer storage slots

The `CollateralConfig` struct can be packed into ``one slot`` less slot as suggested below.
```solidity
protocol\contracts\plugins\assets\FiatCollateral.sol:
  11: struct CollateralConfig {
  12:     uint48 priceTimeout; // {s} The number of seconds over which saved prices decay           slot0
  13:     AggregatorV3Interface chainlinkFeed; // Feed units: {target/ref}                          slot1
  14:     uint192 oracleError; // {1} The % the oracle feed can be off by                           slot2
  15:     IERC20Metadata erc20; // The ERC20 of the collateral token                                slot3
  16:     uint192 maxTradeVolume; // {UoA} The max trade volume, in UoA                             slot4
  17:     uint48 oracleTimeout; // {s} The number of seconds until a oracle value becomes invalid   slot4
  18:     bytes32 targetName; // The bytes32 representation of the target name                      slot5
  19:     uint192 defaultThreshold; // {1} A value like 0.05 that represents a deviation tolerance  slot6
  20:     // set defaultThreshold to zero to create SelfReferentialCollateral                       
  21:     uint48 delayUntilDefault; // {s} The number of seconds an oracle can mulfunction          slot6
  22: }
```

```diff
protocol\contracts\plugins\assets\FiatCollateral.sol:
  11: struct CollateralConfig {
+ 13:     AggregatorV3Interface chainlinkFeed; // Feed units: {target/ref}                          slot0
+ 15:     IERC20Metadata erc20; // The ERC20 of the collateral token                                slot1
+ 18:     bytes32 targetName; // The bytes32 representation of the target name                      slot2
  12:     uint48 priceTimeout; // {s} The number of seconds over which saved prices decay           slot3
- 13:     AggregatorV3Interface chainlinkFeed; // Feed units: {target/ref}                          
  14:     uint192 oracleError; // {1} The % the oracle feed can be off by                           slot3
- 15:     IERC20Metadata erc20; // The ERC20 of the collateral token                                
  16:     uint192 maxTradeVolume; // {UoA} The max trade volume, in UoA                             slot4
  17:     uint48 oracleTimeout; // {s} The number of seconds until a oracle value becomes invalid   slot4
- 18:     bytes32 targetName; // The bytes32 representation of the target name                      
  19:     uint192 defaultThreshold; // {1} A value like 0.05 that represents a deviation tolerance  slot5
  20:     // set defaultThreshold to zero to create SelfReferentialCollateral                       
  21:     uint48 delayUntilDefault; // {s} The number of seconds an oracle can mulfunction          slot5
  22: }
```


### [G-05] Cache storage values in memory to minimize SLOADs

The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

```solidity
protocol\contracts\p1\StRSR.sol:
   62:    uint256 internal stakeRSR; // Amount of RSR backing all stakes {qRSR}
  212:    function stake(uint256 rsrAmount) external {
  222:        uint256 newStakeRSR = stakeRSR + rsrAmount;
  224:        uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;
  225:        uint256 stakeAmount = newTotalStakes - totalStakes;
  229:        stakeRSR += rsrAmount;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L224

**_StRSR.sol. stake(): stakeRSR should be cached_**
```diff
   62:    uint256 internal stakeRSR; // Amount of RSR backing all stakes {qRSR}
  212:    function stake(uint256 rsrAmount) external {
+             uint256 stakeRSR_ = stakeRSR;                                            
- 222:        uint256 newStakeRSR = stakeRSR + rsrAmount;                              
- 224:        uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;            
+ 224:        uint256 newTotalStakes = (stakeRate * (stakeRSR_ + rsrAmount)) / FIX_ONE;
  225:        uint256 stakeAmount = newTotalStakes - totalStakes;
- 229:        stakeRSR += rsrAmount;                                                   
+ 229:        stakeRSR = stakeRSR_ + rsrAmount;                                        
```

### [G-06] Using state variable in `BasketsNeededChanged` emit wastes gas

The ``basketsNeeded`` state variable in ``BasketsNeededChanged`` emit is used. Using existing memory values instead will save gas.

```solidity
protocol\contracts\interfaces\IRToken.sol:
  83:     event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);

protocol\contracts\p1\RToken.sol:
  737     function vestUpTo(address account, uint256 endId) private {
  745         uint192 amtBaskets;
  775:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);
  777         basketsNeeded = basketsNeeded + amtBaskets;
```

**protocol\contracts\p1\RToken.sol#775:**
```diff
  737     function vestUpTo(address account, uint256 endId) private {
  745         uint192 amtBaskets;
+             uint192 basketsNeeded_ = basketNeed;                                   
- 775:        emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);  
+ 775:        emit BasketsNeededChanged(basketsNeeded_, basketsNeeded_ + amtBaskets);
- 777         basketsNeeded = basketsNeeded + amtBaskets;                            
+ 777         basketsNeeded = basketsNeeded_ + amtBaskets; 
```


### [G-07] Using ``delete``  instead of setting struct/mapping/state variable ``0`` saves gas

9 results - 3 files:
```solidity
protocol\contracts\p1\RToken.sol:
  693:    queue.left = 0;

  694:    queue.right = 0;

  784:    queue.left = 0;

  785:    queue.right = 0;
```
[RToken.sol#L693-L694](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L693-L694), [RToken.sol#L784-L785](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L784-L785)

```solidity
protocol\contracts\p1\StRSR.sol:
  575:    stakeRSR = 0;

  576:    totalStakes = 0;

  587:    draftRSR = 0;

  588:    totalDrafts = 0;
```
[StRSR.sol#L575-L576](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L575-L576), [StRSR.sol#L587-L588](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L587-L588)

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  462:    _unclaimedRewards[onBehalfOf] = 0;
```
[StaticATokenLM.sol#L462](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L462)


**Recommendation code:**
```diff
protocol\contracts\plugins\aave\StaticATokenLM.sol#L462
- 462:    _unclaimedRewards[onBehalfOf] = 0;   
+ 462:    delete _unclaimedRewards[onBehalfOf];
```

### [G-08] Using `storage` instead of `memory` for `structs/arrays` saves gas

When fetching data from a storage location, assigning the data to a ``memory`` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the ``memory`` keyword, declaring the variable with the ``storage`` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a ``memory`` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires ``memory``, or if the array/struct is being read from another ``memory`` array/struct

43 results - 12 files:
```solidity
protocol\contracts\libraries\String.sol:
  12:    bytes memory bStr = bytes(str);
  13:    bytes memory bLower = new bytes(bStr.length);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L12-L13

```solidity
protocol\contracts\p1\BackingManager.sol:
  218:   RevenueTotals memory totals = distributor.totals();
  219:   uint256[] memory toRSR = new uint256[](length);
  220:   uint256[] memory toRToken = new uint256[](length);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L218-L220

```solidity
protocol\contracts\p1\BasketHandler.sol:
  169:   uint192[] memory refAmts = new uint192[](basket.erc20s.length);
  225:   bytes32[] memory names = new bytes32[](erc20s.length);
  541:   uint192[] memory goodWeights = new uint192[](targetsLength);
  545:   uint192[] memory totalWeights = new uint192[](targetsLength);
  642:   uint192[] memory refAmts = new uint192[](basketLength);
  698:   IERC20[] memory erc20s,
  699:   bytes32[] memory targetNames,
  700:   uint192[] memory targetAmts
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L169

```solidity
protocol\contracts\p1\Deployer.sol:
  120:   Components memory components = Components({
  230:   IAsset[] memory assets = new IAsset[](2);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L120

```solidity
protocol\contracts\p1\Distributor.sol:
   63:   RevenueTotals memory revTotals = totals();
   96:   RevenueTotals memory revTotals = totals();
  105:   Transfer[] memory transfers = new Transfer[](destinations.length());
  134:   Transfer memory t = transfers[i];
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L63

```solidity
protocol\contracts\p1\RevenueTrader.sol:
  74:    TradeInfo memory trade = TradeInfo({
  82:    TradingRules memory rules = TradingRules({
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L74

```solidity
protocol\contracts\p1\RToken.sol:
  666:   uint256[] memory amt = new uint256[](tokensLen);
  750:   uint256[] memory amtDeposits = new uint256[](tokensLen);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L666

```solidity
protocol\contracts\p1\mixins\RecollateralizationLib.sol:
   70:   ComponentCache memory components = ComponentCache({
   78:   TradingRules memory rules = TradingRules({
   83:   Registry memory reg = components.reg.getRegistry();
   88:   BasketRange memory range = basketRange(components, rules, reg);
   91:   TradeInfo memory trade = nextTradePair(components, rules, reg, range);
  325:   MaxSurplusDeficit memory maxes;
  433:   IERC20[] memory basketERC20s = components.bh.basketTokens();
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L70

```solidity
protocol\contracts\p1\mixins\RewardableLib.sol:
  26:    Registry memory registry = reg.getRegistry();
  62:    IERC20[] memory erc20s = reg.erc20s();
  64:    uint256[] memory deltas = new uint256[](erc20sLen); // {qTok}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L26

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  389:   address[] memory assets = new address[](1);
  411:   address[] memory assets = new address[](1);
  545:   address[] memory assets = new address[](1);
  582:   address[] memory assets = new address[](1);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L389

```solidity
protocol\contracts\plugins\assets\RTokenAsset.sol:
   55:   RecollateralizationLibP1.BasketRange memory range = basketRange(); // {BU}
   96:   RecollateralizationLibP1.BasketRange memory range = basketRange(); // {BU}
  140:   ComponentCache memory components = ComponentCache({
  148:   TradingRules memory rules = TradingRules({
  153:   Registry memory reg = assetRegistry.getRegistry();
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L55


```solidity
protocol\contracts\plugins\trading\GnosisTrade.sol:
  229:   GnosisAuctionData memory data = gnosis.auctionData(auctionId);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L229


### [G-09] Superfluous event fields

``block.timestamp`` and ``block.number`` are added to event information by default so adding them manually wastes gas.

```solidity
protocol\contracts\mixins\Auth.sol:
  156      // effects: unfreezeAt' = now  (i.e, frozen() == false after the call)
  157     function unfreeze() external onlyRole(OWNER) {
  158:         emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));
  159         unfreezeAt = uint48(block.timestamp);
  160     }
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L158


### [G-10] Avoid indexing unnecessary event parameters

Indexing parameters makes events easily searchable by the indexed parameter, which for example is useeful to find all events related to a certain address. However, it increases gas costs for each indexed parameter, which is why it is not recommended to index parameters that do not have a reason to be the search criteria of an event, like transferred amounts. Throughout the code there are many event parameters which are indexed for no apparent reason, and it would save gas to not mark them as indexed.

25 results - 10 files:
```solidity
protocol\contracts\interfaces\IBackingManager.sol:
        ///@audit oldVal, newVal        
  20:   event TradingDelaySet(uint48 indexed oldVal, uint48 indexed newVal);

        ///@audit oldVal, newVal
  21:   event BackingBufferSet(uint192 indexed oldVal, uint192 indexed newVal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L20-L21

```solidity
protocol\contracts\interfaces\IBasketHandler.sol:
        /// @audit nonce
  28:   event BasketSet(uint256 indexed nonce, IERC20[] erc20s, uint192[] refAmts, bool disabled);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L28

```solidity
protocol\contracts\interfaces\IBroker.sol:
        ///@audit oldVal, newVal
  23:   event AuctionLengthSet(uint48 indexed oldVal, uint48 indexed newVal);

        ///@audit prevVal, newVal
  24:   event DisabledSet(bool indexed prevVal, bool indexed newVal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBroker.sol#L23-L24

```solidity
protocol\contracts\interfaces\IDistributor.sol:
        ///@audit amount 
  34:   event RevenueDistributed(IERC20 indexed erc20, address indexed source, uint256 indexed amount);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L34

```solidity
protocol\contracts\interfaces\IFurnace.sol:
        ///@audit oldPeriod, newPeriod
  22:   event PeriodSet(uint48 indexed oldPeriod, uint48 indexed newPeriod);

        ///@audit oldRatio, newRatio
  32:   event RatioSet(uint192 indexed oldRatio, uint192 indexed newRatio);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L22
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L32

```solidity
protocol\contracts\interfaces\IMain.sol:
        ///@audit oldVal, newVal
  52:   event UnfreezeAtSet(uint48 indexed oldVal, uint48 indexed newVal);

        ///@audit oldDuration, newDuration
  57:   event ShortFreezeDurationSet(uint48 indexed oldDuration, uint48 indexed newDuration);

        ///@audit oldDuration, newDuration
  62:   event LongFreezeDurationSet(uint48 indexed oldDuration, uint48 indexed newDuration);

        ///@audit oldVal, newVal
  67:   event PausedSet(bool indexed oldVal, bool indexed newVal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L52-L67

```solidity
protocol\contracts\interfaces\IRewardable.sol:
             ///@audit amount
  14:     event RewardsClaimed(IERC20 indexed erc20, uint256 indexed amount);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRewardable.sol#L14

```solidity
protocol\contracts\interfaces\IRToken.sol:
        ///@audit oldVal, newVal  
  90:   event IssuanceRateSet(uint192 indexed oldVal, uint192 indexed newVal);

        ///@audit oldVal, newVal  
  93:   event ScalingRedemptionRateSet(uint192 indexed oldVal, uint192 indexed newVal);

        ///@audit oldVal, newVal
  96:   event RedemptionRateFloorSet(uint256 indexed oldVal, uint256 indexed newVal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L90-L96

```solidity
protocol\contracts\interfaces\IStRSR.sol:
        ///@audit oldVal, newVal 
  69:   event ExchangeRateSet(uint192 indexed oldVal, uint192 indexed newVal);

        ///@audit rsrAmt  
  72:   event RewardsPaid(uint256 indexed rsrAmt);

        ///@audit newEra
  75:   event AllBalancesReset(uint256 indexed newEra);

        ///@audit newEra  
  77:   event AllUnstakingReset(uint256 indexed newEra);

        ///@audit oldVal, newVal  
  79:   event UnstakingDelaySet(uint48 indexed oldVal, uint48 indexed newVal);

        ///@audit oldVal, newVal  
  80:   event RewardPeriodSet(uint48 indexed oldVal, uint48 indexed newVal);

        ///@audit oldVal, newVal  
  81:   event RewardRatioSet(uint192 indexed oldVal, uint192 indexed newVal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L69-L81

```solidity
protocol\contracts\interfaces\ITrading.sol:
        ///@audit oldVal, newVal
  16:   event MaxTradeSlippageSet(uint192 indexed oldVal, uint192 indexed newVal);

        ///@audit oldVal, newVal
  17:   event MinTradeVolumeSet(uint192 indexed oldVal, uint192 indexed newVal);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L16-L17


### [G-11] Use ``require`` instead of ``assert``

The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

20 results - 12 files:
```solidity
protocol\contracts\p1\BackingManager.sol:
  249:    assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249

```solidity
protocol\contracts\p1\BasketHandler.sol:
  556:    assert(targetIndex < targetsLength);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556

```solidity
protocol\contracts\p1\StRSR.sol:
  696:    assert(totalStakes + amount < type(uint224).max);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696

```solidity
protocol\contracts\p1\mixins\RecollateralizationLib.sol:
  110:    assert(doTrade);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110

```solidity
protocol\contracts\p1\mixins\RewardableLib.sol:
   78:    assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);

  102:    assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78

```solidity
protocol\contracts\p1\mixins\TradeLib.sol:
   44:    assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);

  108:    assert(

  168:    assert(errorCode == 0x11 || errorCode == 0x12);

  170:    assert(keccak256(reason) == UIntOutofBoundsHash);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44

```solidity
protocol\contracts\p1\mixins\Trading.sol:
  114:    assert(address(trades[sell]) == address(0));
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L114

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  345:    assert(amt == amountToWithdraw);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L345

```solidity
protocol\contracts\plugins\assets\Asset.sol:
   98:    assert(low == 0);

  112:    assert(low <= high);

  147:    assert(lotLow <= lotHigh);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L98

```solidity
protocol\contracts\plugins\assets\FiatCollateral.sol:
  137:    assert(low == 0);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L137

```solidity
protocol\contracts\plugins\assets\RTokenAsset.sol:
  74:     assert(low <= high);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L74

```solidity
protocol\contracts\plugins\trading\GnosisTrade.sol:
   63:    assert(status == TradeStatus.PENDING);

   98:    assert(origin_ != address(0));

  182:    assert(isAuctionCleared());
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L63


### [G-12] External visibility can be used in public functions

The appearance of the following functions can be changed from public to external.

15 results - 7 files:
```solidity
protocol\contracts\mixins\Auth.sol:
  107:    function pausedOrFrozen() public view returns (bool) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L107

```solidity
protocol\contracts\mixins\Versioned.sol:
  11:     function version() public pure virtual override returns (string memory) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Versioned.sol#L11

```solidity
protocol\contracts\p1\StRSR.sol:
  604:    function totalSupply() public view returns (uint256) {

  651:    function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {

  657:    function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {

  766     function permit(
  774:    ) public {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L604

```solidity
protocol\contracts\p1\StRSRVotes.sol:
   63:    function numCheckpoints(address account) public view returns (uint48) {

   71:    function getVotes(address account) public view returns (uint256) {

   76:    function getPastVotes(address account, uint256 blockNumber) public view returns (uint256) {

   82:    function getPastTotalSupply(uint256 blockNumber) public view returns (uint256) {

   88:    function getPastEra(uint256 blockNumber) public view returns (uint256) {

  118     function delegateBySig(
  125:    ) public {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L63

```solidity
protocol\contracts\plugins\assets\Asset.sol:
  86:     function refresh() public virtual override {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L86

```solidity
protocol\contracts\plugins\assets\ATokenFiatCollateral.sol:
  45:     function refPerTok() public view override returns (uint192) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L45

```solidity
protocol\contracts\plugins\assets\RTokenAsset.sol:
  63:     function refresh() public virtual override {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L63



### [G-13] Don't use _msgSender() if not supporting EIP-2771

Use ``msg.sender`` if the code does not implement EIP-2771 trusted forwarder support.

Reference: https://eips.ethereum.org/EIPS/eip-2771

35 results - 8 files:
```solidity
protocol\contracts\mixins\Auth.sol:
  122:    _revokeRole(SHORT_FREEZER, _msgSender());

  136:    longFreezes[_msgSender()] -= 1; // reverts on underflow

  139:    if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L122

```solidity
protocol\contracts\p1\BasketHandler.sol:
  168:    require(_msgSender() == address(assetRegistry), "asset registry only");

  187:    main.hasRole(OWNER, _msgSender()) ||
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L168

```solidity
protocol\contracts\p1\Broker.sol:
  125:    require(trades[_msgSender()], "unrecognized trade contract");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L125

```solidity
protocol\contracts\p1\RToken.sol:
  178:    issue(_msgSender(), amtRToken);

  196:    address issuer = _msgSender(); // OK to save: it can't be changed in reentrant runs

  558:    require(_msgSender() == address(backingManager), "not backing manager");

  570:    _burn(_msgSender(), amtRToken);

  581:    require(_msgSender() == address(backingManager), "not backing manager");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L178

```solidity
protocol\contracts\p1\StRSR.sol:
  375:    require(_msgSender() == address(backingManager), "not backing manager");

  421:    IERC20Upgradeable(address(rsr)).safeTransfer(_msgSender(), seizedRSR);

  633:    _approve(_msgSender(), spender, amount);

  646:    _spendAllowance(from, _msgSender(), amount);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L375

```solidity
protocol\contracts\p1\StRSRVotes.sol:
  115:    _delegate(_msgSender(), delegatee);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L115

```solidity
protocol\contracts\p1\mixins\Component.sol:
  52:     require(main.hasRole(OWNER, _msgSender()), "governance only");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L52

```solidity
protocol\contracts\plugins\aave\ERC20.sol:
  118:    _transfer(_msgSender(), recipient, amount);

  143:    _approve(_msgSender(), spender, amount);

  167:    _msgSender(),

  168:    _allowances[sender][_msgSender()].sub(

  189:    _approve(_msgSender(), spender, _allowances[_msgSender()][spender].add(addedValue));

  213:    _msgSender(),

  215:    _allowances[_msgSender()][spender].sub(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L118

### [G-14] Avoid contract existence checks by using solidity version 0.8.10 or later

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

43 results - 15 files:
```solidity
protocol\contracts\p1\BackingManager.sol:
  140:    uint256 bal = req.sell.erc20().balanceOf(address(this));
  175:    IERC20Upgradeable(address(rsr)).safeTransfer(
  177:    rsr.balanceOf(address(this))
  194:    uint192 totalSupply = _safeWrap(rToken.totalSupply()); // {rTok}
  203:    rToken.mint(address(this), uint256(rTok));
  228:    int8(IERC20Metadata(address(erc20s[i])).decimals())
  240:    if (toRToken[i] > 0) erc20.safeTransfer(address(rTokenTrader), toRToken[i]);
  241:    if (toRSR[i] > 0) erc20.safeTransfer(address(rsrTrader), toRSR[i]);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L140

```solidity
protocol\contracts\p1\BasketHandler.sol:
  421:    int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L421

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  89:     _setupDecimals(IERC20Detailed(aToken).decimals());
  298:    ASSET.safeTransferFrom(depositor, address(this), amount);
  301:    ATOKEN.safeTransferFrom(depositor, address(this), amount);
  347:    ATOKEN.safeTransfer(recipient, amountToWithdraw);
  457:    uint256 totBal = REWARD_TOKEN.balanceOf(address(this));
  464:    REWARD_TOKEN.safeTransfer(receiver, reward);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L89

```solidity
protocol\contracts\plugins\assets\CTokenFiatCollateral.sol:
  28:     referenceERC20Decimals = IERC20Metadata(erc20.underlying()).decimals();
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L28

```solidity
protocol\contracts\p1\Furnace.sol:
  43:     lastPayoutBal = rToken.balanceOf(address(this));
  82:     lastPayoutBal = rToken.balanceOf(address(this)) - amount;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L43

```solidity
protocol\contracts\p1\RevenueTrader.sol:
  56:     uint256 bal = erc20.balanceOf(address(this));
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L56


```solidity
protocol\contracts\p1\RToken.sol:
  271:    IERC20Upgradeable(erc20s[i]).safeTransferFrom(
  335:    IERC20Upgradeable(erc20s[i]).safeTransferFrom(issuer, address(this), deposits[i]);
  480:    uint256 bal = IERC20Upgradeable(erc20s[i]).balanceOf(address(backingManager));
  506:    IERC20Upgradeable(erc20s[i]).safeTransferFrom(
  629:    return battery.currentCharge(totalSupply());
  712:    IERC20Upgradeable(queue.tokens[i]).safeTransfer(account, amt[i]);
  794:    IERC20Upgradeable(queue.tokens[i]).safeTransfer(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L271

```solidity
protocol\contracts\p1\StRSR.sol:
  236:    IERC20Upgradeable(address(rsr)).safeTransferFrom(account, address(this), rsrAmount); 
  336:    IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);
  379:    uint256 rsrBalance = rsr.balanceOf(address(this));
  421:    IERC20Upgradeable(address(rsr)).safeTransfer(_msgSender(), seizedRSR);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L236

```solidity
protocol\contracts\p1\mixins\RewardableLib.sol:
   75:     erc20s[i].safeTransfer(address(bm), deltas[i]);
   97:    uint256 amt = erc20.balanceOf(address(this)) - liabilities[erc20];
   99:     erc20.safeTransfer(address(bm), amt);
  102:    assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L75

```solidity
protocol\contracts\plugins\assets\ATokenFiatCollateral.sol:
  54:     uint256 oldBal = stkAAVE.balanceOf(address(this));
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L54


```solidity
protocol\contracts\plugins\assets\CTokenSelfReferentialCollateral.sol:
  57:     uint256 oldBal = comp.balanceOf(address(this));
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L57


```solidity
protocol\contracts\plugins\trading\GnosisTrade.sol:
  191:    if (sellBal > 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);
  192:    if (boughtAmt > 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);
  217:    IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L191

```solidity
protocol\contracts\p1\Broker.sol:
  110:    IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L110

```solidity
protocol\contracts\p1\Distributor.sol:
  135:    IERC20Upgradeable(address(t.erc20)).safeTransferFrom(from, t.addrTo, t.amount);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L135

```solidity
protocol\contracts\plugins\assets\RTokenAsset.sol:
  49:     uint192 supply = _safeWrap(IRToken(address(erc20)).totalSupply());
  92:     uint192 supply = _safeWrap(IRToken(address(erc20)).totalSupply());
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L49

### [G-15] Add ``unchecked {}`` for subtractions where the operands cannot underflow because of a previous ``require`` or ``if`` statement

```
require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a } 
if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
```
This will stop the check for overflow and underflow so it will save gas.

2 results - 2 files:
```solidity
protocol\contracts\libraries\RedemptionBattery.sol:
  33     function discharge(
  41         uint256 charge = currentCharge(battery, supply);
  44:        require(amount <= charge, "redemption battery insufficient");
  48:         battery.lastCharge = charge - amount;
  49     }
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L48

```solidity
protocol\contracts\plugins\trading\GnosisTrade.sol:
  169     function settle()
  194         // Check clearing prices
  195:         if (sellBal < initBal) {
  196:             soldAmt = initBal - sellBal;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L195-L196

### [G-16] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}). Empty receive()/fallback() payable functions that are not used, can be removed to save deployment gas.

11 results - 9 files:
```solidity
protocol\contracts\p1\Main.sol:
  23:     constructor() initializer {}

  64:     function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L23


```solidity
protocol\contracts\p1\RToken.sol:
  838:     function requireNotPausedOrFrozen() private notPausedOrFrozen {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L838

```solidity
protocol\contracts\p1\mixins\Component.sol:
  25:     constructor() initializer {}

  57:     function _authorizeUpgrade(address newImplementation) internal view override governance {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L25

```solidity
protocol\contracts\plugins\aave\ERC20.sol:
  342     function _beforeTokenTransfer(
  343         address from,
  344         address to,
  345         uint256 amount
  346:     ) internal virtual {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L342-L346

```solidity
protocol\contracts\plugins\assets\Asset.sol:
  164:     function claimRewards() external virtual {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L164

```solidity
protocol\contracts\plugins\assets\ATokenFiatCollateral.sol:
  40:     constructor(CollateralConfig memory config) FiatCollateral(config) {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L40

```solidity
protocol\contracts\plugins\assets\RTokenAsset.sol:
  119:     function claimRewards() external virtual {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L119

```solidity
protocol\contracts\plugins\governance\Governance.sol:
  31     constructor(
  44:     {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L31-L44

```solidity
protocol\contracts\vendor\ERC20PermitUpgradeable.sol:
  67:     function __ERC20Permit_init_unchained(string memory) internal onlyInitializing {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L67


### [G-17] Use ``assembly`` to write _address storage values_ 

30 result - 14 files:
```solidity
protocol\contracts\mixins\ComponentRegistry.sol:
   36     function _setRToken(IRToken val) private {
   39:        rToken = val;

   44     function _setStRSR(IStRSR val) private {
   47:        stRSR = val;

   52     function _setAssetRegistry(IAssetRegistry val) private {
   55:        assetRegistry = val;

   60     function _setBasketHandler(IBasketHandler val) private {
   63:        basketHandler = val;

   68     function _setBackingManager(IBackingManager val) private {
   71:        backingManager = val;

   76     function _setDistributor(IDistributor val) private {
   79:        distributor = val;

   84     function _setRSRTrader(IRevenueTrader val) private {
   87:        rsrTrader = val;

   92     function _setRTokenTrader(IRevenueTrader val) private {
   95:        rTokenTrader = val;

  100     function _setFurnace(IFurnace val) private {
  103:        furnace = val;

  108     function _setBroker(IBroker val) private {
  111:        broker = val;
```
[ComponentRegistry.sol#L39](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L39), [ComponentRegistry.sol#L47](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L47), [ComponentRegistry.sol#L55](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L55), [ComponentRegistry.sol#L63](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L63), [ComponentRegistry.sol#L71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L71), [ComponentRegistry.sol#L79](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L79), [ComponentRegistry.sol#L87](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L87), [ComponentRegistry.sol#L95](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L95), [ComponentRegistry.sol#L103](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L103), [ComponentRegistry.sol#L111](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L111)

```solidity
protocol\contracts\p1\mixins\Component.sol:
  33      function __Component_init(IMain main_) internal onlyInitializing {
  36:         main = main_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L36

```solidity
protocol\contracts\p1\Broker.sol:
  51      function init(
  68:         gnosis = gnosis_;
  69:         tradeImplementation = tradeImplementation_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L68-L69

```solidity
protocol\contracts\p1\Deployer.sol:
  42      constructor(
  67:         rsr = rsr_;
  68:         gnosis = gnosis_;
  69:         rsrAsset = rsrAsset_;
  70:         implementations = implementations_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L67-L70

```solidity
protocol\contracts\p1\Main.sol:
  26      function init(
  37:         rsr = rsr_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L37

```solidity
protocol\contracts\p1\RevenueTrader.sol:
  23      function init(
  34:         tokenToBuy = tokenToBuy_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L34

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  78      constructor(
  84:         LENDING_POOL = pool;
  85:         ATOKEN = IERC20(aToken);
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L84-L85

```solidity
protocol\contracts\plugins\assets\Asset.sol:
  40      constructor(
  57:         erc20 = erc20_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L57

```solidity
protocol\contracts\plugins\assets\CTokenFiatCollateral.sol:
  25      constructor(CollateralConfig memory config, IComptroller comptroller_) FiatCollateral(config) {
  29:         comptroller = comptroller_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L29

```solidity
protocol\contracts\plugins\assets\CTokenNonFiatCollateral.sol:
  23      constructor(
  32:         targetUnitChainlinkFeed = targetUnitChainlinkFeed_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L32

```solidity
protocol\contracts\plugins\assets\CTokenSelfReferentialCollateral.sol:
  24      constructor(
  32:         comptroller = comptroller_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L32

```solidity
protocol\contracts\plugins\assets\EURFiatCollateral.sol:
  21      constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
  25:         uoaPerTargetFeed = uoaPerTargetFeed_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L25

```solidity
protocol\contracts\plugins\assets\NonFiatCollateral.sol:
  21      constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
  25:         uoaPerTargetFeed = uoaPerTargetFeed_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L25

```solidity
protocol\contracts\plugins\trading\GnosisTrade.sol:
   81     function init(
  100:        broker = broker_;
  101:        origin = origin_;
  102:        gnosis = gnosis_;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L100-L102

**Recommendation Code:**
```diff
protocol\contracts\plugins\trading\GnosisTrade.sol#L100
    81     function init(
- 100:        broker = broker_;                    
+                  assembly {                      
+                      sstore(broker.slot, broker_)
+                  }                               
```

### [G-18] Setting the _constructor_ to `payable`

You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of ```msg.value == 0``` and saves ```13 gas``` on deployment with no security risks.

17 results - 17 files:
```solidity
protocol\contracts\p1\Deployer.sol:
  42:     constructor(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L42

```solidity
protocol\contracts\p1\Main.sol:
  23:     constructor() initializer {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L23

```solidity
protocol\contracts\p1\mixins\Component.sol:
  25:     constructor() initializer {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L25

```solidity
protocol\contracts\plugins\aave\ERC20.sol:
  57:     constructor(string memory name, string memory symbol) public {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L57

```solidity
protocol\contracts\plugins\aave\ReentrancyGuard.sol:
  38:     constructor() internal {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L38

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  78:     constructor(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L78

```solidity
protocol\contracts\plugins\assets\Asset.sol:
  40:     constructor(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L40

```solidity
protocol\contracts\plugins\assets\ATokenFiatCollateral.sol:
  40:     constructor(CollateralConfig memory config) FiatCollateral(config) {}
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L40

```solidity
protocol\contracts\plugins\assets\CTokenFiatCollateral.sol:
  25:     constructor(CollateralConfig memory config, IComptroller comptroller_) FiatCollateral(config) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L25

```solidity
protocol\contracts\plugins\assets\CTokenNonFiatCollateral.sol:
  23:     constructor(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L23

```solidity
protocol\contracts\plugins\assets\CTokenSelfReferentialCollateral.sol:
  24:     constructor(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L24

```solidity
protocol\contracts\plugins\assets\EURFiatCollateral.sol:
  21:     constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L21

```solidity
protocol\contracts\plugins\assets\FiatCollateral.sol:
  63:     constructor(CollateralConfig memory config)
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L63

```solidity
protocol\contracts\plugins\assets\NonFiatCollateral.sol:
  21:     constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L21

```solidity
protocol\contracts\plugins\assets\RTokenAsset.sol:
  27:     constructor(IRToken erc20_, uint192 maxTradeVolume_) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L27

```solidity
protocol\contracts\plugins\assets\SelfReferentialCollateral.sol:
  18:     constructor(CollateralConfig memory config) FiatCollateral(config) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L18

```solidity
protocol\contracts\plugins\governance\Governance.sol:
  31:     constructor(
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L31

**Recommendation:**
Set the constructor to ```payable```


### [G-19] Use ``double require`` instead of using ``&&``

Using double require instead of operator && can save more gas
When having a require statement with 2 or more expressions needed, place the expression that cost less gas first.

14 results - 8 files:
```solidity
protocol\contracts\mixins\Auth.sol:
  181:         require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");

  188:         require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181

```solidity
protocol\contracts\p1\Broker.sol:
  134         require(
  135:             newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
  136             "invalid auctionLength"
  137         );
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L134-L137

```solidity
protocol\contracts\p1\Deployer.sol:
  48         require(
  49:             address(rsr_) != address(0) &&
  50:                 address(gnosis_) != address(0) &&
  51:                 address(rsrAsset_) != address(0) &&
  52:                 address(implementations_.main) != address(0) &&
  53:                 address(implementations_.trade) != address(0) &&
  54:                 address(implementations_.components.assetRegistry) != address(0) &&
  55:                 address(implementations_.components.backingManager) != address(0) &&
  56:                 address(implementations_.components.basketHandler) != address(0) &&
  57:                 address(implementations_.components.broker) != address(0) &&
  58:                 address(implementations_.components.distributor) != address(0) &&
  59:                 address(implementations_.components.furnace) != address(0) &&
  60:                 address(implementations_.components.rsrTrader) != address(0) &&
  61:                 address(implementations_.components.rTokenTrader) != address(0) &&
  62:                 address(implementations_.components.rToken) != address(0) &&
  63:                 address(implementations_.components.stRSR) != address(0),
  64:             "invalid address"
  65         );

  109:         require(owner != address(0) && owner != address(this), "invalid owner");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48-L65

```solidity
protocol\contracts\p1\Furnace.sol:
  89:         require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89

```solidity
protocol\contracts\p1\RevenueTrader.sol:
  72:         require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72

```solidity
protocol\contracts\p1\RToken.sol:
  410:         require(queue.left <= endId && endId <= queue.right, "out of range");

  590:         require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");

  741:         require(queue.left <= endId && endId <= queue.right, "out of range");

  813:         require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410

```solidity
protocol\contracts\p1\StRSR.sol:
  813:         require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");

  821:         require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813

```solidity
protocol\contracts\plugins\assets\Asset.sol:
  50:         require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L50


**Recommendation Code:**
 ```diff
protocol\contracts\plugins\assets\Asset.sol#L50
- 50:         require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
+ 50:         require(oracleError_ > 0, "oracle error out of range");
+ 50:         require(oracleError_ < FIX_ONE, "oracle error out of range");
```

### [G-20] Use nested if and, avoid multiple check combinations

Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.

16 results - 8 files:
```solidity
protocol\contracts\libraries\RedemptionBattery.sol:
  38:    if (battery.redemptionRateFloor == 0 && battery.scalingRedemptionRate == 0) return;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L38

```solidity
protocol\contracts\libraries\String.sol:
  16:    if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L16

```solidity
protocol\contracts\p1\BasketHandler.sol:
  564:   if (goodCollateral(config.targetNames[erc20], erc20) && targetWeight.gt(FIX_ZERO)) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L564

```solidity
protocol\contracts\p1\Distributor.sol:
  168:   if (share.rsrDist == 0 && share.rTokenDist == 0) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L168

```solidity
protocol\contracts\p1\RToken.sol:
  202:   if (queue.basketNonce > 0 && queue.basketNonce != basketNonce) {

  251:   if (
  252        // D18{blocks} <= D18{1} * {blocks}
  253:       vestingEnd <= FIX_ONE_256 * block.number &&
  254:       queue.left == queue.right &&
  255        status == CollateralStatus.SOUND
  256    ) {

  691:   if (queue.left == left && right == queue.right) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L202

```solidity
protocol\contracts\p1\StRSRVotes.sol:
  171:   if (src != dst && amount > 0) {

  202:   if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L171

```solidity
protocol\contracts\p1\mixins\RecollateralizationLib.sol:
   99:   if (
  100       trade.sellPrice == 0 ||
  101:       (trade.sell.isCollateral() &&
  102           ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)
  103    ) {


  257:   if (
  258:       components.bh.quantity(reg.erc20s[i]) == 0 &&
  259        !TradeLib.isEnoughToSell(reg.assets[i], bal, lotLow, rules.minTradeVolume)
  260    ) continue;


  362:   if (
  363:       isBetterSurplus(maxes, status, delta) &&
  364        TradeLib.isEnoughToSell(
  365            reg.assets[i],
  366            bal.minus(needed),
  367            lotLow,
  368            rules.minTradeVolume
  369        )
  370    ) {


  401:   if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {


  410:   if (
  411:       high > 0 &&
  412        TradeLib.isEnoughToSell(rsrAsset, rsrAvailable, lotLow, rules.minTradeVolume)
  413    ) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L99-L103

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  422:   if (supply > 0 && rewardsAccrued > 0) {

  544:   if (supply != 0 && fresh) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L422

**Recomendation Code:**
```diff
protocol\contracts\plugins\aave\StaticATokenLM.sol#L422:
- 422:   if (supply > 0 && rewardsAccrued > 0) {
+ 422:   if (supply > 0) {                      
+ 422:     if (rewardsAccrued > 0) {            
+           }                                   
+         }                                     
```

### [G-21] Sort Solidity operations using short-circuit mode

Short-circuiting is a solidity contract development model that uses ```OR/AND``` logic to sequence different cost operations. It puts low gas cost operations in the front and high gas cost operations in the back, so that if the front is low If the cost operation is feasible, you can skip (short-circuit) the subsequent high-cost Ethereum virtual machine operation.

```
//f(x) is a low gas cost operation 
//g(y) is a high gas cost operation 

//Sort operations with different gas costs as follows 
f(x) || g(y) 
f(x) && g(y)
```
16 results - 11 files:
```solidity
protocol\contracts\libraries\Fixed.sol:
  319:    if (x_ == FIX_ONE || y == 0) return FIX_ONE;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L319

```solidity
protocol\contracts\p1\AssetRegistry.sol:
  151     require(
  152:      !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L151-L154

```solidity
protocol\contracts\p1\BasketHandler.sol:
  186     require(
  187:         main.hasRole(OWNER, _msgSender()) ||
  188:             (status() == CollateralStatus.DISABLED && !main.pausedOrFrozen()),

  284:    if (disabled || size == 0) return CollateralStatus.DISABLED;

  564:    if (goodCollateral(config.targetNames[erc20], erc20) && targetWeight.gt(FIX_ZERO)) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L186-L190

```solidity
protocol\contracts\p1\RToken.sol:
  623:    require(index >= item.left && index < item.right, "out of range");

  741:    require(queue.left <= endId && endId <= queue.right, "out of range");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L623

```solidity
protocol\contracts\p1\StRSR.sol:
  310:    if (endId == 0 || firstId >= endId) return;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L310

```solidity
protocol\contracts\p1\StRSRVotes.sol:
  171:    if (src != dst && amount > 0) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L171

```solidity
protocol\contracts\p1\mixins\RecollateralizationLib.sol:
  94:     if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L94

```solidity
protocol\contracts\plugins\assets\FiatCollateral.sol:
  142:    if (pegPrice < pegBottom || pegPrice > pegTop || low == 0) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L142

```solidity
protocol\contracts\p1\Distributor.sol:
  182:    require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L182

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  318     require(
  319:       staticAmount == 0 || dynamicAmount == 0,
  320        StaticATokenErrors.ONLY_ONE_AMOUNT_FORMAT_ALLOWED
  321     );


  477     require(
  478:       msg.sender == onBehalfOf || msg.sender == INCENTIVES_CONTROLLER.getClaimer(onBehalfOf),
  479        StaticATokenErrors.INVALID_CLAIMER
  480     );

 544:    if (supply != 0 && fresh) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L318-L321

```solidity
protocol\contracts\plugins\assets\OracleLib.sol:
  22:     if (updateTime == 0 || answeredInRound < roundId) {
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L22


### [G-22] `x += y (x -= y)` costs more gas than `x = x + y (x = x - y)` for state variables

8 results - 3 files:
```solidity
protocol\contracts\p1\BasketHandler.sol:
  636:    nonce += 1;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L636

```solidity
protocol\contracts\p1\Furnace.sol:
  81:     lastPayout += numPeriods * period;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L81

```solidity
protocol\contracts\p1\StRSR.sol:
  229:    stakeRSR += rsrAmount;

  402:    draftRSR -= draftRSRToTake;

  516:    payoutLastPaid += numPeriods * rewardPeriod;

  549:    draftRSR += rsrAmount;

  699:    totalStakes += amount;

  721:    totalStakes -= amount;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L229

**Recommendation code:**
```diff
protocol\contracts\p1\StRSR.sol#L229
- 229:    stakeRSR += rsrAmount;           
+ 229:    stakeRSR = stakeRSR  + rsrAmount;
```

`x += y (x -= y)` costs more gas than `x = x  + y (x = x - y)` for state variables.


### [G-23] Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via ```Method ID```. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because ```22 gas``` are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

**Context:** 
All Contracts


**Recommendation:** 
Find a lower ```method ID``` name for the most called functions for example Call() vs. Call1() is cheaper by ```22 gas```
For example, the function IDs in the ``` BasketHandler.sol ``` contract will be the most used; A lower method ID may be given.

**Proof of Consept:**
https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

BasketHandler.sol function names can be named and sorted according to METHOD ID

```js
Sighash   |   Function Signature
========================
d580a7dc  =>  empty(Basket)
ea03ae43  =>  setFrom(Basket,Basket)
cb543b31  =>  add(Basket,IERC20,uint192)
caa8472b  =>  init(IMain)
e27f3bef  =>  disableBasket()
8a55015b  =>  refreshBasket()
4e3f5e04  =>  setPrimeBasket(IERC20[],uint192[])
4a76324f  =>  setBackupConfig(bytes32,uint256,IERC20[])
e45a5b2d  =>  fullyCollateralized()
200d2ed2  =>  status()
9cf53741  =>  quantity(IERC20)
a035b1fe  =>  price()
271181ec  =>  lotPrice()
560a3a8b  =>  _price(bool)
298b05d6  =>  quantityMulPrice(uint192,uint192)
507a11e9  =>  basketTokens()
88d6bc53  =>  quote(uint192,RoundingMode)
aa47955e  =>  basketsHeldBy(address)
d22eae12  =>  _switchBasket()
93d9dfbc  =>  requireValidCollArray(IERC20[])
3fc5541c  =>  goodCollateral(bytes32,IERC20)
0984fa51  =>  getPrimeBasket()
f95683cd  =>  getBackupConfig(bytes32)
```

### [G-24] Use a more recent version of solidity

> Solidity 0.8.10 has a useful change that reduced gas costs of external calls which expect a return value. 
> 
> In 0.8.15 the conditions necessary for inlining are relaxed. Benchmarks show that the change significantly decreases the bytecode size (which impacts the deployment cost) while the effect on the runtime gas usage is smaller.
> 
> In 0.8.17 prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call; Simplify the starting offset of zero-length operations to zero. More efficient overflow checks for multiplication.

There are 77 contracts in scope. I recommend that you upgrade the versions of all covered contracts to the latest version of solidity, 0.8.17.

The versions of some of these agreements are as seen below.
```solidity
protocol\contracts\interfaces\IAsset.sol:
  2: pragma solidity 0.8.9;

protocol\contracts\plugins\aave\ERC20.sol:
  3: pragma solidity ^0.6.0;

protocol\contracts\plugins\aave\IAaveIncentivesController.sol:
  2: pragma solidity 0.6.12;

protocol\contracts\plugins\aave\ReentrancyGuard.sol:
  3: pragma solidity >=0.6.0 <0.8.0;
```

### [G-25] Upgrade Solidity's optimizer

Make sure Solidity’s optimizer is enabled. It reduces gas costs. If you want to gas optimize for contract deployment (costs less to deploy a contract) then set the Solidity optimizer at a low number. If you want to optimize for run-time gas costs (when functions are called on a contract) then set the optimizer to a high number.

Set the optimization value higher than 800 in your hardhat.config.ts file.

```js
protocol\hardhat.config.ts:

  25: const settings = useEnv('NO_OPT') ? {} : { optimizer: { enabled: true, runs: 200 } }
  
  69:   solidity: {
  70:     compilers: [
  71:       {
  72:         version: '0.8.9',
  73:         settings,
  74:       },
  75:       {
  76:         version: '0.6.12',
  77:       },
  78:       {
  79:         version: '0.4.24',
  80:       },
```

### [G-26] ``>=`` costs less gas than ``>``

The compiler uses opcodes GT and ISZERO for solidity code that uses >, but only requires LT for >=, which saves 3 gas

13 results - 8 files:
```solidity
protocol\contracts\libraries\Fixed.sol:
  142:    return x > y ? x : y;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L142

```solidity
protocol\contracts\libraries\RedemptionBattery.sol:
  69:     uint256 maxCharge = amtPerHour > supply ? supply : amtPerHour;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L69

```solidity
protocol\contracts\p1\BackingManager.sol:
  200:    uint192 rTok = (needed > 0) ? extraBUs.mulDiv(totalSupply, needed) : extraBUs;
 ```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L200

```solidity
protocol\contracts\p1\Broker.sol:
  102:    uint256 maxQty = (req.minBuyAmount > req.sellAmount) ? req.minBuyAmount : req.sellAmount;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L102

```solidity
protocol\contracts\p1\RToken.sol:
  234:    totalSupply() > 0 ? mulDiv256(basketsNeeded, amtRToken, totalSupply()) : amtRToken
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L234

```solidity
protocol\contracts\p1\StRSR.sol:
  315:    uint192 oldDrafts = firstId > 0 ? queue[firstId - 1].drafts : 0;
  559:    uint192 oldDrafts = index > 0 ? queue[index - 1].drafts : 0;
  560:    uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L315

```solidity
protocol\contracts\p1\mixins\TradeLib.sol:
   55:    uint192 s = trade.sellAmount > maxSell ? maxSell : trade.sellAmount; // {sellTok}
  183:    return size > 0 ? size : 1;
  192:    return size > 0 ? size : 1;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L55

```solidity
protocol\contracts\plugins\aave\StaticATokenLM.sol:
  331:    amountToBurn = (staticAmount > userBalance) ? userBalance : staticAmount;
 
  335:    amountToWithdraw = (dynamicAmount > dynamicUserBalance)
  336        ? dynamicUserBalance
  337        : dynamicAmount;
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L331