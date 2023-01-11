
### [G01] State variables only set in the constructor should be declared `immutable`

#### Impact
Avoids a Gusset (20000 gas)
#### Findings:
```
reserve-protocol/contracts/mixins/Auth.sol::36 => uint48 public unfreezeAt; // {s} uint48.max to pause indefinitely
reserve-protocol/contracts/mixins/Auth.sol::37 => uint48 public shortFreeze; // {s} length of an initial freeze
reserve-protocol/contracts/mixins/Auth.sol::38 => uint48 public longFreeze; // {s} length of a freeze extension
reserve-protocol/contracts/mixins/Auth.sol::42 => bool public paused;
reserve-protocol/contracts/mixins/Auth.sol::207 => uint256[48] private __gap;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::34 => IRToken public rToken;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::42 => IStRSR public stRSR;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::50 => IAssetRegistry public assetRegistry;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::58 => IBasketHandler public basketHandler;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::66 => IBackingManager public backingManager;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::74 => IDistributor public distributor;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::82 => IRevenueTrader public rsrTrader;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::90 => IRevenueTrader public rTokenTrader;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::98 => IFurnace public furnace;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::106 => IBroker public broker;
reserve-protocol/contracts/p1/AssetRegistry.sol::16 => IBasketHandler private basketHandler;
reserve-protocol/contracts/p1/AssetRegistry.sol::19 => EnumerableSet.AddressSet private _erc20s;
reserve-protocol/contracts/p1/BackingManager.sol::25 => IAssetRegistry private assetRegistry;
reserve-protocol/contracts/p1/BackingManager.sol::26 => IBasketHandler private basketHandler;
reserve-protocol/contracts/p1/BackingManager.sol::27 => IDistributor private distributor;
reserve-protocol/contracts/p1/BackingManager.sol::28 => IRToken private rToken;
reserve-protocol/contracts/p1/BackingManager.sol::29 => IERC20 private rsr;
reserve-protocol/contracts/p1/BackingManager.sol::30 => IStRSR private stRSR;
reserve-protocol/contracts/p1/BackingManager.sol::31 => IRevenueTrader private rsrTrader;
reserve-protocol/contracts/p1/BackingManager.sol::32 => IRevenueTrader private rTokenTrader;
reserve-protocol/contracts/p1/BackingManager.sol::36 => uint48 public tradingDelay; // {s} how long to wait until resuming trading after switching
reserve-protocol/contracts/p1/BackingManager.sol::37 => uint192 public backingBuffer; // {%} how much extra backing collateral to keep
reserve-protocol/contracts/p1/BasketHandler.sol::120 => IAssetRegistry private assetRegistry;
reserve-protocol/contracts/p1/BasketHandler.sol::121 => IBackingManager private backingManager;
reserve-protocol/contracts/p1/BasketHandler.sol::122 => IERC20 private rsr;
reserve-protocol/contracts/p1/BasketHandler.sol::123 => IRToken private rToken;
reserve-protocol/contracts/p1/BasketHandler.sol::124 => IStRSR private stRSR;
reserve-protocol/contracts/p1/BasketHandler.sol::128 => BasketConfig private config;
reserve-protocol/contracts/p1/BasketHandler.sol::132 => Basket private basket;
reserve-protocol/contracts/p1/BasketHandler.sol::134 => uint48 public override nonce; // A unique identifier for this basket instance
reserve-protocol/contracts/p1/BasketHandler.sol::135 => uint48 public override timestamp; // The timestamp when this basket was last set
reserve-protocol/contracts/p1/BasketHandler.sol::139 => bool private disabled;
reserve-protocol/contracts/p1/BasketHandler.sol::514 => EnumerableSet.Bytes32Set private _targetNames;
reserve-protocol/contracts/p1/BasketHandler.sol::515 => Basket private _newBasket; // Always empty
reserve-protocol/contracts/p1/Broker.sol::26 => IBackingManager private backingManager;
reserve-protocol/contracts/p1/Broker.sol::27 => IRevenueTrader private rsrTrader;
reserve-protocol/contracts/p1/Broker.sol::28 => IRevenueTrader private rTokenTrader;
reserve-protocol/contracts/p1/Broker.sol::31 => ITrade public tradeImplementation;
reserve-protocol/contracts/p1/Broker.sol::34 => IGnosis public gnosis;
reserve-protocol/contracts/p1/Broker.sol::37 => uint48 public auctionLength;
reserve-protocol/contracts/p1/Broker.sol::41 => bool public disabled;
reserve-protocol/contracts/p1/Deployer.sol::38 => Implementations public implementations;
reserve-protocol/contracts/p1/Distributor.sol::36 => IERC20 private rsr;
reserve-protocol/contracts/p1/Distributor.sol::37 => IERC20 private rToken;
reserve-protocol/contracts/p1/Distributor.sol::38 => address private furnace;
reserve-protocol/contracts/p1/Distributor.sol::39 => address private stRSR;
reserve-protocol/contracts/p1/Furnace.sol::18 => uint192 public ratio; // {1} What fraction of balance to melt each period
reserve-protocol/contracts/p1/Furnace.sol::19 => uint48 public period; // {seconds} How often to melt
reserve-protocol/contracts/p1/Furnace.sol::20 => uint256 public lastPayoutBal; // {qRTok} The balance of RToken at the last payout
reserve-protocol/contracts/p1/Furnace.sol::21 => uint48 public lastPayout; // {seconds} The last time we did a payout
reserve-protocol/contracts/p1/Furnace.sol::23 => IRToken private rToken;
reserve-protocol/contracts/p1/RToken.sol::44 => string public mandate;
reserve-protocol/contracts/p1/RToken.sol::50 => uint192 public issuanceRate;
reserve-protocol/contracts/p1/RToken.sol::59 => IAssetRegistry private assetRegistry;
reserve-protocol/contracts/p1/RToken.sol::60 => IBasketHandler private basketHandler;
reserve-protocol/contracts/p1/RToken.sol::61 => IBackingManager private backingManager;
reserve-protocol/contracts/p1/RToken.sol::62 => IFurnace private furnace;
reserve-protocol/contracts/p1/RToken.sol::67 => uint192 public basketsNeeded; // D18{BU}
reserve-protocol/contracts/p1/RToken.sol::72 => uint192 private allVestAt; // D18{fractional block number}
reserve-protocol/contracts/p1/RToken.sol::76 => uint192 private lastIssRate; // D18{rTok/block}
reserve-protocol/contracts/p1/RToken.sol::77 => uint256 private lastIssRateBlock; // {block number}
reserve-protocol/contracts/p1/RToken.sol::98 => RedemptionBatteryLib.Battery private battery;
reserve-protocol/contracts/p1/RevenueTrader.sol::19 => IERC20 public tokenToBuy;
reserve-protocol/contracts/p1/RevenueTrader.sol::20 => IAssetRegistry private assetRegistry;
reserve-protocol/contracts/p1/RevenueTrader.sol::21 => IDistributor private distributor;
reserve-protocol/contracts/p1/StRSR.sol::42 => string public name; // mutable
reserve-protocol/contracts/p1/StRSR.sol::43 => string public symbol; // mutable
reserve-protocol/contracts/p1/StRSR.sol::45 => uint8 public constant decimals = 18;
reserve-protocol/contracts/p1/StRSR.sol::48 => IAssetRegistry private assetRegistry;
reserve-protocol/contracts/p1/StRSR.sol::49 => IBackingManager private backingManager;
reserve-protocol/contracts/p1/StRSR.sol::50 => IBasketHandler private basketHandler;
reserve-protocol/contracts/p1/StRSR.sol::51 => IERC20 private rsr;
reserve-protocol/contracts/p1/StRSR.sol::63 => uint192 public stakeRate; // The exchange rate between stakes and RSR. D18{qStRSR/qRSR}
reserve-protocol/contracts/p1/StRSR.sol::85 => uint192 public draftRate; // The exchange rate between drafts and RSR. D18{qDrafts/qRSR}
reserve-protocol/contracts/p1/StRSR.sol::134 => uint48 public unstakingDelay; // {s} The minimum length of time spent in the draft queue
reserve-protocol/contracts/p1/StRSR.sol::135 => uint48 public rewardPeriod; // {s} The number of seconds between revenue payout events
reserve-protocol/contracts/p1/StRSR.sol::136 => uint192 public rewardRatio; // {1} The fraction of the revenue balance to handout per period
reserve-protocol/contracts/p1/StRSR.sol::147 => uint48 public payoutLastPaid;
reserve-protocol/contracts/p1/mixins/Trading.sol::24 => IBroker private broker;
reserve-protocol/contracts/p1/mixins/Trading.sol::27 => mapping(IERC20 => ITrade) public trades;
reserve-protocol/contracts/p1/mixins/Trading.sol::28 => uint48 public tradesOpen;
reserve-protocol/contracts/p1/mixins/Trading.sol::31 => uint192 public maxTradeSlippage; // {%}
reserve-protocol/contracts/p1/mixins/Trading.sol::33 => uint192 public minTradeVolume; // {UoA}
reserve-protocol/contracts/plugins/aave/ERC20.sol::42 => uint256 private _totalSupply;
reserve-protocol/contracts/plugins/aave/ERC20.sol::46 => uint8 private _decimals;
reserve-protocol/contracts/plugins/aave/ReentrancyGuard.sol::36 => uint256 private _status;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::60 => ILendingPool public override LENDING_POOL;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::61 => IAaveIncentivesController public override INCENTIVES_CONTROLLER;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::62 => IERC20 public override ATOKEN;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::63 => IERC20 public override ASSET;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::64 => IERC20 public override REWARD_TOKEN;
reserve-protocol/contracts/plugins/assets/Asset.sol::29 => uint192 public savedLowPrice; // {UoA/tok} The low price of the token during the last update
reserve-protocol/contracts/plugins/assets/Asset.sol::31 => uint192 public savedHighPrice; // {UoA/tok} The high price of the token during the last update
reserve-protocol/contracts/plugins/assets/Asset.sol::33 => uint48 public lastSave; // {s} The timestamp when prices were last saved
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::60 => uint192 public prevReferencePrice; // previous rate, {ref/tok}
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::37 => ERC20Mock public aaveToken;
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::11 => uint192 public transferFee; // {1}
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::13 => bool public revertDecimals;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::19 => uint8 public override decimals;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::20 => int256 public latestAnswer;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::21 => uint256 public latestTimestamp;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::22 => uint256 public latestRound;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::25 => uint256 public latestAnsweredRound;
reserve-protocol/contracts/plugins/mocks/ComptrollerMock.sol::10 => ERC20Mock public compToken;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::117 => IdToAddressBiMap.Data private registeredUsers;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::118 => uint64 public numUsers;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::119 => uint256 public auctionCounter;
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::52 => Mauction[] public auctions;
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::16 => bool public reenterOnInit;
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::17 => bool public reenterOnSettle;
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::18 => IGnosis public gnosis;
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::13 => bool public simplyRevert;
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::9 => bool public simplyRevert;
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::9 => uint192 private _targetPerRef;
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::31 => bool public priceable;
reserve-protocol/contracts/plugins/mocks/upgrades/AssetRegistryV2.sol::7 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/BackingManagerV2.sol::8 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/BasketHandlerV2.sol::7 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/BrokerV2.sol::7 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/DistributorV2.sol::7 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/FurnaceV2.sol::7 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/MainV2.sol::7 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/RTokenV2.sol::8 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/RevenueTraderV2.sol::8 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/upgrades/StRSRV2.sol::7 => uint256 public newValue;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::891 => address private _owner;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::37 => TradeStatus public status;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::41 => IGnosis public gnosis; // Gnosis Auction contract
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::42 => uint256 public auctionId; // The Gnosis Auction ID returned by gnosis.initiateAuction()
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::43 => IBroker public broker; // The Broker that cloned this contract into existence
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::48 => address public origin;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::49 => IERC20Metadata public sell; // address of token this trade is selling
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::50 => IERC20Metadata public buy; // address of token this trade is buying
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::51 => uint256 public initBal; // {qTok}, this trade's balance of `sell` when init() was called
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::52 => uint48 public endTime; // timestamp after which this trade's auction can be settled
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::53 => uint192 public worstCasePrice; // {buyTok/sellTok}, the worst price we expect to get at Auction
```




### [G02] State variables can be packed into fewer storage slots

#### Impact
If variables occupying the same slot are both written the same 
function or by the constructor avoids a separate Gsset (20000 gas). 
#### Findings:
```
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::48 => address public origin;
```



### [G03] `<array>.length` should not be looked up in every loop of a `for` loop

#### Impact
Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.
#### Findings:
```
reserve-protocol/contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
reserve-protocol/contracts/p1/AssetRegistry.sol::38 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::49 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::127 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::138 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BackingManager.sol::221 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BackingManager.sol::238 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::70 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
reserve-protocol/contracts/p1/BasketHandler.sol::78 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::416 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::437 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
reserve-protocol/contracts/p1/BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/Distributor.sol::143 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::270 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::478 => for (uint256 i = 0; i < erc20length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::501 => for (uint256 i = 0; i < erc20length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::524 => for (uint256 i = 0; i < orders.length; i++) {
```




### [G04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops


#### Findings:
```
reserve-protocol/contracts/libraries/Array.sol::11 => for (uint256 i = 1; i < arrLen; ++i) {
reserve-protocol/contracts/libraries/Array.sol::12 => for (uint256 j = 0; j < i; ++j) {
reserve-protocol/contracts/libraries/Array.sol::23 => for (uint256 i = 1; i < arrLen; ++i) {
reserve-protocol/contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
reserve-protocol/contracts/p1/AssetRegistry.sol::38 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::49 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::127 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::138 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BackingManager.sol::221 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BackingManager.sol::238 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::70 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
reserve-protocol/contracts/p1/BasketHandler.sol::78 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::286 => for (uint256 i = 0; i < size; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::337 => for (uint256 i = 0; i < len; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::397 => for (uint256 i = 0; i < len; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::416 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::437 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::530 => for (uint256 i = 0; i < basketLength; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::548 => for (uint256 i = 0; i < basketLength; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::553 => for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {
reserve-protocol/contracts/p1/BasketHandler.sol::586 => for (uint256 i = 0; i < targetsLength; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::597 => for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
reserve-protocol/contracts/p1/BasketHandler.sol::611 => for (uint256 j = 0; j < backupLength && assigned < size; ++j) {
reserve-protocol/contracts/p1/BasketHandler.sol::643 => for (uint256 i = 0; i < basketLength; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
reserve-protocol/contracts/p1/BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/Distributor.sol::108 => for (uint256 i = 0; i < destinations.length(); ++i) {
reserve-protocol/contracts/p1/Distributor.sol::133 => for (uint256 i = 0; i < numTransfers; i++) {
reserve-protocol/contracts/p1/Distributor.sol::143 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::270 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::303 => for (uint256 i = 0; i < basketSize; ++i) {
reserve-protocol/contracts/p1/RToken.sol::329 => for (uint256 i = 0; i < basketSize; ++i) {
reserve-protocol/contracts/p1/RToken.sol::334 => for (uint256 i = 0; i < basketSize; ++i) {
reserve-protocol/contracts/p1/RToken.sol::478 => for (uint256 i = 0; i < erc20length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::501 => for (uint256 i = 0; i < erc20length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::674 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/RToken.sol::683 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::757 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/RToken.sol::767 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/RToken.sol::793 => for (uint256 i = 0; i < tokensLen; ++i) { 
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::437 => for (uint256 i = 0; i < len; ++i) {
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::67 => for (uint256 i = 0; i < erc20sLen; ++i) {
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::73 => for (uint256 i = 0; i < erc20sLen; ++i) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::344 => for (uint256 i = 0; i < iterationSteps; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::524 => for (uint256 i = 0; i < orders.length; i++) {
```




### [G05] `require()`/`revert()` strings longer than 32 bytes cost extra gas



