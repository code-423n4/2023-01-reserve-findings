### Table of contents
- [FINDINGS](#findings)
  - [Using immutable on variables that are only set in the constructor and never after (Gas saved: 4.2k)](#using-immutable-on-variables-that-are-only-set-in-the-constructor-and-never-after-gas-saved-42k)
  - [Cache storage values in memory to minimize SLOADs](#cache-storage-values-in-memory-to-minimize-sloads)
  - [Auth.sol.freezeUntil(): unfreezeAt should be cached(happy path saves 1 SLOAD)](#authsolfreezeuntil-unfreezeat-should-be-cachedhappy-path-saves-1-sload)
  - [Trading.sol.tryTrade(): broker should be cached](#tradingsoltrytrade-broker-should-be-cached)
  - [Governance.sol.startedInSameEra(): address(token) should be cached](#governancesolstartedinsameera-addresstoken-should-be-cached)
  - [The result of a function call should be cached rather than re-calling the function](#the-result-of-a-function-call-should-be-cached-rather-than-re-calling-the-function)
  - [The result of totalSupply() should be cached](#the-result-of-totalsupply-should-be-cached)
  - [Result of destinations.length() should be cached](#result-of-destinationslength-should-be-cached)
  - [Result of main.rToken() should be cached](#result-of-mainrtoken-should-be-cached)
- [Internal/Private functions only called once can be inlined to save gas](#internalprivate-functions-only-called-once-can-be-inlined-to-save-gas)
- [Multiple accesses of a mapping/array should use a local variable cache](#multiple-accesses-of-a-mappingarray-should-use-a-local-variable-cache)
  - [StRSRVotes.sol.getVotes(): \_checkpoints\[era\]\[account\] should be cached](#strsrvotessolgetvotes-_checkpointseraaccount-should-be-cached)
- [Tighly pack storage variables/optimize the order of variable declaration(Gas saved: 4k)](#tighly-pack-storage-variablesoptimize-the-order-of-variable-declarationgas-saved-4k)
  - [StRSR.sol: Pack uint48 payoutLastPaid with uint192 draftRate (Saves 2k)](#strsrsol-pack-uint48-payoutlastpaid-with-uint192-draftrate-saves-2k)
  - [Pack nonce, timestamp with assetRegistry,disabled with stRSR(Save 2k gas)](#pack-nonce-timestamp-with-assetregistrydisabled-with-strsrsave-2k-gas)
- [Pack structs by putting variable that can fit together next to each other](#pack-structs-by-putting-variable-that-can-fit-together-next-to-each-other)
  - [We can save one slot here: 2k gas](#we-can-save-one-slot-here-2k-gas)
- [Use calldata instead of memory for function parameters](#use-calldata-instead-of-memory-for-function-parameters)
- [Using storage instead of memory for structs/arrays saves gas](#using-storage-instead-of-memory-for-structsarrays-saves-gas)
- [Avoid contract existence checks by using solidity version 0.8.10 or later](#avoid-contract-existence-checks-by-using-solidity-version-0810-or-later)
- [x += y costs more gas than x = x + y for state variables](#x--y-costs-more-gas-than-x--x--y-for-state-variables)
- [Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead](#usage-of-uintsints-smaller-than-32-bytes-256-bits-incurs-overhead)
- [Using unchecked blocks to save gas](#using-unchecked-blocks-to-save-gas)
- [Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)](#using-unchecked-blocks-to-save-gas---increments-in-for-loop-can-be-unchecked---save-30-40-gas-per-loop-iteration)
- [Splitting require() statements that use \&\& saves gas - (saves 8 gas per \&\&)](#splitting-require-statements-that-use--saves-gas---saves-8-gas-per-)
- [require() or revert() statements that check input arguments should be at the top of the function](#require-or-revert-statements-that-check-input-arguments-should-be-at-the-top-of-the-function)

## FINDINGS
NB: Some functions have been truncated where necessary to just show affected parts of the code
Through out the report some places might be denoted with audit tags to show the actual place affected.

### Using immutable on variables that are only set in the constructor and never after (Gas saved: 4.2k)
Use immutable if you want to assign a permanent value at construction. Use constants if you already know the permanent value. Both get directly embedded in bytecode, saving SLOAD.
Variables only set in the constructor and never edited afterwards should be marked as immutable, as it would avoid the expensive storage-writing operation in the constructor (around 20 000 gas per variable) and replace the expensive storage-reading operations (around 2100 gas per reading) to a less expensive value reading (3 gas)

For name and symbol you can either simulate immutable via a function that returns the value or you can store into a bytes32 for the majority of short strings

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L44-L45
**Gas saved: 1 Instance = 2.1k**
`Total Instances: 2, total gas: 2* 2.1k= 4.2k`

```solidity
File: /contracts/plugins/aave/ERC20.sol
44:    string internal _name;
45:    string internal _symbol;
```


### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L196-L200
### Auth.sol.freezeUntil(): unfreezeAt should be cached(happy path saves 1 SLOAD)

```solidity
File: /contracts/mixins/Auth.sol
196:    function freezeUntil(uint48 newUnfreezeAt) private {
197:        require(newUnfreezeAt > unfreezeAt, "frozen");
198:        emit UnfreezeAtSet(unfreezeAt, newUnfreezeAt);
199:        unfreezeAt = newUnfreezeAt;
200:    }
```

```diff
diff --git a/contracts/mixins/Auth.sol b/contracts/mixins/Auth.sol
index 1f4502d9..4452830d 100644
--- a/contracts/mixins/Auth.sol
+++ b/contracts/mixins/Auth.sol
@@ -194,8 +194,9 @@ abstract contract Auth is AccessControlUpgradeable, IAuth {
     // checks: newUnfreezeAt > unfreezeAt
     // effects: unfreezeAt' = newUnfreezeAt
     function freezeUntil(uint48 newUnfreezeAt) private {
-        require(newUnfreezeAt > unfreezeAt, "frozen");
-        emit UnfreezeAtSet(unfreezeAt, newUnfreezeAt);
+        uint48 _unfreezeAt = unfreezeAt;
+        require(newUnfreezeAt > _unfreezeAt, "frozen");
+        emit UnfreezeAtSet(_unfreezeAt, newUnfreezeAt);
         unfreezeAt = newUnfreezeAt;
     }
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L111-L123
### Trading.sol.tryTrade(): broker should be cached
```solidity
File: /contracts/p1/mixins/Trading.sol
111:    function tryTrade(TradeRequest memory req) internal nonReentrant {

116:        IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);
117:        IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);
118:        ITrade trade = broker.openTrade(req);
```

```diff
diff --git a/contracts/p1/mixins/Trading.sol b/contracts/p1/mixins/Trading.sol
index 2ef15d17..88e5f28d 100644
--- a/contracts/p1/mixins/Trading.sol
+++ b/contracts/p1/mixins/Trading.sol
@@ -113,9 +113,10 @@ abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeabl
         IERC20 sell = req.sell.erc20();
         assert(address(trades[sell]) == address(0));

-        IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);
-        IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);
-        ITrade trade = broker.openTrade(req);
+        IBroker  _broker = broker;
+        IERC20Upgradeable(address(sell)).safeApprove(address(_broker), 0);
+        IERC20Upgradeable(address(sell)).safeApprove(address(_broker), req.sellAmount);
+        ITrade trade = _broker.openTrade(req);

```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L170-L175
### Governance.sol.startedInSameEra(): address(token) should be cached
```solidity
File: /contracts/plugins/governance/Governance.sol
170:    function startedInSameEra(uint256 proposalId) private view returns (bool) {
171:        uint256 startBlock = proposalSnapshot(proposalId);
172:        uint256 pastEra = IStRSRVotes(address(token)).getPastEra(startBlock);
173:        uint256 currentEra = IStRSRVotes(address(token)).currentEra();
174:        return currentEra == pastEra;
175:    }
```


```diff
diff --git a/contracts/plugins/governance/Governance.sol b/contracts/plugins/governance/Governance.sol
index f7e0b2ff..43f7ccae 100644
--- a/contracts/plugins/governance/Governance.sol
+++ b/contracts/plugins/governance/Governance.sol
@@ -169,8 +169,9 @@ contract Governance is

     function startedInSameEra(uint256 proposalId) private view returns (bool) {
         uint256 startBlock = proposalSnapshot(proposalId);
-        uint256 pastEra = IStRSRVotes(address(token)).getPastEra(startBlock);
-        uint256 currentEra = IStRSRVotes(address(token)).currentEra();
+        address _token = address(token);
+        uint256 pastEra = IStRSRVotes(_token).getPastEra(startBlock);
+        uint256 currentEra = IStRSRVotes(_token).currentEra();
         return currentEra == pastEra;
     }
 }
```

### The result of a function call should be cached rather than re-calling the function

External calls are expensive. Consider caching the following:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L233-L235
### The result of totalSupply() should be cached
```solidity
File: /contracts/p1/RToken.sol
233:        uint192 amtBaskets = uint192(
234:            totalSupply() > 0 ? mulDiv256(basketsNeeded, amtRToken, totalSupply()) : amtRToken
235:        );
```

```diff
diff --git a/contracts/p1/RToken.sol b/contracts/p1/RToken.sol
index 2420c8d6..438771f8 100644
--- a/contracts/p1/RToken.sol
+++ b/contracts/p1/RToken.sol
@@ -230,8 +230,9 @@ contract RTokenP1 is ComponentP1, ERC20PermitUpgradeable, IRToken {
         // amtBaskets: the BU change to be recorded by this issuance
         // D18{BU} = D18{BU} * {qRTok} / {qRTok}
         // Downcast is safe because an actual quantity of qBUs fits in uint192
+        uint256 _totalSupply = totalSupply();
         uint192 amtBaskets = uint192(
-            totalSupply() > 0 ? mulDiv256(basketsNeeded, amtRToken, totalSupply()) : amtRToken
+            _totalSupply > 0 ? mulDiv256(basketsNeeded, amtRToken, _totalSupply ) : amtRToken
         );

         (address[] memory erc20s, uint256[] memory deposits) = basketHandler.quote(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L87-L137
### Result of destinations.length() should be cached
**More critical as it's also being used inside a loop**
```solidity
File: /contracts/p1/Distributor.sol
87:    function distribute(

105:        Transfer[] memory transfers = new Transfer[](destinations.length());

108:        for (uint256 i = 0; i < destinations.length(); ++i) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L72-L77
### Result of main.rToken() should be cached
```solidity
File: /contracts/p1/BackingManager.sol
75:        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);//@audit: Initial call
76:        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);//@audit: 2nd call
```


## Internal/Private functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

Affected code:

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L199
```solidity
File: /contracts/plugins/assets/FiatCollateral.sol
199:    function alreadyDefaulted() internal view returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L543-L546
```solidity
File: /contracts/p1/StRSR.sol
543:    function pushDraft(address account, uint256 rsrAmount)
544:        internal
545:        returns (uint256 index, uint64 availableAt)
546:    {
```

## Multiple accesses of a mapping/array should use a local variable cache

Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times saves ~42 gas per access due to not having to perform the same offset calculation every time.
**Help the Optimizer by saving a storage variable's reference instead of repeatedly fetching it**

To help the optimizer,declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array. 
As an example, instead of repeatedly calling ```someMap[someIndex]```, save its reference like this: ```SomeStruct storage someStruct = someMap[someIndex]``` and use it.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L71-L74
### StRSRVotes.sol.getVotes(): \_checkpoints\[era]\[account] should be cached
```solidity
File: /contracts/p1/StRSRVotes.sol
71:    function getVotes(address account) public view returns (uint256) {
72:        uint256 pos = _checkpoints[era][account].length;
73:        return pos == 0 ? 0 : _checkpoints[era][account][pos - 1].val;
74:    }
```

## Tighly pack storage variables/optimize the order of variable declaration(Gas saved: 4k)

Here, the storage variables can be tightly packed 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L63-L150
### StRSR.sol: Pack uint48 payoutLastPaid with uint192 draftRate (Saves 2k)
`Gas = 1 instance * 2k = 2k`

```solidity
File: /contracts/p1/StRSR.sol
//@audit:  uint48 public payoutLastPaid; can be packed with uint192 public draftRate to save 1 SLOT

    uint192 public stakeRate; 

    uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE;
    mapping(uint256 => mapping(address => mapping(address => uint256))) private _allowances;

    uint256 internal draftEra;
    struct CumulativeDraft {
        uint176 drafts;
        uint64 availableAt; 
    }

    mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; 
    mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; 
    uint256 internal totalDrafts;
    uint256 internal draftRSR; 
    uint192 public draftRate; 

    uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; 
    mapping(address => CountersUpgradeable.Counter) private _nonces;

    bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );

    uint48 public unstakingDelay;
    uint48 public rewardPeriod;
    uint192 public rewardRatio; 
    uint48 public payoutLastPaid;
    uint256 internal rsrRewardsAtLastPayout;
```

```diff
diff --git a/contracts/p1/StRSR.sol b/contracts/p1/StRSR.sol
index 8fe1c3e7..c20033e5 100644
--- a/contracts/p1/StRSR.sol
+++ b/contracts/p1/StRSR.sol
@@ -83,7 +83,8 @@ abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeab
     uint256 internal totalDrafts; // Total of all drafts {qDrafts}
     uint256 internal draftRSR; // Amount of RSR backing all drafts {qRSR}
     uint192 public draftRate; // The exchange rate between drafts and RSR. D18{qDrafts/qRSR}
-
+    // {seconds} The last time when rewards were paid out
+    uint48 public payoutLastPaid;
     uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}

     // ==== Analysis Definitions for Financial State ====
@@ -143,8 +144,7 @@ abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeab
     //     payoutLastPaid was the timestamp when the last paid-up block ended
     //     rsrRewardsAtLastPayout was the value of rsrRewards() at that time

-    // {seconds} The last time when rewards were paid out
-    uint48 public payoutLastPaid;
+

     // {qRSR} How much reward RSR was held the last time rewards were paid out
     uint256 internal rsrRewardsAtLastPayout;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L120-L139
### Pack nonce, timestamp with assetRegistry,disabled with stRSR(Save 2k gas)

```solidity
File: /contracts/p1/BasketHandler.sol
120:    IAssetRegistry private assetRegistry;
121:    IBackingManager private backingManager;
122:    IERC20 private rsr;
123:    IRToken private rToken;
124:    IStRSR private stRSR;

128:    BasketConfig private config;

132:    Basket private basket;

134:    uint48 public override nonce; // A unique identifier for this basket instance
135:    uint48 public override timestamp; // The timestamp when this basket was last 
    
139:    bool private disabled;
```

```diff
+    uint48 public override nonce; // A unique identifier for this basket instance
+    uint48 public override timestamp; // The timestamp when this basket was last set
     // Peer components
     IAssetRegistry private assetRegistry;
     IBackingManager private backingManager;
     IERC20 private rsr;
     IRToken private rToken;
     IStRSR private stRSR;
+    // If disabled is true, status() is DISABLED, the basket is invalid,
+    // and everything except redemption should be paused.
+    bool private disabled;

     Basket private basket;

-    uint48 public override nonce; // A unique identifier for this basket instance
-    uint48 public override timestamp; // The timestamp when this basket was last set

-    bool private disabled;

```

## Pack structs by putting variable that can fit together next to each other
As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage ~20000 gas

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L19-L48
### We can save one slot here: 2k gas
```solidity
File: /contracts/interfaces/IDeployer.sol
struct DeploymentParams {
    RevenueShare dist; // revenue sharing splits between RToken and RSR
    uint192 minTradeVolume; // {UoA}
    uint192 rTokenMaxTradeVolume; // {UoA}
    uint48 shortFreeze; // {s} how long an initial freeze lasts
    uint48 longFreeze; // {s} how long each freeze extension lasts
    uint192 rewardRatio; // the fraction of available revenues that stRSR holders get each PayPeriod
    uint48 rewardPeriod; // {s} the atomic unit of rewards, determines # of exponential rounds
    uint48 unstakingDelay; // {s} the "thawing time" of staked RSR before withdrawal
    uint48 tradingDelay; // {s} how long to wait until starting auctions after switching basket
    uint48 auctionLength; // {s} the length of an auction
    uint192 backingBuffer; // {1} how much extra backing collateral to keep
    uint192 maxTradeSlippage; // {1} max slippage acceptable in a trade
    uint192 issuanceRate; // {1/block} number of RToken to issue per block / (RToken value)
    uint192 scalingRedemptionRate; // {1/hour} max fraction of supply that can be redeemed hourly
    uint256 redemptionRateFloor; // {qRTok/hour} the lowest possible hourly redemption limit
}
```


```diff
diff --git a/contracts/interfaces/IDeployer.sol b/contracts/interfaces/IDeployer.sol
index e2f61fe5..2282bbe2 100644
--- a/contracts/interfaces/IDeployer.sol
+++ b/contracts/interfaces/IDeployer.sol
@@ -20,31 +20,29 @@ struct DeploymentParams {
     // === Revenue sharing ===
     RevenueShare dist; // revenue sharing splits between RToken and RSR
     //
+    uint48 auctionLength; // {s} the length of an auction 6  slot 4
     // === Trade sizing ===
-    uint192 minTradeVolume; // {UoA}
-    uint192 rTokenMaxTradeVolume; // {UoA}
+    uint192 minTradeVolume; // {UoA} --> 24 slot 1
+    uint192 rTokenMaxTradeVolume; // {UoA} --> 24
     //
     // === Freezing ===
-    uint48 shortFreeze; // {s} how long an initial freeze lasts
-    uint48 longFreeze; // {s} how long each freeze extension lasts
+    uint48 shortFreeze; // {s} how long an initial freeze lasts --> 6 --> slot 2
+    uint48 longFreeze; // {s} how long each freeze extension lasts --> 6
     //
     // === Rewards (Furnace + StRSR) ===
-    uint192 rewardRatio; // the fraction of available revenues that stRSR holders get each PayPeriod
-    uint48 rewardPeriod; // {s} the atomic unit of rewards, determines # of exponential rounds
-    //
-    // === StRSR ===
-    uint48 unstakingDelay; // {s} the "thawing time" of staked RSR before withdrawal
-    //
-    // === BackingManager ===
-    uint48 tradingDelay; // {s} how long to wait until starting auctions after switching basket
-    uint48 auctionLength; // {s} the length of an auction
-    uint192 backingBuffer; // {1} how much extra backing collateral to keep
-    uint192 maxTradeSlippage; // {1} max slippage acceptable in a trade
-    //
+    uint192 rewardRatio; // the fraction of available revenues that stRSR holders get each PayPeriod 24 slot 3
+
```


## Use calldata instead of memory for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L61-L65
```solidity
File: /contracts/p1/Distributor.sol
61:    function setDistribution(address dest, RevenueShare memory share) external governance {
62:        _setDistribution(dest, share);
63:        RevenueTotals memory revTotals = totals();
64:        _ensureNonZeroDistribution(revTotals.rTokenTotal, revTotals.rsrTotal);
65:    }
```

```diff
diff --git a/contracts/p1/Distributor.sol b/contracts/p1/Distributor.sol
index 53322973..a0c47f8a 100644
--- a/contracts/p1/Distributor.sol
+++ b/contracts/p1/Distributor.sol
@@ -58,7 +58,7 @@ contract DistributorP1 is ComponentP1, IDistributor {
     // effects:
     //   destinations' = destinations.add(dest)
     //   distribution' = distribution.set(dest, share)
-    function setDistribution(address dest, RevenueShare memory share) external governance {
+    function setDistribution(address dest, RevenueShare calldata share) external governance {
         _setDistribution(dest, share);
         RevenueTotals memory revTotals = totals();
         _ensureNonZeroDistribution(revTotals.rTokenTotal, revTotals.rsrTotal);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L102-L108
```solidity
File: /contracts/p1/Deployer.sol

//@audit: string memory name,string memory symbol,DeploymentParams memory params should use calldata
102:    function deploy(
103:        string memory name,
104:        string memory symbol,
105:        string calldata mandate,
106:        address owner,
107:        DeploymentParams memory params
108:    ) external returns (address) {
```

## Using storage instead of memory for structs/arrays saves gas

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L229
```solidity
File: /contracts/plugins/trading/GnosisTrade.sol
229:        GnosisAuctionData memory data = gnosis.auctionData(auctionId);
```

```diff
diff --git a/contracts/plugins/trading/GnosisTrade.sol b/contracts/plugins/trading/GnosisTrade.sol
index 889e91f2..63e01f95 100644
--- a/contracts/plugins/trading/GnosisTrade.sol
+++ b/contracts/plugins/trading/GnosisTrade.sol
@@ -226,7 +226,7 @@ contract GnosisTrade is ITrade {
     // === Private ===

     function isAuctionCleared() private view returns (bool) {
-        GnosisAuctionData memory data = gnosis.auctionData(auctionId);
+        GnosisAuctionData storage data = gnosis.auctionData(auctionId);
         return data.clearingPriceOrder != bytes32(0);
     }
 }
```

## Avoid contract existence checks by using solidity version 0.8.10 or later

Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L49
```solidity
File: /contracts/p1/mixins/Trading.sol

//@audit: broker()
49:        broker = main_.broker();

//@audit: canSettle()
69:        require(trade.canSettle(), "cannot settle yet");

//@audit: settle()
75:        (uint256 soldAmt, uint256 boughtAmt) = trade.settle();

//@audit: openTrade(req)
118:        ITrade trade = broker.openTrade(req);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L26

```solidity
File: /contracts/p1/mixins/RewardableLib.sol

//@audit: getRegistry()
26:        Registry memory registry = reg.getRegistry();

//@audit: erc20s()
62:        IERC20[] memory erc20s = reg.erc20s();

//@audit: isRegistered(erc20)
96:        require(reg.isRegistered(erc20), "erc20 unregistered");
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L42
```solidity
File: /contracts/p1/mixins/Component.sol

//@audit: pausedOrFrozen()
42:        require(!main.pausedOrFrozen(), "paused or frozen");

//@audit: frozen()
47:        require(!main.frozen(), "frozen");

//@audit: hasRole(OWNER, _msgSender())
52:        require(main.hasRole(OWNER, _msgSender()), "governance only");
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L69
```solidity
File: /contracts/p1/mixins/RecollateralizationLib.sol

//@audit: main()
69:        IMain main = bm.main();

//@audit: prepareTradeSell(rules)
104:            (doTrade, req) = trade.prepareTradeSell(rules);

//@audit: prepareTradeToCoverDeficit(rules)
106:            (doTrade, req) = trade.prepareTradeToCoverDeficit(rules);

//@audit: status()
357:       status = ICollateral(address(reg.assets[i])).status();


404:      uint192 rsrAvailable = rsrAsset.bal(address(components.bm)).plus(
405:                rsrAsset.bal(address(components.stRSR))
406:            );

//@audit: price()
407:            (uint192 low, uint192 high) = rsrAsset.price(); // {UoA/tok}

//@audit: lotPrice()
408:            (uint192 lotLow, ) = rsrAsset.lotPrice(); // {UoA/tok}

//@audit: bal(address(components.bm))
445:            uint192 held = coll.bal(address(components.bm)); // {tok}

//@audit: price()
449:                (, uint192 priceHigh) = coll.price(); // {UoA/tok}
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L93
```solidity
File: /contracts/plugins/trading/GnosisTrade.sol

//@audit: balanceOf(address(this))
93:        initBal = sell.balanceOf(address(this));

//@audit: feeNumerator()
115:                FEE_DENOMINATOR + gnosis.feeNumerator(),

//@audit: decimals()
125:            DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))

//@audit: initiateAuction()
139:        auctionId = gnosis.initiateAuction(

//@audit:settleAuction(auctionId)
181:            gnosis.settleAuction(auctionId);

//@audit: balanceOf(address(this))
188:        uint256 sellBal = sell.balanceOf(address(this));

//@audit: balanceOf(address(this))
189:        boughtAmt = buy.balanceOf(address(this));

//@audit: reportViolation()
207:                broker.reportViolation();

//@audit: auctionData(auctionId)
229:        GnosisAuctionData memory data = gnosis.auctionData(auctionId);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L150
```solidity
File: /contracts/plugins/governance/Governance.sol

//@audit: getPastVotes(account, blockNumber)
150:        uint256 bal = token.getPastVotes(account, blockNumber); // {qStRSR}

//@audit: getPastTotalSupply(blockNumber)
151:        uint256 totalSupply = token.getPastTotalSupply(blockNumber); // {qStRSR}

//@audit: getPastEra(startBlock)
172:        uint256 pastEra = IStRSRVotes(address(token)).getPastEra(startBlock);

//@audit: currentEra()
173:        uint256 currentEra = IStRSRVotes(address(token)).currentEra();
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L58
```solidity
File: /contracts/plugins/assets/Asset.sol

//@audit: decimals()
58:        erc20Decimals = erc20.decimals();

//@audit: price(oracleTimeout)
79:        uint192 p = chainlinkFeed.price(oracleTimeout); // {UoA/tok}
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L46
```solidity
File: /contracts/plugins/assets/ATokenFiatCollateral.sol

//@audit: rate()
46:        uint256 rateInRAYs = IStaticAToken(address(erc20)).rate(); // {ray ref/tok}

//@audit: REWARD_TOKEN()
53:        IERC20 stkAAVE = IStaticAToken(address(erc20)).REWARD_TOKEN();

//@audit: balanceOf(address(this))
54:        uint256 oldBal = stkAAVE.balanceOf(address(this));

//@audit: claimRewardsToSelf(true)
55:        IStaticAToken(address(erc20)).claimRewardsToSelf(true);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L32-L33
```solidity
File: /contracts/p1/RevenueTrader.sol

//@audit: assetRegistry()
32:        assetRegistry = main_.assetRegistry();

//@audit: distributor()
33:        distributor = main_.distributor();
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L163-L165
```solidity
File: /contracts/p1/RToken.sol

//@audit: assetRegistry()
163:        assetRegistry = main_.assetRegistry();

//@audit: basketHandler()
164:        basketHandler = main_.basketHandler();

//@audit: backingManager()
165:        backingManager = main_.backingManager();

//@audit: furnace()
166:        furnace = main_.furnace();

//@audit:nonce()
198:        uint48 basketNonce = basketHandler.nonce();

//@audit:nonce()
211:            basketNonce = basketHandler.nonce();

//@audit:status()
216:        CollateralStatus status = basketHandler.status();

//@audit: quote(
237:        (address[] memory erc20s, uint256[] memory deposits) = basketHandler.quote(

//@audit:status()
383:        CollateralStatus status = basketHandler.status();

//@audit:nonce()
387:        uint48 basketNonce = basketHandler.nonce();

//@audit:status()
448:        require(basketHandler.status() != CollateralStatus.DISABLED, "collateral default");

//@audit:quote(baskets, FLOOR)
465:        (address[] memory erc20s, uint256[] memory amounts) = basketHandler.quote(baskets, FLOOR);

//@audit:toAsset(erc20)
529:        RewardableLibP1.claimRewardsSingle(assetRegistry.toAsset(erc20));
```


## x += y costs more gas than x = x + y for state variables

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L72
```solidity
File: /contracts/p1/mixins/Trading.sol
72:        tradesOpen--;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L81
```solidity
File: /contracts/p1/Furnace.sol
81:        lastPayout += numPeriods * period;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L229
```solidity
File: /contracts/p1/StRSR.sol
229:        stakeRSR += rsrAmount;

387:        stakeRSR -= stakeRSRToTake;

396:            seizedRSR += stakeRSR;

402:        draftRSR -= draftRSRToTake;
403:        seizedRSR += draftRSRToTake;

412:            seizedRSR += draftRSR;

513:            stakeRSR += payout;

549:        draftRSR += rsrAmount;

699:        totalStakes += amount;

721:        totalStakes -= amount;
```
## Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L65
```solidity
File: /contracts/mixins/Auth.sol

//@audit: uint48 shortFreeze_, uint48 longFreeze_
65:    function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {

//@audit: uint48 shortFreeze_
180:    function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {

//@audit: uint48 longFreeze_
187:    function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {

//@audit: uint48 newUnfreezeAt
196:    function freezeUntil(uint48 newUnfreezeAt) private {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L128
```solidity
File: /contracts/p1/mixins/Trading.sol

//@audit: uint192 val
128:    function setMaxTradeSlippage(uint192 val) public governance {

//@audit: uint192 val
135:    function setMinTradeVolume(uint192 val) public governance {


//@audit:  uint192 x,uint192 y,uint192 z
144:    function mulDivCeil(
145:        uint192 x,
146:        uint192 y,
147:        uint192 z
148:    ) external pure returns (uint192) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L140-L145
```solidity
File: /contracts/p1/mixins/TradeLib.sol

//@audit:  uint192 amt,uint192 price,uint192 minTradeVolume
140:    function isEnoughToSell(
141:        IAsset asset,
142:        uint192 amt,
143:        uint192 price,
144:        uint192 minTradeVolume
145:    ) internal view returns (bool) {


//@audit:  uint192 x,uint192 y,uint192 z
154:    function safeMulDivCeil(
155:        ITrading trader,
156:        uint192 x,
157:        uint192 y,
158:        uint192 z
159:    ) internal pure returns (uint192) {

//@audit:  uint192 minTradeVolume,uint192 price
180:    function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {

//@audit:  uint192 price
188:    function maxTradeSize(IAsset asset, uint192 price) private view returns (uint192) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L428
```solidity
File: /contracts/p1/mixins/RecollateralizationLib.sol

//@audit:  uint192 basketsTop
428:    function collateralShortfall(ComponentCache memory components, uint192 basketsTop)

//@audit:  uint192 surplusAmt
460:    function isBetterSurplus(
461:        MaxSurplusDeficit memory curr,
462:        CollateralStatus other,
463:        uint192 surplusAmt
463:    ) private pure returns (bool) {

```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L81-L87
```solidity
File: /contracts/plugins/trading/GnosisTrade.sol

//@audit: uint48 auctionLength
81:    function init(
82:        IBroker broker_,
83:        address origin_,
84:        IGnosis gnosis_,
85:        uint48 auctionLength,
86:        TradeRequest calldata req
87:    ) external stateTransition(TradeStatus.NOT_STARTED, TradeStatus.OPEN) {
```

## Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L196
```solidity
File: /contracts/plugins/trading/GnosisTrade.sol
196:            soldAmt = initBal - sellBal;
```

The operation `initBal - sellBal` cannot underflow due to the check on [Line 195](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L195) that ensures that `initBal` is greater than `sellBal` before performing the arithmetic operation

```diff
diff --git a/contracts/plugins/trading/GnosisTrade.sol b/contracts/plugins/trading/GnosisTrade.sol
index 889e91f2..3fdd0d8b 100644
--- a/contracts/plugins/trading/GnosisTrade.sol
+++ b/contracts/plugins/trading/GnosisTrade.sol
@@ -193,7 +193,10 @@ contract GnosisTrade is ITrade {

         // Check clearing prices
         if (sellBal < initBal) {
-            soldAmt = initBal - sellBal;
+            unchecked {
+               soldAmt = initBal - sellBal;
+            }
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L315
```solidity
File: /contracts/p1/StRSR.sol
315:        uint192 oldDrafts = firstId > 0 ? queue[firstId - 1].drafts : 0;
```
The operation `firstId - 1` cannot underflow as it would only be performed if `firstId > 0`

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L559
```solidity
File: /contracts/p1/StRSR.sol
559:        uint192 oldDrafts = index > 0 ? queue[index - 1].drafts : 0;
560:        uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
```
The operation `index - 1` cannot underflow as it would only be performed if `index > 0`

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L296
```solidity
File: /contracts/p1/RToken.sol
296:            IssueItem storage prev = queue.items[queue.right - 1];
```
The operation `queue.right - 1` cannot underflow due to the check on [Line 295](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L295) that ensures that `queue.right` is greater than 0.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L810
```solidity
File: /contracts/p1/RToken.sol
810:        uint256 high = (FIX_ONE_256 * basketsNeeded + (supply - 1)) / supply; // D18{BU/rTok}
```
The operation `supply - 1` cannot underflow as supply must be greater than 0 before performing this operation. This condition is checked on [Line 804](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L804)


## Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11
```solidity
File: /contracts/libraries/Array.sol
9:    function allUnique(IERC20[] memory arr) internal pure returns (bool) {
10:        uint256 arrLen = arr.length;
11:        for (uint256 i = 1; i < arrLen; ++i) {
12:            for (uint256 j = 0; j < i; ++j) {
13:                if (arr[i] == arr[j]) return false;
14:            }
15:        }
16:        return true;
17:    }
```

The above should be modified to:

```diff
diff --git a/contracts/libraries/Array.sol b/contracts/libraries/Array.sol
index 9847cf8a..fdad37bc 100644
--- a/contracts/libraries/Array.sol
+++ b/contracts/libraries/Array.sol
@@ -11,6 +11,12 @@ library ArrayLib {
         for (uint256 i = 1; i < arrLen; ++i) {
             for (uint256 j = 0; j < i; ++j) {
                 if (arr[i] == arr[j]) return false;
+                unchecked {
+                    ++j;
+                }
+            }
+            unchecked {
+                ++i;
             }
         }
         return true;
```
**Other Instances to modify**

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23
```solidity
File: /contracts/libraries/Array.sol
23:        for (uint256 i = 1; i < arrLen; ++i) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14
```solidity
File: /contracts/libraries/String.sol
14:        for (uint256 i = 0; i < bStr.length; i++) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27
```solidity
File: /contracts/p1/mixins/RewardableLib.sol
27:        for (uint256 i = 0; i < registry.assets.length; ++i) {

67:        for (uint256 i = 0; i < erc20sLen; ++i) {

73:        for (uint256 i = 0; i < erc20sLen; ++i) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L242
```solidity
File: /contracts/p1/mixins/RecollateralizationLib.sol
242:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {

329:        for (uint256 i = 0; i < reg.erc20s.length; ++i) {

437:        for (uint256 i = 0; i < len; ++i) {

```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L38
```solidity
File: /contracts/p1/AssetRegistry.sol
39:        for (uint256 i = 0; i < length; ++i) {

49:        for (uint256 i = 0; i < length; ++i) {

127:        for (uint256 i = 0; i < length; ++i) {

138:        for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L221
```solidity
File:/contracts/p1/BackingManager.sol
221:        for (uint256 i = 0; i < length; ++i) {

238:        for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L70
```solidity
File: /contracts/p1/BasketHandler.sol
70:        for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;

78:        for (uint256 i = 0; i < length; ++i) {

219:        for (uint256 i = 0; i < config.erc20s.length; ++i) {

227:        for (uint256 i = 0; i < erc20s.length; ++i) {

262:        for (uint256 i = 0; i < erc20s.length; ++i) {

286:        for (uint256 i = 0; i < size; ++i) {

337:        for (uint256 i = 0; i < len; ++i) {

397:        for (uint256 i = 0; i < len; ++i) {

416:        for (uint256 i = 0; i < length; ++i) {

437:        for (uint256 i = 0; i < length; ++i) {

530:        for (uint256 i = 0; i < basketLength; ++i) {

548:        for (uint256 i = 0; i < basketLength; ++i) {

553:        for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {

586:        for (uint256 i = 0; i < targetsLength; ++i) {

597:        for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {

611:        for (uint256 j = 0; j < backupLength && assigned < size; ++j) {

643:        for (uint256 i = 0; i < basketLength; ++i) {

653:        for (uint256 i = 0; i < erc20s.length; i++) {

707:        for (uint256 i = 0; i < erc20s.length; ++i) {

725:        for (uint256 i = 0; i < erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L108
```solidity
File: /contracts/p1/Distributor.sol
108:        for (uint256 i = 0; i < destinations.length(); ++i) {

133:        for (uint256 i = 0; i < numTransfers; i++) {

143:        for (uint256 i = 0; i < length; ++i) {


```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270
```solidity
File: /contracts/p1/RToken.sol
270:            for (uint256 i = 0; i < erc20s.length; ++i) {

303:            for (uint256 i = 0; i < basketSize; ++i) {

329:        for (uint256 i = 0; i < basketSize; ++i) {

334:        for (uint256 i = 0; i < basketSize; ++i) {

478:        for (uint256 i = 0; i < erc20length; ++i) {

501:        for (uint256 i = 0; i < erc20length; ++i) {

674:            for (uint256 i = 0; i < tokensLen; ++i) {

683:            for (uint256 i = 0; i < tokensLen; ++i) {

711:        for (uint256 i = 0; i < queue.tokens.length; ++i) {

757:            for (uint256 i = 0; i < tokensLen; ++i) {

767:            for (uint256 i = 0; i < tokensLen; ++i) {

793:        for (uint256 i = 0; i < tokensLen; ++i) {
```
[see resource](https://github.com/ethereum/solidity/issues/10695)


## Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181
```solidity
File: /contracts/mixins/Auth.sol
181:        require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");

188:        require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");


```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L50
```solidity
File: /contracts/plugins/assets/Asset.sol
50:        require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L186-L190
```solidity
File: /contracts/p1/BasketHandler.sol
186:        require(
187:            main.hasRole(OWNER, _msgSender()) ||
188:                (status() == CollateralStatus.DISABLED && !main.pausedOrFrozen()),
189:            "basket unrefreshable"
190:        );
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L134-L137
```solidity
File: /contracts/p1/Broker.sol
134:        require(
135:            newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
136:            "invalid auctionLength"
137:        );
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48-L65
```solidity
File: /contracts/p1/Deployer.sol
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

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109
```solidity
File: /contracts/p1/Deployer.sol
109:        require(owner != address(0) && owner != address(this), "invalid owner");
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89
```solidity
File: /contracts/p1/Furnace.sol
89:        require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72
```solidity
File: /contracts/p1/RevenueTrader.sol
72:        require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410
```solidity
File: /contracts/p1/RToken.sol
410:        require(queue.left <= endId && endId <= queue.right, "out of range");

590:        require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");

623:        require(index >= item.left && index < item.right, "out of range");

813:        require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
```


##  require() or revert() statements that check input arguments should be at the top of the function

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L257-L277
```solidity
File: /contracts/p1/StRSR.sol
257:    function unstake(uint256 stakeAmount) external notPausedOrFrozen {
258:        address account = _msgSender();
259:        require(stakeAmount > 0, "Cannot withdraw zero");
260:        require(stakes[era][account] >= stakeAmount, "Not enough balance");
```

```diff
diff --git a/contracts/p1/StRSR.sol b/contracts/p1/StRSR.sol
index 8fe1c3e7..da87b976 100644
--- a/contracts/p1/StRSR.sol
+++ b/contracts/p1/StRSR.sol
@@ -255,8 +255,8 @@ abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeab
     //   A draft for (totalDrafts' - totalDrafts) drafts
     //   is freshly appended to the caller's draft record.
     function unstake(uint256 stakeAmount) external notPausedOrFrozen {
-        address account = _msgSender();
         require(stakeAmount > 0, "Cannot withdraw zero");
+        address account = _msgSender();
         require(stakes[era][account] >= stakeAmount, "Not enough balance");

         _payoutRewards();
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L374-L422
```solidity
File: /contracts/p1/StRSR.sol
374:    function seizeRSR(uint256 rsrAmount) external notPausedOrFrozen {
375:        require(_msgSender() == address(backingManager), "not backing manager");
376:        require(rsrAmount > 0, "Amount cannot be zero");
377:        uint192 initRate = exchangeRate();
```

In the above, we should reorder the require statement to have the cheaper one before the most expensive one, this way we can fail early and cheaply.
See below.
```diff
diff --git a/contracts/p1/StRSR.sol b/contracts/p1/StRSR.sol
index 8fe1c3e7..9ec7f8e3 100644
--- a/contracts/p1/StRSR.sol
+++ b/contracts/p1/StRSR.sol
@@ -372,8 +372,8 @@ abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeab
     //   seized >= rsrAmount, which should be a logical consequence of the above effects

     function seizeRSR(uint256 rsrAmount) external notPausedOrFrozen {
-        require(_msgSender() == address(backingManager), "not backing manager");
         require(rsrAmount > 0, "Amount cannot be zero");
+        require(_msgSender() == address(backingManager), "not backing manager");
         uint192 initRate = exchangeRate();

         uint256 rsrBalance = rsr.balanceOf(address(this));
```


