
## Summary

### Gas Optimizations
| |Issue|Instances|Total Gas Saved|
|-|:-|:-:|:-:|
| [G&#x2011;01] | Using `storage` instead of `memory` for structs/arrays saves gas | 1 |  4200 |
| [G&#x2011;02] | State variables only set in the constructor should be declared `immutable` | 13 |  27261 |
| [G&#x2011;03] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 13 |  - |
| [G&#x2011;04] | State variables can be packed into fewer storage slots | 2 |  - |
| [G&#x2011;05] | Ternary unnecessary | 1 |  - |
| [G&#x2011;06] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 69 |  4140 |
| [G&#x2011;07] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 28 |  2380 |

Total: 127 instances over 7 issues with **37981 gas** saved.

Gas totals use lower bounds of ranges and count two iterations of each `for`-loop. All values above are runtime, not deployment, values; deployment values are listed in the individual issue descriptions.

## Gas Optimizations

### [G&#x2011;01]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct.

*There is 1 instance of this issue:*

```solidity
File: protocol\contracts\plugins\mocks\EasyAuction.sol

518         AuctionData memory auction = auctionData[auctionId];
```

### [G&#x2011;02]  State variables only set in the constructor should be declared `immutable`
Avoids a Gsset (**20000 gas**) in the constructor, and replaces the first access in each transaction (Gcoldsload - **2100 gas**) and each access thereafter (Gwarmaccess - **100 gas**) with a `PUSH32` (**3 gas**). 

While `string`s are not value types, and therefore cannot be `immutable`/`constant` if not hard-coded outside of the constructor, the same behavior can be achieved by making the current contract `abstract` with `virtual` functions for the `string` accessors, and having a child contract override the functions with the hard-coded implementation-specific values.

*There are 13 instances of this issue:*

```solidity
File: protocol\contracts\p1\Deployer.sol

70          implementations = implementations_;
```

```solidity
File: protocol\contracts\plugins\aave\ERC20.sol

58          _name = name;

59          _symbol = symbol;
```

```solidity
File: protocol\contracts\plugins\aave\StaticATokenLM.sol

84          LENDING_POOL = pool;

85          ATOKEN = IERC20(aToken);

95                  INCENTIVES_CONTROLLER = incentivesController;

96                  REWARD_TOKEN = IERC20(INCENTIVES_CONTROLLER.REWARD_TOKEN());

100         ASSET = IERC20(IAToken(aToken).UNDERLYING_ASSET_ADDRESS());
```

```solidity
File: protocol\contracts\plugins\mocks\AaveLendingPoolMock.sol

28          _lendingAddressesProvider = ILendingPoolAddressesProvider(lendingAddressesProvider);
```

```solidity
File: protocol\contracts\plugins\mocks\ATokenMock.sol

17          _underlyingAsset = underlyingAsset;

44          aToken = new ATokenMock(name, symbol, underlyingAsset);
```

```solidity
File: protocol\contracts\plugins\mocks\ChainlinkMock.sol

32          decimals = _decimals;
```

```solidity
File: protocol\contracts\plugins\mocks\CTokenMock.sol

19          _underlyingToken = underlyingToken;
```

### [G&#x2011;03]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There is 1 instance of this issue:*
*There are X instances of this issue:*

```solidity
File: protocol\contracts\p1\BasketHandler.sol

40      mapping(IERC20 => uint192) targetAmts;
42      mapping(IERC20 => bytes32) targetNames;
```

```solidity
File: protocol\contracts\p1\StRSR.sol

60      mapping(uint256 => mapping(address => uint256)) private stakes; // Stakes per account {qStRSR}
68      mapping(uint256 => mapping(address => mapping(address => uint256))) private _allowances;

81      mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {drafts}
82      mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index
```

```solidity
File: protocol\contracts\p1\StRSRVotes.sol

38      mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}
40      mapping(uint256 => Checkpoint[]) private _totalSupplyCheckpoints; // {qStRSR}
```

```solidity
File: protocol\contracts\plugins\aave\ERC20.sol

38      mapping(address => uint256) private _balances;
40      mapping(address => mapping(address => uint256)) private _allowances;
```

```solidity
File: protocol\contracts\plugins\aave\StaticATokenLM.sol

74      mapping(address => uint256) private _userSnapshotRewardsPerToken;
76      mapping(address => uint256) private _unclaimedRewards;
```

```solidity
File: protocol\contracts\plugins\mocks\ChainlinkMock.sol

27      mapping(uint256 => int256) public getAnswer;
28      mapping(uint256 => uint256) public getTimestamp;
29      mapping(uint256 => uint256) private getStartedAt;
```

```solidity
File: protocol\contracts\plugins\mocks\EasyAuction.sol

112     mapping(uint256 => IterableOrderedOrderSet.Data) internal sellOrders;
113     mapping(uint256 => AuctionData) public auctionData;
114     mapping(uint256 => address) public auctionAccessManager;
115     mapping(uint256 => bytes) public auctionAccessData;
```

```solidity
File: protocol\contracts\plugins\mocks\WETH.sol

30      mapping(address => uint256) public balanceOf;
31      mapping(address => mapping(address => uint256)) public allowance;
```

```solidity
File: protocol\contracts\plugins\mocks\vendor\EasyAuction.sol

233         mapping(bytes32 => bytes32) nextMap;
234         mapping(bytes32 => bytes32) prevMap;
```

### [G&#x2011;04]  State variables can be packed into fewer storage slots
If variables occupying the same slot are both written by the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper.

*There are 2 instances of this issue:*

```solidity
File: protocol\contracts\p1\BasketHandler.sol

/// @audit can be after `assetRegistry`
134     uint48 public override nonce; // A unique identifier for this basket instance

/// @audit can be after `backingManager`
135     uint48 public override timestamp; // The timestamp when this basket was last set
139     bool private disabled;
```

```solidity
File: protocol\contracts\plugins\mocks\InvalidBrokerMock.sol

/// @audit can be after `gnosis`
22      uint48 public auctionLength; // {s} the length of an auction
24      bool public disabled = false;
```

### [G&#x2011;05]  Ternary unnecessary
`z = (x == y) ? true : false` => `z = (x == y)`.

*There is 1 instance of this issue:*

```solidity
File: protocol\contracts\plugins\mocks\vendor\EasyAuction.sol

363         if (userIdLeft < userIdRight) {
364             return true;
365         }
366         return false;
```

### [G&#x2011;06]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**.

*There are 69 instances of this issue:*

```solidity
File: protocol\contracts\libraries\Array.sol

11          for (uint256 i = 1; i < arrLen; ++i) {

12              for (uint256 j = 0; j < i; ++j) {

23          for (uint256 i = 1; i < arrLen; ++i) {
```

```solidity
File: protocol\contracts\libraries\Fixed.sol

165             result++;

169             result++;
```

```solidity
File: protocol\contracts\libraries\String.sol

14          for (uint256 i = 0; i < bStr.length; i++) {
```

```solidity
File: protocol\contracts\p1\AssetRegistry.sol

38          for (uint256 i = 0; i < length; ++i) {

49          for (uint256 i = 0; i < length; ++i) {

127         for (uint256 i = 0; i < length; ++i) {

138         for (uint256 i = 0; i < length; ++i) {
```

```solidity
File: protocol\contracts\p1\BackingManager.sol

221         for (uint256 i = 0; i < length; ++i) {

238         for (uint256 i = 0; i < length; ++i) {
```

```solidity
File: protocol\contracts\p1\BasketHandler.sol

70          for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;

78          for (uint256 i = 0; i < length; ++i) {

218         for (uint256 i = 0; i < config.erc20s.length; ++i) {

227         for (uint256 i = 0; i < erc20s.length; ++i) {

262         for (uint256 i = 0; i < erc20s.length; ++i) {

286         for (uint256 i = 0; i < size; ++i) {

337         for (uint256 i = 0; i < len; ++i) {

397         for (uint256 i = 0; i < len; ++i) {

416         for (uint256 i = 0; i < length; ++i) {

437         for (uint256 i = 0; i < length; ++i) {

530         for (uint256 i = 0; i < basketLength; ++i) {

548         for (uint256 i = 0; i < basketLength; ++i) {

553             for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {

586         for (uint256 i = 0; i < targetsLength; ++i) {

597             for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {

598                 if (goodCollateral(_targetNames.at(i), backup.erc20s[j])) size++;

611             for (uint256 j = 0; j < backupLength && assigned < size; ++j) {

621                     assigned++;

643         for (uint256 i = 0; i < basketLength; ++i) {

653         for (uint256 i = 0; i < erc20s.length; i++) {

707         for (uint256 i = 0; i < erc20s.length; ++i) {

725         for (uint256 i = 0; i < erc20s.length; ++i) {
```

```solidity
File: protocol\contracts\p1\Distributor.sol

108         for (uint256 i = 0; i < destinations.length(); ++i) {

128             numTransfers++;

133         for (uint256 i = 0; i < numTransfers; i++) {

143         for (uint256 i = 0; i < length; ++i) {
```

```solidity
File: protocol\contracts\p1\RToken.sol

270             for (uint256 i = 0; i < erc20s.length; ++i) {

303             for (uint256 i = 0; i < basketSize; ++i) {

316         queue.right++;

329         for (uint256 i = 0; i < basketSize; ++i) {

334         for (uint256 i = 0; i < basketSize; ++i) {

478         for (uint256 i = 0; i < erc20length; ++i) {

501         for (uint256 i = 0; i < erc20length; ++i) {

674             for (uint256 i = 0; i < tokensLen; ++i) {

683             for (uint256 i = 0; i < tokensLen; ++i) {

711         for (uint256 i = 0; i < queue.tokens.length; ++i) {

757             for (uint256 i = 0; i < tokensLen; ++i) {

767             for (uint256 i = 0; i < tokensLen; ++i) {

793         for (uint256 i = 0; i < tokensLen; ++i) {
```

```solidity
File: protocol\contracts\p1\StRSR.sol

578         era++;

590         draftEra++;
```

```solidity
File: protocol\contracts\p1\mixins\RecollateralizationLib.sol

242         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

329         for (uint256 i = 0; i < reg.erc20s.length; ++i) {

437         for (uint256 i = 0; i < len; ++i) {

```

```solidity
File: protocol\contracts\p1\mixins\RewardableLib.sol

27          for (uint256 i = 0; i < registry.assets.length; ++i) {

67          for (uint256 i = 0; i < erc20sLen; ++i) {

73          for (uint256 i = 0; i < erc20sLen; ++i) {
```

```solidity
File: protocol\contracts\p1\mixins\Trading.sol

121         tradesOpen++;
```

```solidity
File: protocol\contracts\plugins\mocks\ChainlinkMock.sol

39          latestRound++;
```

```solidity
File: protocol\contracts\plugins\mocks\EasyAuction.sol

277             for (uint256 i = 0; i < _minBuyAmounts.length; i++) {

288         for (uint256 i = 0; i < _minBuyAmounts.length; i++) {

316         for (uint256 i = 0; i < _sellOrders.length; i++) {

344         for (uint256 i = 0; i < iterationSteps; i++) {

513         for (uint256 i = 0; i < orders.length; i++) {

524         for (uint256 i = 0; i < orders.length; i++) {
```

```solidity
File: protocol\contracts\plugins\mocks\InvalidChainlinkMock.sol

55              i++;
```

```solidity
File: protocol\contracts\plugins\mocks\InvalidFiatCollateral.sol

36              i++;
```

### [G&#x2011;07]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`.

*There are 28 instances of this issue:*

```solidity
File: protocol\contracts\libraries\Fixed.sol

364         uint192 diff = x <= y ? y - x : x - y;
```

```solidity
File: protocol\contracts\libraries\RedemptionBattery.sol

/// @audit `require` on line 44
48          battery.lastCharge = charge - amount;
```

```solidity
File: protocol\contracts\p1\Furnace.sol

/// @audit `if`-condition on line 71
74          uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;
```

```solidity
File: protocol\contracts\p1\RToken.sol

/// @audit `if`-condition on line 295
296             IssueItem storage prev = queue.items[queue.right - 1];

/// @audit `queue.right++` on line 316
320             queue.right - 1,

360         uint192 nowStart = uint192(FIX_ONE * (block.number - 1)); // D18{block} = D18{1} * {block}

/// @audit `if`-condition on line 672
681             IssueItem storage leftItem = queue.items[left - 1];

/// @audit `if`-condition on line 754
764             IssueItem storage leftItem = queue.items[queue.left - 1];

/// @audit `if`-condition on line 804
810         uint256 high = (FIX_ONE_256 * basketsNeeded + (supply - 1)) / supply; // D18{BU/rTok}
```

```solidity
File: protocol\contracts\p1\StRSR.sol

/// @audit `if`-condition on line 310
313         require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");

/// @audit `if`-condition on line 310
316         uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;

/// @audit `if`-condition on line 391
393             stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);

/// @audit `rsrBalance - 1` on line 386
401         uint256 draftRSRToTake = (draftRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

/// @audit `if`-condition on line 406
408             draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);

/// @audit `rsrBalance - 1` on line 386
417         seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;

/// @audit `if`-condition on line 497
498         uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;

559         uint192 oldDrafts = index > 0 ? queue[index - 1].drafts : 0;

560         uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
```

```solidity
File: protocol\contracts\p1\StRSRVotes.sol

73          return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;

111         return high == 0 ? 0 : ckpts[high - 1].val;

199         oldWeight = pos == 0 ? 0 : ckpts[pos - 1].val;

202         if (pos > 0 && ckpts[pos - 1].fromBlock == block.number) {

/// @audit `if`-condition on line 202
203             ckpts[pos - 1].val = SafeCastUpgradeable.toUint224(newWeight);
```

```solidity
File: protocol\contracts\plugins\assets\Asset.sol

/// @audit `if`-condition on line 138
141             uint192 lotMultiplier = divuu(priceTimeout - delta, priceTimeout);
```

```solidity
File: protocol\contracts\plugins\mocks\GnosisMock.sol

/// @audit `if`-condition on line 117
118             auction.sell.safeTransfer(auction.origin, auction.sellAmount - bid.sellAmount);
```

```solidity
File: protocol\contracts\plugins\mocks\GnosisMockReentrant.sol

84              auction.sell.safeTransfer(auction.origin, auction.sellAmount - bid.sellAmount);
```

```solidity
File: protocol\contracts\plugins\trading\GnosisTrade.sol

/// @audit `if`-condition on line 195
196             soldAmt = initBal - sellBal;

/// @audit `soldAmt = initBal - sellBal` on line 196 and `if`-condition on line 195
199             uint256 adjustedSoldAmt = Math.max(soldAmt - 1, 1);
```