#### Findings:
```
reserve-protocol/contracts/p1/BasketHandler.sol::231 => require(0 < targetAmts[i], "invalid target amount; must be nonzero");
reserve-protocol/contracts/p1/BasketHandler.sol::657 => require(erc20s[i] != zero, "address zero is not valid collateral");
reserve-protocol/contracts/p1/StRSR.sol::380 => require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");
reserve-protocol/contracts/p1/StRSR.sol::660 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
reserve-protocol/contracts/p1/StRSR.sol::675 => require(from != address(0), "ERC20: transfer from the zero address");
reserve-protocol/contracts/p1/StRSR.sol::676 => require(to != address(0), "ERC20: transfer to the zero address");
reserve-protocol/contracts/p1/StRSR.sol::680 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
reserve-protocol/contracts/p1/StRSR.sol::711 => require(account != address(0), "ERC20: burn from the zero address");
reserve-protocol/contracts/p1/StRSR.sol::717 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
reserve-protocol/contracts/p1/StRSR.sol::732 => require(owner != address(0), "ERC20: approve from the zero address");
reserve-protocol/contracts/p1/StRSR.sol::733 => require(spender != address(0), "ERC20: approve to the zero address");
reserve-protocol/contracts/p1/StRSR.sol::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
reserve-protocol/contracts/p1/StRSR.sol::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
reserve-protocol/contracts/plugins/aave/ERC20.sol::242 => require(sender != address(0), "ERC20: transfer from the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::243 => require(recipient != address(0), "ERC20: transfer to the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::283 => require(account != address(0), "ERC20: burn from the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::310 => require(owner != address(0), "ERC20: approve from the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::311 => require(spender != address(0), "ERC20: approve to the zero address");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::131 => require(newFeeNumerator <= 15, "Fee is not allowed to be set higher than 1.5%");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::165 => require(_minBuyAmount > 0, "tokens cannot be auctioned for free");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::174 => require(auctionEndDate > block.timestamp, "auction end date must be in the future");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::289 => require(_minBuyAmounts[i] > 0, "_minBuyAmounts must be greater than 0");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::326 => require(userIdOfIter == userId, "Only the user can cancel his orders");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::526 => require(userIdOrder == userId, "only allowed to claim for same user");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::205 => require(abi.decode(returndata, (bool)), "SafeERC20: ERC20 operation did not succeed");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::362 => require(userIdLeft != userIdRight, "user is not allowed to place same order twice");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::370 => require(!isEmpty(self), "Trying to get first from empty set");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::375 => require(value != QUEUE_END, "Trying to get next of last element");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::377 => require(nextElement != bytes32(0), "Trying to get next of non-existent element");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::519 => require(c / a == b, "SafeMath: multiplication overflow");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::657 => require(value < 2**96, "SafeCast: value doesn't fit in 96 bits");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::662 => require(value < 2**64, "SafeCast: value doesn't fit in 64 bits");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::722 => require(success, "Address: unable to send value, recipient may have reverted");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::793 => require(address(this).balance >= value, "Address: insufficient balance for call");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::826 => require(isContract(target), "Address: static call to non-contract");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::936 => require(newOwner != address(0), "Ownable: new owner is the zero address");
```




### [G06] Not using the named return variables when a function returns, wastes deployment gas


#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::109 => return _safeWrap(shifted);
reserve-protocol/contracts/p1/AssetRegistry.sol::62 => return _register(asset);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::248 => return _staticToDynamicAmount(amount, rate());
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::253 => return _dynamicToStaticAmount(amount, rate());
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::908 => return _owner;
```




### [G07] Using `> 0` costs more gas than `!= 0` when used on a `uint` in a `require()` statement



#### Findings:
```

