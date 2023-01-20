## QA
---

### Layout Order [1]

- The best-practices for layout within a contract is the following order: state variables, events, modifiers, constructor and functions.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol

```solidity
// events coming before some state variables
25:    event Approval(address indexed src, address indexed guy, uint256 wad);
26:    event Transfer(address indexed src, address indexed dst, uint256 wad);
27:    event Deposit(address indexed dst, uint256 wad);
28:    event Withdrawal(address indexed src, uint256 wad);
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol

```solidity
// modifiers coming before state variables and events
18:    modifier atStageOrderPlacement(uint256 auctionId) {
26:    modifier atStageOrderPlacementAndCancelation(uint256 auctionId) {
34:    modifier atStageSolutionSubmission(uint256 auctionId) {
47:    modifier atStageFinished(uint256 auctionId) {

// state variables coming after constructor
123:    uint256 public feeNumerator = 0;
124:    uint256 public constant FEE_DENOMINATOR = 1000;
125:    uint64 public feeReceiverUserId = 1;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol

```solidity
// these structs should come before all functions, they are intercalating with functions
116:    struct BasketRange {
284:    struct MaxSurplusDeficit {
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/mixins/TradingLib.sol

```solidity
// these structs should come before all functions, they are intercalating with functions
82:     struct ComponentCache {
91:     struct TradingRules {
96:     struct TradeInfo {
166:    struct BasketRange {
330:    struct MaxSurplusDeficit {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol

```solidity
// state variables should be declared together before functions
34:    IRToken public rToken;
42:    IStRSR public stRSR;
50:    IAssetRegistry public assetRegistry;
58:    IBasketHandler public basketHandler;
66:    IBackingManager public backingManager;
74:    IDistributor public distributor;
82:    IRevenueTrader public rsrTrader;
90:    IRevenueTrader public rTokenTrader;
98:    IFurnace public furnace;
106:    IBroker public broker;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol

```solidity
// state variable should come before functions
310:    uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L104-L142

```solidity
 // events and functions should not intercalate like this, all events should come before
```

---

### Function Visibility [2]

- Order of Functions: Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. Functions should be grouped according to their visibility and ordered: constructor, receive function (if exists), fallback function (if exists), public, external, internal, private. Within a grouping, place the view and pure functions last. 


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol

```solidity
// external function should come after all public ones
80:    function isCollateral() external pure virtual override(RTokenAsset, IAsset) returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/MockableCollateral.sol

```solidity
// public function coming after external one
17:    function targetPerRef() public view override returns (uint192) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol

```solidity
// visibility functions are all mixed throughout the code, consider ordering them according to specifications
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ERC1271Mock.sol

```solidity
// public function coming after external ones
31:    function isValidSignature(bytes32, bytes memory) public view returns (bytes4 magicValue) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol

```solidity
// external functions coming before public ones
19:    function setTransferFee(uint192 newFee) external {
23:    function setRevertDecimals(bool newVal) external {
27:    function setCensored(address account, bool val) external {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadCollateralPlugin.sol

```solidity
// this public functions should come before external ones
26:    function refresh() public virtual override {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol

```solidity
// this public is coming after internal ones
159:    function supportsInterface(bytes4 interfaceId)
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol

```solidity
// visibility functions are all mixed throughout the code, consider ordering them according to specifications
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol

```solidity
// this external functions is coming after public ones
69:    function tryPrice()
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol

```solidity
// visibility functions are all mixed throughout the code, consider ordering them according to specifications
```

---

### natSpec missing [3]

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/StRSRV2.sol

```solidity
6:   contract StRSRP1VotesV2 is StRSRP1Votes {

9:    function setNewValue(uint256 newValue_) external governance {

13:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/RevenueTraderV2.sol

```solidity
10:    function setNewValue(uint256 newValue_) external governance {

14:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/MainV2.sol

```solidity
6:   contract MainP1V2 is MainP1 {

9:    function setNewValue(uint256 newValue_) external {

14:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/DistributorV2.sol

```solidity
6:   contract DistributorP1V2 is DistributorP1 {

9:    function setNewValue(uint256 newValue_) external governance {

13:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/FurnaceV2.sol

```solidity
6:   contract FurnaceP1V2 is FurnaceP1 {

9:    function setNewValue(uint256 newValue_) external governance {

13:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/DistributorV2.sol

```solidity
6:   contract DistributorP1V2 is DistributorP1 {

9:    function setNewValue(uint256 newValue_) external governance {

13:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BrokerV2.sol

```solidity
6:   contract BrokerP1V2 is BrokerP1 {

9:    function setNewValue(uint256 newValue_) external governance {

13:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BasketHandlerV2.sol

```solidity
6:   contract BasketHandlerP1V2 is BasketHandlerP1 {

9:    function setNewValue(uint256 newValue_) external governance {

13:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/BackingManagerV2.sol

```solidity
10:    function setNewValue(uint256 newValue_) external governance {

14:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/AssetRegistryV2.sol

```solidity
6:   contract AssetRegistryP1V2 is AssetRegistryP1 {

9:    function setNewValue(uint256 newValue_) external governance {

13:    function version() public pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol

```solidity
6:  library AllowListVerifierHelper {

// @params missing
19:    function isAllowed(

117:    function safeTransfer(

125:    function safeTransferFrom(

// @params missing
144:    function safeApprove(

160:    function safeIncreaseAllowance(

172:    function safeDecreaseAllowance(

210:    library IterableOrderedOrderSet {

243:    function initializeEmptyList(Data storage self) internal {

248:    function isEmpty(Data storage self) internal view returns (bool) {

252:    function insert(

// @params missing
304:    function removeKeepHistory(Data storage self, bytes32 elementToRemove) internal returns (bool) {

// @params and @return missing
320:    function remove(Data storage self, bytes32 elementToRemove) internal returns (bool) {

328:    function contains(Data storage self, bytes32 value) internal view returns (bool) {

341:    function smallerThan(bytes32 orderLeft, bytes32 orderRight) internal pure returns (bool) {

369:    function first(Data storage self) internal view returns (bytes32) {

374:    function next(Data storage self, bytes32 value) internal view returns (bytes32) {

381:    function decodeOrder(bytes32 _orderData)

608:    library IdToAddressBiMap {

614:    function hasId(Data storage self, uint64 id) internal view returns (bool) {

618:    function hasAddress(Data storage self, address addr) internal view returns (bool) {

622:    function getAddressAt(Data storage self, uint64 id) internal view returns (address) {

627:    function getId(Data storage self, address addr) internal view returns (uint64) {

632:    function insert(

656:    function toUint96(uint256 value) internal pure returns (uint96) {

661:    function toUint64(uint256 value) internal pure returns (uint64) {

868:    function _msgSender() internal view virtual returns (address payable) {

872:    function _msgData() internal view virtual returns (bytes memory) {

942:    interface IWETH is IERC20 {

943:    function deposit() external payable;

945:    function withdraw(uint256) external;

948:    contract IterableOrderedOrderSetWrapper {

953:    function initializeEmptyList() public {

957:    function insert(bytes32 value) public returns (bool) {

961:    function insertAt(bytes32 value, bytes32 at) public returns (bool) {

965:    function remove(bytes32 value) public returns (bool) {

969:    function removeKeepHistory(bytes32 value) public returns (bool) {

973:    function contains(bytes32 value) public view returns (bool) {

977:    function isEmpty() public view returns (bool) {

981:    function first() public view returns (bytes32) {

985:    function next(bytes32 value) public view returns (bytes32) {

989:    function nextMap(bytes32 value) public view returns (bytes32) {

993:    function prevMap(bytes32 value) public view returns (bytes32) {

997:    function decodeOrder(bytes32 value)

1009:    function encodeOrder(

1017:    function smallerThan(bytes32 orderLeft, bytes32 orderRight) public pure returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ZeroDecimalMock.sol

```solidity
6:   contract ZeroDecimalMock is ERC20Mock {

8:    constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}

10:    function decimals() public pure override returns (uint8) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol

```solidity
37:    function deposit() public payable {

42:    function withdraw(uint256 wad) public {

49:    function totalSupply() public view returns (uint256) {

53:    function approve(address guy, uint256 wad) public returns (bool) {

59:    function transfer(address dst, uint256 wad) public returns (bool) {

63:    function transferFrom(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WBTCMock.sol

```solidity
6:    contract WBTCMock is ERC20Mock {

8:    constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}

10:    function decimals() public pure override returns (uint8) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/UnpricedAssetPlugin.sol

```solidity
30:    function tryPrice()

48:    function setUnpriced(bool on) external {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/USDCMock.sol

```solidity
6:  contract USDCMock is ERC20Mock {

8:    constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}

10:    function decimals() public pure override returns (uint8) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/SelfdestructTransferMock.sol

```solidity
4:   contract SelfdestructTransfer {

5:    function destroyAndTransfer(address payable to) external payable {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol

```solidity
// @params missing
37:    constructor(

55:    function refresh() public virtual override(RTokenAsset, IAsset) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol

```solidity
6:    contract NontrivialPegCollateral0 is FiatCollateral {

9:    constructor(CollateralConfig memory config) FiatCollateral(config) {}

17:   contract NontrivialPegCollateral1 is FiatCollateral {

20:    constructor(CollateralConfig memory config) FiatCollateral(config) {}

28:   contract NontrivialPegCollateral2 is FiatCollateral {

31:    constructor(CollateralConfig memory config) FiatCollateral(config) {}

39:    contract NontrivialPegCollateral3 is FiatCollateral {

42:    constructor(CollateralConfig memory config) FiatCollateral(config) {}
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/MockableCollateral.sol

```solidity
6:   contract MockableCollateral is ATokenFiatCollateral {

11:    constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}

13:    function setTargetPerRef(uint192 val) external {

17:    function targetPerRef() public view override returns (uint192) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidFiatCollateral.sol

```solidity
6:   contract InvalidFiatCollateral is FiatCollateral {

// @return missing
15:    function price() public view virtual returns (uint192, uint192) {

27:    function setSimplyRevert(bool on) external {

31:    function infiniteLoop() external pure {

```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol

```solidity
15:    constructor(uint8 _decimals, int256 _initialAnswer)

19:    function latestRoundData()

46:    function setSimplyRevert(bool on) external {

50:    function infiniteLoop() external pure {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol

```solidity
26:    function init(

38:    function openTrade(TradeRequest memory req) external view notPausedOrFrozen returns (ITrade) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol

```solidity
6:   contract InvalidATokenFiatCollateralMock is ATokenFiatCollateral {

7:    constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol

```solidity
20:    function initiateAuction(

59:    function settleAuction(uint256 auctionId) external override returns (bytes32 encodedOrder) {

92:    function setReenterOnInit(bool value) external {

96:    function setReenterOnSettle(bool value) external {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol

```solidity
11:  interface IBiddable {

//@params missing
56:    function initiateAuction(

//@params missing
90:    function placeBid(uint256 auctionId, Bid memory bid) external {

98:    function settleAuction(uint256 auctionId) external virtual returns (bytes32 encodedOrder) {

126:    function auctionData(uint256 auctionId) external view returns (GnosisAuctionData memory data) {

131:    function numAuctions() external view returns (uint256) {

135:    function _encodeOrder(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol

```solidity
8:   contract EasyAuction is Ownable {

127:    function setFeeParameters(uint256 newFeeNumerator, address newfeeReceiverAddress)

213:    function placeSellOrders(

231:    function placeSellOrdersOnBehalf(

250:    function _placeSellOrders(

310:    function cancelSellOrders(uint256 auctionId, bytes32[] memory _sellOrders)

334:    function precalculateSellAmountSum(uint256 auctionId, uint256 iterationSteps)

365:    function settleAuctionAtomically(

// @param and @return missing
399:    function settleAuction(uint256 auctionId)

508:    function claimFromParticipantOrder(uint256 auctionId, bytes32[] memory orders)

557:    function processFeesAndAuctioneerFunds(

590:    function sendOutTokens(

605:    function registerUser(address user) public returns (uint64 userId) {

612:    function getUserId(address user) public returns (uint64 userId) {

621:    function getSecondsRemainingInBatch(uint256 auctionId) public view returns (uint256) {

628:    function containsOrder(uint256 auctionId, bytes32 order) public view returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ERC20Mock.sol

```solidity
6:     contract ERC20Mock is ERC20 {

8:     constructor(string memory name, string memory symbol) ERC20(name, symbol) {}

10:    function mint(address recipient, uint256 amount) external {

14:    function burn(address sender, uint256 amount) external {

18:    function adminApprove(

```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ERC1271Mock.sol

```solidity
16:    function enableApprovals() external {

20:    function disableApprovals() external {

// @params missing
31:    function isValidSignature(bytes32, bytes memory) public view returns (bytes4 magicValue) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ComptrollerMock.sol

```solidity
14:    function setCompToken(address compToken_) external {

18:    function setRewards(address recipient, uint256 amount) external {

22:    function claimComp(address holder) external {

31:    function getCompAddress() external view returns (address) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol

```solidity
31:    constructor(uint8 _decimals, int256 _initialAnswer) {

36:    function updateAnswer(int256 _answer) public {

56:    function updateRoundData(

71:    function getRoundData(uint80 _roundId)

92:    function latestRoundData()

114:    function description() external pure override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/CTokenMock.sol

```solidity
14:    constructor(

23:    function decimals() public pure override returns (uint8) {

27:    function exchangeRateCurrent() external returns (uint256) {

32:    function exchangeRateStored() external view returns (uint256) {

41:    function underlying() external view returns (address) {

45:    function _toExchangeRate(uint192 fiatcoinRedemptionRate) internal view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol

```solidity
8:  contract BadERC20 is ERC20Mock {

17:    constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}

19:    function setTransferFee(uint192 newFee) external {

23:    function setRevertDecimals(bool newVal) external {

27:    function setCensored(address account, bool val) external {

31:    function decimals() public view override returns (uint8) {

39:    function transfer(address to, uint256 amount) public virtual override returns (bool) {

48:    function transferFrom(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadCollateralPlugin.sol

```solidity
13:    constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}

15:    function setSoftDefaultCheck(bool on) external {

19:    function setHardDefaultCheck(bool on) external {


```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/AaveLendingPoolMock.sol

```solidity
6:    function getAddressesProvider() external view returns (ILendingPoolAddressesProvider);

9:   interface ILendingPoolAddressesProvider {

10:    function getPriceOracle() external view returns (IAaveOracle);

13:    interface IAaveOracle {

15:    function WETH() external view returns (address);

// @param misssing
18:    function getAssetPrice(address token) external view returns (uint256);

21:   contract AaveLendingPoolMock is IAaveLendingPool {

27:    constructor(address lendingAddressesProvider) {

31:    function getAddressesProvider() external view returns (ILendingPoolAddressesProvider) {

35:    function getReserveNormalizedIncome(address asset) external view returns (uint256) {

39:    function setNormalizedIncome(address asset, uint256 newRate) external {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol

```solidity
12:    constructor(

// missing @return
21:    function UNDERLYING_ASSET_ADDRESS() external view returns (address) {

39:    constructor(

50:    function decimals() public pure override returns (uint8) {

54:    function rate() external view returns (uint256) {

58:    function setExchangeRate(uint192 fiatcoinRedemptionRate) external {

62:    function setAaveToken(address aaveToken_) external {

67:    function ATOKEN() external view returns (ATokenMock) {

72:    function REWARD_TOKEN() external view returns (IERC20) {

76:    function setRewards(address recipient, uint256 amount) external {

80:    function claimRewardsToSelf(bool) external {

87:    function getClaimableRewards(address user) external view returns (uint256) {

91:    function _toExchangeRate(uint192 fiatcoinRedemptionRate) internal pure returns (uint256) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol

```solidity
228:    function isAuctionCleared() private view returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol

```solidity
31:    constructor(

48:    function votingDelay() public view override(IGovernor, GovernorSettings) returns (uint256) {

52:    function votingPeriod() public view override(IGovernor, GovernorSettings) returns (uint256) {

57:    function proposalThreshold()

//missing @param
67:    function quorum(uint256)

76:    function state(uint256 proposalId)

85:    function propose(

95:    function queue(

105:    function cancel(

115:    function _execute(

126:    function _cancel(

135:    function _executor()

// missing @params
145:    function _getVotes(

159:    function supportsInterface(bytes4 interfaceId)

170:    function startedInSameEra(uint256 proposalId) private view returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol

```solidity
// @param missing
104:    function bal(address account) external view returns (uint192) {

125:    function basketRange()
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol

```solidity
// @param missing
25:    constructor(CollateralConfig memory config, IComptroller comptroller_) FiatCollateral(config) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol

```solidity
40:    constructor(CollateralConfig memory config) FiatCollateral(config) {}

```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol

```solidity
78:    constructor(

279:    function _dynamicToStaticAmount(uint256 amount, uint256 rate_) internal pure returns (uint256) {

283:    function _staticToDynamicAmount(uint256 amount, uint256 rate_) internal pure returns (uint256) {

287:    function _deposit(

310:    function _withdraw(

403:    function _collectAndUpdateRewards() internal {

468:    function claimRewardsOnBehalf(

484:    function claimRewards(address receiver, bool forceUpdate) external override nonReentrant {

491:    function claimRewardsToSelf(bool forceUpdate) external override nonReentrant {

598:    function getAccRewardsPerToken() external view override returns (uint256) {

602:    function getLifetimeRewardsClaimed() external view override returns (uint256) {

606:    function getLifetimeRewards() external view override returns (uint256) {

610:    function getLastRewardBlock() external view override returns (uint256) {

614:    function getIncentivesController() external view override returns (IAaveIncentivesController) {

618:    function UNDERLYING_ASSET_ADDRESS() external view override returns (address) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol

```solidity
7:   library RayMathNoRounding {

11:    function rayMulNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {

19:    function rayDivNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {

25:    function rayToWadNoRounding(uint256 a) internal pure returns (uint256) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol

```solidity
224:    function getAccRewardsPerToken() external view returns (uint256);

226:    function getLifetimeRewardsClaimed() external view returns (uint256);

228:    function getLifetimeRewards() external view returns (uint256);

230:    function getLastRewardBlock() external view returns (uint256);

232:    function LENDING_POOL() external view returns (ILendingPool);

234:    function INCENTIVES_CONTROLLER() external view returns (IAaveIncentivesController);

236:    function ATOKEN() external view returns (IERC20);

238:    function ASSET() external view returns (IERC20);

240:    function REWARD_TOKEN() external view returns (IERC20);

242:    function UNDERLYING_ASSET_ADDRESS() external view returns (address);

244:    function getIncentivesController() external view returns (IAaveIncentivesController);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol

```solidity
128:    function setMaxTradeSlippage(uint192 val) public governance {

135:    function setMinTradeVolume(uint192 val) public governance {

144:    function mulDivCeil(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol

```solidity
154:    function safeMulDivCeil(

// @param missing
180:    function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {

// @params missing
188:    function maxTradeSize(IAsset asset, uint192 price) private view returns (uint192) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol

```solidity
55:    function currentEra() external view returns (uint256) {

59:    function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {

63:    function numCheckpoints(address account) public view returns (uint48) {

67:    function delegates(address account) public view returns (address) {

71:    function getVotes(address account) public view returns (uint256) {

76:    function getPastVotes(address account, uint256 blockNumber) public view returns (uint256) {

82:    function getPastTotalSupply(uint256 blockNumber) public view returns (uint256) {

88:    function getPastEra(uint256 blockNumber) public view returns (uint256) {

94:    function _checkpointsLookup(Checkpoint[] storage ckpts, uint256 blockNumber)

114:    function delegate(address delegatee) public {

118:    function delegateBySig(

137:    function _mint(address account, uint256 amount) internal override {

142:    function _burn(address account, uint256 amount) internal override {

147:    function _afterTokenTransfer(

166:    function _moveVotingPower(

193:    function _writeCheckpoint(

214:    function _add(uint256 a, uint256 b) private pure returns (uint256) {

218:    function _subtract(uint256 a, uint256 b) private pure returns (uint256) {
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol

```solidity
160:    function init(

// @params missing
462:    function draftQueueLen(uint256 era_, address account) external view returns (uint256) {

604:    function totalSupply() public view returns (uint256) {

608:    function balanceOf(address account) public view returns (uint256) {

612:    function allowance(address owner, address spender)

622:    function transfer(address to, uint256 amount) public returns (bool) {

632:    function approve(address spender, uint256 amount) public returns (bool) {

641:    function transferFrom(

651:    function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {

657:    function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {

670:    function _transfer(

694:    function _mint(address account, uint256 amount) internal virtual {

708:    function _burn(address account, uint256 amount) internal virtual {

727:    function _approve(

739:    function _spendAllowance(

755:    function _afterTokenTransfer(

766:    function permit(

786:    function nonces(address owner) public view returns (uint256) {

791:    function DOMAIN_SEPARATOR() external view returns (bytes32) {

795:    function _useNonce(address owner) internal returns (uint256 current) {

803:    function setName(string calldata name_) external governance {

807:    function setSymbol(string calldata symbol_) external governance {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol

```solidity
//@param missing
378:    function vest(address account, uint256 endId) external notPausedOrFrozen {

621:    function issueItem(address account, uint256 index) external view returns (IssueItem memory) {

//@param missing
634:    function queueBounds(address account) external view returns (uint256 left, uint256 right) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol

```solidity
26:    function init(

53:    function hasRole(bytes32 role, address account)

64:    function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol

```solidity
75:    function setFrom(Basket storage self, Basket storage other) internal {

// @param missing
296:    function quantity(IERC20 erc20) public view returns (uint192) {

// @param missing
332:    function _price(bool useLotPrice) internal view returns (uint192 low, uint192 high) {

// @param missing
407:    function quote(uint192 amount, RoundingMode rounding)

// @param missing
432:    function basketsHeldBy(address account) public view returns (uint192 baskets) {

// @param missing
650:    function requireValidCollArray(IERC20[] calldata erc20s) internal view {

// @param missing
665:    function goodCollateral(bytes32 targetName, IERC20 erc20) private view returns (bool) {
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/mixins/TradingLib.sol

```solidity
//incomplete
594:    function safeMulDivCeil(

625:    function maxTradeSize(IAsset asset, uint192 price) private view returns (uint192) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/mixins/Trading.sol

```solidity
//incomplete
13:   abstract contract TradingP0 is RewardableP0, ITrading {

//incomplete
34:    function __Trading_init(uint192 maxTradeSlippage_, uint192 minTradeVolume_)

44:    function settleTrade(IERC20 sell) public notPausedOrFrozen {

56:    function tryTrade(TradeRequest memory req) internal {

80:    function setMaxTradeSlippage(uint192 val) public governance {

87:    function setMinTradeVolume(uint192 val) public governance {

96:    function mulDivCeil(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Versioned.sol

```solidity
11:    function version() public pure virtual override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol

```solidity
36:    function _setRToken(IRToken val) private {

44:    function _setStRSR(IStRSR val) private {

52:    function _setAssetRegistry(IAssetRegistry val) private {

60:    function _setBasketHandler(IBasketHandler val) private {

68:    function _setBackingManager(IBackingManager val) private {

76:    function _setDistributor(IDistributor val) private {

84:    function _setRSRTrader(IRevenueTrader val) private {

92:    function _setRTokenTrader(IRevenueTrader val) private {

98:    IFurnace public furnace;

108:    function _setBroker(IBroker val) private {
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/StringCallerMock.sol

```solidity
8:   contract StringCallerMock {

9:    function toLower(string memory str) external pure returns (string memory){
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol

```solidity
8:  contract FixedCallerMock {

9:    function toFix_(uint256 x) public pure returns (uint192 ) {

12:    function shiftl_toFix_(uint256 x, int8 d) public pure returns (uint192 ) {

15:    function shiftl_toFix_Rnd(uint256 x, int8 d, RoundingMode rnd) public pure returns (uint192 ) {

19:    function divFix_(uint256 x, uint192  y) public pure returns (uint192 ) {

22:    function divuu_(uint256 x, uint256 y) public pure returns (uint256) {

26:    function fixMin_(uint192  x, uint192  y) public pure returns (uint192 ) {

29:    function fixMax_(uint192  x, uint192  y) public pure returns (uint192 ) {

32:    function abs_(int256 x) public pure returns (uint256) {

35:    function divrnd_(uint256 n, uint256 d, RoundingMode rnd) public pure returns (uint256) {

38:    function toUint(uint192  x) public pure returns (uint256 ) {

41:    function toUintRnd(uint192  x, RoundingMode rnd) public pure returns (uint256 ) {

44:    function shiftl(uint192  x, int8 decimals) public pure returns (uint192 ) {

47:    function shiftlRnd(uint192  x, int8 decimals, RoundingMode rnd) public pure returns (uint192 ) {

50:    function plus(uint192  x, uint192  y) public pure returns (uint192 ) {

53:    function plusu(uint192  x, uint256 y) public pure returns (uint192 ) {

56:    function minus(uint192  x, uint192  y) public pure returns (uint192 ) {

59:    function minusu(uint192  x, uint256 y) public pure returns (uint192 ) {

62:    function mul(uint192  x, uint192  y) public pure returns (uint192 ) {

65:    function mulRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {

68:    function mulu(uint192  x, uint256 y) public pure returns (uint192 ) {

71:    function div(uint192  x, uint192  y) public pure returns (uint192 ) {

74:    function divRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {

77:    function divu(uint192  x, uint256 y) public pure returns (uint192 ) {

80:    function divuRnd(uint192  x, uint256 y, RoundingMode rnd) public pure returns (uint192 ) {

83:    function powu(uint192  x, uint48 y) public pure returns (uint192 ) {

86:    function lt(uint192  x, uint192  y) public pure returns (bool) {

89:    function lte(uint192  x, uint192  y) public pure returns (bool) {

92:    function gt(uint192  x, uint192  y) public pure returns (bool) {

95:    function gte(uint192  x, uint192  y) public pure returns (bool) {

98:    function eq(uint192  x, uint192  y) public pure returns (bool) {

101:    function neq(uint192  x, uint192  y) public pure returns (bool) {

104:    function near(uint192  x, uint192  y, uint192  epsilon) public pure returns (bool) {

109:    function shiftl_toUint(uint192  x, int8 d) public pure returns (uint256) {

112:    function shiftl_toUintRnd(uint192  x, int8 d, RoundingMode rnd) public pure returns (uint256) {

115:    function mulu_toUint(uint192  x, uint256 y) public pure returns (uint256) {

118:    function mulu_toUintRnd(uint192  x, uint256 y, RoundingMode rnd) public pure returns (uint256) {

121:    function mul_toUint(uint192  x, uint192  y) public pure returns (uint256) {

124:    function mul_toUintRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint256) {

127:    function muluDivu(uint192  x, uint256 y, uint256 z) public pure returns (uint192 ) {

130:    function muluDivuRnd(uint192  x, uint256 y, uint256 z, RoundingMode rnd) public pure returns (uint192 ) {

133:    function mulDiv(uint192  x, uint192  y, uint192  z) public pure returns (uint192 ) {

136:    function mulDivRnd(uint192  x, uint192  y, uint192  z, RoundingMode rnd) public pure returns (uint192 ) {

141:    function mulDiv256_(uint256 x, uint256 y, uint256 z) public pure returns (uint256) {

144:    function mulDiv256Rnd_(uint256 x, uint256 y, uint256 z, RoundingMode rnd)

148:    function fullMul_(uint256 x, uint256 y) public pure returns (uint256 h, uint256 l) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol

```solidity
7:  contract ArrayCallerMock {

8:    function allUnique(IERC20[] memory arr) public pure returns (bool) {

12:    function sortedAndAllUnique(IERC20[] memory arr) public pure returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol

```solidity
5:  library StringLib {

// @param missing
11:    function toLower(string memory str) internal pure returns (string memory) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol

```solidity
@param missing
33:    function discharge(

@param missing
53:    function currentCharge(Battery storage battery, uint256 supply)
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol

```solidity
10:    function requireSignature(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol

```solidity
// incomplete
72:  function _safeWrap(uint256 x) pure returns (uint192) {

176:  library FixLib {

332:    function lt(uint192 x, uint192 y) internal pure returns (bool) {

336:    function lte(uint192 x, uint192 y) internal pure returns (bool) {

340:    function gt(uint192 x, uint192 y) internal pure returns (bool) {

344:    function gte(uint192 x, uint192 y) internal pure returns (bool) {

348:    function eq(uint192 x, uint192 y) internal pure returns (bool) {

352:    function neq(uint192 x, uint192 y) internal pure returns (bool) {

//incomplete
359:    function near(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol

```solidity
6:   library ArrayLib {

//incomplete
9:    function allUnique(IERC20[] memory arr) internal pure returns (bool) {

//incomplete
21:    function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IVersioned.sol

```solidity
4:   interface IVersioned {

5:    function version() external view returns (string memory);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrade.sol


```solidity
12:    function sell() external view returns (IERC20Metadata);

14:    function buy() external view returns (IERC20Metadata);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSRVotes.sol

```solidity
6:   interface IStRSRVotes is IVotesUpgradeable {

11:    function getPastEra(uint256 blockNumber) external view returns (uint256);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRewardable.sol

```solidity
14:    event RewardsClaimed(IERC20 indexed erc20, uint256 indexed amount);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol

```solidity
69:    event ExchangeRateSet(uint192 indexed oldVal, uint192 indexed newVal);

72:    event RewardsPaid(uint256 indexed rsrAmt);

75:    event AllBalancesReset(uint256 indexed newEra);

77:    event AllUnstakingReset(uint256 indexed newEra);

79:    event UnstakingDelaySet(uint48 indexed oldVal, uint48 indexed newVal);

80:    event RewardPeriodSet(uint48 indexed oldVal, uint48 indexed newVal);

81:    event RewardRatioSet(uint192 indexed oldVal, uint192 indexed newVal);

84:    function init(

123:   interface TestIStRSR is IStRSR {

124:    function rewardPeriod() external view returns (uint48);

126:    function setRewardPeriod(uint48) external;

128:    function rewardRatio() external view returns (uint192);

130:    function setRewardRatio(uint192) external;

132:    function unstakingDelay() external view returns (uint48);

134:    function setUnstakingDelay(uint48) external;

136:    function setName(string calldata) external;

138:    function setSymbol(string calldata) external;

140:    function increaseAllowance(address, uint256) external returns (bool);

142:    function decreaseAllowance(address, uint256) external returns (bool);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRevenueTrader.sol

```solidity
15:    function init(

29:    interface TestIRevenueTrader is IRevenueTrader, TestITrading {

30:    function tokenToBuy() external view returns (IERC20);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol

```solidity
90:    event IssuanceRateSet(uint192 indexed oldVal, uint192 indexed newVal);

93:    event ScalingRedemptionRateSet(uint192 indexed oldVal, uint192 indexed newVal);

96:    event RedemptionRateFloorSet(uint256 indexed oldVal, uint256 indexed newVal);

99:    function init(

171:   interface TestIRToken is IRToken {

173:    function setIssuanceRate(uint192) external;

179:    function setScalingRedemptionRate(uint192 val) external;

185:    function setRedemptionRateFloor(uint256 val) external;

190:    function increaseAllowance(address, uint256) external returns (bool);

192:    function decreaseAllowance(address, uint256) external returns (bool);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol

```solidity
48:    interface IAuth is IAccessControlUpgradeable {

74:    function pausedOrFrozen() external view returns (bool);

76:    function frozen() external view returns (bool);

78:    function shortFreeze() external view returns (uint48);

80:    function longFreeze() external view returns (uint48);

85:    function freezeForever() external;

88:    function freezeShort() external;

91:    function freezeLong() external;

94:    function unfreeze() external;

96:    function pause() external;

98:    function unpause() external;

101:   interface IComponentRegistry {

104:    event RTokenSet(IRToken indexed oldVal, IRToken indexed newVal);

106:    function rToken() external view returns (IRToken);

108:    event StRSRSet(IStRSR indexed oldVal, IStRSR indexed newVal);

110:    function stRSR() external view returns (IStRSR);

112:    event AssetRegistrySet(IAssetRegistry indexed oldVal, IAssetRegistry indexed newVal);

114:    function assetRegistry() external view returns (IAssetRegistry);

116:    event BasketHandlerSet(IBasketHandler indexed oldVal, IBasketHandler indexed newVal);

118:    function basketHandler() external view returns (IBasketHandler);

120:    event BackingManagerSet(IBackingManager indexed oldVal, IBackingManager indexed newVal);

122:    function backingManager() external view returns (IBackingManager);

124:    event DistributorSet(IDistributor indexed oldVal, IDistributor indexed newVal);

126:    function distributor() external view returns (IDistributor);

128:    event RSRTraderSet(IRevenueTrader indexed oldVal, IRevenueTrader indexed newVal);

130:    function rsrTrader() external view returns (IRevenueTrader);

132:    event RTokenTraderSet(IRevenueTrader indexed oldVal, IRevenueTrader indexed newVal);

134:    function rTokenTrader() external view returns (IRevenueTrader);

136:    event FurnaceSet(IFurnace indexed oldVal, IFurnace indexed newVal);

138:    function furnace() external view returns (IFurnace);

140:    event BrokerSet(IBroker indexed oldVal, IBroker indexed newVal);

142:    function broker() external view returns (IBroker);

150:    function poke() external; // not used in p1

156:    function init(

163:    function rsr() external view returns (IERC20);

166:    interface TestIMain is IMain {

168:    function setShortFreeze(uint48) external;

171:    function setLongFreeze(uint48) external;

173:    function shortFreeze() external view returns (uint48);

171:    function longFreeze() external view returns (uint48);

177:    function longFreezes(address account) external view returns (uint256);

179:    function paused() external view returns (bool);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol

```solidity
26:    function initiateAuction(

40:    function auctionData(uint256 auctionId) external view returns (GnosisAuctionData memory);
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol

```solidity
13:    function init(

24:    function period() external view returns (uint48);

34:    function ratio() external view returns (uint192);

45:    interface TestIFurnace is IFurnace {

46:    function lastPayout() external view returns (uint256);

48:    function lastPayoutBal() external view returns (uint256);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeWrite.sol

```solidity
82:    function deployRToken(ConfigurationParams calldata config, SetupParams calldata setup)

87:    function setupGovernance(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeAct.sol

```solidity
// missing @param
18:    function getActCalldata(RTokenP1 rToken) external returns (address to, bytes memory calldata_);

21:    function claimAndSweepRewards(RTokenP1 rToken) external;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol

```solidity
37:    function init(IMain main_, RevenueShare memory dist) external;

54:interface TestIDistributor is IDistributor {

56:    function FURNACE() external view returns (address);

59:    function ST_RSR() external view returns (address);

//missing @param
63:    function distribution(address) external view returns (uint16 rTokenDist, uint16 rsrDist);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol

```solidity
7:    event DeploymentUnregistered(string version, IDeployer deployer);

8:    event DeploymentRegistered(string version, IDeployer deployer);

9:    event LatestChanged(string version, IDeployer deployer);

// missing param
15:    function register(

22:    function unregister(string calldata version) external;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol

```solidity
97:    interface TestIDeployer is IDeployer {

//incomplete
100:    function ENS() external view returns (string memory);

102:    function rsr() external view returns (IERC20Metadata);

104:    function gnosis() external view returns (IGnosis);

106:    function rsrAsset() external view returns (IAsset);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IComponent.sol

```solidity
14:    function main() external view returns (IMain);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBroker.sol

```solidity
27:    function init(

40:    function reportViolation() external;

42:    function disabled() external view returns (bool);

45:    interface TestIBroker is IBroker {

46:    function gnosis() external view returns (IGnosis);

48:    function auctionLength() external view returns (uint48);

50:    function setAuctionLength(uint48 newAuctionLength) external;

52:    function setDisabled(bool disabled_) external;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol

```solidity
37:    function init(IMain main_) external;

//incomplete
77:    function quantity(IERC20 erc20) external view returns (uint192);

//incomplete
85:    function quote(uint192 amount, RoundingMode rounding)

//incomplete
91:    function basketsHeldBy(address account) external view returns (uint192 baskets);
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol

```solidity
24:    function init(

49:    interface TestIBackingManager is IBackingManager, TestITrading {

50:    function tradingDelay() external view returns (uint48);

52:    function backingBuffer() external view returns (uint192);

54:    function setTradingDelay(uint48 val) external;

56:    function setBackingBuffer(uint192 val) external;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAssetRegistry.sol

```solidity
34:    function init(IMain main_, IAsset[] memory assets_) external;

//incomplete
41:    function toAsset(IERC20 erc20) external view returns (IAsset);

//incomplete
44:    function toColl(IERC20 erc20) external view returns (ICollateral);

//incomplete
47:    function isRegistered(IERC20 erc20) external view returns (bool);

55:    function register(IAsset asset) external returns (bool);

57:    function swapRegistered(IAsset asset) external returns (bool swapped);

59:    function unregister(IAsset asset) external;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol

```solidity
//incomplete
35:    function bal(address account) external view returns (uint192);

50: interface TestIAsset is IAsset {

51:    function chainlinkFeed() external view returns (AggregatorV3Interface);

66:    function worseThan(CollateralStatus a, CollateralStatus b) internal pure returns (bool) {

```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/lib/FacadeWriteLib.sol

```solidity
6:library FacadeWriteLib {

// incomplete spec
8:    function deployGovernance(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/FacadeWrite.sol

```solidity
17:    constructor(IDeployer deployer_) {

23:    function deployRToken(ConfigurationParams calldata config, SetupParams calldata setup)

// spec incomplete
129:    function setupGovernance(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/FacadeAct.sol

```solidity
352:    function claimAndSweepRewards(RTokenP1 rToken) public {
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/DeployerRegistry.sol

```solidity
19:    constructor(address owner_) Ownable() {

46:    function unregister(string calldata version) external onlyOwner {
```

---

### State variable and function names [4]

- Variables should be named according to their specifications
- private and internal `variables` should preppend with `underline`
- private and internal `functions` should preppend with `underline`
- constant state variables should be UPPER_CASE

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol

```solidity
10:    bytes4 internal constant MAGICVALUE = 0x19a05a7e;

117:    function safeTransfer(

125:    function safeTransferFrom(

144:    function safeApprove(

160:    function safeIncreaseAllowance(

172:    function safeDecreaseAllowance(

215:    bytes32 internal constant QUEUE_START =

218:    bytes32 internal constant QUEUE_END =

243:    function initializeEmptyList(Data storage self) internal {

248:    function isEmpty(Data storage self) internal view returns (bool) {

252:    function insert(

304:    function removeKeepHistory(Data storage self, bytes32 elementToRemove) internal returns (bool) {

320:    function remove(Data storage self, bytes32 elementToRemove) internal returns (bool) {

328:    function contains(Data storage self, bytes32 value) internal view returns (bool) {

341:    function smallerThan(bytes32 orderLeft, bytes32 orderRight) internal pure returns (bool) {

369:    function first(Data storage self) internal view returns (bytes32) {

374:    function next(Data storage self, bytes32 value) internal view returns (bytes32) {

381:    function decodeOrder(bytes32 _orderData)

397:    function encodeOrder(

413:    function max(uint256 a, uint256 b) internal pure returns (uint256) {

420:    function min(uint256 a, uint256 b) internal pure returns (uint256) {

428:    function average(uint256 a, uint256 b) internal pure returns (uint256) {

458:    function add(uint256 a, uint256 b) internal pure returns (uint256) {

475:    function sub(uint256 a, uint256 b) internal pure returns (uint256) {

489:    function sub(

510:    function mul(uint256 a, uint256 b) internal pure returns (uint256) {

536:    function div(uint256 a, uint256 b) internal pure returns (uint256) {

552:    function div(

576:    function mod(uint256 a, uint256 b) internal pure returns (uint256) {

592:    function mod(

614:    function hasId(Data storage self, uint64 id) internal view returns (bool) {

618:    function hasAddress(Data storage self, address addr) internal view returns (bool) {

622:    function getAddressAt(Data storage self, uint64 id) internal view returns (address) {

627:    function getId(Data storage self, address addr) internal view returns (uint64) {

632:    function insert(

656:    function toUint96(uint256 value) internal pure returns (uint96) {

661:    function toUint64(uint256 value) internal pure returns (uint64) {

688:    function isContract(address account) internal view returns (bool) {

717:    function sendValue(address payable recipient, uint256 amount) internal {

743:    function functionCall(address target, bytes memory data) internal returns (bytes memory) {

772:    function functionCallWithValue(

787:    function functionCallWithValue(

807:    function functionStaticCall(address target, bytes memory data)

821:    function functionStaticCall(

951:    IterableOrderedOrderSet.Data internal data;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol

```solidity
7:    uint192 private peg = FIX_ONE; // {target/ref}

18:    uint192 private peg = FIX_ONE; // {target/ref}

29:    uint192 private peg = FIX_ONE; // {target/ref}

40:    uint192 private peg = FIX_ONE; // {target/ref}
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol

```solidity
112:    mapping(uint256 => IterableOrderedOrderSet.Data) internal sellOrders;

117:    IdToAddressBiMap.Data private registeredUsers;

557:    function processFeesAndAuctioneerFunds(

590:    function sendOutTokens(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol

```solidity
// constant should be upper-case
17:    uint256 public constant override version = 0;

29:    mapping(uint256 => uint256) private getStartedAt;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol

```solidity
30:    ATokenMock internal aToken;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol

```solidity
228:    function isAuctionCleared() private view returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol

```solidity
170:    function startedInSameEra(uint256 proposalId) private view returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol

```solidity
14:    function price(AggregatorV3Interface chainlinkFeed, uint48 timeout)
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol

```solidity
180:    function markStatus(CollateralStatus status_) internal {

199:    function alreadyDefaulted() internal view returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol

```solidity
41:    bytes32 internal constant EIP712_DOMAIN =

// consider renaming to `nonce` instead of `_nonce`
66:    mapping(address => uint256) public _nonces;

// consider using upper-case variable names for constant and immutables 
60:    ILendingPool public override LENDING_POOL;
61:    IAaveIncentivesController public override INCENTIVES_CONTROLLER;
62:    IERC20 public override ATOKEN;
63:    IERC20 public override ASSET;
64:    IERC20 public override REWARD_TOKEN;
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol

```solidity
8:    uint256 internal constant RAY = 1e27;

9:    uint256 internal constant WAD_RAY_RATIO = 1e9;

11:    function rayMulNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {

19:    function rayDivNoRounding(uint256 a, uint256 b) internal pure returns (uint256) {

25:    function rayToWadNoRounding(uint256 a) internal pure returns (uint256) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol

```solidity
24:    IBroker private broker;

111:    function tryTrade(TradeRequest memory req) internal nonReentrant {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol

```solidity
180:    function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {

188:    function maxTradeSize(IAsset asset, uint192 price) private view returns (uint192) {

38:    function prepareTradeSell(TradeInfo memory trade, TradingRules memory rules)

103:    function prepareTradeToCoverDeficit(TradeInfo memory trade, TradingRules memory rules)

140:    function isEnoughToSell(

154:    function safeMulDivCeil(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol

```solidity
152:    function basketRange(

226:    function totalAssetValue(

319:    function nextTradePair(

428:    function collateralShortfall(ComponentCache memory components, uint192 basketsTop)

469:    function isBetterSurplus(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol

```solidity
49:    function beginEra() internal override {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol

```solidity
48:    IAssetRegistry private assetRegistry;
49:    IBackingManager private backingManager;
50:    IBasketHandler private basketHandler;
51:    IERC20 private rsr;
60:    mapping(uint256 => mapping(address => uint256)) private stakes; // Stakes per account {qStRSR}
65:    uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}
87:    uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}

56:    uint256 internal era;
61:    uint256 internal totalStakes; // Total of all stakes {qStRSR}
62:    uint256 internal stakeRSR; // Amount of RSR backing all stakes {qRSR}
73:    uint256 internal draftEra;
83:    uint256 internal totalDrafts; // Total of all drafts {qDrafts}
84:    uint256 internal draftRSR; // Amount of RSR backing all drafts {qRSR}
150:    uint256 internal rsrRewardsAtLastPayout;
543:    function pushDraft(address account, uint256 rsrAmount)
574:    function beginEra() internal virtual {
586:    function beginDraftEra() internal virtual {
596:    function rsrRewards() internal view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol

```solidity
20:    IAssetRegistry private assetRegistry;
21:    IDistributor private distributor;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol

```solidity
59:    IAssetRegistry private assetRegistry;
60:    IBasketHandler private basketHandler;
61:    IBackingManager private backingManager;
62:    IFurnace private furnace;
72:    uint192 private allVestAt; // D18{fractional block number}
76:    uint192 private lastIssRate; // D18{rTok/block}
77:    uint256 private lastIssRateBlock; // {block number}
98:    RedemptionBatteryLib.Battery private battery;
102:    mapping(IERC20 => uint256) private liabilities;
344:    function whenFinished(uint256 amtRToken) private returns (uint192 finished) {
655:    function refundSpan(
737:    function vestUpTo(address account, uint256 endId) private {
802:    function requireValidBUExchangeRate() private view {
838:    function requireNotPausedOrFrozen() private notPausedOrFrozen {}
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol

```solidity
23:    IRToken private rToken;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol

```solidity
17:    EnumerableSet.AddressSet internal destinations;

36:    IERC20 private rsr;

37:    IERC20 private rToken;

38:    address private furnace;

39:    address private stRSR;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol

```solidity
26:    IBackingManager private backingManager;

27:    IRevenueTrader private rsrTrader;

28:    IRevenueTrader private rTokenTrader;

44:    mapping(address => bool) private trades;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol

```solidity
120:    IAssetRegistry private assetRegistry;

121:    IBackingManager private backingManager;

122:    IERC20 private rsr;

123:    IRToken private rToken;

124:    IStRSR private stRSR;

128:    BasketConfig private config;

132:    Basket private basket;

139:    bool private disabled;

665:    function goodCollateral(bytes32 targetName, IERC20 erc20) private view returns (bool) {

68:    function empty(Basket storage self) internal {

75:    function setFrom(Basket storage self, Basket storage other) internal {

87:    function add(

356:    function quantityMulPrice(uint192 qty, uint192 p) internal pure returns (uint192) {

650:    function requireValidCollArray(IERC20[] calldata erc20s) internal view {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol

```solidity
25:    IAssetRegistry private assetRegistry;

26:    IBasketHandler private basketHandler;

27:    IDistributor private distributor;

28:    IRToken private rToken;

29:    IERC20 private rsr;

30:    IStRSR private stRSR;

31:    IRevenueTrader private rsrTrader;

32:    IRevenueTrader private rTokenTrader;

154:    function handoutExcessAssets(IERC20[] calldata erc20s) private {

248:    function compromiseBasketsNeeded() private {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol

```solidity
16:    IBasketHandler private basketHandler;

22:    mapping(IERC20 => IAsset) private assets;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol

```solidity
16:    IBasketHandler private basketHandler;

22:    mapping(IERC20 => IAsset) private assets;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/mixins/TradingLib.sol

```solidity
// private and internal `functions` should preppend with `underline`
42:    function prepareTradeSell(TradeInfo memory trade, TradingRules memory rules)
202:    function basketRange(
543:    function prepareTradeToCoverDeficit(TradeInfo memory trade, TradingRules memory rules)
580:    function isEnoughToSell(
594:    function safeMulDivCeil(

// private and internal `functions` should preppend with `underline`
276:    function totalAssetValue(
365:    function nextTradePair(
466:    function collateralShortfall(
497:    function isBetterSurplus(
617:    function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {
625:    function maxTradeSize(IAsset asset, uint192 price) private view returns (uint192) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol

```solidity
// private and internal `functions` should preppend with `underline`
33:    function discharge(
53:    function currentCharge(Battery storage battery, uint256 supply)
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol

```solidity
// private and internal `functions` should preppend with `underline`
182:    function toUint(uint192 x) internal pure returns (uint136) {
189:    function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {
197:    function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {
205:    function shiftl(
222:    function plus(uint192 x, uint192 y) internal pure returns (uint192) {
229:    function plusu(uint192 x, uint256 y) internal pure returns (uint192) {
236:    function minus(uint192 x, uint192 y) internal pure returns (uint192) {
243:    function minusu(uint192 x, uint256 y) internal pure returns (uint192) {
251:    function mul(uint192 x, uint192 y) internal pure returns (uint192) {
258:    function mul(
269:    function mulu(uint192 x, uint256 y) internal pure returns (uint192) {
276:    function div(uint192 x, uint192 y) internal pure returns (uint192) {
283:    function div(
295:    function divu(uint192 x, uint256 y) internal pure returns (uint192) {
302:    function divu(
316:    function powu(uint192 x_, uint48 y) internal pure returns (uint192) {
332:    function lt(uint192 x, uint192 y) internal pure returns (bool) {
336:    function lte(uint192 x, uint192 y) internal pure returns (bool) {
340:    function gt(uint192 x, uint192 y) internal pure returns (bool) {
344:    function gte(uint192 x, uint192 y) internal pure returns (bool) {
348:    function eq(uint192 x, uint192 y) internal pure returns (bool) {
352:    function neq(uint192 x, uint192 y) internal pure returns (bool) {
359:    function near(
375:    function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {
382:    function shiftl_toUint(
401:    function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {
408:    function mulu_toUint(
419:    function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {
426:    function mul_toUint(
438:    function muluDivu(
450:    function muluDivu(
463:    function mulDiv(
475:    function mulDiv(
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol

```solidity
// private and internal `functions` should preppend with `underline`
66:    function worseThan(CollateralStatus a, CollateralStatus b) internal pure returns (bool) {
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/FacadeAct.sol

```solidity
// private and internal `functions` should preppend with `underline`
365:    function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {
```

---

### Version [5]

- Pragma versions should be standardized and avoid floating pragma `( ^ )`.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol

```solidity
2:  pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol

```solidity
// this version diverges from the rest of the repo
16: pragma solidity >=0.4.22 <0.6;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol

```solidity
// this version diverges from the rest of the repo
2:  pragma solidity ^0.8.0;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol

```solidity
// this version diverges from the rest of the repo
2:  pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol

```solidity
// this version diverges from the rest of the repo
2:  pragma solidity ^0.8.0;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol

```solidity
// this version diverges from the rest of the repo
2:  pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol

```solidity
// this version diverges from the rest of the repo
2:  pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol

```solidity
// this version diverges from the rest of the repo
3:  pragma solidity >=0.6.0 <0.8.0;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol

```solidity
// this version diverges from the rest of the repo
2:  pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol

```solidity
// this version diverges from the rest of the repo
2: pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol

```solidity
// this version diverges from the rest of the repo
2:  pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol

```solidity
// this version diverges from the rest of the repo
2:  pragma solidity 0.6.12;
```
 
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol

```solidity
// this version diverges from the rest of the repo
3:  pragma solidity ^0.6.0;
```


https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/StringCallerMock.sol

```solidity
// this version diverges from the rest of the repo
2: pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol

```solidity
// this version diverges from the rest of the repo
2: pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol

```solidity
// this version diverges from the rest of the repo
2: pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol

```solidity
// this version diverges from the rest of the repo
3:  pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol

```solidity
// this version diverges from the rest of the repo
3: pragma solidity ^0.8.9;
```

---

### Observations [6]

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ComptrollerMock.sol

```solidity
// empty constructor can be omitted
12:    constructor() {}
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol

```solidity
// this code works the same without the parenthesis encapsulating (bytes4(keccak256("absentDecimalsFn()"))
// ex: bytes memory data = abi.encodePacked(bytes4(keccak256("absentDecimalsFn()")));
32:        bytes memory data = abi.encodePacked((bytes4(keccak256("absentDecimalsFn()"))));
```

---

### Typo [7]

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol

```solidity
13:    /// @param version A semver version string

14:    /// @param makeLatest True iff this deployment should be promoted to be the latest deployment
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol

```solidity
86:    /// VERY IMPORTANT: In any valid `implemntation`, status() MUST become DISABLED in refresh() if
```