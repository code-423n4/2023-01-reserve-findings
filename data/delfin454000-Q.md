## Summary	
### Low risk findings

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Use `require` rather than `assert` where appropriate|11|
|| Total|11|

### Non-critical findings

| Issue | Description | Instances |
| -- | ----------- | -------- |
|1| Named return variable is not used|5|
|2| Constant value definitions including call to `keccak256` should use `immutable`|8|
|3| `nonReentrant` modifier should appear before other modifiers|1|
|4| Unused override function arguments|2|
|5| Open items should be addressed|3|
|6| Event is missing `indexed` fields|10|
|7| Remove references to `pragma experimental ABIEncoderV2`|4|
|8| `pragma solidity` version should be upgraded to latest version before finalization|8 + multiple|
|9| Consider removing `internal` functions that are never used|9|
|10| Long single-line comments|2|
|11| Typos|31|
|12| Update sensitive terms|3|
|| Total|86 + multiple|


### Low risk findings

| No. | Explanation + cases | 
|--| ----------- | 
|1|**Use `require` rather than `assert` where appropriate**|
|  |On failure, the `assert` function causes a `Panic` error and, unlike `require`, does not generate an error string. According to Solidity v0.8.17, "properly functioning code should never create a Panic." Therefore, an `assert` should be used only if, based on the relevant code, it is not expected to throw an exception.|

Below is an example of a valid use of `assert` in Reserve Protocol:

