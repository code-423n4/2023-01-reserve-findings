# Report
## Non-Critical Issues ##

### [N-1]: Function defines a named return variable but then it uses return statements
**Context:**

1. ```return true;``` [L176](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L176) 
1. ```return _price(false);``` [L317](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L317) 
1. ```return _price(true);``` [L326](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L326) 
1. ```return (issueQueues[account].left, issueQueues[account].right);``` [L635](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L635) 
1. ```return (false, req);``` [L50](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L50) 
1. ```return (true, req);``` [L72](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L72) 
1. ```return super.propose(targets, values, calldatas, description);``` [L92](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L92) 
1. ```return (``` [L105](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L105) 
1. ```return``` [L220](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L220) 
1. ```return``` [L239](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L239) 
1. ```return auction.encodedClearingOrder;``` [L111](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L111) 
1. ```return auction.encodedClearingOrder;``` [L123](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L123) 
1. ```return super.price();``` [L52](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol#L52) 

**Recommendation:**

Choose named return variable or return statement. It is unnecessary to use both.

### [N-2]: Use of immutable instead of constant keccak expression
**Context:**

1. ```bytes32 private constant _PERMIT_TYPEHASH =``` [L126](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L126) 
1. ```bytes32 private constant _DELEGATE_TYPEHASH =``` [L27](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L27) 
1. ```bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));``` [L33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L33) 
1. ```bytes32 public constant METADEPOSIT_TYPEHASH =``` [L49](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L49) 
1. ```bytes32 public constant METAWITHDRAWAL_TYPEHASH =``` [L53](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L53) 

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/contracts.html#constant-and-immutable-state-variables) for a constant variable, the expression assigned to it is copied to all the places where it is accessed and also re-evaluated each time. It is recommended to use immutable instead. 

### [N-3]: Scientific notation
**Context:**

1. ```require(share.rsrDist <= 10000, "RSR distribution too high");``` [L165](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165) 
1. ```require(share.rTokenDist <= 10000, "RToken distribution too high");``` [L166](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L166) 

**Description:**

Scientific notation should be used for better code readability.

### [N-4]: No same value input check
**Context:**

1. ```tradingDelay = val;``` [L259](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L259) 
1. ```backingBuffer = val;``` [L266](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L266) 
1. ```auctionLength = newAuctionLength;``` [L139](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L139) 
1. ```period = period_;``` [L91](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L91) 
1. ```ratio = ratio_;``` [L100](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L100) 
1. ```basketsNeeded = basketsNeeded_;``` [L583](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L583) 
1. ```issuanceRate = val;``` [L592](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L592) 
1. ```battery.scalingRedemptionRate = val;``` [L605](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L605) 
1. ```name = name_;``` [L804](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L804) 
1. ```symbol = symbol_;``` [L808](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L808) 
1. ```shortFreeze = shortFreeze_;``` [L183](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L183) 
1. ```longFreeze = longFreeze_;``` [L190](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L190) 

**Recommendation:**

Example how to fix ```require(_newOwner != owner, " Same address");```

### [N-5]: Wrong order of functions
**Context:**

1. ```function register(IAsset asset) external governance returns (bool) {``` [L61](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L61) (external function can not go after public function)
1. ```function setTradingDelay(uint48 val) public governance {``` [L256](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256) (public function can not go after private function)
1. ```function setDisabled(bool disabled_) external governance {``` [L143](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L143) (external function can not go after public function)
1. ```struct Transfer {``` [L67](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L67) (struct definition can not go after external function)
1. ```uint256[47] private __gap;``` [L108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L108) (state variable can not go after public function)
1. ```function poke() external {``` [L43](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L43) (external function can not go after public function)
1. ```function currentEra() external view returns (uint256) {``` [L55](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L55) (external function can not go after internal )
1. ```uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;``` [L310](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L310) (state variable declaration can not go after internal function)
1. ```struct Pending {``` [L43](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeRead.sol#L43) (struct definition can not go after external function)
1. ```event StRSRSet(IStRSR indexed oldVal, IStRSR indexed newVal);``` [L108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L108) (event definition can not go after external function)
1. ```event MainInitialized();``` [L154](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L154) (event definition can not go after external function)
1. ```function grantRole(bytes32 role, address account)``` [L89](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L89) (public function can not go after internal function)
1. ```IRToken public rToken;``` [L34](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L34) (state variable declaration can not go after internal function)
1. ```modifier notPausedOrFrozen() {``` [L41](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L41) (modifier definition can not go after internal function)
1. ```struct BasketRange {``` [L116](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L116) (struct definition can not go after external function)
1. ```function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {``` [L66](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L66) (external function can not go after internal function)
1. ```modifier nonReentrant() {``` [L49](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L49) (modifier definition can not go after constructor)
1. ```function lotPrice() external view virtual returns (uint192 lotLow, uint192 lotHigh) {``` [L126](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L126) (external function can not go after public function)
1. ```function claimRewards() external virtual override {``` [L52](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L52) (external function can not go after public function)
1. ```function claimRewards() external virtual override {``` [L55](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L55) (external function can not go after public function)
1. ```function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {``` [L87](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L87) (external function can not go after public function)
1. ```function rate() external view returns (uint256) {``` [L54](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol#L54) (external function can not go after public function)
1. ```function exchangeRateCurrent() external returns (uint256) {``` [L27](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/CTokenMock.sol#L27) (external function can not go after public function)
1. ```function getRoundData(uint80 _roundId)``` [L71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L71) (external function can not go after public function)
1. ```function setSimplyRevert(bool on) external {``` [L27](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidFiatCollateral.sol#L27) (external function can not go after public function)

**Description:**

According to [official solidity documentation](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions) functions should be grouped according to their visibility and ordered:

+ constructor

+ receive function (if exists)

+ fallback function (if exists)

+ external

+ public

+ internal

+ private

Within a grouping, place the view and pure functions last.

**Recommendation:**

Put the functions in the correct order according to the documentation.

### [N-6]: Typos
**Context:**

1. ```/// Mointain the overall backing policy; handout assets otherwise``` [L89](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L89) (Change **Mointain** to **Maintain**)
1. ```// (trades[addr] == true) iff this contract has created an ITrade clone at addr``` [L47](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L47) (Change **iff** to **if**)
1. ```//   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay leriod)``` [L66](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L66) (Change **leriod** to **period**)
1. ```//     queue.right == left  iff  there are no more pending issuances in this queue``` [L106](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L106) (Change **iff** to **if**)
1. ```// issuances, and so any particular issuance is actually the _difference_ between two adjaacent``` [L109](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L109) (Change **adjaacent** to **adjacent**)
1. ```// The way to keep an IssueQueue striaght in your head is to think of each TotalIssue item as a``` [L112](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L112) (Change **striaght** to **straight**)
1. ```// We define a (partial) ordering on IssueItems: item1 < item2 iff the following all hold:``` [L124](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L124) (Change **iff** to **if**)
1. ```*   If this happens, users balances are zereod out and StRSR is re-issued at a 1:1 exchange rate``` [L25](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L25) (Change **zereod** to **zeroed**)
1. ```//   r'.queue is r.queue with a new entry appeneded for (totalDrafts' - totalDraft) drafts``` [L541](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L541) (Change **appeneded** to **appended**)
1. ```/// Overriden in StRSRVotes to handle rebases``` [L570](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L570) (Change **Overriden** to **Overridden**)
1. ```// _delegates[account] is the address of the delegate that `accountt` has specified``` [L30](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L30) (Change **accountt** to **account**)
1. ```//   Every function should revert iff its result is out of bounds.``` [L21](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L21) (Change **iff** to **if**)
1. ```bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));``` [L33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L33) (Change **UIntOutofBoundsHash** to **UIntOutOfBoundsHash**)
1. ```/// @return backing The worst-case collaterazation % the protocol will have after done trading``` [L100](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeRead.sol#L100) (Change **collaterazation** to **collateralization**???)
1. ```* @notice A UX-friendly layer for interactin with the protocol``` [L68](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeWrite.sol#L68) (Change **interactin** to **interaction**)
1. ```/// That is, id was cancelled iff firstId <= id < endId``` [L47](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L47) (Change **iff** to **if**)
1. ```/// @param redeemer The address of the account redeeeming RTokens``` [L74](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L74) (Change **redeeeming** to **redeeming**)
1. ```/// Set the fraction of the RToken supply that can be reedemed at once``` [L178](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L178) (Change **reedemed** to **redeemed**)
1. ```/// Emitted whenever RSR are paids out``` [L71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L71) (Change **paids** to **paid**)
1. ```* Typically freezing thaws on its own in a predetemined number of blocks.``` [L22](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L22) (Change **predetemined** to **predetermined**)
1. ```// trades[sell] != 0 iff trade[sell] has been opened and not yet settled``` [L37](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L37) (Change **iff** to **if**)
1. ```* @return The amound of pending rewards in RAY``` [L524](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L524) (Change **amound** to **amount**)
1. ```function setFeeParameters(uint256 newFeeNumerator, address newfeeReceiverAddress)``` [L127](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L127) (Change **newfeeReceiverAddress** to **newFeeReceiverAddress**)
1. ```// @dev: function to intiate a new auction``` [L137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L137) (Change intiate** to **initiate**)
1. ```"limit price not better than mimimal offer"``` [L281](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L281) (Change **mimimal** to **minimal**)
1. ```/// A Gnosis Mock that attemts to reenter on initiateAuction``` [L10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol#L10) (Change **attemts** to **attempts**)
1. ```function openTrade(TradeRequest memory req) external view notPausedOrFrozen returns (ITrade) {``` [L38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L38) (external function can not go after public function)
1. ```bytes memory returndata = address(token).functionCall(``` [L198](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L198) (Change **returndata** to **returnData**)
1. ```(bool success, bytes memory returndata) = target.call{ value: value }(data);``` [L797](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L797) (Change **returndata** to **returnData**)
1. ```(bool success, bytes memory returndata) = target.staticcall(data);``` [L829](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L829) (Change **returndata** to **returnData**)
1. ```bytes memory returndata,``` [L835](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L835) (Change **returndata** to **returnData**)
1. ```// from this trade's acution will all eventually go to origin.``` [L47](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L47) (Change **acution** to **auction**)
1. ```//   transfer all balancess of `buy` and `sell` at this address to `origin```` [L166](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L166) (Change **balancess** to **balances**)

### [N-7]: Line is too long
**Context:**

1. ```* @dev Claims reward for an user on behalf, on all the assets of the lending pool, accumulating the pending rewards. The caller must``` [L90](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L90) 
1. ```"Withdraw(address owner,address recipient,uint256 staticAmount,uint256 dynamicAmount,bool toUnderlying,uint256 nonce,uint256 deadline)"``` [L55](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L55) 
1. ```* @notice Compute the pending in RAY (rounded down). Pending is the amount to add (not yet unclaimed) rewards in RAY (rounded down).``` [L520](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L520) 
1. ```* Logic was copied and modified from here: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/SafeCast.sol``` [L653](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L653) 

**Description:**

Maximum suggested line length is [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length).
