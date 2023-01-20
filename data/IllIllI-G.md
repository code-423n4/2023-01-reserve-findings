## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Don't apply the same value to state variables | 1 |  - |
| [G&#x2011;02] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 4 |  - |
| [G&#x2011;03] | State variables only set in the constructor should be declared `immutable` | 6 |  12582 |
| [G&#x2011;04] | State variables can be packed into fewer storage slots | 1 |  - |
| [G&#x2011;05] | Structs can be packed into fewer storage slots | 1 |  - |
| [G&#x2011;06] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 8 |  960 |
| [G&#x2011;07] | Using `storage` instead of `memory` for structs/arrays saves gas | 1 |  4200 |
| [G&#x2011;08] | Avoid contract existence checks by using low level calls | 67 |  6700 |
| [G&#x2011;09] | State variables should be cached in stack variables rather than re-reading them from storage | 60 |  5820 |
| [G&#x2011;10] | Multiple accesses of a mapping/array should use a local variable cache | 1 |  42 |
| [G&#x2011;11] | The result of function calls should be cached rather than re-calling the function | 12 |  - |
| [G&#x2011;12] | `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables | 10 |  1130 |
| [G&#x2011;13] | `internal` functions only called once can be inlined to save gas | 2 |  40 |
| [G&#x2011;14] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 6 |  510 |
| [G&#x2011;15] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 51 |  3060 |
| [G&#x2011;16] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 2 |  - |
| [G&#x2011;17] | Optimize names to save gas | 49 |  1078 |
| [G&#x2011;18] | Use a more recent version of solidity | 7 |  - |
| [G&#x2011;19] | Use a more recent version of solidity | 1 |  - |
| [G&#x2011;20] | `>=` costs less gas than `>` | 3 |  9 |
| [G&#x2011;21] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 1 |  5 |
| [G&#x2011;22] | Splitting `require()` statements that use `&&` saves gas | 15 |  45 |
| [G&#x2011;23] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 68 |  - |
| [G&#x2011;24] | Using `private` rather than `public` for constants, saves gas | 11 |  - |
| [G&#x2011;25] | `require()` or `revert()` statements that check input arguments should be at the top of the function | 2 |  - |
| [G&#x2011;26] | Empty blocks should be removed or emit something | 3 |  - |
| [G&#x2011;27] | Use custom errors rather than `revert()`/`require()` strings to save gas | 25 |  - |
| [G&#x2011;28] | Functions guaranteed to revert when called by normal users can be marked `payable` | 2 |  42 |
| [G&#x2011;29] | Don't use `_msgSender()` if not supporting EIP-2771 | 35 |  560 |
| [G&#x2011;30] | `public` functions not called by the contract should be declared `external` instead | 2 |  - |

Total: 457 instances over 30 issues with **36783 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  Don't apply the same value to state variables
If `_whenDefault` is already `NEVER`, it'll save 2100 gas to not set it to that value again

*There is 1 instance of this issue:*

```solidity
File: /contracts/plugins/assets/FiatCollateral.sol

189:             if (sum >= NEVER) _whenDefault = NEVER;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L189

### [G&#x2011;02]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There are 4 instances of this issue:*

```solidity
File: contracts/p1/StRSR.sol

81        mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {drafts}
82:       mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L81-L82

```solidity
File: contracts/p1/StRSRVotes.sol

38        mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}
39        // `_totalSupplyCheckpoints[era]` is the history of totalSupply values during era `era`
40:       mapping(uint256 => Checkpoint[]) private _totalSupplyCheckpoints; // {qStRSR}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L38-L40

```solidity
File: contracts/plugins/aave/ERC20.sol

38        mapping(address => uint256) private _balances;
39    
40:       mapping(address => mapping(address => uint256)) private _allowances;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L38-L40

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

74        mapping(address => uint256) private _userSnapshotRewardsPerToken;
75        // user => unclaimedRewards (in RAYs)
76:       mapping(address => uint256) private _unclaimedRewards;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L74-L76

### [G&#x2011;03]  State variables only set in the constructor should be declared `immutable`
Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmacces - **100 gas**) with a `PUSH32` (**3 gas**). 

While `string`s are not value types, and therefore cannot be `immutable`/`constant` if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract `abstract` with `virtual` functions for the `string` accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

*There are 6 instances of this issue:*

```solidity
File: contracts/p1/Deployer.sol

/// @audit implementations (constructor)
70:           implementations = implementations_;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L70

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

/// @audit LENDING_POOL (constructor)
84:           LENDING_POOL = pool;

/// @audit INCENTIVES_CONTROLLER (constructor)
95:                   INCENTIVES_CONTROLLER = incentivesController;

/// @audit ATOKEN (constructor)
85:           ATOKEN = IERC20(aToken);

/// @audit ASSET (constructor)
100:          ASSET = IERC20(IAToken(aToken).UNDERLYING_ASSET_ADDRESS());

/// @audit REWARD_TOKEN (constructor)
96:                   REWARD_TOKEN = IERC20(INCENTIVES_CONTROLLER.REWARD_TOKEN());

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L84

### [G&#x2011;04]  State variables can be packed into fewer storage slots
If variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

*There is 1 instance of this issue:*

```solidity
File: contracts/p1/StRSR.sol

/// @audit Variable ordering with 21 slots instead of the current 22:
///           string(32):name, string(32):symbol, uint256(32):era, mapping(32):stakes, uint256(32):totalStakes, uint256(32):stakeRSR, mapping(32):_allowances, uint256(32):draftEra, mapping(32):draftQueues, mapping(32):firstRemainingDraft, uint256(32):totalDrafts, uint256(32):draftRSR, mapping(32):_nonces, uint256(32):rsrRewardsAtLastPayout, uint192(24):stakeRate, uint48(6):unstakingDelay, uint192(24):draftRate, uint48(6):rewardPeriod, uint192(24):rewardRatio, uint48(6):payoutLastPaid, address(20):assetRegistry, address(20):backingManager, address(20):basketHandler, user-defined(20):rsr
42:       string public name; // mutable

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L42

### [G&#x2011;05]  Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

*There is 1 instance of this issue:*

```solidity
File: contracts/interfaces/IGnosis.sol

/// @audit Variable ordering with 11 slots instead of the current 12:
///           uint256(32):orderCancellationEndDate, uint256(32):auctionEndDate, bytes32(32):initialAuctionOrder, uint256(32):minimumBiddingAmountPerOrder, uint256(32):interimSumBidAmount, bytes32(32):interimOrder, bytes32(32):clearingPriceOrder, uint256(32):feeNumerator, uint256(32):minFundingThreshold, user-defined(20):auctioningToken, uint96(12):volumeClearingPriceOrder, user-defined(20):biddingToken, bool(1):minFundingThresholdNotReached, bool(1):isAtomicClosureAllowed
6     struct GnosisAuctionData {
7         IERC20 auctioningToken;
8         IERC20 biddingToken;
9         uint256 orderCancellationEndDate;
10        uint256 auctionEndDate;
11        bytes32 initialAuctionOrder;
12        uint256 minimumBiddingAmountPerOrder;
13        uint256 interimSumBidAmount;
14        bytes32 interimOrder;
15        bytes32 clearingPriceOrder;
16        uint96 volumeClearingPriceOrder;
17        bool minFundingThresholdNotReached;
18        bool isAtomicClosureAllowed;
19        uint256 feeNumerator;
20        uint256 minFundingThreshold;
21:   }

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol#L6-L21

### [G&#x2011;06]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 8 instances of this issue:*

```solidity
File: contracts/p1/Broker.sol

/// @audit req
85:       function openTrade(TradeRequest memory req) external notPausedOrFrozen returns (ITrade) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L85

```solidity
File: contracts/p1/Deployer.sol

/// @audit name
/// @audit symbol
/// @audit params
102       function deploy(
103           string memory name,
104           string memory symbol,
105           string calldata mandate,
106           address owner,
107           DeploymentParams memory params
108:      ) external returns (address) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L102-L108

```solidity
File: contracts/p1/Distributor.sol

/// @audit share
61:       function setDistribution(address dest, RevenueShare memory share) external governance {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L61

```solidity
File: contracts/plugins/governance/Governance.sol

/// @audit targets
/// @audit values
/// @audit calldatas
105       function cancel(
106           address[] memory targets,
107           uint256[] memory values,
108           bytes[] memory calldatas,
109:          bytes32 descriptionHash

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L105-L109

### [G&#x2011;07]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There is 1 instance of this issue:*

```solidity
File: contracts/p1/Distributor.sol

134:              Transfer memory t = transfers[i];

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L134

### [G&#x2011;08]  Avoid contract existence checks by using low level calls
Prior to 0.8.10 the compiler inserted extra code, including `EXTCODESIZE` (**100 gas**), to check for contract existence for external function calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value. Similar behavior can be achieved in earlier versions by using low-level calls, since low level calls never check for contract existence

*There are 67 instances of this issue:*

```solidity
File: contracts/libraries/Permit.sol

/// @audit isValidSignature()
19:                   IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L19

```solidity
File: contracts/p1/BackingManager.sol

/// @audit safeApprove()
75:           IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);

/// @audit safeApprove()
76:           IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);

/// @audit balanceOf()
140:                      uint256 bal = req.sell.erc20().balanceOf(address(this));

/// @audit safeTransfer()
175:              IERC20Upgradeable(address(rsr)).safeTransfer(

/// @audit gt()
225:              if (asset.bal(address(this)).gt(req)) {

/// @audit shiftl_toUint()
/// @audit minus()
227:                  uint256 delta = asset.bal(address(this)).minus(req).shiftl_toUint(

/// @audit decimals()
228:                      int8(IERC20Metadata(address(erc20s[i])).decimals())

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L75

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit isCollateral()
230:              require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");

/// @audit targetName()
236:              names[i] = assetRegistry.toColl(erc20s[i]).targetName();

/// @audit isCollateral()
265:              require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");

/// @audit status()
287:              CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();

/// @audit lotPrice()
342:                  ? assetRegistry.toAsset(basket.erc20s[i]).lotPrice()

/// @audit price()
343:                  : assetRegistry.toAsset(basket.erc20s[i]).price();

/// @audit shiftl_toUint()
420:              quantities[i] = quantity(basket.erc20s[i]).mul(amount, rounding).shiftl_toUint(

/// @audit decimals()
421:                  int8(IERC20Metadata(address(basket.erc20s[i])).decimals()),

/// @audit targetPerRef()
568:                      targetWeight.div(assetRegistry.toColl(erc20).targetPerRef(), CEIL)

/// @audit mulu()
/// @audit targetPerRef()
618:                          needed.div(assetRegistry.toColl(erc20).targetPerRef().mulu(size), CEIL)

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L230

```solidity
File: contracts/p1/Broker.sol

/// @audit clone()
97:           GnosisTrade trade = GnosisTrade(address(tradeImplementation).clone());

/// @audit safeTransferFrom()
110:          IERC20Upgradeable(address(req.sell.erc20())).safeTransferFrom(

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L97

```solidity
File: contracts/p1/Distributor.sol

/// @audit safeTransferFrom()
135:              IERC20Upgradeable(address(t.erc20)).safeTransferFrom(from, t.addrTo, t.amount);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L135

```solidity
File: contracts/p1/Furnace.sol

/// @audit powu()
77:           uint192 payoutRatio = FIX_ONE.minus(FIX_ONE.minus(ratio).powu(numPeriods));

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L77

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

/// @audit status()
102:                  ICollateral(address(trade.sell)).status() != CollateralStatus.SOUND)

/// @audit status()
357:                      status = ICollateral(address(reg.assets[i])).status();

/// @audit plus()
404:              uint192 rsrAvailable = rsrAsset.bal(address(components.bm)).plus(

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L102

```solidity
File: contracts/p1/mixins/RewardableLib.sol

/// @audit functionDelegateCall()
29:               address(registry.assets[i]).functionDelegateCall(

/// @audit functionDelegateCall()
42:           address(asset).functionDelegateCall(

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L29

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit toAsset()
91:           RewardableLibP1.claimRewardsSingle(main.assetRegistry().toAsset(erc20));

/// @audit safeApprove()
116:          IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);

/// @audit safeApprove()
117:          IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L91

```solidity
File: contracts/p1/RevenueTrader.sol

/// @audit safeApprove()
61:               IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), 0);

/// @audit safeApprove()
62:               IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), bal);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L61

```solidity
File: contracts/p1/RToken.sol

/// @audit safeTransferFrom()
271:                  IERC20Upgradeable(erc20s[i]).safeTransferFrom(

/// @audit safeTransferFrom()
335:              IERC20Upgradeable(erc20s[i]).safeTransferFrom(issuer, address(this), deposits[i]);

/// @audit balanceOf()
480:              uint256 bal = IERC20Upgradeable(erc20s[i]).balanceOf(address(backingManager));

/// @audit safeTransferFrom()
506:              IERC20Upgradeable(erc20s[i]).safeTransferFrom(

/// @audit safeTransfer()
712:              IERC20Upgradeable(queue.tokens[i]).safeTransfer(account, amt[i]);

/// @audit safeTransfer()
794:              IERC20Upgradeable(queue.tokens[i]).safeTransfer(

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L271

```solidity
File: contracts/p1/StRSR.sol

/// @audit balanceOf()
181:          rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));

/// @audit safeTransferFrom()
236:          IERC20Upgradeable(address(rsr)).safeTransferFrom(account, address(this), rsrAmount);

/// @audit safeTransfer()
336:          IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);

/// @audit safeTransfer()
421:          IERC20Upgradeable(address(rsr)).safeTransfer(_msgSender(), seizedRSR);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L181

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

/// @audit decimals()
89:           _setupDecimals(IERC20Detailed(aToken).decimals());

/// @audit getIncentivesController()
91:           try IAToken(aToken).getIncentivesController() returns (

/// @audit UNDERLYING_ASSET_ADDRESS()
100:          ASSET = IERC20(IAToken(aToken).UNDERLYING_ASSET_ADDRESS());

/// @audit wadToRay()
394:              uint256 rewardsAccrued = lifetimeRewards.sub(_lifetimeRewards).wadToRay();

/// @audit wadToRay()
420:          uint256 rewardsAccrued = lifetimeRewards.sub(_lifetimeRewards).wadToRay();

/// @audit wadToRay()
550:              uint256 rewardsAccrued = lifetimeRewards.sub(_lifetimeRewards).wadToRay();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L89

```solidity
File: contracts/plugins/assets/ATokenFiatCollateral.sol

/// @audit rate()
46:           uint256 rateInRAYs = IStaticAToken(address(erc20)).rate(); // {ray ref/tok}

/// @audit REWARD_TOKEN()
53:           IERC20 stkAAVE = IStaticAToken(address(erc20)).REWARD_TOKEN();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L46

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

/// @audit decimals()
28:           referenceERC20Decimals = IERC20Metadata(erc20.underlying()).decimals();

/// @audit exchangeRateCurrent()
37:           ICToken(address(erc20)).exchangeRateCurrent();

/// @audit exchangeRateStored()
45:           uint256 rate = ICToken(address(erc20)).exchangeRateStored();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L28

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

/// @audit exchangeRateCurrent()
40:           ICToken(address(erc20)).exchangeRateCurrent();

/// @audit exchangeRateStored()
48:           uint256 rate = ICToken(address(erc20)).exchangeRateStored();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L40

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

/// @audit totalSupply()
49:           uint192 supply = _safeWrap(IRToken(address(erc20)).totalSupply());

/// @audit totalSupply()
92:           uint192 supply = _safeWrap(IRToken(address(erc20)).totalSupply());

/// @audit basketsNeeded()
131:              range.bottom = IRToken(address(erc20)).basketsNeeded();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L49

```solidity
File: contracts/plugins/governance/Governance.sol

/// @audit getPastEra()
172:          uint256 pastEra = IStRSRVotes(address(token)).getPastEra(startBlock);

/// @audit currentEra()
173:          uint256 currentEra = IStRSRVotes(address(token)).currentEra();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L172

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit safeApprove()
136:          IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), 0);

/// @audit safeApprove()
137:          IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), initBal);

/// @audit safeTransfer()
191:          if (sellBal > 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);

/// @audit safeTransfer()
192:          if (boughtAmt > 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);

/// @audit safeTransfer()
217:          IERC20Upgradeable(address(erc20)).safeTransfer(origin, erc20.balanceOf(address(this)));

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L136

### [G&#x2011;09]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replaces each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 60 instances of this issue:*

```solidity
File: contracts/mixins/Auth.sol

/// @audit unfreezeAt on line 197
198:          emit UnfreezeAtSet(unfreezeAt, newUnfreezeAt);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L198

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit assets on line 112
113:          return ICollateral(address(assets[erc20]));

/// @audit assets on line 165
166:              else emit AssetUnregistered(erc20, assets[erc20]);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L113

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit nonce on line 636
646:          emit BasketSet(nonce, basket.erc20s, refAmts, disabled);

/// @audit disabled on line 631
634:          if (!disabled) {

/// @audit disabled on line 634
646:          emit BasketSet(nonce, basket.erc20s, refAmts, disabled);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L646

```solidity
File: contracts/p1/Furnace.sol

/// @audit period on line 71
74:           uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;

/// @audit period on line 74
81:           lastPayout += numPeriods * period;

/// @audit lastPayout on line 71
74:           uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L74

```solidity
File: contracts/p1/RToken.sol

/// @audit basketsNeeded on line 234
259:              uint192 newBasketsNeeded = basketsNeeded + amtBaskets;

/// @audit basketsNeeded on line 259
260:              emit BasketsNeededChanged(basketsNeeded, newBasketsNeeded);

/// @audit basketsNeeded on line 493
494:          emit BasketsNeededChanged(basketsNeeded_, basketsNeeded);

/// @audit basketsNeeded on line 775
775:          emit BasketsNeededChanged(basketsNeeded, basketsNeeded + amtBaskets);

/// @audit basketsNeeded on line 809
810:          uint256 high = (FIX_ONE_256 * basketsNeeded + (supply - 1)) / supply; // D18{BU/rTok}

/// @audit lastIssRate on line 351
353:              if (lastIssRate < MIN_BLOCK_ISSUANCE_LIMIT) lastIssRate = MIN_BLOCK_ISSUANCE_LIMIT;

/// @audit lastIssRate on line 353
/// @audit lastIssRate on line 368
368:          finished = before + uint192((FIX_ONE_256 * amtRToken + (lastIssRate - 1)) / lastIssRate);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L259

```solidity
File: contracts/p1/StRSR.sol

/// @audit era on line 260
276:          emit UnstakingStarted(index, era, account, rsrAmount, stakeAmount, availableAt);

/// @audit era on line 578
580:          emit AllBalancesReset(era);

/// @audit totalStakes on line 504
524:          stakeRate = (stakeRSR == 0 || totalStakes == 0)

/// @audit totalStakes on line 524
526:              : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);

/// @audit stakeRSR on line 387
391:          if (stakeRSR > 0) {

/// @audit stakeRSR on line 391
/// @audit stakeRSR on line 393
393:              stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);

/// @audit stakeRSR on line 393
395:          if (stakeRSR == 0 || stakeRate > MAX_STAKE_RATE) {

/// @audit stakeRSR on line 395
396:              seizedRSR += stakeRSR;

/// @audit stakeRSR on line 513
524:          stakeRate = (stakeRSR == 0 || totalStakes == 0)

/// @audit stakeRSR on line 524
/// @audit stakeRSR on line 526
526:              : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);

/// @audit stakeRate on line 270
270:          uint256 newStakeRSR = (FIX_ONE_256 * totalStakes + (stakeRate - 1)) / stakeRate;

/// @audit stakeRate on line 393
395:          if (stakeRSR == 0 || stakeRate > MAX_STAKE_RATE) {

/// @audit stakeRate on line 427
427:          return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method

/// @audit draftEra on line 308
309:          CumulativeDraft[] storage queue = draftQueues[draftEra][account];

/// @audit draftEra on line 309
319:          firstRemainingDraft[draftEra][account] = endId;

/// @audit draftEra on line 319
333:          emit UnstakingCompleted(firstId, endId, draftEra, account, rsrAmount);

/// @audit draftEra on line 437
440:          (uint256 left, uint256 right) = (firstRemainingDraft[draftEra][account], queue.length);

/// @audit draftEra on line 590
592:          emit AllUnstakingReset(draftEra);

/// @audit draftRSR on line 402
406:          if (draftRSR > 0) {

/// @audit draftRSR on line 406
/// @audit draftRSR on line 408
408:              draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);

/// @audit draftRSR on line 408
411:          if (draftRSR == 0 || draftRate > MAX_DRAFT_RATE) {

/// @audit draftRSR on line 411
412:              seizedRSR += draftRSR;

/// @audit draftRSR on line 549
551:          uint256 newTotalDrafts = (draftRate * draftRSR) / FIX_ONE;

/// @audit draftRate on line 324
324:          uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;

/// @audit draftRate on line 408
411:          if (draftRSR == 0 || draftRate > MAX_DRAFT_RATE) {

/// @audit unstakingDelay on line 815
816:          require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

/// @audit rewardPeriod on line 497
498:          uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;

/// @audit rewardPeriod on line 498
516:          payoutLastPaid += numPeriods * rewardPeriod;

/// @audit rewardPeriod on line 823
824:          require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

/// @audit payoutLastPaid on line 497
498:          uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L276

```solidity
File: contracts/p1/StRSRVotes.sol

/// @audit era on line 72
73:           return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;

/// @audit era on line 174
183:                      _checkpoints[era][dst],

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L73

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

/// @audit _whenDefault on line 169
171:          } else if (_whenDefault > block.timestamp) {

/// @audit _whenDefault on line 189
190:              else if (sum < _whenDefault) _whenDefault = uint48(sum);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L171

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit origin on line 174
191:          if (sellBal > 0) IERC20Upgradeable(address(sell)).safeTransfer(origin, sellBal);

/// @audit origin on line 191
192:          if (boughtAmt > 0) IERC20Upgradeable(address(buy)).safeTransfer(origin, boughtAmt);

/// @audit initBal on line 96
137:          IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), initBal);

/// @audit initBal on line 195
196:              soldAmt = initBal - sellBal;

/// @audit endTime on line 103
142:              endTime,

/// @audit endTime on line 142
143:              endTime,

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L191

### [G&#x2011;10]  Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

*There is 1 instance of this issue:*

```solidity
File: contracts/p1/RToken.sol

/// @audit issueQueues[account] on line 635
635:          return (issueQueues[account].left, issueQueues[account].right);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L635

### [G&#x2011;11]  The result of function calls should be cached rather than re-calling the function
The instances below point to the second+ call of the function within a single function

*There are 12 instances of this issue:*

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit asset.erc20() on line 74
76:           uint192 quantity = basketHandler.quantity(asset.erc20());

/// @audit asset.erc20() on line 88
89:           require(assets[asset.erc20()] == asset, "asset not found");

/// @audit asset.erc20() on line 88
90:           uint192 quantity = basketHandler.quantity(asset.erc20());

/// @audit asset.erc20() on line 88
92:           _erc20s.remove(address(asset.erc20()));

/// @audit asset.erc20() on line 88
93:           assets[asset.erc20()] = IAsset(address(0));

/// @audit asset.erc20() on line 88
94:           emit AssetUnregistered(asset.erc20(), asset);

/// @audit asset.erc20() on line 152
152:              !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L76

```solidity
File: contracts/p1/BackingManager.sol

/// @audit main.rToken() on line 75
76:           IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L76

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit _targetNames.length() on line 523
533:          uint256 targetsLength = _targetNames.length();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L533

```solidity
File: contracts/p1/Distributor.sol

/// @audit destinations.length() on line 105
108:          for (uint256 i = 0; i < destinations.length(); ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L108

```solidity
File: contracts/p1/StRSR.sol

/// @audit main_.rsr() on line 178
181:          rsrRewardsAtLastPayout = main_.rsr().balanceOf(address(this));

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L181

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit buy.decimals() on line 106
125:              DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L125

### [G&#x2011;12]  `<x> += <y>` costs more gas than `<x> = <x> + <y>` for state variables
Using the addition operator instead of plus-equals saves **[113 gas](https://gist.github.com/IllIllI000/cbbfb267425b898e5be734d4008d4fe8)**

*There are 10 instances of this issue:*

```solidity
File: contracts/p1/BasketHandler.sol

636:              nonce += 1;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L636

```solidity
File: contracts/p1/Furnace.sol

81:           lastPayout += numPeriods * period;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L81

```solidity
File: contracts/p1/StRSR.sol

229:          stakeRSR += rsrAmount;

387:          stakeRSR -= stakeRSRToTake;

402:          draftRSR -= draftRSRToTake;

513:              stakeRSR += payout;

516:          payoutLastPaid += numPeriods * rewardPeriod;

549:          draftRSR += rsrAmount;

699:          totalStakes += amount;

721:          totalStakes -= amount;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L229

### [G&#x2011;13]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 2 instances of this issue:*

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

199:      function alreadyDefaulted() internal view returns (bool) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L199

```solidity
File: contracts/plugins/governance/Governance.sol

126       function _cancel(
127           address[] memory targets,
128           uint256[] memory values,
129           bytes[] memory calldatas,
130           bytes32 descriptionHash
131:      ) internal override(Governor, GovernorTimelockControl) returns (uint256) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L126-L131

### [G&#x2011;14]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There are 6 instances of this issue:*

```solidity
File: contracts/libraries/RedemptionBattery.sol

/// @audit require() on line 44
48:           battery.lastCharge = charge - amount;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L48

```solidity
File: contracts/p1/StRSR.sol

/// @audit require() on line 660
662:              _approve(owner, spender, currentAllowance - subtractedValue);

/// @audit require() on line 680
682:              eraStakes[from] = fromBalance - amount;

/// @audit require() on line 717
719:              eraStakes[account] = accountBalance - amount;

/// @audit require() on line 746
748:                  _approve(owner, spender, currentAllowance - amount);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L662

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit if-condition on line 195
196:              soldAmt = initBal - sellBal;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L196

### [G&#x2011;15]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 51 instances of this issue:*

```solidity
File: contracts/libraries/Array.sol

11:           for (uint256 i = 1; i < arrLen; ++i) {

12:               for (uint256 j = 0; j < i; ++j) {

23:           for (uint256 i = 1; i < arrLen; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11

```solidity
File: contracts/libraries/String.sol

14:           for (uint256 i = 0; i < bStr.length; i++) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14

```solidity
File: contracts/p1/AssetRegistry.sol

38:           for (uint256 i = 0; i < length; ++i) {

49:           for (uint256 i = 0; i < length; ++i) {

127:          for (uint256 i = 0; i < length; ++i) {

138:          for (uint256 i = 0; i < length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L38

```solidity
File: contracts/p1/BackingManager.sol

221:          for (uint256 i = 0; i < length; ++i) {

238:          for (uint256 i = 0; i < length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L221

```solidity
File: contracts/p1/BasketHandler.sol

70:           for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;

78:           for (uint256 i = 0; i < length; ++i) {

218:          for (uint256 i = 0; i < config.erc20s.length; ++i) {

227:          for (uint256 i = 0; i < erc20s.length; ++i) {

262:          for (uint256 i = 0; i < erc20s.length; ++i) {

286:          for (uint256 i = 0; i < size; ++i) {

337:          for (uint256 i = 0; i < len; ++i) {

397:          for (uint256 i = 0; i < len; ++i) {

416:          for (uint256 i = 0; i < length; ++i) {

437:          for (uint256 i = 0; i < length; ++i) {

530:          for (uint256 i = 0; i < basketLength; ++i) {

548:          for (uint256 i = 0; i < basketLength; ++i) {

553:              for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {

586:          for (uint256 i = 0; i < targetsLength; ++i) {

597:              for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {

611:              for (uint256 j = 0; j < backupLength && assigned < size; ++j) {

643:          for (uint256 i = 0; i < basketLength; ++i) {

653:          for (uint256 i = 0; i < erc20s.length; i++) {

707:          for (uint256 i = 0; i < erc20s.length; ++i) {

725:          for (uint256 i = 0; i < erc20s.length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L70

```solidity
File: contracts/p1/Distributor.sol

108:          for (uint256 i = 0; i < destinations.length(); ++i) {

133:          for (uint256 i = 0; i < numTransfers; i++) {

143:          for (uint256 i = 0; i < length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L108

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

242:          for (uint256 i = 0; i < reg.erc20s.length; ++i) {

329:          for (uint256 i = 0; i < reg.erc20s.length; ++i) {

437:          for (uint256 i = 0; i < len; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L242

```solidity
File: contracts/p1/mixins/RewardableLib.sol

27:           for (uint256 i = 0; i < registry.assets.length; ++i) {

67:           for (uint256 i = 0; i < erc20sLen; ++i) {

73:           for (uint256 i = 0; i < erc20sLen; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27

```solidity
File: contracts/p1/RToken.sol

270:              for (uint256 i = 0; i < erc20s.length; ++i) {

303:              for (uint256 i = 0; i < basketSize; ++i) {

329:          for (uint256 i = 0; i < basketSize; ++i) {

334:          for (uint256 i = 0; i < basketSize; ++i) {

478:          for (uint256 i = 0; i < erc20length; ++i) {

501:          for (uint256 i = 0; i < erc20length; ++i) {

674:              for (uint256 i = 0; i < tokensLen; ++i) {

683:              for (uint256 i = 0; i < tokensLen; ++i) {

711:          for (uint256 i = 0; i < queue.tokens.length; ++i) {

757:              for (uint256 i = 0; i < tokensLen; ++i) {

767:              for (uint256 i = 0; i < tokensLen; ++i) {

793:          for (uint256 i = 0; i < tokensLen; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270

### [G&#x2011;16]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 2 instances of this issue:*

```solidity
File: contracts/p1/Broker.sol

58            require(
59                address(tradeImplementation_) != address(0),
60                "invalid Trade Implementation address"
61:           );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L58-L61

```solidity
File: contracts/plugins/assets/CTokenNonFiatCollateral.sol

28            require(
29                address(targetUnitChainlinkFeed_) != address(0),
30                "missing target unit chainlink feed"
31:           );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L28-L31

### [G&#x2011;17]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 49 instances of this issue:*

```solidity
File: contracts/interfaces/IAssetRegistry.sol

/// @audit init(), refresh(), toAsset(), toColl(), isRegistered(), erc20s(), getRegistry(), register(), swapRegistered(), unregister()
22:   interface IAssetRegistry is IComponent {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAssetRegistry.sol#L22

```solidity
File: contracts/interfaces/IAsset.sol

/// @audit refresh(), price(), lotPrice(), bal(), erc20(), erc20Decimals(), isCollateral(), maxTradeVolume()
16:   interface IAsset is IRewardable {

/// @audit targetName(), status(), refPerTok(), targetPerRef()
75:   interface ICollateral is IAsset {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L16

```solidity
File: contracts/interfaces/IBackingManager.sol

/// @audit init(), grantRTokenAllowance(), manageTokens(), manageTokensSortedOrder()
19:   interface IBackingManager is IComponent, ITrading {

/// @audit tradingDelay(), backingBuffer(), setTradingDelay(), setBackingBuffer()
49:   interface TestIBackingManager is IBackingManager, TestITrading {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L19

```solidity
File: contracts/interfaces/IBasketHandler.sol

/// @audit init(), setPrimeBasket(), setBackupConfig(), disableBasket(), refreshBasket(), fullyCollateralized(), status(), quantity(), basketTokens(), quote(), basketsHeldBy(), price(), lotPrice(), nonce(), timestamp()
16:   interface IBasketHandler is IComponent {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L16

```solidity
File: contracts/interfaces/IBroker.sol

/// @audit init(), openTrade(), reportViolation(), disabled()
22:   interface IBroker is IComponent {

/// @audit gnosis(), auctionLength(), setAuctionLength(), setDisabled()
45:   interface TestIBroker is IBroker {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBroker.sol#L22

```solidity
File: contracts/interfaces/IDeployerRegistry.sol

/// @audit register(), unregister(), latestDeployment()
6:    interface IDeployerRegistry {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L6

```solidity
File: contracts/interfaces/IDeployer.sol

/// @audit ENS(), rsr(), gnosis(), rsrAsset()
97:   interface TestIDeployer is IDeployer {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L97

```solidity
File: contracts/interfaces/IDistributor.sol

/// @audit init(), setDistribution(), distribute(), totals()
23:   interface IDistributor is IComponent {

/// @audit FURNACE(), ST_RSR(), distribution()
54:   interface TestIDistributor is IDistributor {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L23

```solidity
File: contracts/interfaces/IFurnace.sol

/// @audit init(), period(), setPeriod(), ratio(), setRatio(), melt()
11:   interface IFurnace is IComponent {

/// @audit lastPayout(), lastPayoutBal()
45:   interface TestIFurnace is IFurnace {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L11

```solidity
File: contracts/interfaces/IGnosis.sol

/// @audit initiateAuction(), auctionData(), settleAuction(), feeNumerator()
25:   interface IGnosis {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol#L25

```solidity
File: contracts/interfaces/IMain.sol

/// @audit pausedOrFrozen(), frozen(), shortFreeze(), longFreeze(), freezeForever(), freezeShort(), freezeLong(), unfreeze()
48:   interface IAuth is IAccessControlUpgradeable {

/// @audit rToken(), stRSR(), assetRegistry(), basketHandler(), backingManager(), distributor(), rsrTrader(), rTokenTrader(), furnace(), broker()
101:  interface IComponentRegistry {

/// @audit poke(), init(), rsr()
149:  interface IMain is IVersioned, IAuth, IComponentRegistry {

/// @audit setShortFreeze(), setLongFreeze(), shortFreeze(), longFreeze(), longFreezes()
166:  interface TestIMain is IMain {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L48

```solidity
File: contracts/interfaces/IRToken.sol

/// @audit init(), issue(), issue(), cancel(), vest(), sweepRewards(), sweepRewardsSingle(), melt(), setBasketsNeeded(), basketsNeeded(), redemptionLimit()
18:   interface IRToken is

/// @audit setIssuanceRate(), issuanceRate(), setScalingRedemptionRate(), scalingRedemptionRate(), setRedemptionRateFloor(), redemptionRateFloor()
171:  interface TestIRToken is IRToken {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L18

```solidity
File: contracts/interfaces/IStRSR.sol

/// @audit init(), payoutRewards(), stake(), unstake(), withdraw(), seizeRSR(), endIdForWithdraw(), exchangeRate()
22:   interface IStRSR is IERC20MetadataUpgradeable, IERC20PermitUpgradeable, IComponent {

/// @audit rewardPeriod(), setRewardPeriod(), rewardRatio(), setRewardRatio(), unstakingDelay(), setUnstakingDelay(), setName(), setSymbol(), exchangeRate()
123:  interface TestIStRSR is IStRSR {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L22

```solidity
File: contracts/interfaces/IStRSRVotes.sol

/// @audit currentEra(), getPastEra()
6:    interface IStRSRVotes is IVotesUpgradeable {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSRVotes.sol#L6

```solidity
File: contracts/interfaces/ITrade.sol

/// @audit sell(), buy(), endTime(), canSettle(), settle()
11:   interface ITrade {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrade.sol#L11

```solidity
File: contracts/interfaces/ITrading.sol

/// @audit settleTrade(), maxTradeSlippage(), minTradeVolume(), trades(), mulDivCeil()
15:   interface ITrading is IComponent, IRewardableComponent {

/// @audit setMaxTradeSlippage(), setMinTradeVolume(), tradesOpen()
68:   interface TestITrading is ITrading {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L15

```solidity
File: contracts/mixins/Auth.sol

/// @audit frozen(), pausedOrFrozen(), freezeShort(), freezeLong(), freezeForever(), unfreeze(), setShortFreeze(), setLongFreeze()
16:   abstract contract Auth is AccessControlUpgradeable, IAuth {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L16

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit init(), refresh(), register(), swapRegistered(), unregister(), toAsset(), toColl(), isRegistered(), erc20s(), getRegistry()
12:   contract AssetRegistryP1 is ComponentP1, IAssetRegistry {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L12

```solidity
File: contracts/p1/BackingManager.sol

/// @audit init(), grantRTokenAllowance(), manageTokens(), manageTokensSortedOrder(), setTradingDelay(), setBackingBuffer()
20:   contract BackingManagerP1 is TradingP1, IBackingManager {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L20

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit init(), disableBasket(), refreshBasket(), setPrimeBasket(), setBackupConfig(), fullyCollateralized(), status(), quantity(), price(), lotPrice(), basketTokens(), quote(), basketsHeldBy(), getPrimeBasket(), getBackupConfig()
110:  contract BasketHandlerP1 is ComponentP1, IBasketHandler {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L110

```solidity
File: contracts/p1/Broker.sol

/// @audit init(), openTrade(), reportViolation(), setAuctionLength(), setDisabled()
18:   contract BrokerP1 is ComponentP1, IBroker {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L18

```solidity
File: contracts/p1/Distributor.sol

/// @audit init(), setDistribution(), distribute(), totals()
12:   contract DistributorP1 is ComponentP1, IDistributor {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L12

```solidity
File: contracts/p1/Furnace.sol

/// @audit init(), melt(), setPeriod(), setRatio()
12:   contract FurnaceP1 is ComponentP1, IFurnace {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L12

```solidity
File: contracts/p1/Main.sol

/// @audit init(), poke()
18:   contract MainP1 is Versioned, Initializable, Auth, ComponentRegistry, UUPSUpgradeable, IMain {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L18

```solidity
File: contracts/p1/mixins/RewardableLib.sol

/// @audit claimRewards(), claimRewardsSingle(), sweepRewards(), sweepRewardsSingle()
15:   library RewardableLibP1 {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L15

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit settleTrade(), claimRewards(), claimRewardsSingle(), setMaxTradeSlippage(), setMinTradeVolume(), mulDivCeil()
16:   abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeable, ITrading {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L16

```solidity
File: contracts/p1/RevenueTrader.sol

/// @audit init(), manageToken()
14:   contract RevenueTraderP1 is TradingP1, IRevenueTrader {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L14

```solidity
File: contracts/p1/RToken.sol

/// @audit init(), issue(), issue(), vest(), cancel(), claimRewards(), claimRewardsSingle(), sweepRewards(), sweepRewardsSingle(), melt(), setBasketsNeeded(), setIssuanceRate(), scalingRedemptionRate(), setScalingRedemptionRate(), redemptionRateFloor(), setRedemptionRateFloor(), issueItem(), redemptionLimit(), queueBounds()
24:   contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L24

```solidity
File: contracts/p1/StRSR.sol

/// @audit init(), payoutRewards(), stake(), unstake(), withdraw(), seizeRSR(), exchangeRate(), endIdForWithdraw(), draftQueueLen(), setName(), setSymbol(), setUnstakingDelay(), setRewardPeriod(), setRewardRatio()
33:   abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeable {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L33

```solidity
File: contracts/p1/StRSRVotes.sol

/// @audit currentEra(), checkpoints(), getPastEra()
14:   contract StRSRP1Votes is StRSRP1, IStRSRVotes {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L14

```solidity
File: contracts/plugins/aave/IAaveIncentivesController.sol

/// @audit getAssetData(), setClaimer(), getClaimer(), configureAssets(), handleAction(), getRewardsBalance(), claimRewards(), claimRewardsOnBehalf(), getUserUnclaimedRewards(), getUserAssetData(), REWARD_TOKEN(), PRECISION()
5:    interface IAaveIncentivesController {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L5

```solidity
File: contracts/plugins/aave/IStaticATokenLM.sol

/// @audit deposit(), withdraw(), withdrawDynamicAmount(), metaDeposit(), metaWithdraw(), dynamicBalanceOf(), staticToDynamicAmount(), dynamicToStaticAmount(), rate(), getDomainSeparator(), collectAndUpdateRewards(), claimRewardsOnBehalf(), claimRewards(), claimRewardsToSelf(), getTotalClaimableRewards(), getClaimableRewards(), getUnclaimedRewards(), getAccRewardsPerToken(), getLifetimeRewardsClaimed(), getLifetimeRewards(), getLastRewardBlock(), LENDING_POOL(), INCENTIVES_CONTROLLER(), ATOKEN(), ASSET(), REWARD_TOKEN()
9:    interface IStaticATokenLM is IERC20 {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L9

```solidity
File: contracts/plugins/assets/Asset.sol

/// @audit tryPrice(), price(), lotPrice(), bal(), isCollateral(), claimRewards()
9:    contract Asset is IAsset {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L9

```solidity
File: contracts/plugins/assets/ATokenFiatCollateral.sol

/// @audit claimRewardsToSelf(), rate(), REWARD_TOKEN()
10:   interface IStaticAToken is IERC20Metadata {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L10

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

/// @audit status(), whenDefault(), refPerTok(), targetPerRef()
38:   contract FiatCollateral is ICollateral, Asset {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L38

```solidity
File: contracts/plugins/assets/ICToken.sol

/// @audit claimComp(), getCompAddress()
20:   interface IComptroller {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ICToken.sol#L20

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

/// @audit tryPrice(), price(), lotPrice(), bal(), isCollateral(), claimRewards()
11:   contract RTokenAsset is IAsset {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L11

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit init(), settle(), transferToOriginAfterTradeComplete(), canSettle()
22:   contract GnosisTrade is ITrade {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L22

### [G&#x2011;18]  Use a more recent version of solidity
Use a solidity version of at least 0.8.0 to get overflow protection without `SafeMath`
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There are 7 instances of this issue:*

```solidity
File: contracts/plugins/aave/ERC20.sol

3:    pragma solidity ^0.6.0;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L3

```solidity
File: contracts/plugins/aave/IAaveIncentivesController.sol

2:    pragma solidity 0.6.12;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L2

```solidity
File: contracts/plugins/aave/IAToken.sol

2:    pragma solidity 0.6.12;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol#L2

```solidity
File: contracts/plugins/aave/IStaticATokenLM.sol

2:    pragma solidity 0.6.12;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L2

```solidity
File: contracts/plugins/aave/RayMathNoRounding.sol

2:    pragma solidity 0.6.12;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol#L2

```solidity
File: contracts/plugins/aave/StaticATokenErrors.sol

2:    pragma solidity 0.6.12;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L2

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

2:    pragma solidity 0.6.12;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L2

### [G&#x2011;19]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There is 1 instance of this issue:*

```solidity
File: contracts/plugins/aave/ReentrancyGuard.sol

3:    pragma solidity >=0.6.0 <0.8.0;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L3

### [G&#x2011;20]  `>=` costs less gas than `>`
The compiler uses opcodes `GT` and `ISZERO` for solidity code that uses `>`, but only requires `LT` for `>=`, [which saves **3 gas**](https://gist.github.com/IllIllI000/3dc79d25acccfa16dee4e83ffdc6ffde)

*There are 3 instances of this issue:*

```solidity
File: contracts/libraries/Fixed.sol

142:      return x > y ? x : y;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L142

```solidity
File: contracts/libraries/RedemptionBattery.sol

69:           uint256 maxCharge = amtPerHour > supply ? supply : amtPerHour;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L69

```solidity
File: contracts/p1/mixins/TradeLib.sol

55:           uint192 s = trade.sellAmount > maxSell ? maxSell : trade.sellAmount; // {sellTok}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L55

### [G&#x2011;21]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There is 1 instance of this issue:*

```solidity
File: contracts/p1/mixins/Trading.sol

72:           tradesOpen--;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L72

### [G&#x2011;22]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 15 instances of this issue:*

```solidity
File: contracts/mixins/Auth.sol

181:          require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");

188:          require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181

```solidity
File: contracts/p1/Broker.sol

134           require(
135               newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
136               "invalid auctionLength"
137:          );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L134-L137

```solidity
File: contracts/p1/Deployer.sol

48            require(
49                address(rsr_) != address(0) &&
50                    address(gnosis_) != address(0) &&
51                    address(rsrAsset_) != address(0) &&
52                    address(implementations_.main) != address(0) &&
53                    address(implementations_.trade) != address(0) &&
54                    address(implementations_.components.assetRegistry) != address(0) &&
55                    address(implementations_.components.backingManager) != address(0) &&
56                    address(implementations_.components.basketHandler) != address(0) &&
57                    address(implementations_.components.broker) != address(0) &&
58                    address(implementations_.components.distributor) != address(0) &&
59                    address(implementations_.components.furnace) != address(0) &&
60                    address(implementations_.components.rsrTrader) != address(0) &&
61                    address(implementations_.components.rTokenTrader) != address(0) &&
62                    address(implementations_.components.rToken) != address(0) &&
63                    address(implementations_.components.stRSR) != address(0),
64                "invalid address"
65:           );

109:          require(owner != address(0) && owner != address(this), "invalid owner");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48-L65

```solidity
File: contracts/p1/Furnace.sol

89:           require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89

```solidity
File: contracts/p1/RevenueTrader.sol

72:           require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72

```solidity
File: contracts/p1/RToken.sol

410:          require(queue.left <= endId && endId <= queue.right, "out of range");

590:          require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");

623:          require(index >= item.left && index < item.right, "out of range");

741:          require(queue.left <= endId && endId <= queue.right, "out of range");

813:          require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410

```solidity
File: contracts/p1/StRSR.sol

813:          require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");

821:          require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813

```solidity
File: contracts/plugins/assets/Asset.sol

50:           require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L50

### [G&#x2011;23]  Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
> When using elements that are smaller than 32 bytes, your contractâ€™s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

*There are 68 instances of this issue:*

```solidity
File: contracts/libraries/Fixed.sol

/// @audit int8 shiftLeft
104:      shiftLeft += 18;

/// @audit uint48 y
325:              y = (y >> 1);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L104

```solidity
File: contracts/mixins/Auth.sol

/// @audit uint48 unfreezeAt
159:          unfreezeAt = uint48(block.timestamp);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L159

```solidity
File: contracts/p1/BackingManager.sol

/// @audit uint192 needed
190:              needed = rToken.basketsNeeded(); // {BU}

/// @audit uint192 needed
214:          needed = needed.mul(FIX_ONE.plus(backingBuffer));

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L190

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit uint192 low
349:          low = low256 >= FIX_MAX ? FIX_MAX : uint192(low256);

/// @audit uint192 high
350:          high = high256 >= FIX_MAX ? FIX_MAX : uint192(high256);

/// @audit uint192 baskets
435:          baskets = FIX_MAX;

/// @audit uint192 baskets
453:              baskets = fixMin(baskets, bal.div(q));

/// @audit uint48 nonce
636:              nonce += 1;

/// @audit uint48 timestamp
637:              timestamp = uint48(block.timestamp);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L349

```solidity
File: contracts/p1/Furnace.sol

/// @audit uint48 lastPayout
42:           lastPayout = uint48(block.timestamp);

/// @audit uint48 lastPayout
81:           lastPayout += numPeriods * period;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L42

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

/// @audit uint192 assetsLow
197:          assetsLow = assetsLow.gt(shortfallSlippage) ? assetsLow.minus(shortfallSlippage) : 0;

/// @audit uint192 bal
250:                  bal = bal.plus(reg.assets[i].bal(address(components.stRSR)));

/// @audit uint192 assetsLow
264:              assetsLow += low.mul(bal, FLOOR);

/// @audit uint192 assetsHigh
270:              if (uint256(assetsHigh) + val >= FIX_MAX) assetsHigh = FIX_MAX;

/// @audit uint192 potentialDustLoss
275:              potentialDustLoss = potentialDustLoss.plus(rules.minTradeVolume);

/// @audit uint192 assetsLow
280:          assetsLow = assetsLow.gt(potentialDustLoss) ? assetsLow.minus(potentialDustLoss) : FIX_ZERO;

/// @audit uint192 needed
380:                  needed = range.bottom.mul(components.bh.quantity(reg.erc20s[i]), CEIL); // {buyTok};

/// @audit uint192 shortfall
452:                  shortfall = shortfall.plus(needed.minus(held).mul(priceHigh, CEIL));

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L197

```solidity
File: contracts/p1/RToken.sol

/// @audit uint48 basketNonce
211:              basketNonce = basketHandler.nonce();

/// @audit uint192 lastIssRate
351:              lastIssRate = uint192((issuanceRate * totalSupply()) / FIX_ONE);

/// @audit uint192 lastIssRate
353:              if (lastIssRate < MIN_BLOCK_ISSUANCE_LIMIT) lastIssRate = MIN_BLOCK_ISSUANCE_LIMIT;

/// @audit uint192 finished
368:          finished = before + uint192((FIX_ONE_256 * amtRToken + (lastIssRate - 1)) / lastIssRate);

/// @audit uint192 basketsNeeded
493:          basketsNeeded = basketsNeeded_ - baskets;

/// @audit uint192 amtBaskets
756:              amtBaskets = rightItem.amtBaskets;

/// @audit uint192 amtBaskets
766:              amtBaskets = rightItem.amtBaskets - leftItem.amtBaskets;

/// @audit uint192 basketsNeeded
777:          basketsNeeded = basketsNeeded + amtBaskets;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L211

```solidity
File: contracts/p1/StRSR.sol

/// @audit uint48 payoutLastPaid
180:          payoutLastPaid = uint48(block.timestamp);

/// @audit uint192 stakeRate
393:              stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);

/// @audit uint192 draftRate
408:              draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);

/// @audit uint48 payoutLastPaid
516:          payoutLastPaid += numPeriods * rewardPeriod;

/// @audit uint192 stakeRate
524           stakeRate = (stakeRSR == 0 || totalStakes == 0)
525               ? FIX_ONE
526:              : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);

/// @audit uint64 availableAt
561:          availableAt = uint64(block.timestamp) + unstakingDelay;

/// @audit uint192 stakeRate
577:          stakeRate = FIX_ONE;

/// @audit uint192 draftRate
589:          draftRate = FIX_ONE;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L180

```solidity
File: contracts/plugins/assets/Asset.sol

/// @audit uint8 erc20Decimals
58:           erc20Decimals = erc20.decimals();

/// @audit uint48 lastSave
95:                   lastSave = uint48(block.timestamp);

/// @audit uint192 lotLow
144:              lotLow = savedLowPrice.mul(lotMultiplier);

/// @audit uint192 lotHigh
145:              lotHigh = savedHighPrice.mul(lotMultiplier);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L58

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

/// @audit uint8 referenceERC20Decimals
28:           referenceERC20Decimals = IERC20Metadata(erc20.underlying()).decimals();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L28

```solidity
File: contracts/plugins/assets/CTokenNonFiatCollateral.sol

/// @audit uint192 pegPrice
49:           pegPrice = chainlinkFeed.price(oracleTimeout); // {target/ref}

/// @audit uint192 low
58:           low = p - delta;

/// @audit uint192 high
59:           high = p + delta;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L49

```solidity
File: contracts/plugins/assets/EURFiatCollateral.sol

/// @audit uint192 low
51:           low = refPrice - delta;

/// @audit uint192 high
52:           high = refPrice + delta;

/// @audit uint192 pegPrice
55:           pegPrice = refPrice.div(targetPrice);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L51

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

/// @audit uint48 delayUntilDefault
79:           delayUntilDefault = config.delayUntilDefault;

/// @audit uint192 pegBottom
86:           pegBottom = peg - delta;

/// @audit uint192 pegTop
87:           pegTop = peg + delta;

/// @audit uint192 pegPrice
108:          pegPrice = chainlinkFeed.price(oracleTimeout); // {target/ref}

/// @audit uint192 low
114:          low = p - delta;

/// @audit uint192 high
115:          high = p + delta;

/// @audit uint48 _whenDefault
190:              else if (sum < _whenDefault) _whenDefault = uint48(sum);

/// @audit uint48 _whenDefault
195:              _whenDefault = uint48(block.timestamp);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L79

```solidity
File: contracts/plugins/assets/NonFiatCollateral.sol

/// @audit uint192 pegPrice
42:           pegPrice = chainlinkFeed.price(oracleTimeout); // {target/ref}

/// @audit uint192 low
50:           low = p - delta;

/// @audit uint192 high
51:           high = p + delta;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L42

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

/// @audit uint192 low
58:           low = range.bottom.mulDiv(lowBUPrice, supply);

/// @audit uint192 high
59:           high = range.top.mulDiv(highBUPrice, supply);

/// @audit uint192 lotLow
99:           lotLow = range.bottom.mulDiv(buLow, supply);

/// @audit uint192 lotHigh
100:          lotHigh = range.top.mulDiv(buHigh, supply);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L58

```solidity
File: contracts/plugins/assets/SelfReferentialCollateral.sol

/// @audit uint192 low
40:           low = p - delta;

/// @audit uint192 high
41:           high = p + delta;

/// @audit uint192 pegPrice
42:           pegPrice = targetPerRef();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L40

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit uint48 endTime
103:          endTime = uint48(block.timestamp) + auctionLength;

/// @audit uint192 worstCasePrice
106           worstCasePrice = shiftl_toFix(req.minBuyAmount, -int8(buy.decimals())).div(
107               shiftl_toFix(req.sellAmount, -int8(sell.decimals()))
108:          );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L103

### [G&#x2011;24]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 11 instances of this issue:*

```solidity
File: contracts/plugins/assets/Asset.sol

17:       uint8 public immutable erc20Decimals;

21:       uint48 public immutable oracleTimeout; // {s} Seconds that an oracle value is considered valid

23:       uint192 public immutable oracleError; // {1} The max % deviation allowed by the oracle

27:       uint48 public immutable priceTimeout; // {s} The period over which `savedHighPrice` decays to 0

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L17

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

20:       uint8 public immutable referenceERC20Decimals;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L20

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

18:       uint8 public immutable referenceERC20Decimals;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L18

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

50:       uint48 public immutable delayUntilDefault; // {s} e.g 86400

53:       bytes32 public immutable targetName;

55:       uint192 public immutable pegBottom; // {target/ref} The bottom of the peg

57:       uint192 public immutable pegTop; // {target/ref} The top of the peg

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L50

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

22:       uint8 public immutable erc20Decimals;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L22

### [G&#x2011;25]  `require()` or `revert()` statements that check input arguments should be at the top of the function
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (**2100 gas***) in a function that may ultimately revert in the unhappy case.

*There are 2 instances of this issue:*

```solidity
File: contracts/p1/StRSR.sol

/// @audit expensive op on line 258
259:          require(stakeAmount > 0, "Cannot withdraw zero");

/// @audit expensive op on line 375
376:          require(rsrAmount > 0, "Amount cannot be zero");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L259

### [G&#x2011;26]  Empty blocks should be removed or emit something 
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback() payable` functions that are not used, can be removed to save deployment gas.

*There are 3 instances of this issue:*

```solidity
File: contracts/p1/RToken.sol

452:          try main.furnace().melt() {} catch {}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L452

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

98:           } catch {}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L98

### [G&#x2011;27]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 25 instances of this issue:*

```solidity
File: contracts/libraries/Permit.sol

18                require(
19                    IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==
20                        0x1626ba7e,
21                    "ERC1271: Unauthorized"
22:               );

24                require(
25                    SignatureCheckerUpgradeable.isValidSignatureNow(
26                        owner,
27                        hash,
28                        abi.encodePacked(r, s, v)
29                    ),
30                    "ERC20Permit: invalid signature"
31:               );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L18-L22

```solidity
File: contracts/p1/AssetRegistry.sol

151           require(
152               !_erc20s.contains(address(asset.erc20())) || assets[asset.erc20()] == asset,
153               "duplicate ERC20 detected"
154:          );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L151-L154

```solidity
File: contracts/p1/BasketHandler.sol

186           require(
187               main.hasRole(OWNER, _msgSender()) ||
188                   (status() == CollateralStatus.DISABLED && !main.pausedOrFrozen()),
189               "basket unrefreshable"
190:          );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L186-L190

```solidity
File: contracts/p1/Broker.sol

58            require(
59                address(tradeImplementation_) != address(0),
60                "invalid Trade Implementation address"
61:           );

89            require(
90                caller == address(backingManager) ||
91                    caller == address(rsrTrader) ||
92                    caller == address(rTokenTrader),
93                "only traders"
94:           );

134           require(
135               newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
136               "invalid auctionLength"
137:          );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L58-L61

```solidity
File: contracts/p1/Deployer.sol

48            require(
49                address(rsr_) != address(0) &&
50                    address(gnosis_) != address(0) &&
51                    address(rsrAsset_) != address(0) &&
52                    address(implementations_.main) != address(0) &&
53                    address(implementations_.trade) != address(0) &&
54                    address(implementations_.components.assetRegistry) != address(0) &&
55                    address(implementations_.components.backingManager) != address(0) &&
56                    address(implementations_.components.basketHandler) != address(0) &&
57                    address(implementations_.components.broker) != address(0) &&
58                    address(implementations_.components.distributor) != address(0) &&
59                    address(implementations_.components.furnace) != address(0) &&
60                    address(implementations_.components.rsrTrader) != address(0) &&
61                    address(implementations_.components.rTokenTrader) != address(0) &&
62                    address(implementations_.components.rToken) != address(0) &&
63                    address(implementations_.components.stRSR) != address(0),
64                "invalid address"
65:           );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48-L65

```solidity
File: contracts/plugins/aave/RayMathNoRounding.sol

15:           require(a <= type(uint256).max / b, Errors.MATH_MULTIPLICATION_OVERFLOW);

20:           require(b != 0, Errors.MATH_DIVISION_BY_ZERO);

21:           require(a <= (type(uint256).max) / RAY, Errors.MATH_MULTIPLICATION_OVERFLOW);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol#L15

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

142:          require(owner != address(0), StaticATokenErrors.INVALID_OWNER);

144:          require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);

155:          require(owner == ecrecover(digest, v, r, s), StaticATokenErrors.INVALID_SIGNATURE);

170:          require(depositor != address(0), StaticATokenErrors.INVALID_DEPOSITOR);

172:          require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);

192           require(
193               depositor == ecrecover(digest, sigParams.v, sigParams.r, sigParams.s),
194               StaticATokenErrors.INVALID_SIGNATURE
195:          );

210:          require(owner != address(0), StaticATokenErrors.INVALID_OWNER);

212:          require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);

233           require(
234               owner == ecrecover(digest, sigParams.v, sigParams.r, sigParams.s),
235               StaticATokenErrors.INVALID_SIGNATURE
236:          );

294:          require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);

317:          require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);

318           require(
319               staticAmount == 0 || dynamicAmount == 0,
320               StaticATokenErrors.ONLY_ONE_AMOUNT_FORMAT_ALLOWED
321:          );

477           require(
478               msg.sender == onBehalfOf || msg.sender == INCENTIVES_CONTROLLER.getClaimer(onBehalfOf),
479               StaticATokenErrors.INVALID_CLAIMER
480:          );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L142

```solidity
File: contracts/plugins/assets/CTokenNonFiatCollateral.sol

28            require(
29                address(targetUnitChainlinkFeed_) != address(0),
30                "missing target unit chainlink feed"
31:           );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L28-L31

### [G&#x2011;28]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 2 instances of this issue:*

```solidity
File: contracts/mixins/Auth.sol

89        function grantRole(bytes32 role, address account)
90            public
91            override(AccessControlUpgradeable, IAccessControlUpgradeable)
92:           onlyRole(getRoleAdmin(role))

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L89-L92

```solidity
File: contracts/p1/mixins/Trading.sol

44        function __Trading_init(
45            IMain main_,
46            uint192 maxTradeSlippage_,
47            uint192 minTradeVolume_
48:       ) internal onlyInitializing {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L44-L48

### [G&#x2011;29]  Don't use `_msgSender()` if not supporting EIP-2771
Use `msg.sender` if the code does not implement [EIP-2771 trusted forwarder](https://eips.ethereum.org/EIPS/eip-2771) support

*There are 35 instances of this issue:*

```solidity
File: contracts/mixins/Auth.sol

74:           address msgSender = _msgSender();

122:          _revokeRole(SHORT_FREEZER, _msgSender());

136:          longFreezes[_msgSender()] -= 1; // reverts on underflow

139:          if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L74

```solidity
File: contracts/p1/BasketHandler.sol

168:          require(_msgSender() == address(assetRegistry), "asset registry only");

187:              main.hasRole(OWNER, _msgSender()) ||

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L168

```solidity
File: contracts/p1/Broker.sol

88:           address caller = _msgSender();

125:          require(trades[_msgSender()], "unrecognized trade contract");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L88

```solidity
File: contracts/p1/mixins/Component.sol

52:           require(main.hasRole(OWNER, _msgSender()), "governance only");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L52

```solidity
File: contracts/p1/RToken.sol

178:          issue(_msgSender(), amtRToken);

196:          address issuer = _msgSender(); // OK to save: it can't be changed in reentrant runs

407:          address account = _msgSender();

446:          address redeemer = _msgSender();

558:          require(_msgSender() == address(backingManager), "not backing manager");

570:          _burn(_msgSender(), amtRToken);

581:          require(_msgSender() == address(backingManager), "not backing manager");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L178

```solidity
File: contracts/p1/StRSR.sol

228:          address account = _msgSender();

258:          address account = _msgSender();

375:          require(_msgSender() == address(backingManager), "not backing manager");

421:          IERC20Upgradeable(address(rsr)).safeTransfer(_msgSender(), seizedRSR);

623:          address owner = _msgSender();

633:          _approve(_msgSender(), spender, amount);

646:          _spendAllowance(from, _msgSender(), amount);

652:          address owner = _msgSender();

658:          address owner = _msgSender();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L228

```solidity
File: contracts/p1/StRSRVotes.sol

115:          _delegate(_msgSender(), delegatee);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L115

```solidity
File: contracts/plugins/aave/ERC20.sol

118:          _transfer(_msgSender(), recipient, amount);

143:          _approve(_msgSender(), spender, amount);

167:              _msgSender(),

168:              _allowances[sender][_msgSender()].sub(

189:          _approve(_msgSender(), spender, _allowances[_msgSender()][spender].add(addedValue));

213:              _msgSender(),

215:              _allowances[_msgSender()][spender].sub(

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L118

### [G&#x2011;30]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public` and [prior to solidity version 0.6.9](https://ethereum.stackexchange.com/a/107939) can save gas by doing so.

*There are 2 instances of this issue:*

```solidity
File: contracts/plugins/aave/ERC20.sol

74:       function symbol() public view returns (string memory) {

91:       function decimals() public view returns (uint8) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L74


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | `<array>.length` should not be looked up in every loop of a `for`-loop | 12 |  36 |
| [G&#x2011;02] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 18 |  - |
| [G&#x2011;03] | Using `bool`s for storage incurs overhead | 4 |  68400 |
| [G&#x2011;04] | Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement | 11 |  66 |
| [G&#x2011;05] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 11 |  55 |
| [G&#x2011;06] | Using `private` rather than `public` for constants, saves gas | 33 |  - |
| [G&#x2011;07] | Division by two should use bit shifting | 8 |  160 |
| [G&#x2011;08] | Use custom errors rather than `revert()`/`require()` strings to save gas | 151 |  - |
| [G&#x2011;09] | Functions guaranteed to revert when called by normal users can be marked `payable` | 12 |  252 |

Total: 260 instances over 9 issues with **68969 gas** saved

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions. The table above as well as its gas numbers do not include any of the excluded findings.





## Gas Optimizations

### [G&#x2011;01]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 12 instances of this issue:*

```solidity
File: contracts/libraries/String.sol

/// @audit (valid but excluded finding)
14:           for (uint256 i = 0; i < bStr.length; i++) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit (valid but excluded finding)
218:          for (uint256 i = 0; i < config.erc20s.length; ++i) {

/// @audit (valid but excluded finding)
227:          for (uint256 i = 0; i < erc20s.length; ++i) {

/// @audit (valid but excluded finding)
262:          for (uint256 i = 0; i < erc20s.length; ++i) {

/// @audit (valid but excluded finding)
653:          for (uint256 i = 0; i < erc20s.length; i++) {

/// @audit (valid but excluded finding)
707:          for (uint256 i = 0; i < erc20s.length; ++i) {

/// @audit (valid but excluded finding)
725:          for (uint256 i = 0; i < erc20s.length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L218

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

/// @audit (valid but excluded finding)
242:          for (uint256 i = 0; i < reg.erc20s.length; ++i) {

/// @audit (valid but excluded finding)
329:          for (uint256 i = 0; i < reg.erc20s.length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L242

```solidity
File: contracts/p1/mixins/RewardableLib.sol

/// @audit (valid but excluded finding)
27:           for (uint256 i = 0; i < registry.assets.length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27

```solidity
File: contracts/p1/RToken.sol

/// @audit (valid but excluded finding)
270:              for (uint256 i = 0; i < erc20s.length; ++i) {

/// @audit (valid but excluded finding)
711:          for (uint256 i = 0; i < queue.tokens.length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270

### [G&#x2011;02]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 18 instances of this issue:*

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit (valid but excluded finding)
231:              require(0 < targetAmts[i], "invalid target amount; must be nonzero");

/// @audit (valid but excluded finding)
657:              require(erc20s[i] != zero, "address zero is not valid collateral");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L231

```solidity
File: contracts/p1/StRSR.sol

/// @audit (valid but excluded finding)
380:          require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");

/// @audit (valid but excluded finding)
660:          require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");

/// @audit (valid but excluded finding)
675:          require(from != address(0), "ERC20: transfer from the zero address");

/// @audit (valid but excluded finding)
676:          require(to != address(0), "ERC20: transfer to the zero address");

/// @audit (valid but excluded finding)
680:          require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");

/// @audit (valid but excluded finding)
711:          require(account != address(0), "ERC20: burn from the zero address");

/// @audit (valid but excluded finding)
717:          require(accountBalance >= amount, "ERC20: burn amount exceeds balance");

/// @audit (valid but excluded finding)
732:          require(owner != address(0), "ERC20: approve from the zero address");

/// @audit (valid but excluded finding)
733:          require(spender != address(0), "ERC20: approve to the zero address");

/// @audit (valid but excluded finding)
816:          require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

/// @audit (valid but excluded finding)
824:          require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L380

```solidity
File: contracts/plugins/aave/ERC20.sol

/// @audit (valid but excluded finding)
242:          require(sender != address(0), "ERC20: transfer from the zero address");

/// @audit (valid but excluded finding)
243:          require(recipient != address(0), "ERC20: transfer to the zero address");

/// @audit (valid but excluded finding)
283:          require(account != address(0), "ERC20: burn from the zero address");

/// @audit (valid but excluded finding)
310:          require(owner != address(0), "ERC20: approve from the zero address");

/// @audit (valid but excluded finding)
311:          require(spender != address(0), "ERC20: approve to the zero address");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L242

### [G&#x2011;03]  Using `bool`s for storage incurs overhead
```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past

*There are 4 instances of this issue:*

```solidity
File: contracts/mixins/Auth.sol

/// @audit (valid but excluded finding)
42:       bool public paused;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L42

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit (valid but excluded finding)
139:      bool private disabled;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L139

```solidity
File: contracts/p1/Broker.sol

/// @audit (valid but excluded finding)
41:       bool public disabled;

/// @audit (valid but excluded finding)
44:       mapping(address => bool) private trades;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L41

### [G&#x2011;04]  Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement
This change saves **[6 gas](https://aws1.discourse-cdn.com/business6/uploads/zeppelin/original/2X/3/363a367d6d68851f27d2679d10706cd16d788b96.png)** per instance. The optimization works until solidity version [0.8.13](https://gist.github.com/IllIllI000/bf2c3120f24a69e489f12b3213c06c94) where there is a regression in gas costs.

*There are 11 instances of this issue:*

```solidity
File: contracts/p1/Distributor.sol

/// @audit (valid but excluded finding)
98:               require(totalShares > 0, "nothing to distribute");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L98

```solidity
File: contracts/p1/RToken.sol

/// @audit (valid but excluded finding)
191:          require(amtRToken > 0, "Cannot issue zero");

/// @audit (valid but excluded finding)
440:          require(amount > 0, "Cannot redeem zero");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L191

```solidity
File: contracts/p1/StRSR.sol

/// @audit (valid but excluded finding)
213:          require(rsrAmount > 0, "Cannot stake zero");

/// @audit (valid but excluded finding)
259:          require(stakeAmount > 0, "Cannot withdraw zero");

/// @audit (valid but excluded finding)
376:          require(rsrAmount > 0, "Amount cannot be zero");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L213

```solidity
File: contracts/plugins/assets/Asset.sol

/// @audit (valid but excluded finding)
48:           require(priceTimeout_ > 0, "price timeout zero");

/// @audit (valid but excluded finding)
52:           require(maxTradeVolume_ > 0, "invalid max trade volume");

/// @audit (valid but excluded finding)
53:           require(oracleTimeout_ > 0, "oracleTimeout zero");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L48

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

/// @audit (valid but excluded finding)
29:           require(referenceERC20Decimals_ > 0, "referenceERC20Decimals missing");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L29

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

/// @audit (valid but excluded finding)
29:           require(maxTradeVolume_ > 0, "invalid max trade volume");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L29

### [G&#x2011;05]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 11 instances of this issue:*

```solidity
File: contracts/libraries/Fixed.sol

/// @audit (valid but excluded finding)
165:              result++;

/// @audit (valid but excluded finding)
169:              result++;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L165

```solidity
File: contracts/libraries/String.sol

/// @audit (valid but excluded finding)
14:           for (uint256 i = 0; i < bStr.length; i++) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit (valid but excluded finding)
621:                      assigned++;

/// @audit (valid but excluded finding)
653:          for (uint256 i = 0; i < erc20s.length; i++) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L621

```solidity
File: contracts/p1/Distributor.sol

/// @audit (valid but excluded finding)
128:              numTransfers++;

/// @audit (valid but excluded finding)
133:          for (uint256 i = 0; i < numTransfers; i++) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L128

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit (valid but excluded finding)
121:          tradesOpen++;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L121

```solidity
File: contracts/p1/RToken.sol

/// @audit (valid but excluded finding)
316:          queue.right++;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L316

```solidity
File: contracts/p1/StRSR.sol

/// @audit (valid but excluded finding)
578:          era++;

/// @audit (valid but excluded finding)
590:          draftEra++;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L578

### [G&#x2011;06]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 33 instances of this issue:*

```solidity
File: contracts/mixins/Auth.sol

/// @audit (valid but excluded finding)
27:       bytes32 public constant OWNER_ROLE = OWNER;

/// @audit (valid but excluded finding)
28:       bytes32 public constant SHORT_FREEZER_ROLE = SHORT_FREEZER;

/// @audit (valid but excluded finding)
29:       bytes32 public constant LONG_FREEZER_ROLE = LONG_FREEZER;

/// @audit (valid but excluded finding)
30:       bytes32 public constant PAUSER_ROLE = PAUSER;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L27

```solidity
File: contracts/p1/BackingManager.sol

/// @audit (valid but excluded finding)
33:       uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year

/// @audit (valid but excluded finding)
34:       uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit (valid but excluded finding)
117:      uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L117

```solidity
File: contracts/p1/Broker.sol

/// @audit (valid but excluded finding)
24:       uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24

```solidity
File: contracts/p1/Deployer.sol

/// @audit (valid but excluded finding)
31:       string public constant ENS = "reserveprotocol.eth";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L31

```solidity
File: contracts/p1/Distributor.sol

/// @audit (valid but excluded finding)
34:       uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L34

```solidity
File: contracts/p1/Furnace.sol

/// @audit (valid but excluded finding)
15:       uint192 public constant MAX_RATIO = FIX_ONE; // {1} 100%

/// @audit (valid but excluded finding)
16:       uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L15

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit (valid but excluded finding)
20:       uint192 public constant MIN_TRADE_VOLUME = 1e29; // {UoA}

/// @audit (valid but excluded finding)
21:       uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L20

```solidity
File: contracts/p1/StRSR.sol

/// @audit (valid but excluded finding)
37:       uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year

/// @audit (valid but excluded finding)
38:       uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year

/// @audit (valid but excluded finding)
39:       uint192 public constant MAX_REWARD_RATIO = FIX_ONE; // {1} 100%

/// @audit (valid but excluded finding)
45:       uint8 public constant decimals = 18;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L37

```solidity
File: contracts/plugins/aave/StaticATokenErrors.sol

/// @audit (valid but excluded finding)
5:        string public constant INVALID_OWNER = "1";

/// @audit (valid but excluded finding)
6:        string public constant INVALID_EXPIRATION = "2";

/// @audit (valid but excluded finding)
7:        string public constant INVALID_SIGNATURE = "3";

/// @audit (valid but excluded finding)
8:        string public constant INVALID_DEPOSITOR = "4";

/// @audit (valid but excluded finding)
9:        string public constant INVALID_RECIPIENT = "5";

/// @audit (valid but excluded finding)
10:       string public constant INVALID_CLAIMER = "6";

/// @audit (valid but excluded finding)
11:       string public constant ONLY_ONE_AMOUNT_FORMAT_ALLOWED = "7";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L5

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

/// @audit (valid but excluded finding)
40:       bytes public constant EIP712_REVISION = bytes("1");

/// @audit (valid but excluded finding)
45        bytes32 public constant PERMIT_TYPEHASH =
46            keccak256(
47                "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
48:           );

/// @audit (valid but excluded finding)
49        bytes32 public constant METADEPOSIT_TYPEHASH =
50            keccak256(
51                "Deposit(address depositor,address recipient,uint256 value,uint16 referralCode,bool fromUnderlying,uint256 nonce,uint256 deadline)"
52:           );

/// @audit (valid but excluded finding)
53        bytes32 public constant METAWITHDRAWAL_TYPEHASH =
54            keccak256(
55                "Withdraw(address owner,address recipient,uint256 staticAmount,uint256 dynamicAmount,bool toUnderlying,uint256 nonce,uint256 deadline)"
56:           );

/// @audit (valid but excluded finding)
58:       uint256 public constant STATIC_ATOKEN_LM_REVISION = 0x1;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L40

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit (valid but excluded finding)
27:       uint256 public constant FEE_DENOMINATOR = 1000;

/// @audit (valid but excluded finding)
31:       uint96 public constant MAX_ORDERS = 1e5;

/// @audit (valid but excluded finding)
34:       uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L27

### [G&#x2011;07]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two

*There are 8 instances of this issue:*

```solidity
File: contracts/libraries/Fixed.sol

/// @audit (valid but excluded finding)
164:          if (numerator % divisor > (divisor - 1) / 2) {

/// @audit (valid but excluded finding)
310:      uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;

/// @audit (valid but excluded finding)
323:              if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

/// @audit (valid but excluded finding)
326:              x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;

/// @audit (valid but excluded finding)
539:          if (mm > ((z - 1) / 2)) result += 1; // z should be z-1

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L164

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit (valid but excluded finding)
372:              uint256 shiftDelta = rawDelta + (FIX_ONE / 2);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L372

```solidity
File: contracts/p1/StRSR.sol

/// @audit (valid but excluded finding)
427:          return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method

/// @audit (valid but excluded finding)
453:              test = (left + right) / 2; // left < test < right because left < right - 1

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L427

### [G&#x2011;08]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 151 instances of this issue:*

```solidity
File: contracts/libraries/RedemptionBattery.sol

/// @audit (valid but excluded finding)
44:           require(amount <= charge, "redemption battery insufficient");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L44

```solidity
File: contracts/mixins/Auth.sol

/// @audit (valid but excluded finding)
94:           require(account != address(0), "cannot grant role to address 0");

/// @audit (valid but excluded finding)
181:          require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");

/// @audit (valid but excluded finding)
188:          require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");

/// @audit (valid but excluded finding)
197:          require(newUnfreezeAt > unfreezeAt, "frozen");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L94

```solidity
File: contracts/mixins/ComponentRegistry.sol

/// @audit (valid but excluded finding)
37:           require(address(val) != address(0), "invalid RToken address");

/// @audit (valid but excluded finding)
45:           require(address(val) != address(0), "invalid StRSR address");

/// @audit (valid but excluded finding)
53:           require(address(val) != address(0), "invalid AssetRegistry address");

/// @audit (valid but excluded finding)
61:           require(address(val) != address(0), "invalid BasketHandler address");

/// @audit (valid but excluded finding)
69:           require(address(val) != address(0), "invalid BackingManager address");

/// @audit (valid but excluded finding)
77:           require(address(val) != address(0), "invalid Distributor address");

/// @audit (valid but excluded finding)
85:           require(address(val) != address(0), "invalid RSRTrader address");

/// @audit (valid but excluded finding)
93:           require(address(val) != address(0), "invalid RTokenTrader address");

/// @audit (valid but excluded finding)
101:          require(address(val) != address(0), "invalid Furnace address");

/// @audit (valid but excluded finding)
109:          require(address(val) != address(0), "invalid Broker address");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L37

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit (valid but excluded finding)
74:           require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");

/// @audit (valid but excluded finding)
88:           require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");

/// @audit (valid but excluded finding)
89:           require(assets[asset.erc20()] == asset, "asset not found");

/// @audit (valid but excluded finding)
103:          require(_erc20s.contains(address(erc20)), "erc20 unregistered");

/// @audit (valid but excluded finding)
111:          require(_erc20s.contains(address(erc20)), "erc20 unregistered");

/// @audit (valid but excluded finding)
112:          require(assets[erc20].isCollateral(), "erc20 is not collateral");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L74

```solidity
File: contracts/p1/BackingManager.sol

/// @audit (valid but excluded finding)
73:           require(assetRegistry.isRegistered(erc20), "erc20 unregistered");

/// @audit (valid but excluded finding)
85:           require(ArrayLib.allUnique(erc20s), "duplicate tokens");

/// @audit (valid but excluded finding)
97:           require(ArrayLib.sortedAndAllUnique(erc20s), "duplicate/unsorted tokens");

/// @audit (valid but excluded finding)
111:          require(basketHandler.status() == CollateralStatus.SOUND, "basket not sound");

/// @audit (valid but excluded finding)
257:          require(val <= MAX_TRADING_DELAY, "invalid tradingDelay");

/// @audit (valid but excluded finding)
264:          require(val <= MAX_BACKING_BUFFER, "invalid backingBuffer");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L73

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit (valid but excluded finding)
168:          require(_msgSender() == address(assetRegistry), "asset registry only");

/// @audit (valid but excluded finding)
213:          require(erc20s.length > 0, "cannot empty basket");

/// @audit (valid but excluded finding)
214:          require(erc20s.length == targetAmts.length, "must be same length");

/// @audit (valid but excluded finding)
230:              require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");

/// @audit (valid but excluded finding)
231:              require(0 < targetAmts[i], "invalid target amount; must be nonzero");

/// @audit (valid but excluded finding)
232:              require(targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount; too large");

/// @audit (valid but excluded finding)
265:              require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");

/// @audit (valid but excluded finding)
654:              require(erc20s[i] != rsr, "RSR is not valid collateral");

/// @audit (valid but excluded finding)
655:              require(erc20s[i] != IERC20(address(rToken)), "RToken is not valid collateral");

/// @audit (valid but excluded finding)
656:              require(erc20s[i] != IERC20(address(stRSR)), "stRSR is not valid collateral");

/// @audit (valid but excluded finding)
657:              require(erc20s[i] != zero, "address zero is not valid collateral");

/// @audit (valid but excluded finding)
660:          require(ArrayLib.allUnique(erc20s), "contains duplicates");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L168

```solidity
File: contracts/p1/Broker.sol

/// @audit (valid but excluded finding)
57:           require(address(gnosis_) != address(0), "invalid Gnosis address");

/// @audit (valid but excluded finding)
86:           require(!disabled, "broker disabled");

/// @audit (valid but excluded finding)
125:          require(trades[_msgSender()], "unrecognized trade contract");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L57

```solidity
File: contracts/p1/Deployer.sol

/// @audit (valid but excluded finding)
109:          require(owner != address(0) && owner != address(this), "invalid owner");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109

```solidity
File: contracts/p1/Distributor.sol

/// @audit (valid but excluded finding)
92:           require(erc20 == rsr || erc20 == rToken, "RSR or RToken");

/// @audit (valid but excluded finding)
98:               require(totalShares > 0, "nothing to distribute");

/// @audit (valid but excluded finding)
162:          require(dest != address(0), "dest cannot be zero");

/// @audit (valid but excluded finding)
163:          if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");

/// @audit (valid but excluded finding)
164:          if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");

/// @audit (valid but excluded finding)
165:          require(share.rsrDist <= 10000, "RSR distribution too high");

/// @audit (valid but excluded finding)
166:          require(share.rTokenDist <= 10000, "RToken distribution too high");

/// @audit (valid but excluded finding)
172:              require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");

/// @audit (valid but excluded finding)
182:          require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L92

```solidity
File: contracts/p1/Furnace.sol

/// @audit (valid but excluded finding)
89:           require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");

/// @audit (valid but excluded finding)
97:           require(ratio_ <= MAX_RATIO, "invalid ratio");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89

```solidity
File: contracts/p1/Main.sol

/// @audit (valid but excluded finding)
32:           require(address(rsr_) != address(0), "invalid RSR address");

/// @audit (valid but excluded finding)
48:           require(!pausedOrFrozen(), "paused or frozen");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L32

```solidity
File: contracts/p1/mixins/Component.sol

/// @audit (valid but excluded finding)
34:           require(address(main_) != address(0), "main is zero address");

/// @audit (valid but excluded finding)
42:           require(!main.pausedOrFrozen(), "paused or frozen");

/// @audit (valid but excluded finding)
47:           require(!main.frozen(), "frozen");

/// @audit (valid but excluded finding)
52:           require(main.hasRole(OWNER, _msgSender()), "governance only");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L34

```solidity
File: contracts/p1/mixins/RewardableLib.sol

/// @audit (valid but excluded finding)
96:           require(reg.isRegistered(erc20), "erc20 unregistered");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L96

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit (valid but excluded finding)
69:           require(trade.canSettle(), "cannot settle yet");

/// @audit (valid but excluded finding)
129:          require(val < MAX_TRADE_SLIPPAGE, "invalid maxTradeSlippage");

/// @audit (valid but excluded finding)
136:          require(val <= MIN_TRADE_VOLUME, "invalid minTradeVolume");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L69

```solidity
File: contracts/p1/RevenueTrader.sol

/// @audit (valid but excluded finding)
29:           require(address(tokenToBuy_) != address(0), "invalid token address");

/// @audit (valid but excluded finding)
72:           require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L29

```solidity
File: contracts/p1/RToken.sol

/// @audit (valid but excluded finding)
156:          require(bytes(name_).length > 0, "name empty");

/// @audit (valid but excluded finding)
157:          require(bytes(symbol_).length > 0, "symbol empty");

/// @audit (valid but excluded finding)
158:          require(bytes(mandate_).length > 0, "mandate empty");

/// @audit (valid but excluded finding)
191:          require(amtRToken > 0, "Cannot issue zero");

/// @audit (valid but excluded finding)
217:          require(status == CollateralStatus.SOUND, "basket unsound");

/// @audit (valid but excluded finding)
384:          require(status == CollateralStatus.SOUND, "basket unsound");

/// @audit (valid but excluded finding)
410:          require(queue.left <= endId && endId <= queue.right, "out of range");

/// @audit (valid but excluded finding)
440:          require(amount > 0, "Cannot redeem zero");

/// @audit (valid but excluded finding)
448:          require(basketHandler.status() != CollateralStatus.DISABLED, "collateral default");

/// @audit (valid but excluded finding)
558:          require(_msgSender() == address(backingManager), "not backing manager");

/// @audit (valid but excluded finding)
581:          require(_msgSender() == address(backingManager), "not backing manager");

/// @audit (valid but excluded finding)
590:          require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");

/// @audit (valid but excluded finding)
603:          require(val <= FIX_ONE, "invalid fraction");

/// @audit (valid but excluded finding)
623:          require(index >= item.left && index < item.right, "out of range");

/// @audit (valid but excluded finding)
741:          require(queue.left <= endId && endId <= queue.right, "out of range");

/// @audit (valid but excluded finding)
747:          require(rightItem.when <= FIX_ONE_256 * block.number, "issuance not ready");

/// @audit (valid but excluded finding)
813:          require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");

/// @audit (valid but excluded finding)
833:          require(to != address(this), "RToken transfer to self");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L156

```solidity
File: contracts/p1/StRSR.sol

/// @audit (valid but excluded finding)
168:          require(bytes(name_).length > 0, "name empty");

/// @audit (valid but excluded finding)
169:          require(bytes(symbol_).length > 0, "symbol empty");

/// @audit (valid but excluded finding)
213:          require(rsrAmount > 0, "Cannot stake zero");

/// @audit (valid but excluded finding)
259:          require(stakeAmount > 0, "Cannot withdraw zero");

/// @audit (valid but excluded finding)
260:          require(stakes[era][account] >= stakeAmount, "Not enough balance");

/// @audit (valid but excluded finding)
305:          require(basketHandler.fullyCollateralized(), "RToken uncollateralized");

/// @audit (valid but excluded finding)
306:          require(basketHandler.status() == CollateralStatus.SOUND, "basket defaulted");

/// @audit (valid but excluded finding)
312:          require(endId <= queue.length, "index out-of-bounds");

/// @audit (valid but excluded finding)
313:          require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");

/// @audit (valid but excluded finding)
375:          require(_msgSender() == address(backingManager), "not backing manager");

/// @audit (valid but excluded finding)
376:          require(rsrAmount > 0, "Amount cannot be zero");

/// @audit (valid but excluded finding)
380:          require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");

/// @audit (valid but excluded finding)
660:          require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");

/// @audit (valid but excluded finding)
675:          require(from != address(0), "ERC20: transfer from the zero address");

/// @audit (valid but excluded finding)
676:          require(to != address(0), "ERC20: transfer to the zero address");

/// @audit (valid but excluded finding)
680:          require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");

/// @audit (valid but excluded finding)
695:          require(account != address(0), "ERC20: mint to the zero address");

/// @audit (valid but excluded finding)
711:          require(account != address(0), "ERC20: burn from the zero address");

/// @audit (valid but excluded finding)
717:          require(accountBalance >= amount, "ERC20: burn amount exceeds balance");

/// @audit (valid but excluded finding)
732:          require(owner != address(0), "ERC20: approve from the zero address");

/// @audit (valid but excluded finding)
733:          require(spender != address(0), "ERC20: approve to the zero address");

/// @audit (valid but excluded finding)
746:              require(currentAllowance >= amount, "ERC20: insufficient allowance");

/// @audit (valid but excluded finding)
760:          require(to != address(this), "StRSR transfer to self");

/// @audit (valid but excluded finding)
775:          require(block.timestamp <= deadline, "ERC20Permit: expired deadline");

/// @audit (valid but excluded finding)
813:          require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");

/// @audit (valid but excluded finding)
816:          require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

/// @audit (valid but excluded finding)
821:          require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");

/// @audit (valid but excluded finding)
824:          require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

/// @audit (valid but excluded finding)
829:          require(val <= MAX_REWARD_RATIO, "invalid rewardRatio");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L168

```solidity
File: contracts/p1/StRSRVotes.sol

/// @audit (valid but excluded finding)
77:           require(blockNumber < block.number, "ERC20Votes: block not yet mined");

/// @audit (valid but excluded finding)
83:           require(blockNumber < block.number, "ERC20Votes: block not yet mined");

/// @audit (valid but excluded finding)
89:           require(blockNumber < block.number, "ERC20Votes: block not yet mined");

/// @audit (valid but excluded finding)
126:          require(block.timestamp <= expiry, "ERC20Votes: signature expired");

/// @audit (valid but excluded finding)
133:          require(nonce == _useNonce(signer), "ERC20Votes: invalid nonce");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L77

```solidity
File: contracts/plugins/aave/ERC20.sol

/// @audit (valid but excluded finding)
242:          require(sender != address(0), "ERC20: transfer from the zero address");

/// @audit (valid but excluded finding)
243:          require(recipient != address(0), "ERC20: transfer to the zero address");

/// @audit (valid but excluded finding)
262:          require(account != address(0), "ERC20: mint to the zero address");

/// @audit (valid but excluded finding)
283:          require(account != address(0), "ERC20: burn from the zero address");

/// @audit (valid but excluded finding)
310:          require(owner != address(0), "ERC20: approve from the zero address");

/// @audit (valid but excluded finding)
311:          require(spender != address(0), "ERC20: approve to the zero address");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L242

```solidity
File: contracts/plugins/aave/ReentrancyGuard.sol

/// @audit (valid but excluded finding)
51:           require(_status != _ENTERED, "ReentrancyGuard: reentrant call");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L51

```solidity
File: contracts/plugins/assets/Asset.sol

/// @audit (valid but excluded finding)
48:           require(priceTimeout_ > 0, "price timeout zero");

/// @audit (valid but excluded finding)
49:           require(address(chainlinkFeed_) != address(0), "missing chainlink feed");

/// @audit (valid but excluded finding)
50:           require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");

/// @audit (valid but excluded finding)
51:           require(address(erc20_) != address(0), "missing erc20");

/// @audit (valid but excluded finding)
52:           require(maxTradeVolume_ > 0, "invalid max trade volume");

/// @audit (valid but excluded finding)
53:           require(oracleTimeout_ > 0, "oracleTimeout zero");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L48

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

/// @audit (valid but excluded finding)
26:           require(address(comptroller_) != address(0), "comptroller missing");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L26

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

/// @audit (valid but excluded finding)
29:           require(referenceERC20Decimals_ > 0, "referenceERC20Decimals missing");

/// @audit (valid but excluded finding)
30:           require(address(comptroller_) != address(0), "comptroller missing");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L29

```solidity
File: contracts/plugins/assets/EURFiatCollateral.sol

/// @audit (valid but excluded finding)
24:           require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L24

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

/// @audit (valid but excluded finding)
73:           require(config.targetName != bytes32(0), "targetName missing");

/// @audit (valid but excluded finding)
75:               require(config.delayUntilDefault > 0, "delayUntilDefault zero");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L73

```solidity
File: contracts/plugins/assets/NonFiatCollateral.sol

/// @audit (valid but excluded finding)
24:           require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L24

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

/// @audit (valid but excluded finding)
28:           require(address(erc20_) != address(0), "missing erc20");

/// @audit (valid but excluded finding)
29:           require(maxTradeVolume_ > 0, "invalid max trade volume");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L28

```solidity
File: contracts/plugins/assets/SelfReferentialCollateral.sol

/// @audit (valid but excluded finding)
19:           require(config.defaultThreshold == 0, "default threshold not supported");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L19

```solidity
File: contracts/plugins/governance/Governance.sol

/// @audit (valid but excluded finding)
102:          require(startedInSameEra(proposalId), "new era");

/// @audit (valid but excluded finding)
112:          require(!startedInSameEra(proposalId), "same era");

/// @audit (valid but excluded finding)
123:          require(startedInSameEra(proposalId), "new era");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L102

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit (valid but excluded finding)
60:           require(status == begin, "Invalid trade state");

/// @audit (valid but excluded finding)
88:           require(req.sellAmount <= type(uint96).max, "sellAmount too large");

/// @audit (valid but excluded finding)
89:           require(req.minBuyAmount <= type(uint96).max, "minBuyAmount too large");

/// @audit (valid but excluded finding)
95:           require(initBal <= type(uint96).max, "initBal too large");

/// @audit (valid but excluded finding)
96:           require(initBal >= req.sellAmount, "unfunded trade");

/// @audit (valid but excluded finding)
174:          require(msg.sender == origin, "only origin can settle");

/// @audit (valid but excluded finding)
216:          require(status == TradeStatus.CLOSED, "only after trade is closed");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L60

### [G&#x2011;09]  Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are 
`CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about **21 gas per call** to the function, in addition to the extra deployment cost

*There are 12 instances of this issue:*

```solidity
File: contracts/mixins/Auth.sol

/// @audit (valid but excluded finding)
65:       function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {

/// @audit (valid but excluded finding)
120:      function freezeShort() external onlyRole(SHORT_FREEZER) {

/// @audit (valid but excluded finding)
135:      function freezeLong() external onlyRole(LONG_FREEZER) {

/// @audit (valid but excluded finding)
148:      function freezeForever() external onlyRole(OWNER) {

/// @audit (valid but excluded finding)
157:      function unfreeze() external onlyRole(OWNER) {

/// @audit (valid but excluded finding)
165:      function pause() external onlyRole(PAUSER) {

/// @audit (valid but excluded finding)
172:      function unpause() external onlyRole(PAUSER) {

/// @audit (valid but excluded finding)
180:      function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {

/// @audit (valid but excluded finding)
187:      function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L65

```solidity
File: contracts/mixins/ComponentRegistry.sol

/// @audit (valid but excluded finding)
19:       function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L19

```solidity
File: contracts/p1/Main.sol

/// @audit (valid but excluded finding)
64:       function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L64

```solidity
File: contracts/p1/mixins/Component.sol

/// @audit (valid but excluded finding)
33:       function __Component_init(IMain main_) internal onlyInitializing {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L33