[GnosisTrade.sol: L176-183](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/trading/GnosisTrade.sol#L176-L183)
```solidity
        // Optionally process settlement of the auction in Gnosis
        if (!isAuctionCleared()) {
            // By design, we don't rely on this return value at all, just the
            // "cleared" state of the auction, and the token balances this contract owns.
            // slither-disable-next-line unused-return
            gnosis.settleAuction(auctionId);
            assert(isAuctionCleared());
        }
```
The following `assert` functions appear not to meet the criteria and should be replaced by `require` functions:

[BackingManager.sol: L249](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BackingManager.sol#L249)

[BasketHandler.sol: L556](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L556)

[RecollateralizationLib.sol: L110](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L110)

[RewardableLib.sol: L78](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RewardableLib.sol#L78)

[RewardableLib.sol: L102](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RewardableLib.sol#L102)

[TradeLib.sol: L44](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/TradeLib.sol#L44)

[TradeLib.sol: L108-113](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/TradeLib.sol#L108-L113)

[TradeLib.sol: L170](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/TradeLib.sol#L170)

[Trading.sol: L114](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/Trading.sol#L114)

[StaticATokenLM.sol: L345](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L345)

[GnosisTrade.sol: L98](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/trading/GnosisTrade.sol#L98)
___
___


### Non-critical findings

| No. | Description |
|--| ----------- | 
|1|Named return variable is not used|

___
[TradeLib.sol: L38-41](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/TradeLib.sol#L38-L41)
```solidity
    function prepareTradeSell(TradeInfo memory trade, TradingRules memory rules)
        internal
        view
        returns (bool notDust, TradeRequest memory req)
  }
```
Here `notDust` is never used, which is especially odd since it is discussed fairly extensively in the comments above the `function.`

Similarly for the function below, in which `notDust` is also named:

[TradeLib.sol: L103-106](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/TradeLib.sol#L103-L106)
___
[IFacadeAct.sol: L18](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeAct.sol#L18)
```solidity
    function getActCalldata(RTokenP1 rToken) external returns (address to, bytes memory calldata_);
```
`calldata_` is never used
___
[IGnosis.sol: L45](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol#L45)
```solidity
    function settleAuction(uint256 auctionId) external returns (bytes32 encodedOrder);
```
`encodedOrder` is never used (it is used in a mock file)
___
[IRToken.sol: L119](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L119)
```solidity
    function issue(address recipient, uint256 amount) external returns (uint256 mintedAmount);
```
`mintedAmount` is not used (it is used in the out of scope **contracts/p0/RToken.sol**)
___


| No. | Description |
|--| ----------- | 
|2|Constant value definitions including call to `keccak256` should use `immutable`|
|  |Constant value definitions such as a call to `keccak256` should use `immutable` instead of `constant`|

___
[StRSR.sol: L126-129](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L126-L129)
```solidity
    bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );
```
___
[StRSRVotes.sol: L27-28](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSRVotes.sol#L27-L28)
```solidity
    bytes32 private constant _DELEGATE_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
```
___
[Fixed.sol: L33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L33)
```solidity
bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
```
___
[StaticATokenLM.sol: L41-44](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L41-L44)
```solidity
    bytes32 internal constant EIP712_DOMAIN =
        keccak256(
            "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
        );
```
___
[StaticATokenLM.sol: L45-48](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L45-L48)
```solidity
    bytes32 public constant PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );
```
___
[StaticATokenLM.sol: L49-52](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L49-L52)
```solidity
    bytes32 public constant METADEPOSIT_TYPEHASH =
        keccak256(
            "Deposit(address depositor,address recipient,uint256 value,uint16 referralCode,bool fromUnderlying,uint256 nonce,uint256 deadline)"
        );
```
___
[StaticATokenLM.sol: L53-56](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L53-L56)
```solidity
    bytes32 public constant METAWITHDRAWAL_TYPEHASH =
        keccak256(
            "Withdraw(address owner,address recipient,uint256 staticAmount,uint256 dynamicAmount,bool toUnderlying,uint256 nonce,uint256 deadline)"
        );
```
___
[ERC20PermitUpgradeable.sol: L39-42](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/vendor/ERC20PermitUpgradeable.sol#L39-L42)
```solidity
    bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );
```
___
| No. | Description |
|--| ----------- | 
|3|`nonReentrant` modifier should appear before other modifiers|
|  |Adopting this rule protects against the possibility of reentrancy in other modifiers|	

___
[Trading.sol: L66](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/Trading.sol#L66)
```solidity
    function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {
```
___


| No. | Description |
|--| ----------- | 
|4|Unused override function arguments|
|  |If an override function argument is never used, the variable name should be removed or else commented out to avoid compiler warnings. Below, `newImplementation` is unused.|

___
[Main.sol: L64](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Main.sol#L64)
```solidity
    function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
```
Similarly for the following function:

[Component.sol: L57](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/Component.sol#L57)
___


| No. | Description |
|--| ----------- | 
|5|Open items should be addressed|
|  |Open items should be resolved before deployment|	

___
[Broker.sol: L96](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Broker.sol#L96)
```solidity
        // In the future we'll have more sophisticated choice logic here, probably by trade size
```
___
[Main.sol: L67-68](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Main.sol#L67-L68)
```solidity
     * @dev This empty reserved space is put in place to allow future versions to add new
     * variables without shifting down storage in the inheritance chain.
```
___
[IFacadeAct.sol: L16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeAct.sol#L16)
```solidity
    /// @dev Don't actually execute this!
```
___


| No. | Description |
|--| ----------- | 
|6|Event is missing `indexed` fields|
|  |Each `event` should use three `indexed` fields if there are three or more fields; if fewer, all should be indexed, assuming gas cost not too expensive|

___
[IDistributor.sol: L28](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L28)
```solidity
    event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);
```
___
[IRToken.sol: L78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L78)
```solidity
    event Redemption(address indexed redeemer, uint256 indexed amount, uint192 baskets);
```
___
[IRToken.sol: L83](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L83)
```solidity
    event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);
```
___
[IRToken.sol: L87](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L87)
```solidity
    event Melted(uint256 amount);
```
___
[IAToken.sol: L15](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAToken.sol#L15)
```solidity
    event Mint(address indexed from, uint256 value, uint256 index);
```
___
[IAToken.sol: L37](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAToken.sol#L37)
```solidity
    event Burn(address indexed from, address indexed target, uint256 value, uint256 index);
```
___
[IAToken.sol: L46](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAToken.sol#L46)
```solidity
    event BalanceTransfer(address indexed from, address indexed to, uint256 value, uint256 index);
```
___
[IAaveIncentivesController.sol: L6](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAaveIncentivesController.sol#L6)
```solidity
    event RewardsAccrued(address indexed user, uint256 amount);
```
___
[IAaveIncentivesController.sol: L8](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAaveIncentivesController.sol#L8)
```solidity
    event RewardsClaimed(address indexed user, address indexed to, uint256 amount);
```
___
[IBasketHandler.sol: L28](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L28)
```solidity
    event BasketSet(uint256 indexed nonce, IERC20[] erc20s, uint192[] refAmts, bool disabled);
```
In this case, the only change needed is to convert `bool disabled` to `bool indexed disabled` since `IERC20[]` and `uint192[]` are complex data types 
___


| No. | Description |
|--| ----------- | 
|7|Remove references to `pragma experimental ABIEncoderV2`|
|  |Remove references to `pragma experimental ABIEncoderV2`, which has been depreciated. ABI coder v2 is activated by default by the compiler as of Solidity v0.8.0.|

___
[IAaveIncentivesController.sol: L3](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAaveIncentivesController.sol#L3)
```solidity
pragma experimental ABIEncoderV2;
```
___

Similarly for the following:

[IStaticATokenLM.sol: L3](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IStaticATokenLM.sol#L3)

[RayMathNoRounding.sol: L3](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/RayMathNoRounding.sol#L3)

[StaticATokenLM.sol: L3](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L3)
___


| No. | Description |
|--| ----------- | 
|8|`pragma solidity` version should be upgraded to latest version before finalization|
|  |The solidity version used in the contracts is `0.8.9` or earlier, compared to the latest version of `0.8.17`.|
|  |There are specific upgrades that have been applied since `0.8.9`. For example, a version of at least 0.8.10 is required to have external calls skip contract existence checks if the external call has a return value, 0.8.12 is necessary for `string.concat` to be used instead of `abi.encodePacked`, and 0.8.13 or later is needed to use `using for` with a list of free functions. In addition, only the latest version receives security fixes. |

The majority of the contracts use `pragma solidity 0.8.9`. Below are the contracts with even earlier versions. All need to be upgraded before implementation. 

**pragma solidity ^0.6.0**

[ERC20.sol: L3](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/ERC20.sol#L3)

**pragma solidity >=0.6.0 <0.8.0**

[ReentrancyGuard.sol: L3](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/ReentrancyGuard.sol#L3)

**pragma solidity 0.6.12**

[IAToken.sol: L2](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/IAToken.sol#L2)

[IAaveIncentivesController.sol: L2](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/IAaveIncentivesController.sol#L2)
 
[IStaticATokenLM.sol: L2](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/IStaticATokenLM.sol#L2)

[RayMathNoRounding.sol: L2](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/RayMathNoRounding.sol#L2)

[StaticATokenErrors.sol: L2](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenErrors.sol#L2)

[StaticATokenLM.sol: L2](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L2)
___

| No. | Description |
|--| ----------- | 
|9|Consider removing `internal` functions that are never used|
|  |The following `internal` functions are not used by any contract and should be reviewed:|

___
[Main.sol: L64](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Main.sol#L64)

[Component.sol: L57](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/Component.sol#L57)

[Fixed.sol: L116](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L116)

[Fixed.sol: L229](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L229)

[Fixed.sol: L243](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L243)

[Fixed.sol: L352](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L352)

[Fixed.sol: L359-363](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L359-L363)

[Governance.sol: L145-149](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/governance/Governance.sol#L145-L149)

[ERC20PermitUpgradeable.sol: L67](https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/vendor/ERC20PermitUpgradeable.sol#L67)
___


| No. | Description |
|--| ----------- | 
|10|Long single-line comments|
|  |Very long comments (over approximately 120 characters) can interfere with readability. Almost all of the very long comments in `contest name` wrap. However, below are two instances of extra-long comments whose readability could be improved by wrapping as shown.|
|  |Note that a small indentation is used in each continuation line (which flags for the reader that the comment is ongoing), along with a period at the close (to signal the end of the comment).|

___
[IAaveIncentivesController.sol: L90-91](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAaveIncentivesController.sol#L90-L91)
```solidity
     * @dev Claims reward for an user on behalf, on all the assets of the lending pool, accumulating the pending rewards. The caller must
     * be whitelisted via "allowClaimOnBehalf" function by the RewardsAdmin role manager
```
Suggestion:
```solidity
     * @dev Claims reward for an user on behalf, on all the assets of the lending pool, accumulating the pending rewards —
     *   the caller must be whitelisted via "allowClaimOnBehalf" function by the RewardsAdmin role manager.
```
___
[StaticATokenLM.sol: L520](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L520)
```solidity
     * @notice Compute the pending in RAY (rounded down). Pending is the amount to add (not yet unclaimed) rewards in RAY (rounded down).
```
Suggestion:
```solidity
     * @notice Compute the pending in RAY (rounded down). Pending is the amount
     *   to add (not yet unclaimed) rewards in RAY (rounded down).
```
___


| No. | Description |
|--| ----------- | 
|11|Typos|
___
[BackingManager.sol: L89](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BackingManager.sol#L89)
```solidity
    /// Mointain the overall backing policy; handout assets otherwise
```
Change `Mointain` to `Maintain`
___
[BasketHandler.sol: L321](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L321)
```solidity
    /// lowLow should be nonzero when the asset might be worth selling
```
Change `lowLow` to `lotLow` 
___
[BasketHandler.sol: L537](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L537)
```solidity
        // As such, they're each interepreted as a map from target name -> target weight
```
Change `interepreted` to `interpreted` 
___
The same typo (`permisionlessly`) occurs in both lines below:

[Furnace.sol: L10](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Furnace.sol#L10)

[IFurnace.sol: L9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L9)
```solidity
 * @notice A helper to melt RTokens slowly and permisionlessly.
```
Change `permisionlessly` to `permissionlessly` in both cases
___
[Furnace.sol: L66](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Furnace.sol#L66)
```solidity
    //   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay leriod)
```
Change `leriod` to `period` 
___
[RToken.sol: L109](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L109)
```solidity
    // issuances, and so any particular issuance is actually the _difference_ between two adjaacent
```
Change `adjaacent` to `adjacent` 
___
[RToken.sol: L112](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L112)
```solidity
    // The way to keep an IssueQueue striaght in your head is to think of each TotalIssue item as a
```
Change `striaght` to `straight` 
___
[StRSR.sol: L25](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L25)
```solidity
 *   If this happens, users balances are zereod out and StRSR is re-issued at a 1:1 exchange rate
```
Change `zereod` to `zeroed` 
___
[StRSR.sol: L541](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L541)
```solidity
    //   r'.queue is r.queue with a new entry appeneded for (totalDrafts' - totalDraft) drafts
```
Change `appeneded` to `appended` 
___
[StRSR.sol: L570](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L570)
```solidity
    /// Overriden in StRSRVotes to handle rebases
```
Change `Overriden` to `Overridden` 
___
The same typo (`that it's`) occurs in both lines below:

[RecollateralizationLib.sol: L306](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L306)

[RecollateralizationLib.sol: L311](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/mixins/RecollateralizationLib.sol#L311)
```solidity
    //   and sellAmount is the quantity of that token that it's in surplus (in qTok).
```
Change `that it's` to `that's` in both cases
___
[FacadeRead.sol: L201](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeRead.sol#L201)
```solidity
    /// @dev Indices are shared aross return values
```
Change `aross` to `across` 
___
[FacadeRead.sol: L256](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/facade/FacadeRead.sol#L256)
```solidity
        //      this functions will then panic when `uoaHeld.div(uoaNeeded)`
```
Change `functions` to `function` 
___
[IAsset.sol: L86](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L86)
```solidity
    /// VERY IMPORTANT: In any valid implemntation, status() MUST become DISABLED in refresh() if
```
Change `implemntation` to `implementation` 
___
The same typo (repeated word `the`) occurs in both lines below:

[IBackingManager.sol: L17](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L17)

[IAToken.sol: L22](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAToken.sol#L22)
```solidity
 *   vast majority of cases we expect the the O(n^2) function to be acceptable.
```
Change `the the` to `the` in both cases
___
[IFacadeRead.sol: L100](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeRead.sol#L100)
```solidity
    /// @return backing The worst-case collaterazation % the protocol will have after done trading
```
Change `collaterazation` to `collateralization` 
___
[IFacadeWrite.sol: L68](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeWrite.sol#L68)
```solidity
 * @notice A UX-friendly layer for interactin with the protocol
```
Change `interactin` to `interacting` 
___
[IRToken.sol: L74](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L74)
```solidity
    /// @param redeemer The address of the account redeeeming RTokens
```
Change `redeeeming` to `redeeming` 
___
[IRToken.sol: L178](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L178)
```solidity
    /// Set the fraction of the RToken supply that can be reedemed at once
```
Change `reedemed` to `redeemed` 
___
[IStRSR.sol: L71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L71)
```solidity
    /// Emitted whenever RSR are paids out
```
Change `paids` to `paid` 
___
[IStRSR.sol: L76](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L76)
```solidity
    /// Emitted if all the RSR in the unstakin pool is seized, and all ongoing unstaking is voided.
```
Change `unstakin` to `unstaking` 
___
[Auth.sol: L22](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L22)
```solidity
     * Typically freezing thaws on its own in a predetemined number of blocks.
```
Change `predetemined` to `predetermined` 
___
[StaticATokenLM.sol: L524](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/StaticATokenLM.sol#L524)
```solidity
     * @return The amound of pending rewards in RAY
```
Change `amound` to `amount` 
___
[FiatCollateral.sol: L21](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/assets/FiatCollateral.sol#L21)
```solidity
    uint48 delayUntilDefault; // {s} The number of seconds an oracle can mulfunction
```
Change `mulfunction` to `malfunction` 
___
[FiatCollateral.sol: L45](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/assets/FiatCollateral.sol#L45)
```solidity
    //                In this case, the asset may recover, reachiving _whenDefault == NEVER.
```
Change `reachiving` to `reaching` if that was what was intended
___
[GnosisTrade.sol: L47](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/trading/GnosisTrade.sol#L47)
```solidity
    // from this trade's acution will all eventually go to origin.
```
Change `acution` to `auction` 
___
[GnosisTrade.sol: L121](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/trading/GnosisTrade.sol#L121)
```solidity
        uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd
```
Change `require'd` to `required` 
___
[GnosisTrade.sol: L166](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/trading/GnosisTrade.sol#L166)
```solidity
    //   transfer all balancess of `buy` and `sell` at this address to `origin`
```
Change `balancess` to `balances` 
___


| No. | Description |
|--| ----------- | 
|12|Update sensitive terms|
|  |Terms incorporating "black," "white," "slave" or "master" are potentially problematic. Substituting more neutral terminology is becoming [common practice](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/)|

___
[IAaveIncentivesController.sol: L41](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAaveIncentivesController.sol#L41)
```solidity
     * @dev Returns the whitelisted claimer for a certain address (0x0 if not set)
```
Suggestion: Change `whitelisted` to `allowlisted` 
___
Similarly for the following instances of `whitelist` variants:

[IAaveIncentivesController.sol: L34](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAaveIncentivesController.sol#L34)

[IAaveIncentivesController.sol: L90-91](https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/plugins/aave/IAaveIncentivesController.sol#L90-L91)
___
___