reserve-protocol/contracts/mixins/Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
reserve-protocol/contracts/mixins/Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
reserve-protocol/contracts/p1/BasketHandler.sol::213 => require(erc20s.length > 0, "cannot empty basket");
reserve-protocol/contracts/p1/Distributor.sol::98 => require(totalShares > 0, "nothing to distribute");
reserve-protocol/contracts/p1/Distributor.sol::182 => require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");
reserve-protocol/contracts/p1/Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
reserve-protocol/contracts/p1/RToken.sol::156 => require(bytes(name_).length > 0, "name empty");
reserve-protocol/contracts/p1/RToken.sol::157 => require(bytes(symbol_).length > 0, "symbol empty");
reserve-protocol/contracts/p1/RToken.sol::158 => require(bytes(mandate_).length > 0, "mandate empty");
reserve-protocol/contracts/p1/RToken.sol::191 => require(amtRToken > 0, "Cannot issue zero");
reserve-protocol/contracts/p1/RToken.sol::440 => require(amount > 0, "Cannot redeem zero");
reserve-protocol/contracts/p1/RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
reserve-protocol/contracts/p1/RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
reserve-protocol/contracts/p1/StRSR.sol::168 => require(bytes(name_).length > 0, "name empty");
reserve-protocol/contracts/p1/StRSR.sol::169 => require(bytes(symbol_).length > 0, "symbol empty");
reserve-protocol/contracts/p1/StRSR.sol::213 => require(rsrAmount > 0, "Cannot stake zero");
reserve-protocol/contracts/p1/StRSR.sol::259 => require(stakeAmount > 0, "Cannot withdraw zero");
reserve-protocol/contracts/p1/StRSR.sol::376 => require(rsrAmount > 0, "Amount cannot be zero");
reserve-protocol/contracts/p1/StRSR.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
reserve-protocol/contracts/p1/StRSR.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
reserve-protocol/contracts/plugins/assets/Asset.sol::48 => require(priceTimeout_ > 0, "price timeout zero");
reserve-protocol/contracts/plugins/assets/Asset.sol::50 => require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
reserve-protocol/contracts/plugins/assets/Asset.sol::52 => require(maxTradeVolume_ > 0, "invalid max trade volume");
reserve-protocol/contracts/plugins/assets/Asset.sol::53 => require(oracleTimeout_ > 0, "oracleTimeout zero");
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::29 => require(referenceERC20Decimals_ > 0, "referenceERC20Decimals missing");
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::74 => if (config.defaultThreshold > 0) {
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::75 => require(config.delayUntilDefault > 0, "delayUntilDefault zero");
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::29 => require(maxTradeVolume_ > 0, "invalid max trade volume");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::164 => require(_auctionedSellAmount > 0, "cannot auction zero tokens");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::165 => require(_minBuyAmount > 0, "tokens cannot be auctioned for free");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::289 => require(_minBuyAmounts[i] > 0, "_minBuyAmounts must be greater than 0");
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::69 => require(auctionedSellAmount > 0, "sell amount is zero");
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::92 => require(bid.buyAmount > 0, "zero volume bid");
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::33 => require(auctionedSellAmount > 0, "sell amount is zero");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::557 => require(b > 0, errorMessage);
```




### [G08] It costs more gas to initialize variables to zero than to let the default of zero be applied



#### Findings:
```
reserve-protocol/contracts/libraries/Array.sol::12 => for (uint256 j = 0; j < i; ++j) {
reserve-protocol/contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
reserve-protocol/contracts/p1/AssetRegistry.sol::38 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::49 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::127 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/AssetRegistry.sol::138 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BackingManager.sol::221 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BackingManager.sol::238 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::70 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
reserve-protocol/contracts/p1/BasketHandler.sol::78 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::286 => for (uint256 i = 0; i < size; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::337 => for (uint256 i = 0; i < len; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::397 => for (uint256 i = 0; i < len; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::416 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::437 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::530 => for (uint256 i = 0; i < basketLength; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::548 => for (uint256 i = 0; i < basketLength; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::553 => for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {
reserve-protocol/contracts/p1/BasketHandler.sol::586 => for (uint256 i = 0; i < targetsLength; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::597 => for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
reserve-protocol/contracts/p1/BasketHandler.sol::611 => for (uint256 j = 0; j < backupLength && assigned < size; ++j) {
reserve-protocol/contracts/p1/BasketHandler.sol::643 => for (uint256 i = 0; i < basketLength; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
reserve-protocol/contracts/p1/BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/Distributor.sol::108 => for (uint256 i = 0; i < destinations.length(); ++i) {
reserve-protocol/contracts/p1/Distributor.sol::133 => for (uint256 i = 0; i < numTransfers; i++) {
reserve-protocol/contracts/p1/Distributor.sol::143 => for (uint256 i = 0; i < length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::270 => for (uint256 i = 0; i < erc20s.length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::303 => for (uint256 i = 0; i < basketSize; ++i) {
reserve-protocol/contracts/p1/RToken.sol::329 => for (uint256 i = 0; i < basketSize; ++i) {
reserve-protocol/contracts/p1/RToken.sol::334 => for (uint256 i = 0; i < basketSize; ++i) {
reserve-protocol/contracts/p1/RToken.sol::478 => for (uint256 i = 0; i < erc20length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::501 => for (uint256 i = 0; i < erc20length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::674 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/RToken.sol::683 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
reserve-protocol/contracts/p1/RToken.sol::757 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/RToken.sol::767 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/RToken.sol::793 => for (uint256 i = 0; i < tokensLen; ++i) {
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::437 => for (uint256 i = 0; i < len; ++i) {
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::67 => for (uint256 i = 0; i < erc20sLen; ++i) {
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::73 => for (uint256 i = 0; i < erc20sLen; ++i) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::344 => for (uint256 i = 0; i < iterationSteps; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::524 => for (uint256 i = 0; i < orders.length; i++) {
```




### [G09] `++i` costs less gas than `i++`, especially when it’s used in forloops (`--i`/`i--` too)



#### Findings:
```

reserve-protocol/contracts/libraries/String.sol::14 => for (uint256 i = 0; i < bStr.length; i++) {
reserve-protocol/contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
reserve-protocol/contracts/p1/Distributor.sol::133 => for (uint256 i = 0; i < numTransfers; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::344 => for (uint256 i = 0; i < iterationSteps; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::524 => for (uint256 i = 0; i < orders.length; i++) {
```




### [G10] Splitting `require()` statements that use `&&` Cost gas

#### Impact
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) for an example
#### Findings:
```
reserve-protocol/contracts/mixins/Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
reserve-protocol/contracts/mixins/Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
reserve-protocol/contracts/p1/Deployer.sol::109 => require(owner != address(0) && owner != address(this), "invalid owner");
reserve-protocol/contracts/p1/Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
reserve-protocol/contracts/p1/RToken.sol::410 => require(queue.left <= endId && endId <= queue.right, "out of range");
reserve-protocol/contracts/p1/RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
reserve-protocol/contracts/p1/RToken.sol::623 => require(index >= item.left && index < item.right, "out of range");
reserve-protocol/contracts/p1/RToken.sol::741 => require(queue.left <= endId && endId <= queue.right, "out of range");
reserve-protocol/contracts/p1/RToken.sol::813 => require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
reserve-protocol/contracts/p1/RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
reserve-protocol/contracts/p1/StRSR.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
reserve-protocol/contracts/p1/StRSR.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
reserve-protocol/contracts/plugins/assets/Asset.sol::50 => require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
```




### [G11] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

#### Impact
> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed
#### Findings:
```
reserve-protocol/contracts/interfaces/IAsset.sol::26 => function price() external view returns (uint192 low, uint192 high);
reserve-protocol/contracts/interfaces/IAsset.sol::32 => function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh);
reserve-protocol/contracts/interfaces/IAsset.sol::35 => function bal(address account) external view returns (uint192);
reserve-protocol/contracts/interfaces/IAsset.sol::41 => function erc20Decimals() external view returns (uint8);
reserve-protocol/contracts/interfaces/IAsset.sol::47 => function maxTradeVolume() external view returns (uint192);
reserve-protocol/contracts/interfaces/IAsset.sol::98 => function refPerTok() external view returns (uint192);
reserve-protocol/contracts/interfaces/IAsset.sol::101 => function targetPerRef() external view returns (uint192);
reserve-protocol/contracts/interfaces/IBackingManager.sol::20 => event TradingDelaySet(uint48 indexed oldVal, uint48 indexed newVal);
reserve-protocol/contracts/interfaces/IBackingManager.sol::21 => event BackingBufferSet(uint192 indexed oldVal, uint192 indexed newVal);
reserve-protocol/contracts/interfaces/IBackingManager.sol::26 => uint48 tradingDelay_,
reserve-protocol/contracts/interfaces/IBackingManager.sol::27 => uint192 backingBuffer_,
reserve-protocol/contracts/interfaces/IBackingManager.sol::28 => uint192 maxTradeSlippage_,
reserve-protocol/contracts/interfaces/IBackingManager.sol::29 => uint192 minTradeVolume_
reserve-protocol/contracts/interfaces/IBackingManager.sol::50 => function tradingDelay() external view returns (uint48);
reserve-protocol/contracts/interfaces/IBackingManager.sol::52 => function backingBuffer() external view returns (uint192);
reserve-protocol/contracts/interfaces/IBackingManager.sol::54 => function setTradingDelay(uint48 val) external;
reserve-protocol/contracts/interfaces/IBackingManager.sol::56 => function setBackingBuffer(uint192 val) external;
reserve-protocol/contracts/interfaces/IBasketHandler.sol::21 => event PrimeBasketSet(IERC20[] erc20s, uint192[] targetAmts, bytes32[] targetNames);
reserve-protocol/contracts/interfaces/IBasketHandler.sol::44 => function setPrimeBasket(IERC20[] memory erc20s, uint192[] memory targetAmts) external;
reserve-protocol/contracts/interfaces/IBasketHandler.sol::77 => function quantity(IERC20 erc20) external view returns (uint192);
reserve-protocol/contracts/interfaces/IBasketHandler.sol::85 => function quote(uint192 amount, RoundingMode rounding)
reserve-protocol/contracts/interfaces/IBasketHandler.sol::91 => function basketsHeldBy(address account) external view returns (uint192 baskets);
reserve-protocol/contracts/interfaces/IBasketHandler.sol::96 => function price() external view returns (uint192 low, uint192 high);
reserve-protocol/contracts/interfaces/IBasketHandler.sol::102 => function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh);
reserve-protocol/contracts/interfaces/IBasketHandler.sol::105 => function nonce() external view returns (uint48);
reserve-protocol/contracts/interfaces/IBasketHandler.sol::108 => function timestamp() external view returns (uint48);
reserve-protocol/contracts/interfaces/IBroker.sol::23 => event AuctionLengthSet(uint48 indexed oldVal, uint48 indexed newVal);
reserve-protocol/contracts/interfaces/IBroker.sol::31 => uint48 auctionLength_
reserve-protocol/contracts/interfaces/IBroker.sol::48 => function auctionLength() external view returns (uint48);
reserve-protocol/contracts/interfaces/IBroker.sol::50 => function setAuctionLength(uint48 newAuctionLength) external;
reserve-protocol/contracts/interfaces/IDeployer.sol::24 => uint192 minTradeVolume; // {UoA}
reserve-protocol/contracts/interfaces/IDeployer.sol::25 => uint192 rTokenMaxTradeVolume; // {UoA}
reserve-protocol/contracts/interfaces/IDeployer.sol::28 => uint48 shortFreeze; // {s} how long an initial freeze lasts
reserve-protocol/contracts/interfaces/IDeployer.sol::29 => uint48 longFreeze; // {s} how long each freeze extension lasts
reserve-protocol/contracts/interfaces/IDeployer.sol::32 => uint192 rewardRatio; // the fraction of available revenues that stRSR holders get each PayPeriod
reserve-protocol/contracts/interfaces/IDeployer.sol::33 => uint48 rewardPeriod; // {s} the atomic unit of rewards, determines # of exponential rounds
reserve-protocol/contracts/interfaces/IDeployer.sol::36 => uint48 unstakingDelay; // {s} the "thawing time" of staked RSR before withdrawal
reserve-protocol/contracts/interfaces/IDeployer.sol::39 => uint48 tradingDelay; // {s} how long to wait until starting auctions after switching basket
reserve-protocol/contracts/interfaces/IDeployer.sol::40 => uint48 auctionLength; // {s} the length of an auction
reserve-protocol/contracts/interfaces/IDeployer.sol::41 => uint192 backingBuffer; // {1} how much extra backing collateral to keep
reserve-protocol/contracts/interfaces/IDeployer.sol::42 => uint192 maxTradeSlippage; // {1} max slippage acceptable in a trade
reserve-protocol/contracts/interfaces/IDeployer.sol::45 => uint192 issuanceRate; // {1/block} number of RToken to issue per block / (RToken value)
reserve-protocol/contracts/interfaces/IDeployer.sol::46 => uint192 scalingRedemptionRate; // {1/hour} max fraction of supply that can be redeemed hourly
reserve-protocol/contracts/interfaces/IDistributor.sol::8 => uint16 rTokenDist; // {revShare} A value between [0, 10,000]
reserve-protocol/contracts/interfaces/IDistributor.sol::9 => uint16 rsrDist; // {revShare} A value between [0, 10,000]
reserve-protocol/contracts/interfaces/IDistributor.sol::14 => uint24 rTokenTotal; // {revShare}
reserve-protocol/contracts/interfaces/IDistributor.sol::15 => uint24 rsrTotal; // {revShare}
reserve-protocol/contracts/interfaces/IDistributor.sol::28 => event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);
reserve-protocol/contracts/interfaces/IDistributor.sol::63 => function distribution(address) external view returns (uint16 rTokenDist, uint16 rsrDist);
reserve-protocol/contracts/interfaces/IFacadeRead.sol::37 => uint192[] memory uoaShares,
reserve-protocol/contracts/interfaces/IFacadeRead.sol::80 => uint192[] memory targetAmts
reserve-protocol/contracts/interfaces/IFacadeRead.sol::106 => returns (uint192 backing, uint192 overCollateralization);
reserve-protocol/contracts/interfaces/IFacadeRead.sol::110 => function price(IRToken rToken) external view returns (uint192 low, uint192 high);
reserve-protocol/contracts/interfaces/IFacadeTest.sol::25 => function totalAssetValue(IRToken rToken) external returns (uint192 total);
reserve-protocol/contracts/interfaces/IFacadeWrite.sol::28 => uint192[] weights;
reserve-protocol/contracts/interfaces/IFurnace.sol::15 => uint48 period_,
reserve-protocol/contracts/interfaces/IFurnace.sol::16 => uint192 ratio_
reserve-protocol/contracts/interfaces/IFurnace.sol::22 => event PeriodSet(uint48 indexed oldPeriod, uint48 indexed newPeriod);
reserve-protocol/contracts/interfaces/IFurnace.sol::24 => function period() external view returns (uint48);
reserve-protocol/contracts/interfaces/IFurnace.sol::27 => function setPeriod(uint48) external;
reserve-protocol/contracts/interfaces/IFurnace.sol::32 => event RatioSet(uint192 indexed oldRatio, uint192 indexed newRatio);
reserve-protocol/contracts/interfaces/IFurnace.sol::34 => function ratio() external view returns (uint192);
reserve-protocol/contracts/interfaces/IFurnace.sol::38 => function setRatio(uint192) external;
reserve-protocol/contracts/interfaces/IGnosis.sol::16 => uint96 volumeClearingPriceOrder;
reserve-protocol/contracts/interfaces/IGnosis.sol::31 => uint96 auctionedSellAmount,
reserve-protocol/contracts/interfaces/IGnosis.sol::32 => uint96 minBuyAmount,
reserve-protocol/contracts/interfaces/IMain.sol::52 => event UnfreezeAtSet(uint48 indexed oldVal, uint48 indexed newVal);
reserve-protocol/contracts/interfaces/IMain.sol::57 => event ShortFreezeDurationSet(uint48 indexed oldDuration, uint48 indexed newDuration);
reserve-protocol/contracts/interfaces/IMain.sol::62 => event LongFreezeDurationSet(uint48 indexed oldDuration, uint48 indexed newDuration);
reserve-protocol/contracts/interfaces/IMain.sol::78 => function shortFreeze() external view returns (uint48);
reserve-protocol/contracts/interfaces/IMain.sol::80 => function longFreeze() external view returns (uint48);
reserve-protocol/contracts/interfaces/IMain.sol::159 => uint48 shortFreeze_,
reserve-protocol/contracts/interfaces/IMain.sol::160 => uint48 longFreeze_
reserve-protocol/contracts/interfaces/IMain.sol::168 => function setShortFreeze(uint48) external;
reserve-protocol/contracts/interfaces/IMain.sol::171 => function setLongFreeze(uint48) external;
reserve-protocol/contracts/interfaces/IMain.sol::173 => function shortFreeze() external view returns (uint48);
reserve-protocol/contracts/interfaces/IMain.sol::175 => function longFreeze() external view returns (uint48);
reserve-protocol/contracts/interfaces/IRToken.sol::36 => uint192 baskets,
reserve-protocol/contracts/interfaces/IRToken.sol::39 => uint192 blockAvailableAt
reserve-protocol/contracts/interfaces/IRToken.sol::83 => event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);
reserve-protocol/contracts/interfaces/IRToken.sol::90 => event IssuanceRateSet(uint192 indexed oldVal, uint192 indexed newVal);
reserve-protocol/contracts/interfaces/IRToken.sol::93 => event ScalingRedemptionRateSet(uint192 indexed oldVal, uint192 indexed newVal);
reserve-protocol/contracts/interfaces/IRToken.sol::104 => uint192 issuanceRate_,
reserve-protocol/contracts/interfaces/IRToken.sol::105 => uint192 redemptionBattery_,
reserve-protocol/contracts/interfaces/IRToken.sol::162 => function setBasketsNeeded(uint192 basketsNeeded) external;
reserve-protocol/contracts/interfaces/IRToken.sol::165 => function basketsNeeded() external view returns (uint192);
reserve-protocol/contracts/interfaces/IRToken.sol::173 => function setIssuanceRate(uint192) external;
reserve-protocol/contracts/interfaces/IRToken.sol::176 => function issuanceRate() external view returns (uint192);
reserve-protocol/contracts/interfaces/IRToken.sol::179 => function setScalingRedemptionRate(uint192 val) external;
reserve-protocol/contracts/interfaces/IRToken.sol::182 => function scalingRedemptionRate() external view returns (uint192);
reserve-protocol/contracts/interfaces/IRevenueTrader.sol::18 => uint192 maxTradeSlippage_,
reserve-protocol/contracts/interfaces/IRevenueTrader.sol::19 => uint192 minTradeVolume_
reserve-protocol/contracts/interfaces/IStRSR.sol::69 => event ExchangeRateSet(uint192 indexed oldVal, uint192 indexed newVal);
reserve-protocol/contracts/interfaces/IStRSR.sol::79 => event UnstakingDelaySet(uint48 indexed oldVal, uint48 indexed newVal);
reserve-protocol/contracts/interfaces/IStRSR.sol::80 => event RewardPeriodSet(uint48 indexed oldVal, uint48 indexed newVal);
reserve-protocol/contracts/interfaces/IStRSR.sol::81 => event RewardRatioSet(uint192 indexed oldVal, uint192 indexed newVal);
reserve-protocol/contracts/interfaces/IStRSR.sol::88 => uint48 unstakingDelay_,
reserve-protocol/contracts/interfaces/IStRSR.sol::89 => uint48 rewardPeriod_,
reserve-protocol/contracts/interfaces/IStRSR.sol::90 => uint192 rewardRatio_
reserve-protocol/contracts/interfaces/IStRSR.sol::120 => function exchangeRate() external view returns (uint192);
reserve-protocol/contracts/interfaces/IStRSR.sol::124 => function rewardPeriod() external view returns (uint48);
reserve-protocol/contracts/interfaces/IStRSR.sol::126 => function setRewardPeriod(uint48) external;
reserve-protocol/contracts/interfaces/IStRSR.sol::128 => function rewardRatio() external view returns (uint192);
reserve-protocol/contracts/interfaces/IStRSR.sol::130 => function setRewardRatio(uint192) external;
reserve-protocol/contracts/interfaces/IStRSR.sol::132 => function unstakingDelay() external view returns (uint48);
reserve-protocol/contracts/interfaces/IStRSR.sol::134 => function setUnstakingDelay(uint48) external;
reserve-protocol/contracts/interfaces/ITrade.sol::17 => function endTime() external view returns (uint48);
reserve-protocol/contracts/interfaces/ITrading.sol::16 => event MaxTradeSlippageSet(uint192 indexed oldVal, uint192 indexed newVal);
reserve-protocol/contracts/interfaces/ITrading.sol::17 => event MinTradeVolumeSet(uint192 indexed oldVal, uint192 indexed newVal);
reserve-protocol/contracts/interfaces/ITrading.sol::52 => function maxTradeSlippage() external view returns (uint192);
reserve-protocol/contracts/interfaces/ITrading.sol::55 => function minTradeVolume() external view returns (uint192);
reserve-protocol/contracts/interfaces/ITrading.sol::62 => uint192 x,
reserve-protocol/contracts/interfaces/ITrading.sol::63 => uint192 y,
reserve-protocol/contracts/interfaces/ITrading.sol::64 => uint192 z
reserve-protocol/contracts/interfaces/ITrading.sol::65 => ) external pure returns (uint192);
reserve-protocol/contracts/interfaces/ITrading.sol::70 => function setMaxTradeSlippage(uint192 val) external;
reserve-protocol/contracts/interfaces/ITrading.sol::73 => function setMinTradeVolume(uint192 val) external;
reserve-protocol/contracts/interfaces/ITrading.sol::76 => function tradesOpen() external view returns (uint48);
reserve-protocol/contracts/libraries/Array.sol::24 => if (uint160(address(arr[i])) <= uint160(address(arr[i - 1]))) return false;
reserve-protocol/contracts/libraries/Fixed.sol::37 => uint8 constant FIX_DECIMALS = 18;
reserve-protocol/contracts/libraries/Fixed.sol::41 => uint64 constant FIX_SCALE = 1e18;
reserve-protocol/contracts/libraries/Fixed.sol::44 => uint128 constant FIX_SCALE_SQ = 1e36;
reserve-protocol/contracts/libraries/Fixed.sol::48 => uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;
reserve-protocol/contracts/libraries/Fixed.sol::50 => uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.
reserve-protocol/contracts/libraries/Fixed.sol::51 => uint192 constant FIX_ONE = FIX_SCALE; // The uint192 representation of one.
reserve-protocol/contracts/libraries/Fixed.sol::52 => uint192 constant FIX_MAX = type(uint192).max; // The largest uint192. (Not an integer!)
reserve-protocol/contracts/libraries/Fixed.sol::53 => uint192 constant FIX_MIN = 0; // The smallest uint192.
reserve-protocol/contracts/libraries/Fixed.sol::74 => return uint192(x);
reserve-protocol/contracts/libraries/Fixed.sol::98 => ) pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::101 => if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5
reserve-protocol/contracts/libraries/Fixed.sol::182 => function toUint(uint192 x) internal pure returns (uint136) {
reserve-protocol/contracts/libraries/Fixed.sol::189 => function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {
reserve-protocol/contracts/libraries/Fixed.sol::197 => function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::206 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::209 => ) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::222 => function plus(uint192 x, uint192 y) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::236 => function minus(uint192 x, uint192 y) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::251 => function mul(uint192 x, uint192 y) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::259 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::260 => uint192 y,
reserve-protocol/contracts/libraries/Fixed.sol::262 => ) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::276 => function div(uint192 x, uint192 y) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::284 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::285 => uint192 y,
reserve-protocol/contracts/libraries/Fixed.sol::287 => ) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::303 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::306 => ) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
reserve-protocol/contracts/libraries/Fixed.sol::316 => function powu(uint192 x_, uint48 y) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::332 => function lt(uint192 x, uint192 y) internal pure returns (bool) {
reserve-protocol/contracts/libraries/Fixed.sol::336 => function lte(uint192 x, uint192 y) internal pure returns (bool) {
reserve-protocol/contracts/libraries/Fixed.sol::340 => function gt(uint192 x, uint192 y) internal pure returns (bool) {
reserve-protocol/contracts/libraries/Fixed.sol::344 => function gte(uint192 x, uint192 y) internal pure returns (bool) {
reserve-protocol/contracts/libraries/Fixed.sol::348 => function eq(uint192 x, uint192 y) internal pure returns (bool) {
reserve-protocol/contracts/libraries/Fixed.sol::352 => function neq(uint192 x, uint192 y) internal pure returns (bool) {
reserve-protocol/contracts/libraries/Fixed.sol::360 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::361 => uint192 y,
reserve-protocol/contracts/libraries/Fixed.sol::362 => uint192 epsilon
reserve-protocol/contracts/libraries/Fixed.sol::364 => uint192 diff = x <= y ? y - x : x - y;
reserve-protocol/contracts/libraries/Fixed.sol::409 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::427 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::428 => uint192 y,
reserve-protocol/contracts/libraries/Fixed.sol::439 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::442 => ) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::451 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::455 => ) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::464 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::465 => uint192 y,
reserve-protocol/contracts/libraries/Fixed.sol::466 => uint192 z
reserve-protocol/contracts/libraries/Fixed.sol::467 => ) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Fixed.sol::476 => uint192 x,
reserve-protocol/contracts/libraries/Fixed.sol::477 => uint192 y,
reserve-protocol/contracts/libraries/Fixed.sol::478 => uint192 z,
reserve-protocol/contracts/libraries/Fixed.sol::480 => ) internal pure returns (uint192) {
reserve-protocol/contracts/libraries/Permit.sol::13 => uint8 v,
reserve-protocol/contracts/libraries/RedemptionBattery.sol::11 => uint48 constant BLOCKS_PER_HOUR = 300; // {blocks/hour}
reserve-protocol/contracts/libraries/RedemptionBattery.sol::20 => using FixLib for uint192;
reserve-protocol/contracts/libraries/RedemptionBattery.sol::24 => uint192 scalingRedemptionRate; // {1/hour} charging rate as a fraction of supply
reserve-protocol/contracts/libraries/RedemptionBattery.sol::27 => uint48 lastBlock; // {blocks}
reserve-protocol/contracts/libraries/RedemptionBattery.sol::47 => battery.lastBlock = uint48(block.number);
reserve-protocol/contracts/libraries/RedemptionBattery.sol::64 => uint48 blocks = uint48(block.number) - battery.lastBlock;
reserve-protocol/contracts/libraries/String.sol::16 => if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {
reserve-protocol/contracts/libraries/String.sol::18 => bLower[i] = bytes1(uint8(bStr[i]) + 32);
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::44 => function shiftl(uint192  x, int8 decimals) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::47 => function shiftlRnd(uint192  x, int8 decimals, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::50 => function plus(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::56 => function minus(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::62 => function mul(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::65 => function mulRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::71 => function div(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::74 => function divRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::83 => function powu(uint192  x, uint48 y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::86 => function lt(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::89 => function lte(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::92 => function gt(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::95 => function gte(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::98 => function eq(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::101 => function neq(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::104 => function near(uint192  x, uint192  y, uint192  epsilon) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::133 => function mulDiv(uint192  x, uint192  y, uint192  z) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::136 => function mulDivRnd(uint192  x, uint192  y, uint192  z, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/mixins/Auth.sol::8 => uint48 constant MAX_UNFREEZE_AT = type(uint48).max;
reserve-protocol/contracts/mixins/Auth.sol::9 => uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month
reserve-protocol/contracts/mixins/Auth.sol::10 => uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year
reserve-protocol/contracts/mixins/Auth.sol::36 => uint48 public unfreezeAt; // {s} uint48.max to pause indefinitely
reserve-protocol/contracts/mixins/Auth.sol::37 => uint48 public shortFreeze; // {s} length of an initial freeze
reserve-protocol/contracts/mixins/Auth.sol::38 => uint48 public longFreeze; // {s} length of a freeze extension
reserve-protocol/contracts/mixins/Auth.sol::65 => function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {
reserve-protocol/contracts/mixins/Auth.sol::123 => freezeUntil(uint48(block.timestamp) + shortFreeze);
reserve-protocol/contracts/mixins/Auth.sol::140 => freezeUntil(uint48(block.timestamp) + longFreeze);
reserve-protocol/contracts/mixins/Auth.sol::158 => emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));
reserve-protocol/contracts/mixins/Auth.sol::159 => unfreezeAt = uint48(block.timestamp);
reserve-protocol/contracts/mixins/Auth.sol::180 => function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
reserve-protocol/contracts/mixins/Auth.sol::187 => function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
reserve-protocol/contracts/mixins/Auth.sol::196 => function freezeUntil(uint48 newUnfreezeAt) private {
reserve-protocol/contracts/p1/AssetRegistry.sol::76 => uint192 quantity = basketHandler.quantity(asset.erc20());
reserve-protocol/contracts/p1/AssetRegistry.sol::90 => uint192 quantity = basketHandler.quantity(asset.erc20());
reserve-protocol/contracts/p1/BackingManager.sol::21 => using FixLib for uint192;
reserve-protocol/contracts/p1/BackingManager.sol::33 => uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
reserve-protocol/contracts/p1/BackingManager.sol::34 => uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%
reserve-protocol/contracts/p1/BackingManager.sol::36 => uint48 public tradingDelay; // {s} how long to wait until resuming trading after switching
reserve-protocol/contracts/p1/BackingManager.sol::37 => uint192 public backingBuffer; // {%} how much extra backing collateral to keep
reserve-protocol/contracts/p1/BackingManager.sol::46 => uint48 tradingDelay_,
reserve-protocol/contracts/p1/BackingManager.sol::47 => uint192 backingBuffer_,
reserve-protocol/contracts/p1/BackingManager.sol::48 => uint192 maxTradeSlippage_,
reserve-protocol/contracts/p1/BackingManager.sol::49 => uint192 minTradeVolume_
reserve-protocol/contracts/p1/BackingManager.sol::113 => uint48 basketTimestamp = basketHandler.timestamp();
reserve-protocol/contracts/p1/BackingManager.sol::188 => uint192 needed; // {BU}
reserve-protocol/contracts/p1/BackingManager.sol::191 => uint192 held = basketHandler.basketsHeldBy(address(this)); // {BU}
reserve-protocol/contracts/p1/BackingManager.sol::194 => uint192 totalSupply = _safeWrap(rToken.totalSupply()); // {rTok}
reserve-protocol/contracts/p1/BackingManager.sol::197 => uint192 extraBUs = held.minus(needed);
reserve-protocol/contracts/p1/BackingManager.sol::200 => uint192 rTok = (needed > 0) ? extraBUs.mulDiv(totalSupply, needed) : extraBUs;
reserve-protocol/contracts/p1/BackingManager.sol::224 => uint192 req = needed.mul(basketHandler.quantity(erc20s[i]), CEIL);
reserve-protocol/contracts/p1/BackingManager.sol::256 => function setTradingDelay(uint48 val) public governance {
reserve-protocol/contracts/p1/BackingManager.sol::263 => function setBackingBuffer(uint192 val) public governance {
reserve-protocol/contracts/p1/BasketHandler.sol::40 => mapping(IERC20 => uint192) targetAmts;
reserve-protocol/contracts/p1/BasketHandler.sol::55 => mapping(IERC20 => uint192) refAmts; // {ref/BU}
reserve-protocol/contracts/p1/BasketHandler.sol::63 => using FixLib for uint192;
reserve-protocol/contracts/p1/BasketHandler.sol::90 => uint192 weight
reserve-protocol/contracts/p1/BasketHandler.sol::115 => using FixLib for uint192;
reserve-protocol/contracts/p1/BasketHandler.sol::117 => uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight
reserve-protocol/contracts/p1/BasketHandler.sol::134 => uint48 public override nonce; // A unique identifier for this basket instance
reserve-protocol/contracts/p1/BasketHandler.sol::135 => uint48 public override timestamp; // The timestamp when this basket was last set
reserve-protocol/contracts/p1/BasketHandler.sol::169 => uint192[] memory refAmts = new uint192[](basket.erc20s.length);
reserve-protocol/contracts/p1/BasketHandler.sol::209 => function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts)
reserve-protocol/contracts/p1/BasketHandler.sol::296 => function quantity(IERC20 erc20) public view returns (uint192) {
reserve-protocol/contracts/p1/BasketHandler.sol::300 => uint192 refPerTok = coll.refPerTok(); // {ref/tok}
reserve-protocol/contracts/p1/BasketHandler.sol::316 => function price() external view returns (uint192 low, uint192 high) {
reserve-protocol/contracts/p1/BasketHandler.sol::325 => function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {
reserve-protocol/contracts/p1/BasketHandler.sol::332 => function _price(bool useLotPrice) internal view returns (uint192 low, uint192 high) {
reserve-protocol/contracts/p1/BasketHandler.sol::338 => uint192 qty = quantity(basket.erc20s[i]);
reserve-protocol/contracts/p1/BasketHandler.sol::341 => (uint192 lowP, uint192 highP) = useLotPrice
reserve-protocol/contracts/p1/BasketHandler.sol::349 => low = low256 >= FIX_MAX ? FIX_MAX : uint192(low256);
reserve-protocol/contracts/p1/BasketHandler.sol::350 => high = high256 >= FIX_MAX ? FIX_MAX : uint192(high256);
reserve-protocol/contracts/p1/BasketHandler.sol::356 => function quantityMulPrice(uint192 qty, uint192 p) internal pure returns (uint192) {
reserve-protocol/contracts/p1/BasketHandler.sol::407 => function quote(uint192 amount, RoundingMode rounding)
reserve-protocol/contracts/p1/BasketHandler.sol::432 => function basketsHeldBy(address account) public view returns (uint192 baskets) {
reserve-protocol/contracts/p1/BasketHandler.sol::441 => uint192 refPerTok = coll.refPerTok();
reserve-protocol/contracts/p1/BasketHandler.sol::447 => uint192 bal = coll.bal(account);
reserve-protocol/contracts/p1/BasketHandler.sol::450 => uint192 q = basket.refAmts[basket.erc20s[i]].div(refPerTok, CEIL);
reserve-protocol/contracts/p1/BasketHandler.sol::541 => uint192[] memory goodWeights = new uint192[](targetsLength);
reserve-protocol/contracts/p1/BasketHandler.sol::545 => uint192[] memory totalWeights = new uint192[](targetsLength);
reserve-protocol/contracts/p1/BasketHandler.sol::637 => timestamp = uint48(block.timestamp);
reserve-protocol/contracts/p1/BasketHandler.sol::642 => uint192[] memory refAmts = new uint192[](basketLength);
reserve-protocol/contracts/p1/BasketHandler.sol::700 => uint192[] memory targetAmts
reserve-protocol/contracts/p1/BasketHandler.sol::705 => targetAmts = new uint192[](erc20s.length);
reserve-protocol/contracts/p1/Broker.sol::24 => uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
reserve-protocol/contracts/p1/Broker.sol::37 => uint48 public auctionLength;
reserve-protocol/contracts/p1/Broker.sol::55 => uint48 auctionLength_
reserve-protocol/contracts/p1/Broker.sol::133 => function setAuctionLength(uint48 newAuctionLength) public governance {
reserve-protocol/contracts/p1/Distributor.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/p1/Distributor.sol::34 => uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;
reserve-protocol/contracts/p1/Distributor.sol::181 => function _ensureNonZeroDistribution(uint24 rTokenDist, uint24 rsrDist) internal pure {
reserve-protocol/contracts/p1/Furnace.sol::13 => using FixLib for uint192;
reserve-protocol/contracts/p1/Furnace.sol::15 => uint192 public constant MAX_RATIO = FIX_ONE; // {1} 100%
reserve-protocol/contracts/p1/Furnace.sol::16 => uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
reserve-protocol/contracts/p1/Furnace.sol::18 => uint192 public ratio; // {1} What fraction of balance to melt each period
reserve-protocol/contracts/p1/Furnace.sol::19 => uint48 public period; // {seconds} How often to melt
reserve-protocol/contracts/p1/Furnace.sol::21 => uint48 public lastPayout; // {seconds} The last time we did a payout
reserve-protocol/contracts/p1/Furnace.sol::35 => uint48 period_,
reserve-protocol/contracts/p1/Furnace.sol::36 => uint192 ratio_
reserve-protocol/contracts/p1/Furnace.sol::42 => lastPayout = uint48(block.timestamp);
reserve-protocol/contracts/p1/Furnace.sol::71 => if (uint48(block.timestamp) < uint64(lastPayout) + period) return;
reserve-protocol/contracts/p1/Furnace.sol::74 => uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;
reserve-protocol/contracts/p1/Furnace.sol::77 => uint192 payoutRatio = FIX_ONE.minus(FIX_ONE.minus(ratio).powu(numPeriods));
reserve-protocol/contracts/p1/Furnace.sol::88 => function setPeriod(uint48 period_) public governance {
reserve-protocol/contracts/p1/Furnace.sol::96 => function setRatio(uint192 ratio_) public governance {
reserve-protocol/contracts/p1/Main.sol::29 => uint48 shortFreeze_,
reserve-protocol/contracts/p1/Main.sol::30 => uint48 longFreeze_
reserve-protocol/contracts/p1/RToken.sol::15 => uint192 constant MIN_BLOCK_ISSUANCE_LIMIT = 10_000 * FIX_ONE;
reserve-protocol/contracts/p1/RToken.sol::18 => uint192 constant MAX_ISSUANCE_RATE = FIX_ONE; // {1}
reserve-protocol/contracts/p1/RToken.sol::50 => uint192 public issuanceRate;
reserve-protocol/contracts/p1/RToken.sol::67 => uint192 public basketsNeeded; // D18{BU}
reserve-protocol/contracts/p1/RToken.sol::72 => uint192 private allVestAt; // D18{fractional block number}
reserve-protocol/contracts/p1/RToken.sol::76 => uint192 private lastIssRate; // D18{rTok/block}
reserve-protocol/contracts/p1/RToken.sol::81 => uint192 when; // D18{fractional block number}
reserve-protocol/contracts/p1/RToken.sol::83 => uint192 amtBaskets; // D18{BU} Total amount of baskets that should back those RTokens
reserve-protocol/contracts/p1/RToken.sol::152 => uint192 issuanceRate_,
reserve-protocol/contracts/p1/RToken.sol::153 => uint192 scalingRedemptionRate_,
reserve-protocol/contracts/p1/RToken.sol::198 => uint48 basketNonce = basketHandler.nonce();
reserve-protocol/contracts/p1/RToken.sol::233 => uint192 amtBaskets = uint192(
reserve-protocol/contracts/p1/RToken.sol::243 => uint192 vestingEnd = whenFinished(amtRToken); // D18{block number}
reserve-protocol/contracts/p1/RToken.sol::259 => uint192 newBasketsNeeded = basketsNeeded + amtBaskets;
reserve-protocol/contracts/p1/RToken.sol::351 => lastIssRate = uint192((issuanceRate * totalSupply()) / FIX_ONE);
reserve-protocol/contracts/p1/RToken.sol::358 => uint192 before = allVestAt; // D18{block number}
reserve-protocol/contracts/p1/RToken.sol::360 => uint192 nowStart = uint192(FIX_ONE * (block.number - 1)); // D18{block} = D18{1} * {block}
reserve-protocol/contracts/p1/RToken.sol::368 => finished = before + uint192((FIX_ONE_256 * amtRToken + (lastIssRate - 1)) / lastIssRate);
reserve-protocol/contracts/p1/RToken.sol::387 => uint48 basketNonce = basketHandler.nonce();
reserve-protocol/contracts/p1/RToken.sol::454 => uint192 basketsNeeded_ = basketsNeeded; // gas optimization
reserve-protocol/contracts/p1/RToken.sol::462 => uint192 baskets = uint192(mulDiv256(basketsNeeded_, amount, supply));
reserve-protocol/contracts/p1/RToken.sol::475 => uint192 prorate = uint192((FIX_ONE_256 * amount) / supply);
reserve-protocol/contracts/p1/RToken.sol::579 => function setBasketsNeeded(uint192 basketsNeeded_) external {
reserve-protocol/contracts/p1/RToken.sol::589 => function setIssuanceRate(uint192 val) public governance {
reserve-protocol/contracts/p1/RToken.sol::596 => function scalingRedemptionRate() external view returns (uint192) {
reserve-protocol/contracts/p1/RToken.sol::602 => function setScalingRedemptionRate(uint192 val) public governance {
reserve-protocol/contracts/p1/RToken.sol::745 => uint192 amtBaskets;
reserve-protocol/contracts/p1/RToken.sol::813 => require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
reserve-protocol/contracts/p1/RevenueTrader.sol::15 => using FixLib for uint192;
reserve-protocol/contracts/p1/RevenueTrader.sol::26 => uint192 maxTradeSlippage_,
reserve-protocol/contracts/p1/RevenueTrader.sol::27 => uint192 minTradeVolume_
reserve-protocol/contracts/p1/RevenueTrader.sol::69 => (uint192 sellPrice, ) = sell.price(); // {UoA/tok}
reserve-protocol/contracts/p1/RevenueTrader.sol::70 => (, uint192 buyPrice) = buy.price(); // {UoA/tok}
reserve-protocol/contracts/p1/StRSR.sol::37 => uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
reserve-protocol/contracts/p1/StRSR.sol::38 => uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
reserve-protocol/contracts/p1/StRSR.sol::39 => uint192 public constant MAX_REWARD_RATIO = FIX_ONE; // {1} 100%
reserve-protocol/contracts/p1/StRSR.sol::45 => uint8 public constant decimals = 18;
reserve-protocol/contracts/p1/StRSR.sol::63 => uint192 public stakeRate; // The exchange rate between stakes and RSR. D18{qStRSR/qRSR}
reserve-protocol/contracts/p1/StRSR.sol::65 => uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}
reserve-protocol/contracts/p1/StRSR.sol::77 => uint176 drafts; // Total amount of drafts that will become available // {qDraft}
reserve-protocol/contracts/p1/StRSR.sol::78 => uint64 availableAt; // When the last of the drafts will become available
reserve-protocol/contracts/p1/StRSR.sol::85 => uint192 public draftRate; // The exchange rate between drafts and RSR. D18{qDrafts/qRSR}
reserve-protocol/contracts/p1/StRSR.sol::87 => uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}
reserve-protocol/contracts/p1/StRSR.sol::134 => uint48 public unstakingDelay; // {s} The minimum length of time spent in the draft queue
reserve-protocol/contracts/p1/StRSR.sol::135 => uint48 public rewardPeriod; // {s} The number of seconds between revenue payout events
reserve-protocol/contracts/p1/StRSR.sol::136 => uint192 public rewardRatio; // {1} The fraction of the revenue balance to handout per period
reserve-protocol/contracts/p1/StRSR.sol::147 => uint48 public payoutLastPaid;
reserve-protocol/contracts/p1/StRSR.sol::164 => uint48 unstakingDelay_,
reserve-protocol/contracts/p1/StRSR.sol::165 => uint48 rewardPeriod_,
reserve-protocol/contracts/p1/StRSR.sol::166 => uint192 rewardRatio_
reserve-protocol/contracts/p1/StRSR.sol::180 => payoutLastPaid = uint48(block.timestamp);
reserve-protocol/contracts/p1/StRSR.sol::315 => uint192 oldDrafts = firstId > 0 ? queue[firstId - 1].drafts : 0;
reserve-protocol/contracts/p1/StRSR.sol::316 => uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;
reserve-protocol/contracts/p1/StRSR.sol::377 => uint192 initRate = exchangeRate();
reserve-protocol/contracts/p1/StRSR.sol::393 => stakeRate = uint192((FIX_ONE_256 * totalStakes + (stakeRSR - 1)) / stakeRSR);
reserve-protocol/contracts/p1/StRSR.sol::408 => draftRate = uint192((FIX_ONE_256 * totalDrafts + (draftRSR - 1)) / draftRSR);
reserve-protocol/contracts/p1/StRSR.sol::425 => function exchangeRate() public view returns (uint192) {
reserve-protocol/contracts/p1/StRSR.sol::498 => uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;
reserve-protocol/contracts/p1/StRSR.sol::500 => uint192 initRate = exchangeRate();
reserve-protocol/contracts/p1/StRSR.sol::509 => uint192 payoutRatio = FIX_ONE - FixLib.powu(FIX_ONE - rewardRatio, numPeriods);
reserve-protocol/contracts/p1/StRSR.sol::526 => : uint192((totalStakes * FIX_ONE_256 + (stakeRSR - 1)) / stakeRSR);
reserve-protocol/contracts/p1/StRSR.sol::559 => uint192 oldDrafts = index > 0 ? queue[index - 1].drafts : 0;
reserve-protocol/contracts/p1/StRSR.sol::560 => uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
reserve-protocol/contracts/p1/StRSR.sol::561 => availableAt = uint64(block.timestamp) + unstakingDelay;
reserve-protocol/contracts/p1/StRSR.sol::566 => queue.push(CumulativeDraft(uint176(oldDrafts + draftAmount), availableAt));
reserve-protocol/contracts/p1/StRSR.sol::696 => assert(totalStakes + amount < type(uint224).max);
reserve-protocol/contracts/p1/StRSR.sol::771 => uint8 v,
reserve-protocol/contracts/p1/StRSR.sol::812 => function setUnstakingDelay(uint48 val) public governance {
reserve-protocol/contracts/p1/StRSR.sol::820 => function setRewardPeriod(uint48 val) public governance {
reserve-protocol/contracts/p1/StRSR.sol::828 => function setRewardRatio(uint192 val) public governance {
reserve-protocol/contracts/p1/StRSRVotes.sol::23 => uint48 fromBlock;
reserve-protocol/contracts/p1/StRSRVotes.sol::24 => uint224 val;
reserve-protocol/contracts/p1/StRSRVotes.sol::59 => function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {
reserve-protocol/contracts/p1/StRSRVotes.sol::63 => function numCheckpoints(address account) public view returns (uint48) {
reserve-protocol/contracts/p1/StRSRVotes.sol::122 => uint8 v,
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::25 => uint192 minTradeVolume; // {UoA}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::26 => uint192 maxTradeSlippage; // {1}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::32 => uint192 sellAmount; // {sellTok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::33 => uint192 buyAmount; // {buyTok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::34 => uint192 sellPrice; // {UoA/sellTok} can be 0
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::35 => uint192 buyPrice; // {UoA/buyTok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::50 => using FixLib for uint192;
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::117 => uint192 top; // {BU}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::118 => uint192 bottom; // {BU}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::158 => (uint192 basketPriceLow, uint192 basketPriceHigh) = components.bh.price();
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::166 => (uint192 assetsLow, uint192 assetsHigh) = totalAssetValue(components, rules, reg);
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::172 => uint192 basketsHigh = components.bm.safeMulDivCeil(FIX_ONE, assetsHigh, basketPriceLow);
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::185 => uint192 shortfall = collateralShortfall(components, range.top);
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::190 => uint192 shortfallSlippage = rules.maxTradeSlippage.mulDiv(
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::230 => ) private view returns (uint192 assetsLow, uint192 assetsHigh) {
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::236 => uint192 potentialDustLoss; // {UoA}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::246 => uint192 bal = reg.assets[i].bal(address(components.bm)); // {tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::253 => (uint192 low, uint192 high) = reg.assets[i].price(); // {UoA/tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::254 => (uint192 lotLow, ) = reg.assets[i].lotPrice(); // {UoA/tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::269 => uint192 val = components.bm.safeMulDivCeil(high, bal, FIX_ONE);
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::286 => uint192 surplus; // {UoA}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::287 => uint192 deficit; // {UoA}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::332 => uint192 bal = reg.assets[i].bal(address(components.bm)); // {tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::336 => uint192 needed = range.top.mul(components.bh.quantity(reg.erc20s[i]), CEIL); // {tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::338 => uint192 low; // {UoA/sellTok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::342 => uint192 high; // {UoA/sellTok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::349 => (uint192 lotLow, ) = reg.assets[i].lotPrice(); // {UoA/sellTok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::352 => uint192 delta = bal.minus(needed).mul(lotLow, FLOOR);
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::382 => uint192 amtShort = needed.minus(bal); // {buyTok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::383 => (, uint192 high) = reg.assets[i].price(); // {UoA/buyTok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::386 => uint192 delta = amtShort.mul(high, CEIL);
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::404 => uint192 rsrAvailable = rsrAsset.bal(address(components.bm)).plus(
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::407 => (uint192 low, uint192 high) = rsrAsset.price(); // {UoA/tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::408 => (uint192 lotLow, ) = rsrAsset.lotPrice(); // {UoA/tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::428 => function collateralShortfall(ComponentCache memory components, uint192 basketsTop)
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::431 => returns (uint192 shortfall)
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::442 => uint192 needed = basketsTop.mul(components.bh.quantity(basketERC20s[i]), CEIL); // {tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::445 => uint192 held = coll.bal(address(components.bm)); // {tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::449 => (, uint192 priceHigh) = coll.price(); // {UoA/tok}
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::463 => uint192 surplusAmt
reserve-protocol/contracts/p1/mixins/TradeLib.sol::19 => using FixLib for uint192;
reserve-protocol/contracts/p1/mixins/TradeLib.sol::46 => (uint192 lotLow, uint192 lotHigh) = trade.sell.lotPrice();
reserve-protocol/contracts/p1/mixins/TradeLib.sol::54 => uint192 maxSell = maxTradeSize(trade.sell, lotHigh); // {sellTok}
reserve-protocol/contracts/p1/mixins/TradeLib.sol::55 => uint192 s = trade.sellAmount > maxSell ? maxSell : trade.sellAmount; // {sellTok}
reserve-protocol/contracts/p1/mixins/TradeLib.sol::59 => uint192 b = safeMulDivCeil(
reserve-protocol/contracts/p1/mixins/TradeLib.sol::122 => uint192 exactSellAmount = trade.buyAmount.mulDiv(trade.buyPrice, trade.sellPrice, CEIL);
reserve-protocol/contracts/p1/mixins/TradeLib.sol::126 => uint192 slippedSellAmount = exactSellAmount.div(
reserve-protocol/contracts/p1/mixins/TradeLib.sol::142 => uint192 amt,
reserve-protocol/contracts/p1/mixins/TradeLib.sol::143 => uint192 price,
reserve-protocol/contracts/p1/mixins/TradeLib.sol::144 => uint192 minTradeVolume
reserve-protocol/contracts/p1/mixins/TradeLib.sol::156 => uint192 x,
reserve-protocol/contracts/p1/mixins/TradeLib.sol::157 => uint192 y,
reserve-protocol/contracts/p1/mixins/TradeLib.sol::158 => uint192 z
reserve-protocol/contracts/p1/mixins/TradeLib.sol::159 => ) internal pure returns (uint192) {
reserve-protocol/contracts/p1/mixins/TradeLib.sol::160 => try trader.mulDivCeil(x, y, z) returns (uint192 result) {
reserve-protocol/contracts/p1/mixins/TradeLib.sol::180 => function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {
reserve-protocol/contracts/p1/mixins/TradeLib.sol::182 => uint192 size = price == 0 ? FIX_MAX : minTradeVolume.div(price, CEIL);
reserve-protocol/contracts/p1/mixins/TradeLib.sol::188 => function maxTradeSize(IAsset asset, uint192 price) private view returns (uint192) {
reserve-protocol/contracts/p1/mixins/TradeLib.sol::191 => uint192 size = price == 0 ? FIX_MAX : asset.maxTradeVolume().div(price, FLOOR);
reserve-protocol/contracts/p1/mixins/Trading.sol::17 => using FixLib for uint192;
reserve-protocol/contracts/p1/mixins/Trading.sol::20 => uint192 public constant MIN_TRADE_VOLUME = 1e29; // {UoA}
reserve-protocol/contracts/p1/mixins/Trading.sol::21 => uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}
reserve-protocol/contracts/p1/mixins/Trading.sol::28 => uint48 public tradesOpen;
reserve-protocol/contracts/p1/mixins/Trading.sol::31 => uint192 public maxTradeSlippage; // {%}
reserve-protocol/contracts/p1/mixins/Trading.sol::33 => uint192 public minTradeVolume; // {UoA}
reserve-protocol/contracts/p1/mixins/Trading.sol::46 => uint192 maxTradeSlippage_,
reserve-protocol/contracts/p1/mixins/Trading.sol::47 => uint192 minTradeVolume_
reserve-protocol/contracts/p1/mixins/Trading.sol::128 => function setMaxTradeSlippage(uint192 val) public governance {
reserve-protocol/contracts/p1/mixins/Trading.sol::135 => function setMinTradeVolume(uint192 val) public governance {
reserve-protocol/contracts/p1/mixins/Trading.sol::145 => uint192 x,
reserve-protocol/contracts/p1/mixins/Trading.sol::146 => uint192 y,
reserve-protocol/contracts/p1/mixins/Trading.sol::147 => uint192 z
reserve-protocol/contracts/p1/mixins/Trading.sol::148 => ) external pure returns (uint192) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::46 => uint8 private _decimals;
reserve-protocol/contracts/plugins/aave/ERC20.sol::91 => function decimals() public view returns (uint8) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::324 => function _setupDecimals(uint8 decimals_) internal {
reserve-protocol/contracts/plugins/aave/IAaveIncentivesController.sol::127 => function PRECISION() external view returns (uint8);
reserve-protocol/contracts/plugins/aave/IStaticATokenLM.sol::11 => uint8 v;
reserve-protocol/contracts/plugins/aave/IStaticATokenLM.sol::30 => uint16 referralCode,
reserve-protocol/contracts/plugins/aave/IStaticATokenLM.sol::82 => uint8 v,
reserve-protocol/contracts/plugins/aave/IStaticATokenLM.sol::106 => uint16 referralCode,
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::108 => uint16 referralCode,
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::138 => uint8 v,
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::165 => uint16 referralCode,
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::291 => uint16 referralCode,
reserve-protocol/contracts/plugins/assets/ATokenFiatCollateral.sol::36 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/ATokenFiatCollateral.sol::45 => function refPerTok() public view override returns (uint192) {
reserve-protocol/contracts/plugins/assets/Asset.sol::10 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/Asset.sol::17 => uint8 public immutable erc20Decimals;
reserve-protocol/contracts/plugins/assets/Asset.sol::19 => uint192 public immutable override maxTradeVolume; // {UoA}
reserve-protocol/contracts/plugins/assets/Asset.sol::21 => uint48 public immutable oracleTimeout; // {s} Seconds that an oracle value is considered valid
reserve-protocol/contracts/plugins/assets/Asset.sol::23 => uint192 public immutable oracleError; // {1} The max % deviation allowed by the oracle
reserve-protocol/contracts/plugins/assets/Asset.sol::27 => uint48 public immutable priceTimeout; // {s} The period over which `savedHighPrice` decays to 0
reserve-protocol/contracts/plugins/assets/Asset.sol::29 => uint192 public savedLowPrice; // {UoA/tok} The low price of the token during the last update
reserve-protocol/contracts/plugins/assets/Asset.sol::31 => uint192 public savedHighPrice; // {UoA/tok} The high price of the token during the last update
reserve-protocol/contracts/plugins/assets/Asset.sol::33 => uint48 public lastSave; // {s} The timestamp when prices were last saved
reserve-protocol/contracts/plugins/assets/Asset.sol::41 => uint48 priceTimeout_,
reserve-protocol/contracts/plugins/assets/Asset.sol::43 => uint192 oracleError_,
reserve-protocol/contracts/plugins/assets/Asset.sol::45 => uint192 maxTradeVolume_,
reserve-protocol/contracts/plugins/assets/Asset.sol::46 => uint48 oracleTimeout_
reserve-protocol/contracts/plugins/assets/Asset.sol::74 => uint192 low,
reserve-protocol/contracts/plugins/assets/Asset.sol::75 => uint192 high,
reserve-protocol/contracts/plugins/assets/Asset.sol::76 => uint192
reserve-protocol/contracts/plugins/assets/Asset.sol::79 => uint192 p = chainlinkFeed.price(oracleTimeout); // {UoA/tok}
reserve-protocol/contracts/plugins/assets/Asset.sol::80 => uint192 delta = p.mul(oracleError);
reserve-protocol/contracts/plugins/assets/Asset.sol::87 => try this.tryPrice() returns (uint192 low, uint192 high, uint192) {
reserve-protocol/contracts/plugins/assets/Asset.sol::95 => lastSave = uint48(block.timestamp);
reserve-protocol/contracts/plugins/assets/Asset.sol::110 => function price() public view virtual returns (uint192, uint192) {
reserve-protocol/contracts/plugins/assets/Asset.sol::111 => try this.tryPrice() returns (uint192 low, uint192 high, uint192) {
reserve-protocol/contracts/plugins/assets/Asset.sol::126 => function lotPrice() external view virtual returns (uint192 lotLow, uint192 lotHigh) {
reserve-protocol/contracts/plugins/assets/Asset.sol::127 => try this.tryPrice() returns (uint192 low, uint192 high, uint192) {
reserve-protocol/contracts/plugins/assets/Asset.sol::137 => uint48 delta = uint48(block.timestamp) - lastSave; // {s}
reserve-protocol/contracts/plugins/assets/Asset.sol::141 => uint192 lotMultiplier = divuu(priceTimeout - delta, priceTimeout);
reserve-protocol/contracts/plugins/assets/Asset.sol::151 => function bal(address account) external view returns (uint192) {
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::16 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::20 => uint8 public immutable referenceERC20Decimals;
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::44 => function refPerTok() public view override returns (uint192) {
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::15 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::44 => uint192 low,
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::45 => uint192 high,
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::46 => uint192 pegPrice
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::51 => uint192 pricePerTarget = targetUnitChainlinkFeed.price(oracleTimeout); // {UoA/target}
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::54 => uint192 p = pricePerTarget.mul(pegPrice).mul(refPerTok());
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::57 => uint192 delta = p.mul(oracleError);
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::18 => uint8 public immutable referenceERC20Decimals;
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::26 => uint8 referenceERC20Decimals_,
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::47 => function refPerTok() public view override returns (uint192) {
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::37 => uint192 low,
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::38 => uint192 high,
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::39 => uint192 pegPrice
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::42 => uint192 refPrice = chainlinkFeed.price(oracleTimeout); // {UoA/ref}
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::43 => uint192 targetPrice = uoaPerTargetFeed.price(oracleTimeout); // {UoA/target}
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::50 => uint192 delta = refPrice.mul(oracleError);
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::12 => uint48 priceTimeout; // {s} The number of seconds over which saved prices decay
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::14 => uint192 oracleError; // {1} The % the oracle feed can be off by
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::16 => uint192 maxTradeVolume; // {UoA} The max trade volume, in UoA
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::17 => uint48 oracleTimeout; // {s} The number of seconds until a oracle value becomes invalid
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::19 => uint192 defaultThreshold; // {1} A value like 0.05 that represents a deviation tolerance
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::21 => uint48 delayUntilDefault; // {s} The number of seconds an oracle can mulfunction
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::39 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::47 => uint48 private constant NEVER = type(uint48).max;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::48 => uint48 private _whenDefault = NEVER;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::50 => uint48 public immutable delayUntilDefault; // {s} e.g 86400
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::55 => uint192 public immutable pegBottom; // {target/ref} The bottom of the peg
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::57 => uint192 public immutable pegTop; // {target/ref} The top of the peg
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::60 => uint192 public prevReferencePrice; // previous rate, {ref/tok}
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::82 => uint192 peg = targetPerRef(); // {target/ref}
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::85 => uint192 delta = peg.mul(config.defaultThreshold);
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::103 => uint192 low,
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::104 => uint192 high,
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::105 => uint192 pegPrice
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::111 => uint192 p = pegPrice.mul(refPerTok());
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::112 => uint192 delta = p.mul(oracleError);
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::126 => try this.tryPrice() returns (uint192 low, uint192 high, uint192 pegPrice) {
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::134 => lastSave = uint48(block.timestamp);
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::154 => uint192 referencePrice = refPerTok();
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::190 => else if (sum < _whenDefault) _whenDefault = uint48(sum);
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::195 => _whenDefault = uint48(block.timestamp);
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::37 => uint192 low,
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::38 => uint192 high,
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::39 => uint192 pegPrice
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::43 => uint192 pricePerTarget = uoaPerTargetFeed.price(oracleTimeout); // {UoA/target}
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::46 => uint192 p = pricePerTarget.mul(pegPrice).mul(refPerTok());
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::49 => uint192 delta = p.mul(oracleError);
reserve-protocol/contracts/plugins/assets/OracleLib.sol::14 => function price(AggregatorV3Interface chainlinkFeed, uint48 timeout)
reserve-protocol/contracts/plugins/assets/OracleLib.sol::17 => returns (uint192)
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::12 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::22 => uint8 public immutable erc20Decimals;
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::24 => uint192 public immutable override maxTradeVolume; // {UoA}
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::27 => constructor(IRToken erc20_, uint192 maxTradeVolume_) {
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::44 => function tryPrice() external view virtual returns (uint192 low, uint192 high) {
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::45 => (uint192 lowBUPrice, uint192 highBUPrice) = basketHandler.price(); // {UoA/BU}
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::49 => uint192 supply = _safeWrap(IRToken(address(erc20)).totalSupply());
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::72 => function price() public view virtual returns (uint192, uint192) {
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::73 => try this.tryPrice() returns (uint192 low, uint192 high) {
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::87 => function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::88 => (uint192 buLow, uint192 buHigh) = basketHandler.lotPrice(); // {UoA/BU}
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::92 => uint192 supply = _safeWrap(IRToken(address(erc20)).totalSupply());
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::104 => function bal(address account) external view returns (uint192) {
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::31 => uint192 low,
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::32 => uint192 high,
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::33 => uint192 pegPrice
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::37 => uint192 p = chainlinkFeed.price(oracleTimeout).mul(refPerTok());
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::38 => uint192 delta = p.mul(oracleError);
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::28 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::50 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::7 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::32 => try this.tryPrice() returns (uint192 low, uint192 high, uint192 pegPrice) {
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::40 => lastSave = uint48(block.timestamp);
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::56 => uint192 referencePrice = refPerTok();
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::10 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::11 => uint192 public transferFee; // {1}
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::19 => function setTransferFee(uint192 newFee) external {
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::31 => function decimals() public view override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::9 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::23 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::37 => function setExchangeRate(uint192 fiatcoinRedemptionRate) external {
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::47 => uint192 start = shiftl_toFix(2, -2); // 0.02
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::19 => uint8 public override decimals;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::31 => constructor(uint8 _decimals, int256 _initialAnswer) {
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::57 => uint80 _roundId,
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::71 => function getRoundData(uint80 _roundId)
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::76 => uint80 roundId,
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::80 => uint80 answeredInRound
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::98 => uint80 roundId,
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::102 => uint80 answeredInRound
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::106 => uint80(latestRound),
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::110 => uint80(latestAnsweredRound)
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::10 => using SafeMath for uint64;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::11 => using SafeMath for uint96;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::57 => uint64 indexed userId,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::58 => uint96 buyAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::59 => uint96 sellAmount
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::63 => uint64 indexed userId,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::64 => uint96 buyAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::65 => uint96 sellAmount
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::69 => uint64 indexed userId,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::70 => uint96 buyAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::71 => uint96 sellAmount
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::73 => event NewUser(uint64 indexed userId, address indexed userAddress);
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::80 => uint64 userId,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::81 => uint96 _auctionedSellAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::82 => uint96 _minBuyAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::90 => uint96 soldAuctioningTokens,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::91 => uint96 soldBiddingTokens,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::94 => event UserRegistration(address indexed user, uint64 userId);
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::106 => uint96 volumeClearingPriceOrder;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::118 => uint64 public numUsers;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::125 => uint64 public feeReceiverUserId = 1;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::150 => uint96 _auctionedSellAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::151 => uint96 _minBuyAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::177 => uint64 userId = getUserId(msg.sender);
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::215 => uint96[] memory _minBuyAmounts,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::216 => uint96[] memory _sellAmounts,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::219 => ) external atStageOrderPlacement(auctionId) returns (uint64 userId) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::233 => uint96[] memory _minBuyAmounts,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::234 => uint96[] memory _sellAmounts,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::238 => ) external atStageOrderPlacement(auctionId) returns (uint64 userId) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::252 => uint96[] memory _minBuyAmounts,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::253 => uint96[] memory _sellAmounts,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::257 => ) internal returns (uint64 userId) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::274 => uint96 buyAmountOfInitialAuctionOrder,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::275 => uint96 sellAmountOfInitialAuctionOrder
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::322 => uint64 userIdOfIter,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::323 => uint96 buyAmountOfIter,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::324 => uint96 sellAmountOfIter
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::346 => (, , uint96 sellAmountOfIter) = iterOrder.decodeOrder();
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::355 => (, uint96 buyAmountOfIter, uint96 sellAmountOfIter) = iterOrder.decodeOrder();
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::367 => uint96[] memory _minBuyAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::368 => uint96[] memory _sellAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::380 => uint64 userId = getUserId(msg.sender);
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::406 => uint96 minAuctionedBuyAmount,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::407 => uint96 fullAuctionedAmount
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::414 => uint96 fillVolumeOfAuctioneerOrder = fullAuctionedAmount;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::496 => uint96(currentBidSum),
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::519 => (, uint96 priceNumerator, uint96 priceDenominator) = auction
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::522 => (uint64 userId, , ) = orders[0].decodeOrder();
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::560 => uint64 auctioneerId,
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::561 => uint96 fullAuctionedAmount
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::570 => (, uint96 priceNumerator, uint96 priceDenominator) = auctionData[auctionId]
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::594 => uint64 userId
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::605 => function registerUser(address user) public returns (uint64 userId) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::612 => function getUserId(address user) public returns (uint64 userId) {
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::47 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::61 => uint96 auctionedSellAmount,
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::62 => uint96 minBuyAmount,
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::13 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::25 => uint96 auctionedSellAmount,
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::26 => uint96 minBuyAmount,
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::22 => uint48 public auctionLength; // {s} the length of an auction
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::30 => uint48 auctionLength_
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::52 => function setAuctionLength(uint48 newAuctionLength) external governance {}
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::15 => constructor(uint8 _decimals, int256 _initialAnswer)
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::24 => uint80 roundId,
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::28 => uint80 answeredInRoundyarn
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::38 => uint80(latestRound),
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::42 => uint80(latestRound)
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::7 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::15 => function price() public view virtual returns (uint192, uint192) {
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::7 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::9 => uint192 private _targetPerRef;
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::13 => function setTargetPerRef(uint192 val) external {
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::17 => function targetPerRef() public view override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::7 => uint192 private peg = FIX_ONE; // {target/ref}
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::12 => function targetPerRef() public view virtual override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::18 => uint192 private peg = FIX_ONE; // {target/ref}
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::23 => function targetPerRef() public view virtual override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::29 => uint192 private peg = FIX_ONE; // {target/ref}
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::34 => function targetPerRef() public view virtual override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::40 => uint192 private peg = FIX_ONE; // {target/ref}
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::45 => function targetPerRef() public view virtual override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::19 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::39 => uint192 maxTradeVolume_,
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::51 => function price() public view override(RTokenAsset, IAsset) returns (uint192 low, uint192 high) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::85 => function refPerTok() public view virtual returns (uint192) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::92 => function targetPerRef() public view virtual returns (uint192) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::98 => return IRToken(address(erc20)).basketsNeeded().div(uint192(supply));
reserve-protocol/contracts/plugins/mocks/USDCMock.sol::10 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::10 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::21 => uint48 priceTimeout_,
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::23 => uint192 oracleError_,
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::25 => uint192 maxTradeVolume_,
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::26 => uint48 oracleTimeout_
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::35 => uint192 low,
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::36 => uint192 high,
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::37 => uint192
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::43 => uint192 p = chainlinkFeed.price(oracleTimeout); // {UoA/tok}
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::44 => uint192 delta = p.mul(oracleError);
reserve-protocol/contracts/plugins/mocks/WBTCMock.sol::10 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/WETH.sol::23 => uint8 public decimals = 18;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::211 => using SafeMath for uint96;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::238 => uint64 owner;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::239 => uint96 buyAmount;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::240 => uint96 sellAmount;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::257 => (, , uint96 denominator) = decodeOrder(elementToInsert);
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::258 => require(denominator != uint96(0), "Inserting zero is not supported");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::342 => (uint64 userIdLeft, uint96 priceNumeratorLeft, uint96 priceDenominatorLeft) = decodeOrder(
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::346 => uint64 userIdRight,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::347 => uint96 priceNumeratorRight,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::348 => uint96 priceDenominatorRight
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::385 => uint64 userId,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::386 => uint96 buyAmount,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::387 => uint96 sellAmount
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::398 => uint64 userId,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::399 => uint96 buyAmount,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::400 => uint96 sellAmount
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::610 => mapping(uint64 => address) idToAddress;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::611 => mapping(address => uint64) addressToId;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::614 => function hasId(Data storage self, uint64 id) internal view returns (bool) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::622 => function getAddressAt(Data storage self, uint64 id) internal view returns (address) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::627 => function getId(Data storage self, address addr) internal view returns (uint64) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::634 => uint64 id,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::638 => require(id != uint64(-1), "Cannot insert max uint64");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::1001 => uint64,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::1002 => uint96,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::1003 => uint96
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::1010 => uint64 userId,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::1011 => uint96 sellAmount,
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::1012 => uint96 buyAmount
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::23 => using FixLib for uint192;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::31 => uint96 public constant MAX_ORDERS = 1e5;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::34 => uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::52 => uint48 public endTime; // timestamp after which this trade's auction can be settled
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::53 => uint192 public worstCasePrice; // {buyTok/sellTok}, the worst price we expect to get at Auction
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::85 => uint48 auctionLength,
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::88 => require(req.sellAmount <= type(uint96).max, "sellAmount too large");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::89 => require(req.minBuyAmount <= type(uint96).max, "minBuyAmount too large");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::95 => require(initBal <= type(uint96).max, "initBal too large");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::103 => endTime = uint48(block.timestamp) + auctionLength;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::112 => uint96 sellAmount = uint96(
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::121 => uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::202 => uint192 clearingPrice = shiftl_toFix(boughtAmt, -int8(buy.decimals())).div(
```



### [G12] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function


#### Findings:
```
reserve-protocol/contracts/p1/AssetRegistry.sol::103 => require(_erc20s.contains(address(erc20)), "erc20 unregistered");
reserve-protocol/contracts/p1/BasketHandler.sol::230 => require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
reserve-protocol/contracts/p1/RToken.sol::217 => require(status == CollateralStatus.SOUND, "basket unsound");
reserve-protocol/contracts/p1/RToken.sol::558 => require(_msgSender() == address(backingManager), "not backing manager");
reserve-protocol/contracts/p1/StRSR.sol::213 => require(rsrAmount > 0, "Cannot stake zero");
reserve-protocol/contracts/p1/StRSRVotes.sol::89 => require(blockNumber < block.number, "ERC20Votes: block not yet mined");
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::172 => require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::294 => require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);
reserve-protocol/contracts/plugins/governance/Governance.sol::102 => require(startedInSameEra(proposalId), "new era");
```


### [G13] Use custom errors rather than `revert()`/`require()` strings to save deployment gas



#### Findings:
```
reserve-protocol/contracts/libraries/RedemptionBattery.sol::44 => require(amount <= charge, "redemption battery insufficient");
reserve-protocol/contracts/mixins/Auth.sol::94 => require(account != address(0), "cannot grant role to address 0");
reserve-protocol/contracts/mixins/Auth.sol::181 => require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
reserve-protocol/contracts/mixins/Auth.sol::188 => require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
reserve-protocol/contracts/mixins/Auth.sol::197 => require(newUnfreezeAt > unfreezeAt, "frozen");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::37 => require(address(val) != address(0), "invalid RToken address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::45 => require(address(val) != address(0), "invalid StRSR address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::53 => require(address(val) != address(0), "invalid AssetRegistry address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::61 => require(address(val) != address(0), "invalid BasketHandler address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::69 => require(address(val) != address(0), "invalid BackingManager address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::77 => require(address(val) != address(0), "invalid Distributor address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::85 => require(address(val) != address(0), "invalid RSRTrader address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::93 => require(address(val) != address(0), "invalid RTokenTrader address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::101 => require(address(val) != address(0), "invalid Furnace address");
reserve-protocol/contracts/mixins/ComponentRegistry.sol::109 => require(address(val) != address(0), "invalid Broker address");
reserve-protocol/contracts/p1/AssetRegistry.sol::74 => require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");
reserve-protocol/contracts/p1/AssetRegistry.sol::88 => require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");
reserve-protocol/contracts/p1/AssetRegistry.sol::89 => require(assets[asset.erc20()] == asset, "asset not found");
reserve-protocol/contracts/p1/AssetRegistry.sol::103 => require(_erc20s.contains(address(erc20)), "erc20 unregistered");
reserve-protocol/contracts/p1/AssetRegistry.sol::111 => require(_erc20s.contains(address(erc20)), "erc20 unregistered");
reserve-protocol/contracts/p1/AssetRegistry.sol::112 => require(assets[erc20].isCollateral(), "erc20 is not collateral");
reserve-protocol/contracts/p1/BackingManager.sol::73 => require(assetRegistry.isRegistered(erc20), "erc20 unregistered");
reserve-protocol/contracts/p1/BackingManager.sol::85 => require(ArrayLib.allUnique(erc20s), "duplicate tokens");
reserve-protocol/contracts/p1/BackingManager.sol::97 => require(ArrayLib.sortedAndAllUnique(erc20s), "duplicate/unsorted tokens");
reserve-protocol/contracts/p1/BackingManager.sol::111 => require(basketHandler.status() == CollateralStatus.SOUND, "basket not sound");
reserve-protocol/contracts/p1/BackingManager.sol::257 => require(val <= MAX_TRADING_DELAY, "invalid tradingDelay");
reserve-protocol/contracts/p1/BackingManager.sol::264 => require(val <= MAX_BACKING_BUFFER, "invalid backingBuffer");
reserve-protocol/contracts/p1/BasketHandler.sol::168 => require(_msgSender() == address(assetRegistry), "asset registry only");
reserve-protocol/contracts/p1/BasketHandler.sol::213 => require(erc20s.length > 0, "cannot empty basket");
reserve-protocol/contracts/p1/BasketHandler.sol::214 => require(erc20s.length == targetAmts.length, "must be same length");
reserve-protocol/contracts/p1/BasketHandler.sol::230 => require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
reserve-protocol/contracts/p1/BasketHandler.sol::231 => require(0 < targetAmts[i], "invalid target amount; must be nonzero");
reserve-protocol/contracts/p1/BasketHandler.sol::232 => require(targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount; too large");
reserve-protocol/contracts/p1/BasketHandler.sol::654 => require(erc20s[i] != rsr, "RSR is not valid collateral");
reserve-protocol/contracts/p1/BasketHandler.sol::655 => require(erc20s[i] != IERC20(address(rToken)), "RToken is not valid collateral");
reserve-protocol/contracts/p1/BasketHandler.sol::656 => require(erc20s[i] != IERC20(address(stRSR)), "stRSR is not valid collateral");
reserve-protocol/contracts/p1/BasketHandler.sol::657 => require(erc20s[i] != zero, "address zero is not valid collateral");
reserve-protocol/contracts/p1/BasketHandler.sol::660 => require(ArrayLib.allUnique(erc20s), "contains duplicates");
reserve-protocol/contracts/p1/Broker.sol::57 => require(address(gnosis_) != address(0), "invalid Gnosis address");
reserve-protocol/contracts/p1/Broker.sol::86 => require(!disabled, "broker disabled");
reserve-protocol/contracts/p1/Broker.sol::125 => require(trades[_msgSender()], "unrecognized trade contract");
reserve-protocol/contracts/p1/Deployer.sol::109 => require(owner != address(0) && owner != address(this), "invalid owner");
reserve-protocol/contracts/p1/Distributor.sol::92 => require(erc20 == rsr || erc20 == rToken, "RSR or RToken");
reserve-protocol/contracts/p1/Distributor.sol::98 => require(totalShares > 0, "nothing to distribute");
reserve-protocol/contracts/p1/Distributor.sol::162 => require(dest != address(0), "dest cannot be zero");
reserve-protocol/contracts/p1/Distributor.sol::163 => if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");
reserve-protocol/contracts/p1/Distributor.sol::164 => if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");
reserve-protocol/contracts/p1/Distributor.sol::165 => require(share.rsrDist <= 10000, "RSR distribution too high");
reserve-protocol/contracts/p1/Distributor.sol::166 => require(share.rTokenDist <= 10000, "RToken distribution too high");
reserve-protocol/contracts/p1/Distributor.sol::172 => require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");
reserve-protocol/contracts/p1/Distributor.sol::182 => require(rTokenDist > 0 || rsrDist > 0, "no distribution defined");
reserve-protocol/contracts/p1/Furnace.sol::89 => require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
reserve-protocol/contracts/p1/Furnace.sol::97 => require(ratio_ <= MAX_RATIO, "invalid ratio");
reserve-protocol/contracts/p1/Main.sol::32 => require(address(rsr_) != address(0), "invalid RSR address");
reserve-protocol/contracts/p1/Main.sol::48 => require(!pausedOrFrozen(), "paused or frozen");
reserve-protocol/contracts/p1/RToken.sol::156 => require(bytes(name_).length > 0, "name empty");
reserve-protocol/contracts/p1/RToken.sol::157 => require(bytes(symbol_).length > 0, "symbol empty");
reserve-protocol/contracts/p1/RToken.sol::158 => require(bytes(mandate_).length > 0, "mandate empty");
reserve-protocol/contracts/p1/RToken.sol::191 => require(amtRToken > 0, "Cannot issue zero");
reserve-protocol/contracts/p1/RToken.sol::217 => require(status == CollateralStatus.SOUND, "basket unsound");
reserve-protocol/contracts/p1/RToken.sol::384 => require(status == CollateralStatus.SOUND, "basket unsound");
reserve-protocol/contracts/p1/RToken.sol::410 => require(queue.left <= endId && endId <= queue.right, "out of range");
reserve-protocol/contracts/p1/RToken.sol::440 => require(amount > 0, "Cannot redeem zero");
reserve-protocol/contracts/p1/RToken.sol::448 => require(basketHandler.status() != CollateralStatus.DISABLED, "collateral default");
reserve-protocol/contracts/p1/RToken.sol::513 => if (allZero) revert("Empty redemption");
reserve-protocol/contracts/p1/RToken.sol::558 => require(_msgSender() == address(backingManager), "not backing manager");
reserve-protocol/contracts/p1/RToken.sol::581 => require(_msgSender() == address(backingManager), "not backing manager");
reserve-protocol/contracts/p1/RToken.sol::590 => require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
reserve-protocol/contracts/p1/RToken.sol::603 => require(val <= FIX_ONE, "invalid fraction");
reserve-protocol/contracts/p1/RToken.sol::623 => require(index >= item.left && index < item.right, "out of range");
reserve-protocol/contracts/p1/RToken.sol::705 => revert("Bad refundSpan");
reserve-protocol/contracts/p1/RToken.sol::741 => require(queue.left <= endId && endId <= queue.right, "out of range");
reserve-protocol/contracts/p1/RToken.sol::747 => require(rightItem.when <= FIX_ONE_256 * block.number, "issuance not ready");
reserve-protocol/contracts/p1/RToken.sol::813 => require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
reserve-protocol/contracts/p1/RToken.sol::833 => require(to != address(this), "RToken transfer to self");
reserve-protocol/contracts/p1/RevenueTrader.sol::29 => require(address(tokenToBuy_) != address(0), "invalid token address");
reserve-protocol/contracts/p1/RevenueTrader.sol::72 => require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
reserve-protocol/contracts/p1/StRSR.sol::168 => require(bytes(name_).length > 0, "name empty");
reserve-protocol/contracts/p1/StRSR.sol::169 => require(bytes(symbol_).length > 0, "symbol empty");
reserve-protocol/contracts/p1/StRSR.sol::213 => require(rsrAmount > 0, "Cannot stake zero");
reserve-protocol/contracts/p1/StRSR.sol::259 => require(stakeAmount > 0, "Cannot withdraw zero");
reserve-protocol/contracts/p1/StRSR.sol::260 => require(stakes[era][account] >= stakeAmount, "Not enough balance");
reserve-protocol/contracts/p1/StRSR.sol::305 => require(basketHandler.fullyCollateralized(), "RToken uncollateralized");
reserve-protocol/contracts/p1/StRSR.sol::306 => require(basketHandler.status() == CollateralStatus.SOUND, "basket defaulted");
reserve-protocol/contracts/p1/StRSR.sol::312 => require(endId <= queue.length, "index out-of-bounds");
reserve-protocol/contracts/p1/StRSR.sol::313 => require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");
reserve-protocol/contracts/p1/StRSR.sol::375 => require(_msgSender() == address(backingManager), "not backing manager");
reserve-protocol/contracts/p1/StRSR.sol::376 => require(rsrAmount > 0, "Amount cannot be zero");
reserve-protocol/contracts/p1/StRSR.sol::380 => require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");
reserve-protocol/contracts/p1/StRSR.sol::660 => require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
reserve-protocol/contracts/p1/StRSR.sol::675 => require(from != address(0), "ERC20: transfer from the zero address");
reserve-protocol/contracts/p1/StRSR.sol::676 => require(to != address(0), "ERC20: transfer to the zero address");
reserve-protocol/contracts/p1/StRSR.sol::680 => require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
reserve-protocol/contracts/p1/StRSR.sol::695 => require(account != address(0), "ERC20: mint to the zero address");
reserve-protocol/contracts/p1/StRSR.sol::711 => require(account != address(0), "ERC20: burn from the zero address");
reserve-protocol/contracts/p1/StRSR.sol::717 => require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
reserve-protocol/contracts/p1/StRSR.sol::732 => require(owner != address(0), "ERC20: approve from the zero address");
reserve-protocol/contracts/p1/StRSR.sol::733 => require(spender != address(0), "ERC20: approve to the zero address");
reserve-protocol/contracts/p1/StRSR.sol::746 => require(currentAllowance >= amount, "ERC20: insufficient allowance");
reserve-protocol/contracts/p1/StRSR.sol::760 => require(to != address(this), "StRSR transfer to self");
reserve-protocol/contracts/p1/StRSR.sol::775 => require(block.timestamp <= deadline, "ERC20Permit: expired deadline");
reserve-protocol/contracts/p1/StRSR.sol::813 => require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
reserve-protocol/contracts/p1/StRSR.sol::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
reserve-protocol/contracts/p1/StRSR.sol::821 => require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
reserve-protocol/contracts/p1/StRSR.sol::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
reserve-protocol/contracts/p1/StRSR.sol::829 => require(val <= MAX_REWARD_RATIO, "invalid rewardRatio");
reserve-protocol/contracts/p1/StRSRVotes.sol::77 => require(blockNumber < block.number, "ERC20Votes: block not yet mined");
reserve-protocol/contracts/p1/StRSRVotes.sol::83 => require(blockNumber < block.number, "ERC20Votes: block not yet mined");
reserve-protocol/contracts/p1/StRSRVotes.sol::89 => require(blockNumber < block.number, "ERC20Votes: block not yet mined");
reserve-protocol/contracts/p1/StRSRVotes.sol::126 => require(block.timestamp <= expiry, "ERC20Votes: signature expired");
reserve-protocol/contracts/p1/StRSRVotes.sol::133 => require(nonce == _useNonce(signer), "ERC20Votes: invalid nonce");
reserve-protocol/contracts/p1/mixins/Component.sol::34 => require(address(main_) != address(0), "main is zero address");
reserve-protocol/contracts/p1/mixins/Component.sol::42 => require(!main.pausedOrFrozen(), "paused or frozen");
reserve-protocol/contracts/p1/mixins/Component.sol::47 => require(!main.frozen(), "frozen");
reserve-protocol/contracts/p1/mixins/Component.sol::52 => require(main.hasRole(OWNER, _msgSender()), "governance only");
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::96 => require(reg.isRegistered(erc20), "erc20 unregistered");
reserve-protocol/contracts/p1/mixins/Trading.sol::69 => require(trade.canSettle(), "cannot settle yet");
reserve-protocol/contracts/p1/mixins/Trading.sol::129 => require(val < MAX_TRADE_SLIPPAGE, "invalid maxTradeSlippage");
reserve-protocol/contracts/p1/mixins/Trading.sol::136 => require(val <= MIN_TRADE_VOLUME, "invalid minTradeVolume");
reserve-protocol/contracts/plugins/aave/ERC20.sol::242 => require(sender != address(0), "ERC20: transfer from the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::243 => require(recipient != address(0), "ERC20: transfer to the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::262 => require(account != address(0), "ERC20: mint to the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::283 => require(account != address(0), "ERC20: burn from the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::310 => require(owner != address(0), "ERC20: approve from the zero address");
reserve-protocol/contracts/plugins/aave/ERC20.sol::311 => require(spender != address(0), "ERC20: approve to the zero address");
reserve-protocol/contracts/plugins/aave/ReentrancyGuard.sol::51 => require(_status != _ENTERED, "ReentrancyGuard: reentrant call");
reserve-protocol/contracts/plugins/assets/Asset.sol::48 => require(priceTimeout_ > 0, "price timeout zero");
reserve-protocol/contracts/plugins/assets/Asset.sol::49 => require(address(chainlinkFeed_) != address(0), "missing chainlink feed");
reserve-protocol/contracts/plugins/assets/Asset.sol::50 => require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
reserve-protocol/contracts/plugins/assets/Asset.sol::51 => require(address(erc20_) != address(0), "missing erc20");
reserve-protocol/contracts/plugins/assets/Asset.sol::52 => require(maxTradeVolume_ > 0, "invalid max trade volume");
reserve-protocol/contracts/plugins/assets/Asset.sol::53 => require(oracleTimeout_ > 0, "oracleTimeout zero");
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::26 => require(address(comptroller_) != address(0), "comptroller missing");
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::29 => require(referenceERC20Decimals_ > 0, "referenceERC20Decimals missing");
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::30 => require(address(comptroller_) != address(0), "comptroller missing");
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::24 => require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::73 => require(config.targetName != bytes32(0), "targetName missing");
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::75 => require(config.delayUntilDefault > 0, "delayUntilDefault zero");
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::24 => require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::28 => require(address(erc20_) != address(0), "missing erc20");
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::29 => require(maxTradeVolume_ > 0, "invalid max trade volume");
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::19 => require(config.defaultThreshold == 0, "default threshold not supported");
reserve-protocol/contracts/plugins/governance/Governance.sol::102 => require(startedInSameEra(proposalId), "new era");
reserve-protocol/contracts/plugins/governance/Governance.sol::112 => require(!startedInSameEra(proposalId), "same era");
reserve-protocol/contracts/plugins/governance/Governance.sol::123 => require(startedInSameEra(proposalId), "new era");
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::35 => if (revertDecimals) address(this).functionStaticCall(data, "No Decimals");
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::41 => if (censored[owner] || censored[to]) revert("censored");
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::54 => if (censored[from] || censored[to]) revert("censored");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::131 => require(newFeeNumerator <= 15, "Fee is not allowed to be set higher than 1.5%");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::164 => require(_auctionedSellAmount > 0, "cannot auction zero tokens");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::165 => require(_minBuyAmount > 0, "tokens cannot be auctioned for free");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::174 => require(auctionEndDate > block.timestamp, "auction end date must be in the future");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::289 => require(_minBuyAmounts[i] > 0, "_minBuyAmounts must be greater than 0");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::292 => require(_sellAmounts[i] > minimumBiddingAmountPerOrder, "order too small");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::326 => require(userIdOfIter == userId, "Only the user can cancel his orders");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::350 => require(iterOrder != IterableOrderedOrderSet.QUEUE_END, "reached end of order list");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::516 => require(sellOrders[auctionId].remove(orders[i]), "order is no longer claimable");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::526 => require(userIdOrder == userId, "only allowed to claim for same user");
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::607 => require(registeredUsers.insert(numUsers, user), "User already registered");
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::69 => require(auctionedSellAmount > 0, "sell amount is zero");
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::91 => require(bid.sellAmount <= auctions[auctionId].sellAmount, "invalid bid sell");
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::92 => require(bid.buyAmount > 0, "zero volume bid");
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::100 => require(auction.endTime <= block.timestamp, "too early to close auction");
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::101 => require(auction.status == MauctionStatus.OPEN, "auction already closed");
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::33 => require(auctionedSellAmount > 0, "sell amount is zero");
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::61 => require(auction.endTime <= block.timestamp, "too early to close auction");
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::62 => require(auction.status == MauctionStatus.OPEN, "auction already closed");
reserve-protocol/contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol::11 => revert("claimRewards() error");
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::39 => require(!disabled, "broker disabled");
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::43 => revert("Failure opening trade");
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::17 => revert("errormsg"); // Revert with no reason
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::43 => require(targetName_ != bytes32(0), "targetName missing");
reserve-protocol/contracts/plugins/mocks/upgrades/MainV2.sol::10 => require(!pausedOrFrozen(), "paused or frozen");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::205 => require(abi.decode(returndata, (bool)), "SafeERC20: ERC20 operation did not succeed");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::258 => require(denominator != uint96(0), "Inserting zero is not supported");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::362 => require(userIdLeft != userIdRight, "user is not allowed to place same order twice");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::370 => require(!isEmpty(self), "Trying to get first from empty set");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::375 => require(value != QUEUE_END, "Trying to get next of last element");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::377 => require(nextElement != bytes32(0), "Trying to get next of non-existent element");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::460 => require(c >= a, "SafeMath: addition overflow");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::519 => require(c / a == b, "SafeMath: multiplication overflow");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::623 => require(hasId(self, id), "Must have ID to get Address");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::628 => require(hasAddress(self, addr), "Must have Address to get ID");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::637 => require(addr != address(0), "Cannot insert zero address");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::638 => require(id != uint64(-1), "Cannot insert max uint64");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::657 => require(value < 2**96, "SafeCast: value doesn't fit in 96 bits");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::662 => require(value < 2**64, "SafeCast: value doesn't fit in 64 bits");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::703 => * `recipient`, forwarding all available gas and reverting on errors.
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::718 => require(address(this).balance >= amount, "Address: insufficient balance");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::722 => require(success, "Address: unable to send value, recipient may have reverted");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::793 => require(address(this).balance >= value, "Address: insufficient balance for call");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::794 => require(isContract(target), "Address: call to non-contract");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::826 => require(isContract(target), "Address: static call to non-contract");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::915 => require(_owner == _msgSender(), "Ownable: caller is not the owner");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::936 => require(newOwner != address(0), "Ownable: new owner is the zero address");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::60 => require(status == begin, "Invalid trade state");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::88 => require(req.sellAmount <= type(uint96).max, "sellAmount too large");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::89 => require(req.minBuyAmount <= type(uint96).max, "minBuyAmount too large");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::95 => require(initBal <= type(uint96).max, "initBal too large");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::96 => require(initBal >= req.sellAmount, "unfunded trade");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::174 => require(msg.sender == origin, "only origin can settle");
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::216 => require(status == TradeStatus.CLOSED, "only after trade is closed");
```




### [G14] Functions guaranteed to revert when called by normal users can be marked `payable`

#### Impact
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
#### Findings:
```
reserve-protocol/contracts/mixins/Auth.sol::65 => function __Auth_init(uint48 shortFreeze_, uint48 longFreeze_) internal onlyInitializing {
reserve-protocol/contracts/mixins/Auth.sol::120 => function freezeShort() external onlyRole(SHORT_FREEZER) {
reserve-protocol/contracts/mixins/Auth.sol::135 => function freezeLong() external onlyRole(LONG_FREEZER) {
reserve-protocol/contracts/mixins/Auth.sol::148 => function freezeForever() external onlyRole(OWNER) {
reserve-protocol/contracts/mixins/Auth.sol::157 => function unfreeze() external onlyRole(OWNER) {
reserve-protocol/contracts/mixins/Auth.sol::165 => function pause() external onlyRole(PAUSER) {
reserve-protocol/contracts/mixins/Auth.sol::172 => function unpause() external onlyRole(PAUSER) {
reserve-protocol/contracts/mixins/Auth.sol::180 => function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
reserve-protocol/contracts/mixins/Auth.sol::187 => function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
reserve-protocol/contracts/mixins/ComponentRegistry.sol::19 => function __ComponentRegistry_init(Components memory components_) internal onlyInitializing {
reserve-protocol/contracts/p1/Main.sol::64 => function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
reserve-protocol/contracts/p1/mixins/Component.sol::33 => function __Component_init(IMain main_) internal onlyInitializing {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::926 => function renounceOwnership() public virtual onlyOwner {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::935 => function transferOwnership(address newOwner) public virtual onlyOwner {
```




### [G15] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.10 to have external calls skip
 contract existence checks if the external call has a return value
#### Findings:
```
reserve-protocol/contracts/interfaces/IAsset.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IAssetRegistry.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IBackingManager.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IBasketHandler.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IBroker.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IComponent.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IDeployer.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IDeployerRegistry.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IDistributor.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IFacadeAct.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IFacadeRead.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IFacadeTest.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IFacadeWrite.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IFurnace.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IGnosis.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IMain.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IRToken.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IRevenueTrader.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IRewardable.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IStRSR.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IStRSRVotes.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/ITrade.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/ITrading.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/interfaces/IVersioned.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/libraries/Array.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/libraries/Fixed.sol::3 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/Permit.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/libraries/RedemptionBattery.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/libraries/String.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/libraries/test/ArrayCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/test/StringCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/mixins/Auth.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/mixins/ComponentRegistry.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/mixins/Versioned.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/AssetRegistry.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/BackingManager.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/BasketHandler.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/Broker.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/Deployer.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/Distributor.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/Furnace.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/Main.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/RToken.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/RevenueTrader.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/StRSR.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/StRSRVotes.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/mixins/Component.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/mixins/TradeLib.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/p1/mixins/Trading.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/aave/ERC20.sol::3 => pragma solidity ^0.6.0;
reserve-protocol/contracts/plugins/aave/IAToken.sol::2 => pragma solidity 0.6.12;
reserve-protocol/contracts/plugins/aave/IAaveIncentivesController.sol::2 => pragma solidity 0.6.12;
reserve-protocol/contracts/plugins/aave/IStaticATokenLM.sol::2 => pragma solidity 0.6.12;
reserve-protocol/contracts/plugins/aave/RayMathNoRounding.sol::2 => pragma solidity 0.6.12;
reserve-protocol/contracts/plugins/aave/ReentrancyGuard.sol::3 => pragma solidity >=0.6.0 <0.8.0;
reserve-protocol/contracts/plugins/aave/StaticATokenErrors.sol::2 => pragma solidity 0.6.12;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::2 => pragma solidity 0.6.12;
reserve-protocol/contracts/plugins/assets/ATokenFiatCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/Asset.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/ICToken.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/OracleLib.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/governance/Governance.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/AaveLendingPoolMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::2 => pragma solidity ^0.8.0;
reserve-protocol/contracts/plugins/mocks/ComptrollerMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/ERC1271Mock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/ERC20Mock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::2 => pragma solidity 0.6.12;
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::2 => pragma solidity ^0.8.0;
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/SelfdestructTransferMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/USDCMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/WBTCMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/WETH.sol::16 => pragma solidity >=0.4.22 <0.6;
reserve-protocol/contracts/plugins/mocks/ZeroDecimalMock.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/AssetRegistryV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/BackingManagerV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/BasketHandlerV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/BrokerV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/DistributorV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/FurnaceV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/MainV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/RTokenV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/RevenueTraderV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/upgrades/StRSRV2.sol::2 => pragma solidity 0.8.9;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::2 => pragma solidity 0.6.12;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::2 => pragma solidity 0.8.9;
```




### [G16] Bitshift for divide by 2

#### Impact
When multiplying or dividing by a power of two, it is cheaper to bitshift than to use standard math operations. 
#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::164 => if (numerator % divisor > (divisor - 1) / 2) {
reserve-protocol/contracts/libraries/Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
reserve-protocol/contracts/libraries/Fixed.sol::323 => if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
reserve-protocol/contracts/libraries/Fixed.sol::326 => x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
reserve-protocol/contracts/libraries/Fixed.sol::539 => if (mm > ((z - 1) / 2)) result += 1; // z should be z-1
reserve-protocol/contracts/p1/BasketHandler.sol::372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
reserve-protocol/contracts/p1/BasketHandler.sol::374 => //          A)  shiftDelta = rawDelta + (FIX_ONE / 2)
reserve-protocol/contracts/p1/StRSR.sol::427 => return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
reserve-protocol/contracts/p1/StRSR.sol::453 => test = (left + right) / 2; // left < test < right because left < right - 1
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::429 => // (a + b) / 2 can overflow, so we distribute
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::430 => return (a / 2) + (b / 2) + (((a % 2) + (b % 2)) / 2);
```

### [G17] Use `calldata` instead of `memory` for function parameters

#### Impact
Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.

There are several cases of function arguments using memory instead of
#### Findings:
```
reserve-protocol/contracts/libraries/String.sol::11 => function toLower(string memory str) internal pure returns (string memory) {
reserve-protocol/contracts/libraries/test/StringCallerMock.sol::9 => function toLower(string memory str) external pure returns (string memory){
reserve-protocol/contracts/plugins/aave/ERC20.sol::57 => constructor(string memory name, string memory symbol) public {
```



### [G18] Amounts should be checked for 0 before calling a transfer

#### Impact
Checking non-zero transfer values can avoid an expensive external call and save gas.

While this is done in some places, it’s not consistently done in the solution.

I suggest adding a non-zero-value check here:
#### Findings:
```
reserve-protocol/contracts/p1/BackingManager.sol::175 => IERC20Upgradeable(address(rsr)).safeTransfer(
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::347 => ATOKEN.safeTransfer(recipient, amountToWithdraw);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::464 => REWARD_TOKEN.safeTransfer(receiver, reward);
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::331 => auctionData[auctionId].biddingToken.safeTransfer(msg.sender, claimableAmount); //[2]
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::598 => auctionData[auctionId].auctioningToken.safeTransfer(userAddress, auctioningTokenAmount);
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::601 => auctionData[auctionId].biddingToken.safeTransfer(userAddress, biddingTokenAmount);
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::109 => auction.sell.safeTransfer(auction.origin, auction.sellAmount);
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::115 => auction.sell.safeTransfer(bid.bidder, bid.sellAmount);
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::116 => auction.buy.safeTransfer(auction.origin, bid.buyAmount);
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::118 => auction.sell.safeTransfer(auction.origin, auction.sellAmount - bid.sellAmount);
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::75 => auction.sell.safeTransfer(auction.origin, auction.sellAmount);
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::81 => auction.sell.safeTransfer(bid.bidder, bid.sellAmount);
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::82 => auction.buy.safeTransfer(auction.origin, bid.buyAmount);
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::84 => auction.sell.safeTransfer(auction.origin, auction.sellAmount - bid.sellAmount);
reserve-protocol/contracts/plugins/mocks/WETH.sol::45 => msg.sender.transfer(wad);
```


### [G19] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate

#### Impact
Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot
#### Findings:
```
reserve-protocol/contracts/p1/BasketHandler.sol::40 => mapping(IERC20 => uint192) targetAmts;
reserve-protocol/contracts/p1/BasketHandler.sol::55 => mapping(IERC20 => uint192) refAmts; // {ref/BU}

reserve-protocol/contracts/p1/StRSR.sol::60 => mapping(uint256 => mapping(address => uint256)) private stakes; // Stakes per account {qStRSR}
reserve-protocol/contracts/p1/StRSR.sol::82 => mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index
reserve-protocol/contracts/p1/StRSR.sol::678 => mapping(address => uint256) storage eraStakes = stakes[era];
reserve-protocol/contracts/p1/StRSR.sol::713 => mapping(address => uint256) storage eraStakes = stakes[era];

reserve-protocol/contracts/p1/mixins/RewardableLib.sol::58 => mapping(IERC20 => uint256) storage liabilities,
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::91 => mapping(IERC20 => uint256) storage liabilities,

reserve-protocol/contracts/plugins/aave/ERC20.sol::38 => mapping(address => uint256) private _balances;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::66 => mapping(address => uint256) public _nonces;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::74 => mapping(address => uint256) private _userSnapshotRewardsPerToken;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::76 => mapping(address => uint256) private _unclaimedRewards;

reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::27 => mapping(uint256 => int256) public getAnswer;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::28 => mapping(uint256 => uint256) public getTimestamp;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::29 => mapping(uint256 => uint256) private getStartedAt;

reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::112 => mapping(uint256 => IterableOrderedOrderSet.Data) internal sellOrders;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::113 => mapping(uint256 => AuctionData) public auctionData;

reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::233 => mapping(bytes32 => bytes32) nextMap;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::234 => mapping(bytes32 => bytes32) prevMap;
```




### [G20] Remove unused local variable


#### Findings:
```
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::721 => (bool success, ) = recipient.call{ value: amount }("");
```




### [G21] Using `bools` for storage incurs overhead



#### Findings:
```
reserve-protocol/contracts/mixins/Auth.sol::42 => bool public paused;
reserve-protocol/contracts/p1/BasketHandler.sol::139 => bool private disabled;
reserve-protocol/contracts/p1/Broker.sol::41 => bool public disabled;
reserve-protocol/contracts/p1/Broker.sol::44 => mapping(address => bool) private trades;
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::10 => bool public checkSoftDefault = true; // peg
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::11 => bool public checkHardDefault = true; // defi invariant
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::15 => function setSoftDefaultCheck(bool on) external {
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::19 => function setHardDefaultCheck(bool on) external {
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::13 => bool public revertDecimals;
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::15 => mapping(address => bool) public censored;
reserve-protocol/contracts/plugins/mocks/ERC1271Mock.sol::14 => bool public approvalsOn = false;
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::16 => bool public reenterOnInit;
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::17 => bool public reenterOnSettle;
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::20 => mapping(address => bool) private trades;
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::24 => bool public disabled = false;
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::13 => bool public simplyRevert;
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::9 => bool public simplyRevert;
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::31 => bool public priceable;
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::13 => bool public unpriced = false;
```

### [G22] Using `private` rather than `public` for constants, saves gas

#### Impact
If needed, the value can be read from the verified contract source 
code.
#### Findings:
```
reserve-protocol/contracts/mixins/Auth.sol::27 => bytes32 public constant OWNER_ROLE = OWNER;
reserve-protocol/contracts/mixins/Auth.sol::28 => bytes32 public constant SHORT_FREEZER_ROLE = SHORT_FREEZER;
reserve-protocol/contracts/mixins/Auth.sol::29 => bytes32 public constant LONG_FREEZER_ROLE = LONG_FREEZER;
reserve-protocol/contracts/mixins/Auth.sol::30 => bytes32 public constant PAUSER_ROLE = PAUSER;
reserve-protocol/contracts/p1/BackingManager.sol::33 => uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
reserve-protocol/contracts/p1/BackingManager.sol::34 => uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%
reserve-protocol/contracts/p1/BasketHandler.sol::117 => uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight
reserve-protocol/contracts/p1/Broker.sol::24 => uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
reserve-protocol/contracts/p1/Deployer.sol::31 => string public constant ENS = "reserveprotocol.eth";
reserve-protocol/contracts/p1/Distributor.sol::31 => address public constant FURNACE = address(1);
reserve-protocol/contracts/p1/Distributor.sol::32 => address public constant ST_RSR = address(2);
reserve-protocol/contracts/p1/Distributor.sol::34 => uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;
reserve-protocol/contracts/p1/Furnace.sol::15 => uint192 public constant MAX_RATIO = FIX_ONE; // {1} 100%
reserve-protocol/contracts/p1/Furnace.sol::16 => uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
reserve-protocol/contracts/p1/StRSR.sol::37 => uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
reserve-protocol/contracts/p1/StRSR.sol::38 => uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
reserve-protocol/contracts/p1/StRSR.sol::39 => uint192 public constant MAX_REWARD_RATIO = FIX_ONE; // {1} 100%
reserve-protocol/contracts/p1/StRSR.sol::45 => uint8 public constant decimals = 18;
reserve-protocol/contracts/p1/mixins/Trading.sol::20 => uint192 public constant MIN_TRADE_VOLUME = 1e29; // {UoA}
reserve-protocol/contracts/p1/mixins/Trading.sol::21 => uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}
reserve-protocol/contracts/plugins/aave/StaticATokenErrors.sol::5 => string public constant INVALID_OWNER = "1";
reserve-protocol/contracts/plugins/aave/StaticATokenErrors.sol::6 => string public constant INVALID_EXPIRATION = "2";
reserve-protocol/contracts/plugins/aave/StaticATokenErrors.sol::7 => string public constant INVALID_SIGNATURE = "3";
reserve-protocol/contracts/plugins/aave/StaticATokenErrors.sol::8 => string public constant INVALID_DEPOSITOR = "4";
reserve-protocol/contracts/plugins/aave/StaticATokenErrors.sol::9 => string public constant INVALID_RECIPIENT = "5";
reserve-protocol/contracts/plugins/aave/StaticATokenErrors.sol::10 => string public constant INVALID_CLAIMER = "6";
reserve-protocol/contracts/plugins/aave/StaticATokenErrors.sol::11 => string public constant ONLY_ONE_AMOUNT_FORMAT_ALLOWED = "7";
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::40 => bytes public constant EIP712_REVISION = bytes("1");
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::45 => bytes32 public constant PERMIT_TYPEHASH =
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::49 => bytes32 public constant METADEPOSIT_TYPEHASH =
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::53 => bytes32 public constant METAWITHDRAWAL_TYPEHASH =
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::58 => uint256 public constant STATIC_ATOKEN_LM_REVISION = 0x1;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::17 => uint256 public constant override version = 0;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::124 => uint256 public constant FEE_DENOMINATOR = 1000;
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::50 => uint256 public constant feeNumerator = 0; // Does not support a fee
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::27 => uint256 public constant FEE_DENOMINATOR = 1000;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::31 => uint96 public constant MAX_ORDERS = 1e5;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::34 => uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}
```




### [G23] Usage of `assert()` instead of `require()`


#### Findings:
```
reserve-protocol/contracts/p1/BackingManager.sol::249 => assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
reserve-protocol/contracts/p1/BasketHandler.sol::556 => assert(targetIndex < targetsLength);
reserve-protocol/contracts/p1/StRSR.sol::696 => assert(totalStakes + amount < type(uint224).max);
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::110 => assert(doTrade);
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::78 => assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::102 => assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
reserve-protocol/contracts/p1/mixins/TradeLib.sol::44 => assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
reserve-protocol/contracts/p1/mixins/TradeLib.sol::108 => assert(
reserve-protocol/contracts/p1/mixins/TradeLib.sol::168 => assert(errorCode == 0x11 || errorCode == 0x12);
reserve-protocol/contracts/p1/mixins/TradeLib.sol::170 => assert(keccak256(reason) == UIntOutofBoundsHash);
reserve-protocol/contracts/p1/mixins/Trading.sol::114 => assert(address(trades[sell]) == address(0));
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::345 => assert(amt == amountToWithdraw);
reserve-protocol/contracts/plugins/assets/Asset.sol::98 => assert(low == 0);
reserve-protocol/contracts/plugins/assets/Asset.sol::112 => assert(low <= high);
reserve-protocol/contracts/plugins/assets/Asset.sol::147 => assert(lotLow <= lotHigh);
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::137 => assert(low == 0);
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::74 => assert(low <= high);
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::63 => assert(status == TradeStatus.PENDING);
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::98 => assert(origin_ != address(0));
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::182 => assert(isAuctionCleared());
```



### [G24] Empty blocks should be removed or emit something

#### Impact
The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be abstract and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})
#### Findings:
```
reserve-protocol/contracts/p1/Main.sol::23 => constructor() initializer {}
reserve-protocol/contracts/p1/Main.sol::64 => function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
reserve-protocol/contracts/p1/RToken.sol::452 => try main.furnace().melt() {} catch {}
reserve-protocol/contracts/p1/RToken.sol::838 => function requireNotPausedOrFrozen() private notPausedOrFrozen {}
reserve-protocol/contracts/p1/mixins/Component.sol::25 => constructor() initializer {}
reserve-protocol/contracts/p1/mixins/Component.sol::57 => function _authorizeUpgrade(address newImplementation) internal view override governance {}
reserve-protocol/contracts/plugins/aave/ERC20.sol::346 => ) internal virtual {}
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::98 => } catch {}
reserve-protocol/contracts/plugins/assets/ATokenFiatCollateral.sol::40 => constructor(CollateralConfig memory config) FiatCollateral(config) {}
reserve-protocol/contracts/plugins/assets/Asset.sol::164 => function claimRewards() external virtual {}
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::119 => function claimRewards() external virtual {}
reserve-protocol/contracts/plugins/governance/Governance.sol::44 => {}
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::13 => constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::17 => constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}
reserve-protocol/contracts/plugins/mocks/ComptrollerMock.sol::12 => constructor() {}
reserve-protocol/contracts/plugins/mocks/ERC20Mock.sol::8 => constructor(string memory name, string memory symbol) ERC20(name, symbol) {}
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::121 => constructor() public Ownable() {}
reserve-protocol/contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol::7 => constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::48 => function reportViolation() external {}
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::52 => function setAuctionLength(uint48 newAuctionLength) external governance {}
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::56 => function setDisabled(bool disabled_) external governance {}
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::17 => {}
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::12 => constructor(CollateralConfig memory config) FiatCollateral(config) {}
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::11 => constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::9 => constructor(CollateralConfig memory config) FiatCollateral(config) {}
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::20 => constructor(CollateralConfig memory config) FiatCollateral(config) {}
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::31 => constructor(CollateralConfig memory config) FiatCollateral(config) {}
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::42 => constructor(CollateralConfig memory config) FiatCollateral(config) {}
reserve-protocol/contracts/plugins/mocks/USDCMock.sol::8 => constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::27 => ) Asset(priceTimeout_, chainlinkFeed_, oracleError_, erc20_, maxTradeVolume_, oracleTimeout_) {}
reserve-protocol/contracts/plugins/mocks/WBTCMock.sol::8 => constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}
reserve-protocol/contracts/plugins/mocks/ZeroDecimalMock.sol::8 => constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}
```




### [G25] Remove or replace unused state variables

#### Impact
Saves a storage slot. If the variable is assigned a non-zero value, 
saves Gsset (20000 gas). If it’s assigned a zero value, saves Gsreset 
(2900 gas). If the variable remains unassigned, there is no gas savings 
unless the variable is public, in which case the 
compiler-generated non-payable getter deployment cost is saved. If the 
state variable is overriding an interface’s public function, mark the 
variable as constant or immutable so that it does not use a storage slot
#### Findings:
```
reserve-protocol/contracts/p1/StRSR.sol::81 => mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {drafts}
reserve-protocol/contracts/p1/StRSRVotes.sol::38 => mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}
reserve-protocol/contracts/p1/StRSRVotes.sol::40 => mapping(uint256 => Checkpoint[]) private _totalSupplyCheckpoints; // {qStRSR}
```



### [G26] `abi.encode()` is less efficient than abi.encodePacked()


#### Findings:
```
reserve-protocol/contracts/p1/StRSR.sol::778 => abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)
reserve-protocol/contracts/p1/StRSRVotes.sol::128 => _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::151 => abi.encode(PERMIT_TYPEHASH, owner, spender, value, currentValidNonce, deadline)
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::179 => abi.encode(
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::219 => abi.encode(
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::269 => abi.encode(
```



### [G27] Multiplication/division by two should use bit shifting

#### Impact
<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas
#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::164 => if (numerator % divisor > (divisor - 1) / 2) {
reserve-protocol/contracts/libraries/Fixed.sol::310 => uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
reserve-protocol/contracts/libraries/Fixed.sol::323 => if (y & 1 == 1) result = (result * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
reserve-protocol/contracts/libraries/Fixed.sol::326 => x = (x * x + FIX_SCALE_SQ / 2) / FIX_SCALE_SQ;
reserve-protocol/contracts/libraries/Fixed.sol::539 => if (mm > ((z - 1) / 2)) result += 1; // z should be z-1
reserve-protocol/contracts/p1/BasketHandler.sol::372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
reserve-protocol/contracts/p1/StRSR.sol::427 => return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
reserve-protocol/contracts/p1/StRSR.sol::453 => test = (left + right) / 2; // left < test < right because left < right - 1
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::139 => // 2^96 units of the biddingToken, don't start the auction, as
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::430 => return (a / 2) + (b / 2) + (((a % 2) + (b % 2)) / 2);
```




### [G28] Optimize names to save gas

#### Impact
public/external function names and public member variable names can be optimized to save gas. See this
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
#### Findings:
```
reserve-protocol/contracts/mixins/Auth.sol::16 => abstract contract Auth is AccessControlUpgradeable, IAuth {
reserve-protocol/contracts/p1/StRSR.sol::33 => abstract contract StRSRP1 is Initializable, ComponentP1, IStRSR, EIP712Upgradeable {
reserve-protocol/contracts/p1/mixins/Trading.sol::16 => abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeable, ITrading {
```


