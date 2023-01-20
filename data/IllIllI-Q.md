## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Invariants not enforced | 1 | 
| [L&#x2011;02] | Wrong answer from `supportsInterface() | 1 | 
| [L&#x2011;03] | Inconsistent behavior when periods/ratios are changed | 1 | 
| [L&#x2011;04] | Incorrect initializer order | 1 | 
| [L&#x2011;05] | Inconsistent max approval behavior | 1 | 
| [L&#x2011;06] | Aave plugins don't work properly with Aave V3 | 1 | 
| [L&#x2011;07] | Not all parent contracts are upgradeable | 3 | 
| [L&#x2011;08] | Loss of precision | 3 | 
| [L&#x2011;09] | Signatures vulnerable to malleability attacks | 3 | 
| [L&#x2011;10] | Owner can renounce while system is paused | 1 | 
| [L&#x2011;11] | `require()` should be used instead of `assert()` | 20 | 
| [L&#x2011;12] | Draft imports may break in new minor versions | 3 | 

Total: 39 instances over 12 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | Empty comment | 1 | 
| [N&#x2011;02] | Consider adding a `setDistributions()` function | 1 | 
| [N&#x2011;03] | Import declarations should import specific identifiers, rather than the whole file | 278 | 
| [N&#x2011;04] | Unused file | 1 | 
| [N&#x2011;05] | The `nonReentrant` `modifier` should occur before all other modifiers | 1 | 
| [N&#x2011;06] | `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings | 2 | 
| [N&#x2011;07] | Non-assembly method available | 1 | 
| [N&#x2011;08] | External calls in an un-bounded `for-`loop may result in a DOS | 17 | 
| [N&#x2011;09] | `constant`s should be defined rather than using magic numbers | 24 | 
| [N&#x2011;10] | Numeric values having to do with time should use time units for readability | 8 | 
| [N&#x2011;11] | Missing event and or timelock for critical parameter change | 2 | 
| [N&#x2011;12] | Events that mark critical parameter changes should contain both the old and the new value | 2 | 
| [N&#x2011;13] | Use a more recent version of solidity | 2 | 
| [N&#x2011;14] | Use a more recent version of solidity | 26 | 
| [N&#x2011;15] | Use a more recent version of solidity | 1 | 
| [N&#x2011;16] | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 6 | 
| [N&#x2011;17] | Inconsistent spacing in comments | 3 | 
| [N&#x2011;18] | Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables | 5 | 
| [N&#x2011;19] | Non-library/interface files should use fixed compiler versions, not floating ones | 1 | 
| [N&#x2011;20] | Typos | 17 | 
| [N&#x2011;21] | File is missing NatSpec | 4 | 
| [N&#x2011;22] | NatSpec is incomplete | 164 | 
| [N&#x2011;23] | Not using the named return variables anywhere in the function is confusing | 13 | 
| [N&#x2011;24] | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 12 | 
| [N&#x2011;25] | Consider using `delete` rather than assigning zero to clear values | 5 | 
| [N&#x2011;26] | Avoid the use of sensitive terms | 3 | 
| [N&#x2011;27] | Contracts should have full test coverage | 1 | 
| [N&#x2011;28] | Function ordering does not follow the Solidity style guide | 42 | 
| [N&#x2011;29] | Contract does not follow the Solidity style guide's suggested layout ordering | 45 | 
| [N&#x2011;30] | Interfaces should be indicated with an `I` prefix in the contract name | 11 | 

Total: 699 instances over 30 issues





## Low Risk Issues

### [L&#x2011;01]  Invariants not enforced
The invariant below states that the function should `only return FIX_MAX for high if low is 0`, but this is not enforced by the function, only when it's called by `refresh()`, and not by `price()` or `lotPrice()`

*There is 1 instance of this issue:*

```solidity
File: /contracts/plugins/assets/Asset.sol

63       /// Can revert, used by other contract functions in order to catch errors
64       /// Should not return FIX_MAX for low
65       /// Should only return FIX_MAX for high if low is 0
66       /// @dev The third (unused) variable is only here for compatibility with Collateral
67       /// @param low {UoA/tok} The low price estimate
68       /// @param high {UoA/tok} The high price estimate
69       function tryPrice()
70           external
71           view
72           virtual
73           returns (
74               uint192 low,
75               uint192 high,
76               uint192
77           )
78       {
79           uint192 p = chainlinkFeed.price(oracleTimeout); // {UoA/tok}
80           uint192 delta = p.mul(oracleError);
81           return (p - delta, p + delta, 0);
82:      }

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L63-L82

### [L&#x2011;02]  Wrong answer from `supportsInterface()
Because of the order of the inherited contracts, `Governance.supportsInterface()`, because it calls `super`, will return only the right answer for `Governor`, and not for `GovernorTimelockControl`

*There is 1 instance of this issue:*

```solidity
File: /contracts/plugins/governance/Governance.sol

22   contract Governance is
23       Governor,
24       GovernorSettings,
25       GovernorCountingSimple,
26       GovernorVotes,
27       GovernorVotesQuorumFraction,
28       GovernorTimelockControl
29:  {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L22-L29

### [L&#x2011;03]  Inconsistent behavior when periods/ratios are changed
It is possible that multiple periods are pending when a request to change the period or ratio comes in. If this happens and the change is to a much larger period, then some of the pending melts that should have taken place are skipped until the next period. If a large period is made smaller, a bunch of smaller melts will immediately become available. If the ratio changes at around the same time as the period changes, governance may get unexpected melting behavior. It's safest to force a melt before either value changes, and then fully reset the melt timer to zero afterwards.

*There is 1 instance of this issue:*

```solidity
File: /contracts/p1/Furnace.sol

88       function setPeriod(uint48 period_) public governance {
89           require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
90           emit PeriodSet(period, period_);
91           period = period_;
92       }
93   
94       /// Ratio setting
95       /// @custom:governance
96       function setRatio(uint192 ratio_) public governance {
97           require(ratio_ <= MAX_RATIO, "invalid ratio");
98           // The ratio can safely be set to 0
99           emit RatioSet(ratio, ratio_);
100          ratio = ratio_;
101:     }

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L88-L101

### [L&#x2011;04]  Incorrect initializer order
Initializers should be executed in the order of most low-level to high level, in case one depends on another. In the case below, `__UUPSUpgradeable_init()` should be done first

*There is 1 instance of this issue:*

```solidity
File: /contracts/p1/Main.sol

33           __Auth_init(shortFreeze_, longFreeze_);
34           __ComponentRegistry_init(components);
35:          __UUPSUpgradeable_init();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L33-L35

### [L&#x2011;05]  Inconsistent max approval behavior
The stRSR token supports the setting of infinite approvals, but the plugins based on ERC20 do not. This inconsistent behavior within the same project will lead to confusion and bugs down the line

*There is 1 instance of this issue:*

```solidity
File: /contracts/plugins/aave/ERC20.sol

168:             _allowances[sender][_msgSender()].sub(

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L168

### [L&#x2011;06]  Aave plugins don't work properly with Aave V3
Aave V2 only supports one reward token, but V3 supports multiple. If this plugin is used with V3 tokens, there won't be any way to claim other reward tokens. There are no checks to ensure that the token provided is a V2 token

*There is 1 instance of this issue:*

```solidity
File: /contracts/plugins/assets/ATokenFiatCollateral.sol

53:          IERC20 stkAAVE = IStaticAToken(address(erc20)).REWARD_TOKEN();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L53

### [L&#x2011;07]  Not all parent contracts are upgradeable
Upgrades will break if the contracts in question have variables whose values are initialized in the constructor, or have variables where initial values are [set in field declarations](https://docs.openzeppelin.com/upgrades-plugins/1.x/writing-upgradeable#avoid-initial-values-in-field-declarations)

*There are 3 instances of this issue:*

```solidity
File: contracts/p1/mixins/Component.sol

/// @audit Versioned
14:   abstract contract ComponentP1 is

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L14

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit Multicall
/// @audit ComponentP1
16:   abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeable, ITrading {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L16

### [L&#x2011;08]  Loss of precision
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator

*There are 3 instances of this issue:*

```solidity
File: contracts/p1/BackingManager.sol

232:                  toRSR[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rsrTotal;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L232

```solidity
File: contracts/p1/Distributor.sol

99:               tokensPerShare = amount / totalShares;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L99

```solidity
File: contracts/p1/RToken.sol

475:          uint192 prorate = uint192((FIX_ONE_256 * amount) / supply);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L475

### [L&#x2011;09]  Signatures vulnerable to malleability attacks
`ecrecover()` accepts as valid, two versions of signatures, meaning an attacker can use the same signature twice. Consider adding checks for signature malleability, or using OpenZeppelin's `ECDSA` library to perform the extra checks necessary in order to prevent this attack.

*There are 3 instances of this issue:*

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

155:          require(owner == ecrecover(digest, v, r, s), StaticATokenErrors.INVALID_SIGNATURE);

193:              depositor == ecrecover(digest, sigParams.v, sigParams.r, sigParams.s),

234:              owner == ecrecover(digest, sigParams.v, sigParams.r, sigParams.s),

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L155

### [L&#x2011;10]  Owner can renounce while system is paused
The contract owner or single user with a role is not prevented from renouncing the role/ownership while the contract is paused, which would cause any user assets stored in the protocol, to be locked indefinitely

*There is 1 instance of this issue:*

```solidity
File: contracts/mixins/Auth.sol

165       function pause() external onlyRole(PAUSER) {
166           emit PausedSet(paused, true);
167           paused = true;
168:      }

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L165-L168

### [L&#x2011;11]  `require()` should be used instead of `assert()`
Prior to solidity version 0.8.0, hitting an assert consumes the **remainder of the transaction's available gas** rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

*There are 20 instances of this issue:*

```solidity
File: contracts/p1/BackingManager.sol

249:          assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249

```solidity
File: contracts/p1/BasketHandler.sol

556:              assert(targetIndex < targetsLength);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

110:          assert(doTrade);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110

```solidity
File: contracts/p1/mixins/RewardableLib.sol

78:                   assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);

102:              assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78

```solidity
File: contracts/p1/mixins/TradeLib.sol

44:           assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);

108           assert(
109               trade.sellPrice > 0 &&
110                   trade.sellPrice < FIX_MAX &&
111                   trade.buyPrice > 0 &&
112                   trade.buyPrice < FIX_MAX
113:          );

168:              assert(errorCode == 0x11 || errorCode == 0x12);

170:              assert(keccak256(reason) == UIntOutofBoundsHash);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44

```solidity
File: contracts/p1/mixins/Trading.sol

114:          assert(address(trades[sell]) == address(0));

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L114

```solidity
File: contracts/p1/StRSR.sol

696:          assert(totalStakes + amount < type(uint224).max);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

345:              assert(amt == amountToWithdraw);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L345

```solidity
File: contracts/plugins/assets/Asset.sol

98:                   assert(low == 0);

112:              assert(low <= high);

147:          assert(lotLow <= lotHigh);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L98

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

137:                  assert(low == 0);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L137

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

74:               assert(low <= high);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L74

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

63:           assert(status == TradeStatus.PENDING);

98:           assert(origin_ != address(0));

182:              assert(isAuctionCleared());

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L63

### [L&#x2011;12]  Draft imports may break in new minor versions
While OpenZeppelin draft contracts are safe to use and have been audited, their 'draft' status means that the EIPs they're based on are not finalized, and thus there may be breaking changes in even [minor releases](https://docs.openzeppelin.com/contracts/3.x/api/drafts). If a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to has breaking changes in the new version, you'll encounter unnecessary delays in porting and testing replacement contracts. Ensure that you have extensive test coverage of this area so that differences can be automatically detected, and have a plan in place for how you would fully test a new version of these contracts if they do indeed change unexpectedly. Consider creating a forked version of the file rather than importing it from the package, and manually patch your fork as changes are made.

*There are 3 instances of this issue:*

```solidity
File: contracts/interfaces/IRToken.sol

6:    import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L6

```solidity
File: contracts/interfaces/IStRSR.sol

6:    import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L6

```solidity
File: contracts/p1/StRSR.sol

7:    import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L7

## Non-critical Issues

### [N&#x2011;01]  Empty comment

*There is 1 instance of this issue:*

```solidity
File: /contracts/p1/mixins/Trading.sol

112:         /*  */

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L112

### [N&#x2011;02]  Consider adding a `setDistributions()` function
Currently in order to change the distribution, one must change each one separately, and the shares may be unevenly applied as compared to their original values, once later distributions are added. Consider adding a function that will clear all old distributions and set an array of new ones

*There is 1 instance of this issue:*

```solidity
File: /contracts/p1/Distributor.sol

61       function setDistribution(address dest, RevenueShare memory share) external governance {
62           _setDistribution(dest, share);
63           RevenueTotals memory revTotals = totals();
64           _ensureNonZeroDistribution(revTotals.rTokenTotal, revTotals.rsrTotal);
65:      }

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L61-L65

### [N&#x2011;03]  Import declarations should import specific identifiers, rather than the whole file
Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation

*There are 278 instances of this issue:*

```solidity
File: contracts/interfaces/IAssetRegistry.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "./IAsset.sol";

6:    import "./IComponent.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAssetRegistry.sol#L4

```solidity
File: contracts/interfaces/IAsset.sol

4:    import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

5:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

6:    import "../libraries/Fixed.sol";

7:    import "./IMain.sol";

8:    import "./IRewardable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L4

```solidity
File: contracts/interfaces/IBackingManager.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "./IComponent.sol";

6:    import "./ITrading.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L4

```solidity
File: contracts/interfaces/IBasketHandler.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "../libraries/Fixed.sol";

6:    import "./IAsset.sol";

7:    import "./IComponent.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L4

```solidity
File: contracts/interfaces/IBroker.sol

4:    import "./IAsset.sol";

5:    import "./IComponent.sol";

6:    import "./IGnosis.sol";

7:    import "./ITrade.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBroker.sol#L4

```solidity
File: contracts/interfaces/IComponent.sol

4:    import "./IMain.sol";

5:    import "./IVersioned.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IComponent.sol#L4

```solidity
File: contracts/interfaces/IDeployerRegistry.sol

4:    import "./IDeployer.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L4

```solidity
File: contracts/interfaces/IDeployer.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "./IAsset.sol";

6:    import "./IDistributor.sol";

7:    import "./IGnosis.sol";

8:    import "./IMain.sol";

9:    import "./IRToken.sol";

10:   import "./IStRSR.sol";

11:   import "./ITrade.sol";

12:   import "./IVersioned.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L4

```solidity
File: contracts/interfaces/IDistributor.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "./IComponent.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L4

```solidity
File: contracts/interfaces/IFurnace.sol

4:    import "../libraries/Fixed.sol";

5:    import "./IComponent.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L4

```solidity
File: contracts/interfaces/IGnosis.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol#L4

```solidity
File: contracts/interfaces/IMain.sol

4:    import "@openzeppelin/contracts-upgradeable/access/IAccessControlUpgradeable.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "./IAssetRegistry.sol";

7:    import "./IBasketHandler.sol";

8:    import "./IBackingManager.sol";

9:    import "./IBroker.sol";

10:   import "./IGnosis.sol";

11:   import "./IFurnace.sol";

12:   import "./IDistributor.sol";

13:   import "./IRToken.sol";

14:   import "./IRevenueTrader.sol";

15:   import "./IStRSR.sol";

16:   import "./ITrading.sol";

17:   import "./IVersioned.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L4

```solidity
File: contracts/interfaces/IRevenueTrader.sol

4:    import "./IComponent.sol";

5:    import "./ITrading.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRevenueTrader.sol#L4

```solidity
File: contracts/interfaces/IRewardable.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "./IComponent.sol";

6:    import "./IMain.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRewardable.sol#L4

```solidity
File: contracts/interfaces/IRToken.sol

4:    import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/IERC20MetadataUpgradeable.sol";

6:    import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

7:    import "../libraries/Fixed.sol";

8:    import "./IAsset.sol";

9:    import "./IComponent.sol";

10:   import "./IMain.sol";

11:   import "./IRewardable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L4

```solidity
File: contracts/interfaces/IStRSR.sol

4:    import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/IERC20MetadataUpgradeable.sol";

6:    import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

7:    import "../libraries/Fixed.sol";

8:    import "./IComponent.sol";

9:    import "./IMain.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L4

```solidity
File: contracts/interfaces/IStRSRVotes.sol

4:    import "@openzeppelin/contracts-upgradeable/governance/utils/IVotesUpgradeable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSRVotes.sol#L4

```solidity
File: contracts/interfaces/ITrade.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrade.sol#L4

```solidity
File: contracts/interfaces/ITrading.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "../libraries/Fixed.sol";

6:    import "./IAsset.sol";

7:    import "./IComponent.sol";

8:    import "./ITrade.sol";

9:    import "./IRewardable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L4

```solidity
File: contracts/libraries/Array.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L4

```solidity
File: contracts/libraries/Permit.sol

4:    import "@openzeppelin/contracts-upgradeable/utils/AddressUpgradeable.sol";

5:    import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L4

```solidity
File: contracts/libraries/RedemptionBattery.sol

4:    import "../interfaces/IRToken.sol";

5:    import "./Fixed.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L4

```solidity
File: contracts/mixins/Auth.sol

4:    import "@openzeppelin/contracts-upgradeable/access/AccessControlUpgradeable.sol";

5:    import "../interfaces/IMain.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L4

```solidity
File: contracts/mixins/ComponentRegistry.sol

4:    import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5:    import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7:    import "../interfaces/IMain.sol";

8:    import "./Auth.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L4

```solidity
File: contracts/mixins/Versioned.sol

4:    import "../interfaces/IVersioned.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Versioned.sol#L4

```solidity
File: contracts/p1/AssetRegistry.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

6:    import "../interfaces/IAssetRegistry.sol";

7:    import "../interfaces/IMain.sol";

8:    import "./mixins/Component.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L4

```solidity
File: contracts/p1/BackingManager.sol

4:    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "../interfaces/IAsset.sol";

7:    import "../interfaces/IBackingManager.sol";

8:    import "../interfaces/IMain.sol";

9:    import "../libraries/Array.sol";

10:   import "../libraries/Fixed.sol";

11:   import "./mixins/Trading.sol";

12:   import "./mixins/RecollateralizationLib.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L4

```solidity
File: contracts/p1/BasketHandler.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

8:    import "../interfaces/IAssetRegistry.sol";

9:    import "../interfaces/IBasketHandler.sol";

10:   import "../interfaces/IMain.sol";

11:   import "../libraries/Array.sol";

12:   import "../libraries/Fixed.sol";

13:   import "./mixins/Component.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L4

```solidity
File: contracts/p1/Broker.sol

4:    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

5:    import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

6:    import "@openzeppelin/contracts/proxy/Clones.sol";

7:    import "../interfaces/IBroker.sol";

8:    import "../interfaces/IMain.sol";

9:    import "../interfaces/ITrade.sol";

10:   import "../libraries/Fixed.sol";

11:   import "./mixins/Component.sol";

12:   import "../plugins/trading/GnosisTrade.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L4

```solidity
File: contracts/p1/Deployer.sol

4:    import "@openzeppelin/contracts/proxy/Clones.sol";

5:    import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";

6:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

7:    import "../interfaces/IAsset.sol";

8:    import "../interfaces/IAssetRegistry.sol";

9:    import "../interfaces/IBackingManager.sol";

10:   import "../interfaces/IBasketHandler.sol";

11:   import "../interfaces/IBroker.sol";

12:   import "../interfaces/IDeployer.sol";

13:   import "../interfaces/IDistributor.sol";

14:   import "../interfaces/IFurnace.sol";

15:   import "../interfaces/IRevenueTrader.sol";

16:   import "../interfaces/IRToken.sol";

17:   import "../interfaces/IStRSR.sol";

18:   import "../mixins/Versioned.sol";

19:   import "../plugins/assets/Asset.sol";

20:   import "../plugins/assets/RTokenAsset.sol";

21:   import "./Main.sol";

22:   import "../libraries/String.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L4

```solidity
File: contracts/p1/Distributor.sol

4:    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

7:    import "../interfaces/IDistributor.sol";

8:    import "../interfaces/IMain.sol";

9:    import "../libraries/Fixed.sol";

10:   import "./mixins/Component.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L4

```solidity
File: contracts/p1/Furnace.sol

4:    import "../libraries/Fixed.sol";

5:    import "../interfaces/IFurnace.sol";

6:    import "./mixins/Component.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L4

```solidity
File: contracts/p1/Main.sol

4:    import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5:    import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6:    import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";

7:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

8:    import "../interfaces/IMain.sol";

9:    import "../mixins/ComponentRegistry.sol";

10:   import "../mixins/Auth.sol";

11:   import "../mixins/Versioned.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L4

```solidity
File: contracts/p1/mixins/Component.sol

4:    import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";

5:    import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6:    import "@openzeppelin/contracts-upgradeable/utils/ContextUpgradeable.sol";

7:    import "../../interfaces/IComponent.sol";

8:    import "../../interfaces/IMain.sol";

9:    import "../../mixins/Versioned.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L4

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "../../interfaces/IAsset.sol";

6:    import "../../interfaces/IAssetRegistry.sol";

7:    import "../../interfaces/IBackingManager.sol";

8:    import "../../libraries/Fixed.sol";

9:    import "./TradeLib.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L4

```solidity
File: contracts/p1/mixins/RewardableLib.sol

4:    import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "@openzeppelin/contracts/utils/Address.sol";

7:    import "../../interfaces/IAssetRegistry.sol";

8:    import "../../interfaces/IBackingManager.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L4

```solidity
File: contracts/p1/mixins/TradeLib.sol

4:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

5:    import "../../interfaces/IAsset.sol";

6:    import "../../interfaces/IAssetRegistry.sol";

7:    import "../../interfaces/ITrading.sol";

8:    import "../../libraries/Fixed.sol";

9:    import "./RecollateralizationLib.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L4

```solidity
File: contracts/p1/mixins/Trading.sol

4:    import "@openzeppelin/contracts-upgradeable/security/ReentrancyGuardUpgradeable.sol";

5:    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

6:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7:    import "@openzeppelin/contracts/utils/Multicall.sol";

8:    import "../../interfaces/ITrade.sol";

9:    import "../../interfaces/ITrading.sol";

10:   import "../../libraries/Fixed.sol";

11:   import "./Component.sol";

12:   import "./RewardableLib.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L4

```solidity
File: contracts/p1/RevenueTrader.sol

4:    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

5:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:    import "../interfaces/IMain.sol";

7:    import "../interfaces/IAssetRegistry.sol";

8:    import "./mixins/Trading.sol";

9:    import "./mixins/TradeLib.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L4

```solidity
File: contracts/p1/RToken.sol

5:    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

6:    import "../interfaces/IMain.sol";

7:    import "../interfaces/IRToken.sol";

8:    import "../libraries/Fixed.sol";

9:    import "../libraries/RedemptionBattery.sol";

10:   import "./mixins/Component.sol";

11:   import "./mixins/RewardableLib.sol";

12:   import "../vendor/ERC20PermitUpgradeable.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L5

```solidity
File: contracts/p1/StRSR.sol

4:    import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";

5:    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

6:    import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

7:    import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

8:    import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";

9:    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

11:   import "../interfaces/IStRSR.sol";

12:   import "../interfaces/IMain.sol";

13:   import "../libraries/Fixed.sol";

14:   import "../libraries/Permit.sol";

15:   import "./mixins/Component.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L4

```solidity
File: contracts/p1/StRSRVotes.sol

4:    import "@openzeppelin/contracts-upgradeable/utils/math/SafeCastUpgradeable.sol";

5:    import "@openzeppelin/contracts-upgradeable/utils/math/MathUpgradeable.sol";

6:    import "../interfaces/IStRSRVotes.sol";

7:    import "./StRSR.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L4

```solidity
File: contracts/plugins/aave/ERC20.sol

5:    import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/Context.sol";

6:    import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";

7:    import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/SafeMath.sol";

8:    import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/Address.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L5

```solidity
File: contracts/plugins/aave/IAToken.sol

4:    import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";

5:    import "@aave/protocol-v2/contracts/interfaces/IScaledBalanceToken.sol";

6:    import "./IAaveIncentivesController.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol#L4

```solidity
File: contracts/plugins/assets/Asset.sol

4:    import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

5:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

6:    import "../../interfaces/IAsset.sol";

7:    import "./OracleLib.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L4

```solidity
File: contracts/plugins/assets/ATokenFiatCollateral.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "../../libraries/Fixed.sol";

6:    import "./FiatCollateral.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L4

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "./FiatCollateral.sol";

6:    import "./ICToken.sol";

7:    import "../../libraries/Fixed.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L4

```solidity
File: contracts/plugins/assets/CTokenNonFiatCollateral.sol

4:    import "../../libraries/Fixed.sol";

5:    import "./CTokenFiatCollateral.sol";

6:    import "./ICToken.sol";

7:    import "./OracleLib.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L4

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

4:    import "./ICToken.sol";

5:    import "./SelfReferentialCollateral.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L4

```solidity
File: contracts/plugins/assets/EURFiatCollateral.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "../../libraries/Fixed.sol";

6:    import "./FiatCollateral.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L4

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "../../interfaces/IAsset.sol";

6:    import "../../libraries/Fixed.sol";

7:    import "./FiatCollateral.sol";

8:    import "./Asset.sol";

9:    import "./OracleLib.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L4

```solidity
File: contracts/plugins/assets/ICToken.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ICToken.sol#L4

```solidity
File: contracts/plugins/assets/NonFiatCollateral.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "../../libraries/Fixed.sol";

6:    import "./FiatCollateral.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L4

```solidity
File: contracts/plugins/assets/OracleLib.sol

4:    import "@chainlink/contracts/src/v0.8/interfaces/AggregatorV3Interface.sol";

5:    import "../../libraries/Fixed.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L4

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

4:    import "../../p1/mixins/RecollateralizationLib.sol";

5:    import "../../interfaces/IMain.sol";

6:    import "../../interfaces/IRToken.sol";

7:    import "./Asset.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L4

```solidity
File: contracts/plugins/assets/SelfReferentialCollateral.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "../../libraries/Fixed.sol";

6:    import "./FiatCollateral.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L4

```solidity
File: contracts/plugins/governance/Governance.sol

4:    import "@openzeppelin/contracts/governance/Governor.sol";

5:    import "@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol";

6:    import "@openzeppelin/contracts/governance/extensions/GovernorSettings.sol";

7:    import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";

8:    import "@openzeppelin/contracts/governance/extensions/GovernorVotes.sol";

9:    import "@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol";

10:   import "../../interfaces/IStRSRVotes.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L4

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

4:    import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:    import "@openzeppelin/contracts/utils/math/Math.sol";

6:    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";

7:    import "../../libraries/Fixed.sol";

8:    import "../../interfaces/IBroker.sol";

9:    import "../../interfaces/IGnosis.sol";

10:   import "../../interfaces/ITrade.sol";

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L4

### [N&#x2011;04]  Unused file
The file is never imported by any other file

*There is 1 instance of this issue:*

```solidity
File: contracts/interfaces/IDeployerRegistry.sol

1:    // SPDX-License-Identifier: BlueOak-1.0.0

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L1

### [N&#x2011;05]  The `nonReentrant` `modifier` should occur before all other modifiers
This is a best-practice to protect against reentrancy in other modifiers

*There is 1 instance of this issue:*

```solidity
File: contracts/p1/mixins/Trading.sol

66:       function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L66

### [N&#x2011;06]  `override` function arguments that are unused should have the variable name removed or commented out to avoid compiler warnings

*There are 2 instances of this issue:*

```solidity
File: contracts/p1/Main.sol

64:       function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L64

```solidity
File: contracts/p1/mixins/Component.sol

57:       function _authorizeUpgrade(address newImplementation) internal view override governance {}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L57

### [N&#x2011;07]  Non-assembly method available 
`assembly{ id := chainid() }` => `uint256 id = block.chainid`, `assembly { size := extcodesize() }` => `uint256 size = address().code.length`, `assembly { hash := extcodehash() }` => `bytes32 hash = address().codehash`
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary

*There is 1 instance of this issue:*

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

265:              chainId := chainid()

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L265

### [N&#x2011;08]  External calls in an un-bounded `for-`loop may result in a DOS
Consider limiting the number of iterations in `for-`loops that make external calls

*There are 17 instances of this issue:*

```solidity
File: contracts/p1/BackingManager.sol

/// @audit decimals
221:          for (uint256 i = 0; i < length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L221

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit status
286:          for (uint256 i = 0; i < size; ++i) {

/// @audit lotPrice
/// @audit price
337:          for (uint256 i = 0; i < len; ++i) {

/// @audit decimals
416:          for (uint256 i = 0; i < length; ++i) {

/// @audit targetPerRef
548:          for (uint256 i = 0; i < basketLength; ++i) {

/// @audit targetPerRef
586:          for (uint256 i = 0; i < targetsLength; ++i) {

/// @audit targetPerRef
611:              for (uint256 j = 0; j < backupLength && assigned < size; ++j) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L286

```solidity
File: contracts/p1/Distributor.sol

/// @audit safeTransferFrom
133:          for (uint256 i = 0; i < numTransfers; i++) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L133

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

/// @audit status
329:          for (uint256 i = 0; i < reg.erc20s.length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L329

```solidity
File: contracts/p1/mixins/RewardableLib.sol

/// @audit functionDelegateCall
27:           for (uint256 i = 0; i < registry.assets.length; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27

```solidity
File: contracts/p1/RToken.sol

/// @audit safeTransferFrom
270:              for (uint256 i = 0; i < erc20s.length; ++i) {

/// @audit safeTransferFrom
334:          for (uint256 i = 0; i < basketSize; ++i) {

/// @audit balanceOf
478:          for (uint256 i = 0; i < erc20length; ++i) {

/// @audit safeTransferFrom
501:          for (uint256 i = 0; i < erc20length; ++i) {

/// @audit safeTransfer
711:          for (uint256 i = 0; i < queue.tokens.length; ++i) {

/// @audit safeTransfer
793:          for (uint256 i = 0; i < tokensLen; ++i) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270

### [N&#x2011;09]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 24 instances of this issue:*

```solidity
File: contracts/libraries/Fixed.sol

/// @audit 96
101:      if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5

/// @audit 18
104:      shiftLeft += 18;

/// @audit 59
212:          if (decimals <= -59) return (rounding == CEIL ? 1 : 0); // 59, because 1e58 > 2**192

/// @audit 42
389:          if (decimals <= -42) return (rounding == CEIL ? 1 : 0);

/// @audit 18
392:          decimals -= 18; // shift so that toUint happens at the same time.

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L101

```solidity
File: contracts/libraries/Permit.sol

/// @audit 0x1626ba7e
20:                       0x1626ba7e,

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L20

```solidity
File: contracts/libraries/String.sol

/// @audit 65
/// @audit 90
16:               if ((uint8(bStr[i]) >= 65) && (uint8(bStr[i]) <= 90)) {

/// @audit 32
18:                   bLower[i] = bytes1(uint8(bStr[i]) + 32);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L16

```solidity
File: contracts/p1/Distributor.sol

/// @audit 10000
165:          require(share.rsrDist <= 10000, "RSR distribution too high");

/// @audit 10000
166:          require(share.rTokenDist <= 10000, "RToken distribution too high");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165

```solidity
File: contracts/p1/mixins/TradeLib.sol

/// @audit 0x11
/// @audit 0x12
168:              assert(errorCode == 0x11 || errorCode == 0x12);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L168

```solidity
File: contracts/p1/RToken.sol

/// @audit 10_000
15:   uint192 constant MIN_BLOCK_ISSUANCE_LIMIT = 10_000 * FIX_ONE;

/// @audit 1e9
/// @audit 1e27
813:          require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L15

```solidity
File: contracts/plugins/aave/ERC20.sol

/// @audit 18
60:           _decimals = 18;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L60

```solidity
File: contracts/plugins/assets/ATokenFiatCollateral.sol

/// @audit 27
47:           return shiftl_toFix(rateInRAYs, -27);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L47

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

/// @audit 8
/// @audit 18
46:           int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L46

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

/// @audit 8
/// @audit 18
49:           int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L49

```solidity
File: contracts/plugins/governance/Governance.sol

/// @audit 1e6
73:           return quorumNumerator() * 1e6;

/// @audit 1e8
156:          return (bal * 1e8) / totalSupply;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L73

### [N&#x2011;10]  Numeric values having to do with time should use time units for readability
There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

*There are 8 instances of this issue:*

```solidity
File: contracts/libraries/RedemptionBattery.sol

/// @audit 300
11:   uint48 constant BLOCKS_PER_HOUR = 300; // {blocks/hour}

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L11

```solidity
File: contracts/mixins/Auth.sol

/// @audit 2592000
9:    uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month

/// @audit 31536000
10:   uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L9

```solidity
File: contracts/p1/BackingManager.sol

/// @audit 31536000
33:       uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33

```solidity
File: contracts/p1/Broker.sol

/// @audit 604800
24:       uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24

```solidity
File: contracts/p1/Furnace.sol

/// @audit 31536000
16:       uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L16

```solidity
File: contracts/p1/StRSR.sol

/// @audit 31536000
37:       uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year

/// @audit 31536000
38:       uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L37

### [N&#x2011;11]  Missing event and or timelock for critical parameter change
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

*There are 2 instances of this issue:*

```solidity
File: contracts/p1/StRSR.sol

803       function setName(string calldata name_) external governance {
804           name = name_;
805:      }

807       function setSymbol(string calldata symbol_) external governance {
808           symbol = symbol_;
809:      }

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L803-L805

### [N&#x2011;12]  Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

*There are 2 instances of this issue:*

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit setPrimeBasket()
240:          emit PrimeBasketSet(erc20s, targetAmts, names);

/// @audit setBackupConfig()
268:          emit BackupConfigSet(targetName, max, erc20s);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L240

### [N&#x2011;13]  Use a more recent version of solidity
Use a solidity version of at least 0.8.12 to get `string.concat()` to be used instead of `abi.encodePacked(<str>,<str>)`

*There are 2 instances of this issue:*

```solidity
File: contracts/libraries/Permit.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L2

```solidity
File: contracts/p1/Deployer.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L2

### [N&#x2011;14]  Use a more recent version of solidity
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

*There are 26 instances of this issue:*

```solidity
File: contracts/libraries/RedemptionBattery.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L2

```solidity
File: contracts/p1/AssetRegistry.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L2

```solidity
File: contracts/p1/BackingManager.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L2

```solidity
File: contracts/p1/BasketHandler.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L2

```solidity
File: contracts/p1/Broker.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L2

```solidity
File: contracts/p1/Distributor.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L2

```solidity
File: contracts/p1/Furnace.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L2

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L2

```solidity
File: contracts/p1/mixins/RewardableLib.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L2

```solidity
File: contracts/p1/mixins/TradeLib.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L2

```solidity
File: contracts/p1/mixins/Trading.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L2

```solidity
File: contracts/p1/RevenueTrader.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L2

```solidity
File: contracts/p1/RToken.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L2

```solidity
File: contracts/p1/StRSR.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L2

```solidity
File: contracts/plugins/aave/ERC20.sol

3:    pragma solidity ^0.6.0;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L3

```solidity
File: contracts/plugins/assets/Asset.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L2

```solidity
File: contracts/plugins/assets/ATokenFiatCollateral.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L2

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L2

```solidity
File: contracts/plugins/assets/CTokenNonFiatCollateral.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L2

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L2

```solidity
File: contracts/plugins/assets/EURFiatCollateral.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L2

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L2

```solidity
File: contracts/plugins/assets/NonFiatCollateral.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L2

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L2

```solidity
File: contracts/plugins/assets/SelfReferentialCollateral.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L2

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

2:    pragma solidity 0.8.9;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L2

### [N&#x2011;15]  Use a more recent version of solidity
Use a solidity version of at least 0.8.4 to get `bytes.concat()` instead of `abi.encodePacked(<bytes>,<bytes>)`
Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<str>,<str>)`

*There is 1 instance of this issue:*

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

2:    pragma solidity 0.6.12;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L2

### [N&#x2011;16]  Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
While it **doesn't save any gas** because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

*There are 6 instances of this issue:*

```solidity
File: contracts/p1/StRSR.sol

126       bytes32 private constant _PERMIT_TYPEHASH =
127           keccak256(
128               "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
129:          );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L126-L129

```solidity
File: contracts/p1/StRSRVotes.sol

27        bytes32 private constant _DELEGATE_TYPEHASH =
28:           keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L27-L28

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

41        bytes32 internal constant EIP712_DOMAIN =
42            keccak256(
43                "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
44:           );

45        bytes32 public constant PERMIT_TYPEHASH =
46            keccak256(
47                "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
48:           );

49        bytes32 public constant METADEPOSIT_TYPEHASH =
50            keccak256(
51                "Deposit(address depositor,address recipient,uint256 value,uint16 referralCode,bool fromUnderlying,uint256 nonce,uint256 deadline)"
52:           );

53        bytes32 public constant METAWITHDRAWAL_TYPEHASH =
54            keccak256(
55                "Withdraw(address owner,address recipient,uint256 staticAmount,uint256 dynamicAmount,bool toUnderlying,uint256 nonce,uint256 deadline)"
56:           );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L41-L44

### [N&#x2011;17]  Inconsistent spacing in comments
Some lines use `// x` and some use `//x`. The instances below point out the usages that don't follow the majority, within each file

*There are 3 instances of this issue:*

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

73:       // user => _accRewardsPerToken at last interaction (in RAYs)

75:       // user => unclaimedRewards (in RAYs)

385:                  // No rewards can have accrued since last because there were no funds.

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L73

### [N&#x2011;18]  Variable names that consist of all capital letters should be reserved for `constant`/`immutable` variables
If the variable needs to be different based on which class it comes from, a `view`/`pure` _function_ should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).

*There are 5 instances of this issue:*

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

60:       ILendingPool public override LENDING_POOL;

61:       IAaveIncentivesController public override INCENTIVES_CONTROLLER;

62:       IERC20 public override ATOKEN;

63:       IERC20 public override ASSET;

64:       IERC20 public override REWARD_TOKEN;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L60

### [N&#x2011;19]  Non-library/interface files should use fixed compiler versions, not floating ones

*There is 1 instance of this issue:*

```solidity
File: contracts/plugins/aave/ERC20.sol

3:    pragma solidity ^0.6.0;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L3

### [N&#x2011;20]  Typos

*There are 17 instances of this issue:*

```solidity
File: contracts/interfaces/IAsset.sol

/// @audit implemntation
86:       /// VERY IMPORTANT: In any valid implemntation, status() MUST become DISABLED in refresh() if

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L86

```solidity
File: contracts/interfaces/IRToken.sol

/// @audit reedemed
178:      /// Set the fraction of the RToken supply that can be reedemed at once

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L178

```solidity
File: contracts/interfaces/IStRSR.sol

/// @audit unstakin
76:       /// Emitted if all the RSR in the unstakin pool is seized, and all ongoing unstaking is voided.

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L76

```solidity
File: contracts/mixins/Auth.sol

/// @audit predetemined
22:        * Typically freezing thaws on its own in a predetemined number of blocks.

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L22

```solidity
File: contracts/p1/BackingManager.sol

/// @audit Mointain
89:       /// Mointain the overall backing policy; handout assets otherwise

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L89

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit interepreted
537:          // As such, they're each interepreted as a map from target name -> target weight

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L537

```solidity
File: contracts/p1/Furnace.sol

/// @audit leriod
66:       //   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay leriod)

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L66

```solidity
File: contracts/p1/RToken.sol

/// @audit adjaacent
109:      // issuances, and so any particular issuance is actually the _difference_ between two adjaacent

/// @audit TotalIssue
112:      // The way to keep an IssueQueue striaght in your head is to think of each TotalIssue item as a

/// @audit bskt
144:      //     so, queue.tokens was the bskt token list when basketHandler.nonce() was queue.basketNonce

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L109

```solidity
File: contracts/p1/StRSR.sol

/// @audit zereod
25:    *   If this happens, users balances are zereod out and StRSR is re-issued at a 1:1 exchange rate

/// @audit totalDraft
541:      //   r'.queue is r.queue with a new entry appeneded for (totalDrafts' - totalDraft) drafts

/// @audit Overriden
570:      /// Overriden in StRSRVotes to handle rebases

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L25

```solidity
File: contracts/p1/StRSRVotes.sol

/// @audit accountt
30:       // _delegates[account] is the address of the delegate that `accountt` has specified

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L30

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

/// @audit amound
524:       * @return The amound of pending rewards in RAY

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L524

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

/// @audit mulfunction
21:       uint48 delayUntilDefault; // {s} The number of seconds an oracle can mulfunction

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L21

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit acution
47:       // from this trade's acution will all eventually go to origin.

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L47

### [N&#x2011;21]  File is missing NatSpec

*There are 4 instances of this issue:*

```solidity
File: contracts/interfaces/IVersioned.sol


```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IVersioned.sol

```solidity
File: contracts/libraries/Permit.sol


```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol

```solidity
File: contracts/plugins/aave/RayMathNoRounding.sol


```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol

```solidity
File: contracts/plugins/aave/StaticATokenErrors.sol


```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol

### [N&#x2011;22]  NatSpec is incomplete

*There are 164 instances of this issue:*

```solidity
File: contracts/interfaces/IAssetRegistry.sol

/// @audit Missing: '@param erc20'
40        /// @return The corresponding asset for ERC20, or reverts if not registered
41:       function toAsset(IERC20 erc20) external view returns (IAsset);

/// @audit Missing: '@param erc20'
43        /// @return The corresponding collateral, or reverts if unregistered or not collateral
44:       function toColl(IERC20 erc20) external view returns (ICollateral);

/// @audit Missing: '@param erc20'
46        /// @return If the ERC20 is registered
47:       function isRegistered(IERC20 erc20) external view returns (bool);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAssetRegistry.sol#L40-L41

```solidity
File: contracts/interfaces/IAsset.sol

/// @audit Missing: '@param account'
34        /// @return {tok} The balance of the ERC20 in whole tokens
35:       function bal(address account) external view returns (uint192);

/// @audit Missing: '@return'
46        /// @param {UoA} The max trade volume, in UoA
47:       function maxTradeVolume() external view returns (uint192);

/// @audit Missing: '@param a'
/// @audit Missing: '@param b'
65        /// @return Whether a is worse than b
66:       function worseThan(CollateralStatus a, CollateralStatus b) internal pure returns (bool) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L34-L35

```solidity
File: contracts/interfaces/IBasketHandler.sol

/// @audit Missing: '@param erc20'
73        /// @return {tok/BU} The whole token quantity of token in the reference basket
74        /// Returns 0 if erc20 is not registered, disabled, or not in the basket
75        /// Returns FIX_MAX (in lieu of +infinity) if Collateral.refPerTok() is 0.
76        /// Otherwise, returns (token's basket.refAmts / token's Collateral.refPerTok())
77:       function quantity(IERC20 erc20) external view returns (uint192);

/// @audit Missing: '@param rounding'
82        /// @param amount {BU}
83        /// @return erc20s The addresses of the ERC20 tokens in the reference basket
84        /// @return quantities {qTok} The quantity of each ERC20 token to issue `amount` baskets
85        function quote(uint192 amount, RoundingMode rounding)
86            external
87            view
88:           returns (address[] memory erc20s, uint256[] memory quantities);

/// @audit Missing: '@param account'
90        /// @return baskets {BU} The quantity of complete baskets at an address. A balance for BUs
91:       function basketsHeldBy(address account) external view returns (uint192 baskets);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L73-L77

```solidity
File: contracts/interfaces/IDeployerRegistry.sol

/// @audit Missing: '@param deployer'
11        /// Register a deployer address, keyed by a version.
12        /// @dev Does not allow overwriting without deregistration
13        /// @param version A semver version string
14        /// @param makeLatest True iff this deployment should be promoted to be the latest deployment
15        function register(
16            string calldata version,
17            IDeployer deployer,
18:           bool makeLatest

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L11-L18

```solidity
File: contracts/interfaces/IDistributor.sol

/// @audit Missing: '@param address'
61        /// @return rTokenDist The RToken distribution for the address
62        /// @return rsrDist The RSR distribution for the address
63:       function distribution(address) external view returns (uint16 rTokenDist, uint16 rsrDist);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L61-L63

```solidity
File: contracts/interfaces/IRToken.sol

/// @audit Missing: '@param endId'
129       /// Completes vested slow issuances for the account, up to endId.
130       /// @param recipient The address of the account to vest issuances for
131       /// @custom:interaction
132:      function vest(address recipient, uint256 endId) external;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L129-L132

```solidity
File: contracts/interfaces/IStRSRVotes.sol

/// @audit Missing: '@param blockNumber'
10        /// @return The era at a past block number
11:       function getPastEra(uint256 blockNumber) external view returns (uint256);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSRVotes.sol#L10-L11

```solidity
File: contracts/interfaces/ITrading.sol

/// @audit Missing: '@param sell'
57        /// @return The ongoing trade for a sell token, or the zero address
58:       function trades(IERC20 sell) external view returns (ITrade);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L57-L58

```solidity
File: contracts/libraries/Array.sol

/// @audit Missing: '@param arr'
7         /// O(n^2)
8         /// @return If the array contains all unique addresses
9:        function allUnique(IERC20[] memory arr) internal pure returns (bool) {

/// @audit Missing: '@param arr'
19        /// O(n) -- must already be in sorted ascending order!
20        /// @return If the array contains all unique addresses, in ascending order
21:       function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L7-L9

```solidity
File: contracts/libraries/Fixed.sol

/// @audit Missing: '@param x'
77    /// Convert a uint to its Fix representation.
78    /// @return x
79    // as-ints: x * 1e18
80:   function toFix(uint256 x) pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param shiftLeft'
84    /// Convert a uint to its fixed-point representation, and left-shift its value `shiftLeft`
85    /// decimal digits.
86    /// @return x * 10**shiftLeft
87    // as-ints: x * 10**(shiftLeft + 18)
88:   function shiftl_toFix(uint256 x, int8 shiftLeft) pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
112   /// Divide a uint by a uint192, yielding a uint192
113   /// This may also fail if the result is MIN_uint192! not fixing this for optimization's sake.
114   /// @return x / y
115   // as-ints: x * 1e36 / y
116:  function divFix(uint256 x, uint192 y) pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
126   /// Divide a uint by a uint, yielding a  uint192
127   /// @return x / y
128   // as-ints: x * 1e18 / y
129:  function divuu(uint256 x, uint256 y) pure returns (uint192) {

/// @audit Missing: '@param numerator'
/// @audit Missing: '@param divisor'
/// @audit Missing: '@param rounding'
151   /// Divide two uints, returning a uint, using rounding mode `rounding`.
152   /// @return numerator / divisor
153   // as-ints: numerator / divisor
154   function _divrnd(
155       uint256 numerator,
156       uint256 divisor,
157       RoundingMode rounding
158:  ) pure returns (uint256) {

/// @audit Missing: '@param x'
179       /// Convert this fixed-point value to a uint. Round towards zero if needed.
180       /// @return x
181       // as-ints: x / 1e18
182:      function toUint(uint192 x) internal pure returns (uint136) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param rounding'
186       /// Convert this uint192 to a uint
187       /// @return x
188       // as-ints: x / 1e18 with rounding
189:      function toUint(uint192 x, RoundingMode rounding) internal pure returns (uint136) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param decimals'
193       /// Return the uint192 shifted to the left by `decimal` digits
194       /// (Similar to a bitshift but in base 10)
195       /// @return x * 10**decimals
196       // as-ints: x * 10**decimals
197:      function shiftl(uint192 x, int8 decimals) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param decimals'
/// @audit Missing: '@param rounding'
201       /// Return the uint192 shifted to the left by `decimal` digits
202       /// (Similar to a bitshift but in base 10)
203       /// @return x * 10**decimals
204       // as-ints: x * 10**decimals
205       function shiftl(
206           uint192 x,
207           int8 decimals,
208           RoundingMode rounding
209:      ) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
219       /// Add a uint192 to this uint192
220       /// @return x + y
221       // as-ints: x + y
222:      function plus(uint192 x, uint192 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
226       /// Add a uint to this uint192
227       /// @return x + y
228       // as-ints: x + y*1e18
229:      function plusu(uint192 x, uint256 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
233       /// Subtract a uint192 from this uint192
234       /// @return x - y
235       // as-ints: x - y
236:      function minus(uint192 x, uint192 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
240       /// Subtract a uint from this uint192
241       /// @return x - y
242       // as-ints: x - y*1e18
243:      function minusu(uint192 x, uint256 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
247       /// Multiply this uint192 by a uint192
248       /// Round truncated values to the nearest available value. 5e-19 rounds away from zero.
249       /// @return x * y
250       // as-ints: x * y/1e18  [division using ROUND, not FLOOR]
251:      function mul(uint192 x, uint192 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param rounding'
255       /// Multiply this uint192 by a uint192
256       /// @return x * y
257       // as-ints: x * y/1e18
258       function mul(
259           uint192 x,
260           uint192 y,
261           RoundingMode rounding
262:      ) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
266       /// Multiply this uint192 by a uint
267       /// @return x * y
268       // as-ints: x * y
269:      function mulu(uint192 x, uint256 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
273       /// Divide this uint192 by a uint192
274       /// @return x / y
275       // as-ints: x * 1e18 / y
276:      function div(uint192 x, uint192 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param rounding'
280       /// Divide this uint192 by a uint192
281       /// @return x / y
282       // as-ints: x * 1e18 / y
283       function div(
284           uint192 x,
285           uint192 y,
286           RoundingMode rounding
287:      ) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
292       /// Divide this uint192 by a uint
293       /// @return x / y
294       // as-ints: x / y
295:      function divu(uint192 x, uint256 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param rounding'
299       /// Divide this uint192 by a uint
300       /// @return x / y
301       // as-ints: x / y
302       function divu(
303           uint192 x,
304           uint256 y,
305           RoundingMode rounding
306:      ) internal pure returns (uint192) {

/// @audit Missing: '@param x_'
/// @audit Missing: '@param y'
312       /// Raise this uint192 to a nonnegative integer power. Requires that x_ <= FIX_ONE
313       /// Gas cost is O(lg(y)), precision is +- 1e-18.
314       /// @return x_ ** y
315       // as-ints: x_ ** y / 1e18**(y-1)    <- technically correct for y = 0. :D
316:      function powu(uint192 x_, uint48 y) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param epsilon'
356       /// Return whether or not this uint192 is less than epsilon away from y.
357       /// @return |x - y| < epsilon
358       // as-ints: |x - y| < epsilon
359       function near(
360           uint192 x,
361           uint192 y,
362           uint192 epsilon
363:      ) internal pure returns (bool) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param decimals'
372       /// Shift this uint192 left by `decimals` digits, and convert to a uint
373       /// @return x * 10**decimals
374       // as-ints: x * 10**(decimals - 18)
375:      function shiftl_toUint(uint192 x, int8 decimals) internal pure returns (uint256) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param decimals'
/// @audit Missing: '@param rounding'
379       /// Shift this uint192 left by `decimals` digits, and convert to a uint.
380       /// @return x * 10**decimals
381       // as-ints: x * 10**(decimals - 18)
382       function shiftl_toUint(
383           uint192 x,
384           int8 decimals,
385           RoundingMode rounding
386:      ) internal pure returns (uint256) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
398       /// Multiply this uint192 by a uint, and output the result as a uint
399       /// @return x * y
400       // as-ints: x * y / 1e18
401:      function mulu_toUint(uint192 x, uint256 y) internal pure returns (uint256) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param rounding'
405       /// Multiply this uint192 by a uint, and output the result as a uint
406       /// @return x * y
407       // as-ints: x * y / 1e18
408       function mulu_toUint(
409           uint192 x,
410           uint256 y,
411           RoundingMode rounding
412:      ) internal pure returns (uint256) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
416       /// Multiply this uint192 by a uint192 and output the result as a uint
417       /// @return x * y
418       // as-ints: x * y / 1e36
419:      function mul_toUint(uint192 x, uint192 y) internal pure returns (uint256) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param rounding'
423       /// Multiply this uint192 by a uint192 and output the result as a uint
424       /// @return x * y
425       // as-ints: x * y / 1e36
426       function mul_toUint(
427           uint192 x,
428           uint192 y,
429           RoundingMode rounding
430:      ) internal pure returns (uint256) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param z'
434       /// Compute x * y / z avoiding intermediate overflow
435       /// @dev Only use if you need to avoid overflow; costlier than x * y / z
436       /// @return x * y / z
437       // as-ints: x * y / z
438       function muluDivu(
439           uint192 x,
440           uint256 y,
441           uint256 z
442:      ) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param z'
/// @audit Missing: '@param rounding'
446       /// Compute x * y / z, avoiding intermediate overflow
447       /// @dev Only use if you need to avoid overflow; costlier than x * y / z
448       /// @return x * y / z
449       // as-ints: x * y / z
450       function muluDivu(
451           uint192 x,
452           uint256 y,
453           uint256 z,
454           RoundingMode rounding
455:      ) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param z'
459       /// Compute x * y / z on Fixes, avoiding intermediate overflow
460       /// @dev Only use if you need to avoid overflow; costlier than x * y / z
461       /// @return x * y / z
462       // as-ints: x * y / z
463       function mulDiv(
464           uint192 x,
465           uint192 y,
466           uint192 z
467:      ) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param z'
/// @audit Missing: '@param rounding'
471       /// Compute x * y / z on Fixes, avoiding intermediate overflow
472       /// @dev Only use if you need to avoid overflow; costlier than x * y / z
473       /// @return x * y / z
474       // as-ints: x * y / z
475       function mulDiv(
476           uint192 x,
477           uint192 y,
478           uint192 z,
479           RoundingMode rounding
480:      ) internal pure returns (uint192) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param z'
489   /// Return (x*y/z), avoiding intermediate overflow.
490   //  Adapted from sources:
491   //    https://medium.com/coinmonks/4db014e080b1, https://medium.com/wicketh/afa55870a65
492   //    and quite a few of the other excellent "Mathemagic" posts from https://medium.com/wicketh
493   /// @dev Only use if you need to avoid overflow; costlier than x * y / z
494   /// @return result x * y / z
495   function mulDiv256(
496       uint256 x,
497       uint256 y,
498       uint256 z
499:  ) pure returns (uint256 result) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param z'
/// @audit Missing: '@param rounding'
523   /// Return (x*y/z), avoiding intermediate overflow.
524   /// @dev Only use if you need to avoid overflow; costlier than x * y / z
525   /// @return x * y / z
526   function mulDiv256(
527       uint256 x,
528       uint256 y,
529       uint256 z,
530       RoundingMode rounding
531:  ) pure returns (uint256) {

/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
544   /// Return (x*y) as a "virtual uint512" (lo, hi), representing (hi*2**256 + lo)
545   ///   Adapted from sources:
546   ///   https://medium.com/wicketh/27650fec525d, https://medium.com/coinmonks/4db014e080b1
547   /// @dev Intended to be internal to this library
548   /// @return hi (hi, lo) satisfies  hi*(2**256) + lo == x * y
549   /// @return lo (paired with `hi`)
550:  function fullMul(uint256 x, uint256 y) pure returns (uint256 hi, uint256 lo) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L77-L80

```solidity
File: contracts/libraries/RedemptionBattery.sol

/// @audit Missing: '@param battery'
31        /// @param supply {qRTok} Total RToken supply before redemption
32        /// @param amount {qRTok} Amount of RToken being redeemed
33        function discharge(
34            Battery storage battery,
35            uint256 supply,
36:           uint256 amount

/// @audit Missing: '@param battery'
51        /// @param supply {qRTok} Total RToken supply before the burn step
52        /// @return charge {qRTok} The current total charge as an amount of RToken
53        function currentCharge(Battery storage battery, uint256 supply)
54            internal
55            view
56:           returns (uint256 charge)

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L31-L36

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit Missing: '@param asset'
54        /// Register `asset`
55        /// If either the erc20 address or the asset was already registered, fail
56        /// @return true if the erc20 address was not already registered.
57        /// @custom:governance
58        // checks: asset.erc20() not in keys(assets) or assets[asset.erc20] == asset
59        // effects: assets' = assets.set(asset.erc20(), asset)
60        // returns: (asset.erc20 not in keys(assets))
61:       function register(IAsset asset) external governance returns (bool) {

/// @audit Missing: '@param asset'
65        /// Register `asset` if and only if its erc20 address is already registered.
66        /// If the erc20 address was not registered, revert.
67        /// @return swapped If the asset was swapped for a previously-registered asset
68        /// @custom:governance
69        // contract
70        // checks: asset.erc20() in assets
71        // effects: assets' = assets + {asset.erc20(): asset}
72        // actions: if asset.erc20() is in basketHandler's basket then basketHandler.disableBasket()
73:       function swapRegistered(IAsset asset) external governance returns (bool swapped) {

/// @audit Missing: '@param asset'
144       /// Register an asset
145       /// Forbids registering a different asset for an ERC20 that is already registered
146       /// @return registered If the asset was moved from unregistered to registered
147       // checks: (asset.erc20() not in assets) or (assets[asset.erc20()] == asset)
148       // effects: assets' = assets.set(asset.erc20(), asset)
149       // returns: assets.erc20() not in assets
150:      function _register(IAsset asset) internal returns (bool registered) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L54-L61

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit Missing: '@param erc20'
292       /// @return {tok/BU} The token-quantity of an ERC20 token in the basket.
293       // Returns 0 if erc20 is not registered, disabled, or not in the basket
294       // Returns FIX_MAX (in lieu of +infinity) if Collateral.refPerTok() is 0.
295       // Otherwise returns (token's basket.refAmts / token's Collateral.refPerTok())
296:      function quantity(IERC20 erc20) public view returns (uint192) {

/// @audit Missing: '@param useLotPrice'
329       /// Returns the price of a BU, using the lot prices if `useLotPrice` is true
330       /// @return low {UoA/tok} The lower end of the lot price estimate
331       /// @return high {UoA/tok} The upper end of the lot price estimate
332:      function _price(bool useLotPrice) internal view returns (uint192 low, uint192 high) {

/// @audit Missing: '@return'
354       /// @param qty {tok/BU}
355       /// @param p {UoA/tok}
356:      function quantityMulPrice(uint192 qty, uint192 p) internal pure returns (uint192) {

/// @audit Missing: '@param rounding'
402       /// Return the current issuance/redemption value of `amount` BUs
403       /// @param amount {BU}
404       /// @return erc20s The backing collateral erc20s
405       /// @return quantities {qTok} ERC20 token quantities equal to `amount` BUs
406       // Returns (erc20s, [quantity(e) * amount {as qTok} for e in erc20s])
407       function quote(uint192 amount, RoundingMode rounding)
408           external
409           view
410:          returns (address[] memory erc20s, uint256[] memory quantities)

/// @audit Missing: '@param account'
427       /// @return baskets {BU} The balance of basket units held by `account`
428       /// @dev Returns FIX_ZERO for an empty basket
429       // Returns:
430       //    0, if (basket.erc20s is empty) or (disabled is true) or (status() is DISABLED)
431       //    min(e.balanceOf(account) / quantity(e) for e in basket.erc20s if quantity(e) > 0),
432:      function basketsHeldBy(address account) public view returns (uint192 baskets) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L292-L296

```solidity
File: contracts/p1/mixins/RecollateralizationLib.sol

/// @audit Missing: '@param components'
/// @audit Missing: '@param rules'
/// @audit Missing: '@param reg'
/// @audit Missing: '@param range'
290       // Choose next sell/buy pair to trade, with reference to the basket range
291       // Skip over trading surplus dust amounts
292       /// @return trade
293       ///   sell: Surplus collateral OR address(0)
294       ///   deficit Deficit collateral OR address(0)
295       ///   sellAmount {sellTok} Surplus amount (whole tokens)
296       ///   buyAmount {buyTok} Deficit amount (whole tokens)
297       ///   sellPrice {UoA/sellTok} The worst-case price of the sell token on secondary markets
298       ///   buyPrice {UoA/sellTok} The worst-case price of the buy token on secondary markets
299       ///
300       // Defining "sell" and "buy":
301       // If bal(e) > (quantity(e) * range.top), then e is in surplus by the difference
302       // If bal(e) < (quantity(e) * range.bottom), then e is in deficit by the difference
303       //
304       // First, ignoring RSR:
305       //   `trade.sell` is the token from erc20s with the greatest surplus value (in UoA),
306       //   and sellAmount is the quantity of that token that it's in surplus (in qTok).
307       //   if `trade.sell` == 0, then no token is in surplus by at least minTradeSize,
308       //        and `trade.sellAmount` and `trade.sellPrice` are unset.
309       //
310       //   `trade.buy` is the token from erc20s with the greatest deficit value (in UoA),
311       //   and buyAmount is the quantity of that token that it's in deficit (in qTok).
312       //   if `trade.buy` == 0, then no token is in deficit at all,
313       //        and `trade.buyAmount` and `trade.buyPrice` are unset.
314       //
315       // Then, just if we have a buy asset and no sell asset, consider selling available RSR.
316       //
317       // Prefer selling assets in this order: DISABLED -> SOUND -> IFFY.
318       // All collateral in the basket have already been guaranteed to be SOUND by upstream checks.
319       function nextTradePair(
320           ComponentCache memory components,
321           TradingRules memory rules,
322           Registry memory reg,
323           BasketRange memory range
324:      ) private view returns (TradeInfo memory trade) {

/// @audit Missing: '@param components'
421       /// @param basketsTop {BU} The top end of the basket range estimate
422       /// @return shortfall {UoA} The missing re-collateralization in UoA terms
423       // Specifically, returns:
424       //   sum( shortfall(c, basketsLow) for each backing erc20 c in the basket)
425       //   where shortfall(c,numBUs) == (numBus * bh.quantity(c) - c.balanceOf(bm)) * c.price().high
426       //         (that is, shortfall(c, numBUs) is the market value of the c that `this` would
427       //          need to be given in order to have enough of c to cover `basketsTop` BUs)
428       function collateralShortfall(ComponentCache memory components, uint192 basketsTop)
429           private
430           view
431:          returns (uint192 shortfall)

/// @audit Missing: '@return'
458       /// @param other The collateral status of the asset in consideration
459       /// @param surplusAmt {UoA} The amount by which the asset in consideration is in surplus
460       function isBetterSurplus(
461           MaxSurplusDeficit memory curr,
462           CollateralStatus other,
463           uint192 surplusAmt
464:      ) private pure returns (bool) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L290-L324

```solidity
File: contracts/p1/mixins/RewardableLib.sol

/// @audit Missing: '@param reg'
83        /// Sweep a ERC20 in excess of liabilities to the BackingManager
84        /// @custom:interaction
85        /// @param liabilities The storage mapping of liabilities by token
86        /// @param erc20 The erc20 to sweep
87        /// @param bm The BackingManager
88        // actions:
89        //   erc20.safeTransfer(bm, bal - liabilities[erc20]) if bal > liabilities[erc20]
90        function sweepRewardsSingle(
91            mapping(IERC20 => uint256) storage liabilities,
92            IERC20 erc20,
93            IAssetRegistry reg,
94:           IBackingManager bm

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L83-L94

```solidity
File: contracts/p1/mixins/TradeLib.sol

/// @audit Missing: '@param trade'
/// @audit Missing: '@param rules'
21        /// @dev Caller must be ITrading
22        /// Prepare a trade to sell `trade.sellAmount` that guarantees a reasonable closing price,
23        /// without explicitly aiming at a particular quantity to purchase.
24        /// @param trade:
25        ///   sell != 0, sellAmount >= 0 {sellTok}, sellPrice >= 0 {UoA/sellTok}
26        ///   buy != 0, buyAmount (unused) {buyTok}, buyPrice > 0 {UoA/buyTok}
27        /// @return notDust True when the trade is larger than the dust amount
28        /// @return req The prepared trade request to send to the Broker
29        //
30        // If notDust is true, then the returned trade request satisfies:
31        //   req.sell == trade.sell and req.buy == trade.buy,
32        //   req.minBuyAmount * trade.buyPrice ~=
33        //        trade.sellAmount * trade.sellPrice * (1-rules.maxTradeSlippage),
34        //   req.sellAmount == min(trade.sell.maxTradeSize().toQTok(), trade.sellAmount.toQTok(sell)
35        //   1 < req.sellAmount
36        //
37        // If notDust is false, no trade exists that satisfies those constraints.
38        function prepareTradeSell(TradeInfo memory trade, TradingRules memory rules)
39            internal
40            view
41:           returns (bool notDust, TradeRequest memory req)

/// @audit Missing: '@param trade'
/// @audit Missing: '@param rules'
75        /// Assuming we have `trade.sellAmount` sell tokens available, prepare a trade to cover as
76        /// much of our deficit of `trade.buyAmount` buy tokens as possible, given expected trade
77        /// slippage and the sell asset's maxTradeVolume().
78        /// @param trade:
79        ///   sell != 0
80        ///   buy != 0
81        ///   sellAmount (unused) {sellTok}
82        ///   buyAmount >= 0 {buyTok}
83        ///   sellPrice > 0 {UoA/sellTok}
84        ///   buyPrice > 0 {UoA/buyTok}
85        /// @return notDust Whether the prepared trade is large enough to be worth trading
86        /// @return req The prepared trade request to send to the Broker
87        //
88        // Returns prepareTradeSell(trade, rules), where
89        //   req.sellAmount = min(trade.sellAmount,
90        //                trade.buyAmount * (trade.buyPrice / trade.sellPrice) / (1-maxTradeSlippage))
91        //   i.e, the minimum of trade.sellAmount and (a sale amount that, at current prices and
92        //   maximum slippage, will yield at least the requested trade.buyAmount)
93        //
94        // Which means we should get that, if notDust is true, then:
95        //   req.sell = sell and req.buy = buy
96        //
97        //   1 <= req.minBuyAmount <= max(trade.buyAmount, buy.minTradeSize()).toQTok(trade.buy)
98        //   1 < req.sellAmount <= min(trade.sellAmount.toQTok(trade.sell),
99        //                               sell.maxTradeSize().toQTok(trade.sell))
100       //   req.minBuyAmount ~= trade.sellAmount * sellPrice / buyPrice * (1-maxTradeSlippage)
101       //
102       //   req.sellAmount (and req.minBuyAmount) are maximal satisfying all these conditions
103       function prepareTradeToCoverDeficit(TradeInfo memory trade, TradingRules memory rules)
104           internal
105           view
106:          returns (bool notDust, TradeRequest memory req)

/// @audit Missing: '@param trader'
/// @audit Missing: '@param x'
/// @audit Missing: '@param y'
/// @audit Missing: '@param z'
153       /// @return The result of FixLib.mulDiv bounded from above by FIX_MAX in the case of overflow
154       function safeMulDivCeil(
155           ITrading trader,
156           uint192 x,
157           uint192 y,
158           uint192 z
159:      ) internal pure returns (uint192) {

/// @audit Missing: '@param price'
177       /// Calculates the minTradeSize for an asset based on the given minTradeVolume and price
178       /// @param minTradeVolume {UoA} The min trade volume, passed in for gas optimization
179       /// @return {tok} The min trade size for the asset in whole tokens
180:      function minTradeSize(uint192 minTradeVolume, uint192 price) private pure returns (uint192) {

/// @audit Missing: '@param asset'
/// @audit Missing: '@param price'
186       /// Calculates the maxTradeSize for an asset based on the asset's maxTradeVolume and price
187       /// @return {tok} The max trade size for the asset in whole tokens
188:      function maxTradeSize(IAsset asset, uint192 price) private view returns (uint192) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L21-L41

```solidity
File: contracts/p1/RToken.sol

/// @audit Missing: '@param amtRToken'
342       /// Add amtRToken's worth of issuance delay to allVestAt, and return the resulting finish time.
343       /// @return finished D18{block} The new value of allVestAt
344:      function whenFinished(uint256 amtRToken) private returns (uint192 finished) {

/// @audit Missing: '@param endId'
372       /// Vest all available issuance for the account
373       /// Callable by anyone!
374       /// @param account The address of the account to vest issuances for
375       /// @custom:completion
376       /// @custom:interaction CEI
377       // Thin wrapper over refundSpan() and vestUpTo(); see those for correctness analysis
378:      function vest(address account, uint256 endId) external notPausedOrFrozen {

/// @audit Missing: '@param account'
632       /// @return left The index of the left sides of the issuance queue for the account
633       /// @return right The index of the right sides of the issuance queue for the account
634:      function queueBounds(address account) external view returns (uint256 left, uint256 right) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L342-L344

```solidity
File: contracts/p1/StRSR.sol

/// @audit Missing: '@param era_'
/// @audit Missing: '@param account'
460       /// Used by FacadeP1
461       /// @return The length of the draft queue for an account in an era
462:      function draftQueueLen(uint256 era_, address account) external view returns (uint256) {

/// @audit Missing: '@param account'
532       /// @param rsrAmount {qRSR}
533       /// @return index The index of the draft
534       /// @return availableAt {s} The timestamp the cumulative draft vests
535       // effects:
536       //   draftRSR' = draftRSR + rsrAmount
537       //   draftRate' = draftRate    (ie, unchanged)
538       //   totalDrafts' = floor(draftRSR' * draftRate' / 1e18)
539       //   r'.left = r.left
540       //   r'.right = r.right + 1
541       //   r'.queue is r.queue with a new entry appeneded for (totalDrafts' - totalDraft) drafts
542       //   where r = draft[account] and r' = draft'[account]
543       function pushDraft(address account, uint256 rsrAmount)
544           internal
545:          returns (uint256 index, uint64 availableAt)

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L460-L462

```solidity
File: contracts/plugins/aave/IAaveIncentivesController.sol

/// @audit Missing: '@param assets'
67        /**
68         * @dev Returns the total of rewards of an user, already accrued + not yet accrued
69         * @param user The address of the user
70         * @return The rewards
71         **/
72        function getRewardsBalance(address[] calldata assets, address user)
73            external
74            view
75:           returns (uint256);

/// @audit Missing: '@param assets'
77        /**
78         * @dev Claims reward for an user, on all the assets of the lending pool, accumulating the pending rewards
79         * @param amount Amount of rewards to claim
80         * @param to Address that will be receiving the rewards
81         * @return Rewards claimed
82         **/
83        function claimRewards(
84            address[] calldata assets,
85            uint256 amount,
86            address to
87:       ) external returns (uint256);

/// @audit Missing: '@param assets'
89        /**
90         * @dev Claims reward for an user on behalf, on all the assets of the lending pool, accumulating the pending rewards. The caller must
91         * be whitelisted via "allowClaimOnBehalf" function by the RewardsAdmin role manager
92         * @param amount Amount of rewards to claim
93         * @param user Address to check and claim rewards
94         * @param to Address that will be receiving the rewards
95         * @return Rewards claimed
96         **/
97        function claimRewardsOnBehalf(
98            address[] calldata assets,
99            uint256 amount,
100           address user,
101           address to
102:      ) external returns (uint256);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L67-L75

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

/// @audit Missing: '@param uint256'
353       /**
354        * @notice Updates rewards for senders and receiver in a transfer (not updating rewards for address(0))
355        * @param from The address of the sender of tokens
356        * @param to The address of the receiver of tokens
357        */
358       function _beforeTokenTransfer(
359           address from,
360           address to,
361:          uint256

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L353-L361

```solidity
File: contracts/plugins/assets/Asset.sol

/// @audit Missing: '@param erc20_'
35        /// @param priceTimeout_ {s} The number of seconds over which savedHighPrice decays to 0
36        /// @param chainlinkFeed_ Feed units: {UoA/tok}
37        /// @param oracleError_ {1} The % the oracle feed can be off by
38        /// @param maxTradeVolume_ {UoA} The max trade volume, in UoA
39        /// @param oracleTimeout_ {s} The number of seconds until a oracle value becomes invalid
40        constructor(
41            uint48 priceTimeout_,
42            AggregatorV3Interface chainlinkFeed_,
43            uint192 oracleError_,
44            IERC20Metadata erc20_,
45            uint192 maxTradeVolume_,
46:           uint48 oracleTimeout_

/// @audit Missing: '@return'
67        /// @param low {UoA/tok} The low price estimate
68        /// @param high {UoA/tok} The high price estimate
69        function tryPrice()
70            external
71            view
72            virtual
73            returns (
74                uint192 low,
75                uint192 high,
76:               uint192

/// @audit Missing: '@param account'
150       /// @return {tok} The balance of the ERC20 in whole tokens
151:      function bal(address account) external view returns (uint192) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L35-L46

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

/// @audit Missing: '@param config'
24        /// @param comptroller_ The CompoundFinance Comptroller
25:       constructor(CollateralConfig memory config, IComptroller comptroller_) FiatCollateral(config) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L24-L25

```solidity
File: contracts/plugins/assets/CTokenNonFiatCollateral.sol

/// @audit Missing: '@param config'
20        /// @param config.chainlinkFeed Feed units: {target/ref}
21        /// @param targetUnitChainlinkFeed_ Feed units: {UoA/target}
22        /// @param comptroller_ The CompoundFinance Comptroller
23        constructor(
24            CollateralConfig memory config,
25            AggregatorV3Interface targetUnitChainlinkFeed_,
26            IComptroller comptroller_
27:       ) CTokenFiatCollateral(config, comptroller_) {

/// @audit Missing: '@return'
37        /// @param high {UoA/tok} The high price estimate
38        /// @param pegPrice {target/ref}
39        function tryPrice()
40            external
41            view
42            override
43            returns (
44                uint192 low,
45                uint192 high,
46:               uint192 pegPrice

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L20-L27

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

/// @audit Missing: '@param config'
22        /// @param referenceERC20Decimals_ The number of decimals in the reference token
23        /// @param comptroller_ The CompoundFinance Comptroller
24        constructor(
25            CollateralConfig memory config,
26            uint8 referenceERC20Decimals_,
27            IComptroller comptroller_
28:       ) SelfReferentialCollateral(config) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L22-L28

```solidity
File: contracts/plugins/assets/EURFiatCollateral.sol

/// @audit Missing: '@param config'
19        /// @param config.chainlinkFeed Feed units:{UoA/ref}
20        /// @param uoaPerTargetFeed_ Feed units: {UoA/target}
21        constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
22:           FiatCollateral(config)

/// @audit Missing: '@return'
30        /// @param high {UoA/tok} The high price estimate
31        /// @param pegPrice {UoA/ref}
32        function tryPrice()
33            external
34            view
35            override
36            returns (
37                uint192 low,
38                uint192 high,
39:               uint192 pegPrice

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L19-L22

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

/// @audit Missing: '@param config'
62        /// @param config.chainlinkFeed Feed units: {UoA/ref}
63        constructor(CollateralConfig memory config)
64            Asset(
65                config.priceTimeout,
66                config.chainlinkFeed,
67                config.oracleError,
68                config.erc20,
69                config.maxTradeVolume,
70                config.oracleTimeout
71:           )

/// @audit Missing: '@return'
95        /// @param high {UoA/tok} The high price estimate
96        /// @param pegPrice {target/ref} The actual price observed in the peg
97        function tryPrice()
98            external
99            view
100           virtual
101           override
102           returns (
103               uint192 low,
104               uint192 high,
105:              uint192 pegPrice

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L62-L71

```solidity
File: contracts/plugins/assets/NonFiatCollateral.sol

/// @audit Missing: '@param config'
19        /// @param config.chainlinkFeed Feed units: {target/ref}
20        /// @param uoaPerTargetFeed_ Feed units: {UoA/target}
21        constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
22:           FiatCollateral(config)

/// @audit Missing: '@return'
30        /// @param high {UoA/tok} The high price estimate
31        /// @param pegPrice {target/ref}
32        function tryPrice()
33            external
34            view
35            override
36            returns (
37                uint192 low,
38                uint192 high,
39:               uint192 pegPrice

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L19-L22

```solidity
File: contracts/plugins/assets/OracleLib.sol

/// @audit Missing: '@param chainlinkFeed'
11        /// @dev Use for on-the-fly calculations that should revert
12        /// @param timeout The number of seconds after which oracle values should be considered stale
13        /// @return {UoA/tok}
14        function price(AggregatorV3Interface chainlinkFeed, uint48 timeout)
15            internal
16            view
17:           returns (uint192)

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L11-L17

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

/// @audit Missing: '@param erc20_'
26        /// @param maxTradeVolume_ {UoA} The max trade volume, in UoA
27:       constructor(IRToken erc20_, uint192 maxTradeVolume_) {

/// @audit Missing: '@return'
42        /// @param low {UoA/tok} The low price estimate
43        /// @param high {UoA/tok} The high price estimate
44:       function tryPrice() external view virtual returns (uint192 low, uint192 high) {

/// @audit Missing: '@param account'
103       /// @return {tok} The balance of the ERC20 in whole tokens
104:      function bal(address account) external view returns (uint192) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L26-L27

```solidity
File: contracts/plugins/assets/SelfReferentialCollateral.sol

/// @audit Missing: '@param config'
17        /// @param config.chainlinkFeed Feed units: {UoA/ref}
18:       constructor(CollateralConfig memory config) FiatCollateral(config) {

/// @audit Missing: '@return'
24        /// @param high {UoA/tok} The high price estimate
25        /// @param pegPrice {target/ref}
26        function tryPrice()
27            external
28            view
29            override
30            returns (
31                uint192 low,
32                uint192 high,
33:               uint192 pegPrice

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L17-L18

```solidity
File: contracts/plugins/governance/Governance.sol

/// @audit Missing: '@param uint256'
66        /// @return Returns the quorum required, in units of micro %, e.g 4e6 for 4%
67        function quorum(uint256)
68            public
69            view
70            override(IGovernor, GovernorVotesQuorumFraction)
71:           returns (uint256)

/// @audit Missing: '@param account'
/// @audit Missing: '@param blockNumber'
/// @audit Missing: '@param bytes'
144       /// @return {micro %} The portion of the StRSR supply the account had at a previous blocknumber
145       function _getVotes(
146           address account,
147           uint256 blockNumber,
148           bytes memory /*params*/
149:      ) internal view override(Governor, GovernorVotes) returns (uint256) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L66-L71

### [N&#x2011;23]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 13 instances of this issue:*

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit swapped
162:      function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L162

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit low
/// @audit high
316:      function price() external view returns (uint192 low, uint192 high) {

/// @audit lotLow
/// @audit lotHigh
325:      function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L316

```solidity
File: contracts/p1/mixins/TradeLib.sol

/// @audit notDust
38        function prepareTradeSell(TradeInfo memory trade, TradingRules memory rules)
39            internal
40            view
41:           returns (bool notDust, TradeRequest memory req)

/// @audit notDust
/// @audit req
103       function prepareTradeToCoverDeficit(TradeInfo memory trade, TradingRules memory rules)
104           internal
105           view
106:          returns (bool notDust, TradeRequest memory req)

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L38-L41

```solidity
File: contracts/p1/RToken.sol

/// @audit left
/// @audit right
634:      function queueBounds(address account) external view returns (uint256 left, uint256 right) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L634

```solidity
File: contracts/plugins/assets/Asset.sol

/// @audit low
/// @audit high
69        function tryPrice()
70            external
71            view
72            virtual
73            returns (
74                uint192 low,
75                uint192 high,
76:               uint192

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L69-L76

```solidity
File: contracts/plugins/governance/Governance.sol

/// @audit proposalId
85        function propose(
86            address[] memory targets,
87            uint256[] memory values,
88            bytes[] memory calldatas,
89            string memory description
90:       ) public override(Governor, IGovernor) returns (uint256 proposalId) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L85-L90

### [N&#x2011;24]  Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
The compiler will inline the function, which will avoid `JUMP` instructions usually associated with functions

*There are 12 instances of this issue:*

```solidity
File: contracts/p1/AssetRegistry.sol

111:          require(_erc20s.contains(address(erc20)), "erc20 unregistered");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L111

```solidity
File: contracts/p1/BasketHandler.sol

265:              require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L265

```solidity
File: contracts/p1/RToken.sol

384:          require(status == CollateralStatus.SOUND, "basket unsound");

741:          require(queue.left <= endId && endId <= queue.right, "out of range");

581:          require(_msgSender() == address(backingManager), "not backing manager");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L384

```solidity
File: contracts/p1/StRSR.sol

824:          require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L824

```solidity
File: contracts/p1/StRSRVotes.sol

83:           require(blockNumber < block.number, "ERC20Votes: block not yet mined");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L83

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

210:          require(owner != address(0), StaticATokenErrors.INVALID_OWNER);

172:          require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);

317:          require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L210

```solidity
File: contracts/plugins/assets/Asset.sol

116:              if (errData.length == 0) revert(); // solhint-disable-line reason-string

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L116

```solidity
File: contracts/plugins/governance/Governance.sol

123:          require(startedInSameEra(proposalId), "new era");

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L123

### [N&#x2011;25]  Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic

*There are 5 instances of this issue:*

```solidity
File: contracts/p1/RToken.sol

693:              queue.left = 0;

694:              queue.right = 0;

784:              queue.left = 0;

785:              queue.right = 0;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L693

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

462:              _unclaimedRewards[onBehalfOf] = 0;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L462

### [N&#x2011;26]  Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist

*There are 3 instances of this issue:*

```solidity
File: contracts/plugins/aave/IAaveIncentivesController.sol

34:        * @dev Whitelists an address to claim the rewards on behalf of another address

41:        * @dev Returns the whitelisted claimer for a certain address (0x0 if not set)

91:        * be whitelisted via "allowClaimOnBehalf" function by the RewardsAdmin role manager

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L34

### [N&#x2011;27]  Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

*There is 1 instance of this issue:*

```solidity
File: Various Files


```

### [N&#x2011;28]  Function ordering does not follow the Solidity style guide
According to the [Solidity style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

*There are 42 instances of this issue:*

```solidity
File: contracts/interfaces/IAsset.sol

/// @audit worseThan() came earlier
90:       function targetName() external view returns (bytes32);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L90

```solidity
File: contracts/libraries/Fixed.sol

/// @audit _divrnd() came earlier
182:      function toUint(uint192 x) internal pure returns (uint136) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L182

```solidity
File: contracts/mixins/Auth.sol

/// @audit __Auth_init() came earlier
89        function grantRole(bytes32 role, address account)
90            public
91            override(AccessControlUpgradeable, IAccessControlUpgradeable)
92:           onlyRole(getRoleAdmin(role))

/// @audit grantRole() came earlier
101:      function frozen() external view returns (bool) {

/// @audit pausedOrFrozen() came earlier
120:      function freezeShort() external onlyRole(SHORT_FREEZER) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L89-L92

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit refresh() came earlier
61:       function register(IAsset asset) external governance returns (bool) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L61

```solidity
File: contracts/p1/BackingManager.sol

/// @audit compromiseBasketsNeeded() came earlier
256:      function setTradingDelay(uint48 val) public governance {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit add() came earlier
151:      function init(IMain main_) external initializer {

/// @audit quantity() came earlier
316:      function price() external view returns (uint192 low, uint192 high) {

/// @audit quantityMulPrice() came earlier
394:      function basketTokens() external view returns (IERC20[] memory erc20s) {

/// @audit _switchBasket() came earlier
650:      function requireValidCollArray(IERC20[] calldata erc20s) internal view {

/// @audit goodCollateral() came earlier
694       function getPrimeBasket()
695           external
696           view
697           returns (
698               IERC20[] memory erc20s,
699               bytes32[] memory targetNames,
700:              uint192[] memory targetAmts

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L151

```solidity
File: contracts/p1/Broker.sol

/// @audit setAuctionLength() came earlier
143:      function setDisabled(bool disabled_) external governance {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L143

```solidity
File: contracts/p1/Main.sol

/// @audit init() came earlier
43        function poke() external {
44            // == Refresher ==
45:           assetRegistry.refresh();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L43-L45

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit __Trading_init() came earlier
66:       function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {

/// @audit tryTrade() came earlier
128:      function setMaxTradeSlippage(uint192 val) public governance {

/// @audit setMinTradeVolume() came earlier
144       function mulDivCeil(
145           uint192 x,
146           uint192 y,
147           uint192 z
148:      ) external pure returns (uint192) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L66

```solidity
File: contracts/p1/RToken.sol

/// @audit whenFinished() came earlier
378:      function vest(address account, uint256 endId) external notPausedOrFrozen {

/// @audit setIssuanceRate() came earlier
596:      function scalingRedemptionRate() external view returns (uint192) {

/// @audit setScalingRedemptionRate() came earlier
609:      function redemptionRateFloor() external view returns (uint256) {

/// @audit setRedemptionRateFloor() came earlier
621:      function issueItem(address account, uint256 index) external view returns (IssueItem memory) {

/// @audit requireValidBUExchangeRate() came earlier
828       function _beforeTokenTransfer(
829           address,
830           address to,
831:          uint256

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L378

```solidity
File: contracts/p1/StRSR.sol

/// @audit exchangeRate() came earlier
435:      function endIdForWithdraw(address account) external view returns (uint256) {

/// @audit rsrRewards() came earlier
604:      function totalSupply() public view returns (uint256) {

/// @audit _afterTokenTransfer() came earlier
766       function permit(
767           address owner,
768           address spender,
769           uint256 value,
770           uint256 deadline,
771           uint8 v,
772           bytes32 r,
773:          bytes32 s

/// @audit nonces() came earlier
791:      function DOMAIN_SEPARATOR() external view returns (bytes32) {

/// @audit _useNonce() came earlier
803:      function setName(string calldata name_) external governance {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L435

```solidity
File: contracts/p1/StRSRVotes.sol

/// @audit beginEra() came earlier
55:       function currentEra() external view returns (uint256) {

/// @audit _checkpointsLookup() came earlier
114:      function delegate(address delegatee) public {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L55

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

/// @audit _collectAndUpdateRewards() came earlier
436:      function collectAndUpdateRewards() external override nonReentrant {

/// @audit _claimRewardsOnBehalf() came earlier
468       function claimRewardsOnBehalf(
469           address onBehalfOf,
470           address receiver,
471           bool forceUpdate
472:      ) external override nonReentrant {

/// @audit _getClaimableRewards() came earlier
577:      function getTotalClaimableRewards() external view override returns (uint256) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L436

```solidity
File: contracts/plugins/assets/Asset.sol

/// @audit price() came earlier
126:      function lotPrice() external view virtual returns (uint192 lotLow, uint192 lotHigh) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L126

```solidity
File: contracts/plugins/assets/ATokenFiatCollateral.sol

/// @audit REWARD_TOKEN() came earlier
40:       constructor(CollateralConfig memory config) FiatCollateral(config) {}

/// @audit refPerTok() came earlier
52        function claimRewards() external virtual {
53:           IERC20 stkAAVE = IStaticAToken(address(erc20)).REWARD_TOKEN();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L40

```solidity
File: contracts/plugins/assets/CTokenFiatCollateral.sol

/// @audit refPerTok() came earlier
52        function claimRewards() external virtual override {
53:           IERC20 comp = IERC20(comptroller.getCompAddress());

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L52-L53

```solidity
File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol

/// @audit refPerTok() came earlier
55        function claimRewards() external virtual override {
56:           IERC20 comp = IERC20(comptroller.getCompAddress());

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L55-L56

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

/// @audit alreadyDefaulted() came earlier
203:      function whenDefault() external view returns (uint256) {

/// @audit targetPerRef() came earlier
220:      function isCollateral() external pure virtual override(Asset, IAsset) returns (bool) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L203

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

/// @audit price() came earlier
87:       function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L87

```solidity
File: contracts/plugins/governance/Governance.sol

/// @audit queue() came earlier
105       function cancel(
106           address[] memory targets,
107           uint256[] memory values,
108           bytes[] memory calldatas,
109:          bytes32 descriptionHash

/// @audit _getVotes() came earlier
159       function supportsInterface(bytes4 interfaceId)
160           public
161           view
162           override(Governor, GovernorTimelockControl)
163:          returns (bool)

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L105-L109

### [N&#x2011;29]  Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Modifiers, and 5) Functions, but the contract(s) below do not follow this ordering

*There are 45 instances of this issue:*

```solidity
File: contracts/interfaces/IAsset.sol

/// @audit function chainlinkFeed came earlier
57    enum CollateralStatus {
58        SOUND,
59        IFFY, // When a peg is not holding or a chainlink feed is stale
60        DISABLED // When the collateral has completely defaulted
61:   }

/// @audit function worseThan came earlier
79        event CollateralStatusChanged(
80            CollateralStatus indexed oldStatus,
81            CollateralStatus indexed newStatus
82:       );

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L57-L61

```solidity
File: contracts/interfaces/IFurnace.sol

/// @audit function init came earlier
22:       event PeriodSet(uint48 indexed oldPeriod, uint48 indexed newPeriod);

/// @audit function setPeriod came earlier
32:       event RatioSet(uint192 indexed oldRatio, uint192 indexed newRatio);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L22

```solidity
File: contracts/interfaces/IMain.sol

/// @audit function unpause came earlier
104:      event RTokenSet(IRToken indexed oldVal, IRToken indexed newVal);

/// @audit function rToken came earlier
108:      event StRSRSet(IStRSR indexed oldVal, IStRSR indexed newVal);

/// @audit function stRSR came earlier
112:      event AssetRegistrySet(IAssetRegistry indexed oldVal, IAssetRegistry indexed newVal);

/// @audit function assetRegistry came earlier
116:      event BasketHandlerSet(IBasketHandler indexed oldVal, IBasketHandler indexed newVal);

/// @audit function basketHandler came earlier
120:      event BackingManagerSet(IBackingManager indexed oldVal, IBackingManager indexed newVal);

/// @audit function backingManager came earlier
124:      event DistributorSet(IDistributor indexed oldVal, IDistributor indexed newVal);

/// @audit function distributor came earlier
128:      event RSRTraderSet(IRevenueTrader indexed oldVal, IRevenueTrader indexed newVal);

/// @audit function rsrTrader came earlier
132:      event RTokenTraderSet(IRevenueTrader indexed oldVal, IRevenueTrader indexed newVal);

/// @audit function rTokenTrader came earlier
136:      event FurnaceSet(IFurnace indexed oldVal, IFurnace indexed newVal);

/// @audit function furnace came earlier
140:      event BrokerSet(IBroker indexed oldVal, IBroker indexed newVal);

/// @audit function poke came earlier
154:      event MainInitialized();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L104

```solidity
File: contracts/libraries/Fixed.sol

/// @audit function divu came earlier
310:      uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L310

```solidity
File: contracts/mixins/Auth.sol

/// @audit function freezeUntil came earlier
207:      uint256[48] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L207

```solidity
File: contracts/mixins/ComponentRegistry.sol

/// @audit function __ComponentRegistry_init came earlier
34:       IRToken public rToken;

/// @audit function _setRToken came earlier
42:       IStRSR public stRSR;

/// @audit function _setStRSR came earlier
50:       IAssetRegistry public assetRegistry;

/// @audit function _setAssetRegistry came earlier
58:       IBasketHandler public basketHandler;

/// @audit function _setBasketHandler came earlier
66:       IBackingManager public backingManager;

/// @audit function _setBackingManager came earlier
74:       IDistributor public distributor;

/// @audit function _setDistributor came earlier
82:       IRevenueTrader public rsrTrader;

/// @audit function _setRSRTrader came earlier
90:       IRevenueTrader public rTokenTrader;

/// @audit function _setRTokenTrader came earlier
98:       IFurnace public furnace;

/// @audit function _setFurnace came earlier
106:      IBroker public broker;

/// @audit function _setBroker came earlier
119:      uint256[40] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L34

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit function _registerIgnoringCollisions came earlier
184:      uint256[47] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L184

```solidity
File: contracts/p1/BackingManager.sol

/// @audit function setBackingBuffer came earlier
274:      uint256[41] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L274

```solidity
File: contracts/p1/BasketHandler.sol

/// @audit function add came earlier
117:      uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight

/// @audit function basketsHeldBy came earlier
514:      EnumerableSet.Bytes32Set private _targetNames;

/// @audit function getBackupConfig came earlier
738:      uint256[42] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L117

```solidity
File: contracts/p1/Broker.sol

/// @audit function setDisabled came earlier
153:      uint256[44] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L153

```solidity
File: contracts/p1/Distributor.sol

/// @audit function _ensureNonZeroDistribution came earlier
190:      uint256[46] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L190

```solidity
File: contracts/p1/Furnace.sol

/// @audit function setRatio came earlier
108:      uint256[47] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L108

```solidity
File: contracts/p1/Main.sol

/// @audit function _authorizeUpgrade came earlier
71:       uint256[49] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L71

```solidity
File: contracts/p1/mixins/Component.sol

/// @audit function __Component_init came earlier
41        modifier notPausedOrFrozen() {
42            require(!main.pausedOrFrozen(), "paused or frozen");
43            _;
44:       }

/// @audit function _authorizeUpgrade came earlier
64:       uint256[49] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L41-L44

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit function mulDivCeil came earlier
157:      uint256[46] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L157

```solidity
File: contracts/p1/RevenueTrader.sol

/// @audit function manageToken came earlier
101:      uint256[47] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L101

```solidity
File: contracts/p1/RToken.sol

/// @audit function requireNotPausedOrFrozen came earlier
845:      uint256[37] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L845

```solidity
File: contracts/p1/StRSR.sol

/// @audit function setRewardRatio came earlier
839:      uint256[30] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L839

```solidity
File: contracts/p1/StRSRVotes.sol

/// @audit function _subtract came earlier
227:      uint256[46] private __gap;

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L227

```solidity
File: contracts/plugins/aave/IAToken.sol

/// @audit function mint came earlier
37:       event Burn(address indexed from, address indexed target, uint256 value, uint256 index);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol#L37

### [N&#x2011;30]  Interfaces should be indicated with an `I` prefix in the contract name

*There are 11 instances of this issue:*

```solidity
File: contracts/interfaces/IAsset.sol

50:   interface TestIAsset is IAsset {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L50

```solidity
File: contracts/interfaces/IBackingManager.sol

49:   interface TestIBackingManager is IBackingManager, TestITrading {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L49

```solidity
File: contracts/interfaces/IBroker.sol

45:   interface TestIBroker is IBroker {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBroker.sol#L45

```solidity
File: contracts/interfaces/IDeployer.sol

97:   interface TestIDeployer is IDeployer {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L97

```solidity
File: contracts/interfaces/IDistributor.sol

54:   interface TestIDistributor is IDistributor {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L54

```solidity
File: contracts/interfaces/IFurnace.sol

45:   interface TestIFurnace is IFurnace {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L45

```solidity
File: contracts/interfaces/IMain.sol

166:  interface TestIMain is IMain {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L166

```solidity
File: contracts/interfaces/IRevenueTrader.sol

29:   interface TestIRevenueTrader is IRevenueTrader, TestITrading {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRevenueTrader.sol#L29

```solidity
File: contracts/interfaces/IRToken.sol

171:  interface TestIRToken is IRToken {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L171

```solidity
File: contracts/interfaces/IStRSR.sol

123:  interface TestIStRSR is IStRSR {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L123

```solidity
File: contracts/interfaces/ITrading.sol

68:   interface TestITrading is ITrading {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L68


___

## Excluded findings
These findings are excluded from awards calculations because there are publicly-available automated tools that find them. The valid ones appear here for completeness

## Summary

### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | `safeApprove()` is deprecated | 9 | 

Total: 9 instances over 1 issues


### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `require()`/`revert()` statements should have descriptive reason strings | 6 | 
| [N&#x2011;02] | `public` functions not called by the contract should be declared `external` instead | 11 | 
| [N&#x2011;03] | `constant`s should be defined rather than using magic numbers | 3 | 
| [N&#x2011;04] | Event is missing `indexed` fields | 15 | 

Total: 35 instances over 4 issues





## Low Risk Issues

### [L&#x2011;01]  `safeApprove()` is deprecated
[Deprecated](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/bfff03c0d2a59bcd8e2ead1da9aed9edf0080d05/contracts/token/ERC20/utils/SafeERC20.sol#L38-L45) in favor of `safeIncreaseAllowance()` and `safeDecreaseAllowance()`. If only setting the initial allowance to the value that means infinite, `safeIncreaseAllowance()` can be used instead. The function may currently work, but if a bug is found in this version of OpenZeppelin, and the version that you're forced to upgrade to no longer has this function, you'll encounter unnecessary delays in porting and testing replacement contracts.

*There are 9 instances of this issue:*

```solidity
File: contracts/p1/BackingManager.sol

/// @audit (valid but excluded finding)
75:           IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);

/// @audit (valid but excluded finding)
76:           IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L75

```solidity
File: contracts/p1/mixins/Trading.sol

/// @audit (valid but excluded finding)
116:          IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);

/// @audit (valid but excluded finding)
117:          IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L116

```solidity
File: contracts/p1/RevenueTrader.sol

/// @audit (valid but excluded finding)
61:               IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), 0);

/// @audit (valid but excluded finding)
62:               IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), bal);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L61

```solidity
File: contracts/plugins/aave/StaticATokenLM.sol

/// @audit (valid but excluded finding)
101:          ASSET.safeApprove(address(pool), type(uint256).max);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L101

```solidity
File: contracts/plugins/trading/GnosisTrade.sol

/// @audit (valid but excluded finding)
136:          IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), 0);

/// @audit (valid but excluded finding)
137:          IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), initBal);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L136

## Non-critical Issues

### [N&#x2011;01]  `require()`/`revert()` statements should have descriptive reason strings

*There are 6 instances of this issue:*

```solidity
File: contracts/libraries/Fixed.sol

/// @audit (valid but excluded finding)
317:          require(x_ <= FIX_ONE);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L317

```solidity
File: contracts/plugins/assets/Asset.sol

/// @audit (valid but excluded finding)
102:              if (errData.length == 0) revert(); // solhint-disable-line reason-string

/// @audit (valid but excluded finding)
116:              if (errData.length == 0) revert(); // solhint-disable-line reason-string

/// @audit (valid but excluded finding)
133:              if (errData.length == 0) revert(); // solhint-disable-line reason-string

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L102

```solidity
File: contracts/plugins/assets/FiatCollateral.sol

/// @audit (valid but excluded finding)
149:              if (errData.length == 0) revert(); // solhint-disable-line reason-string

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L149

```solidity
File: contracts/plugins/assets/RTokenAsset.sol

/// @audit (valid but excluded finding)
78:               if (errData.length == 0) revert(); // solhint-disable-line reason-string

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L78

### [N&#x2011;02]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 11 instances of this issue:*

```solidity
File: contracts/p1/AssetRegistry.sol

/// @audit (valid but excluded finding)
46        function refresh() public {
47            // It's a waste of gas to require notPausedOrFrozen because assets can be updated directly
48:           uint256 length = _erc20s.length();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L46-L48

```solidity
File: contracts/p1/StRSRVotes.sol

/// @audit (valid but excluded finding)
59:       function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {

/// @audit (valid but excluded finding)
63:       function numCheckpoints(address account) public view returns (uint48) {

/// @audit (valid but excluded finding)
71:       function getVotes(address account) public view returns (uint256) {

/// @audit (valid but excluded finding)
76:       function getPastVotes(address account, uint256 blockNumber) public view returns (uint256) {

/// @audit (valid but excluded finding)
82:       function getPastTotalSupply(uint256 blockNumber) public view returns (uint256) {

/// @audit (valid but excluded finding)
88:       function getPastEra(uint256 blockNumber) public view returns (uint256) {

/// @audit (valid but excluded finding)
114:      function delegate(address delegatee) public {

/// @audit (valid but excluded finding)
118       function delegateBySig(
119           address delegatee,
120           uint256 nonce,
121           uint256 expiry,
122           uint8 v,
123           bytes32 r,
124:          bytes32 s

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L59

```solidity
File: contracts/plugins/aave/ERC20.sol

/// @audit (valid but excluded finding)
74:       function symbol() public view returns (string memory) {

/// @audit (valid but excluded finding)
91:       function decimals() public view returns (uint8) {

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L74

### [N&#x2011;03]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 3 instances of this issue:*

```solidity
File: contracts/libraries/Fixed.sol

/// @audit 40 - (valid but excluded finding)
102:      if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57

/// @audit 58 - (valid but excluded finding)
213:          if (58 <= decimals) revert UIntOutOfBounds(); // 58, because x * 1e58 > 2 ** 192 if x != 0

/// @audit 96 - (valid but excluded finding)
390:          if (96 <= decimals) revert UIntOutOfBounds();

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L102

### [N&#x2011;04]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible [to off-chain tools](https://ethereum.stackexchange.com/questions/40396/can-somebody-please-explain-the-concept-of-event-indexing) that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 15 instances of this issue:*

```solidity
File: contracts/interfaces/IBasketHandler.sol

/// @audit (valid but excluded finding)
21:       event PrimeBasketSet(IERC20[] erc20s, uint192[] targetAmts, bytes32[] targetNames);

/// @audit (valid but excluded finding)
28:       event BasketSet(uint256 indexed nonce, IERC20[] erc20s, uint192[] refAmts, bool disabled);

/// @audit (valid but excluded finding)
34:       event BackupConfigSet(bytes32 indexed targetName, uint256 indexed max, IERC20[] erc20s);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L21

```solidity
File: contracts/interfaces/IDeployerRegistry.sol

/// @audit (valid but excluded finding)
7:        event DeploymentUnregistered(string version, IDeployer deployer);

/// @audit (valid but excluded finding)
8:        event DeploymentRegistered(string version, IDeployer deployer);

/// @audit (valid but excluded finding)
9:        event LatestChanged(string version, IDeployer deployer);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L7

```solidity
File: contracts/interfaces/IDistributor.sol

/// @audit (valid but excluded finding)
28:       event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L28

```solidity
File: contracts/interfaces/IRToken.sol

/// @audit (valid but excluded finding)
78:       event Redemption(address indexed redeemer, uint256 indexed amount, uint192 baskets);

/// @audit (valid but excluded finding)
83:       event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);

/// @audit (valid but excluded finding)
87:       event Melted(uint256 amount);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L78

```solidity
File: contracts/plugins/aave/IAaveIncentivesController.sol

/// @audit (valid but excluded finding)
6:        event RewardsAccrued(address indexed user, uint256 amount);

/// @audit (valid but excluded finding)
8:        event RewardsClaimed(address indexed user, address indexed to, uint256 amount);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L6

```solidity
File: contracts/plugins/aave/IAToken.sol

/// @audit (valid but excluded finding)
15:       event Mint(address indexed from, uint256 value, uint256 index);

/// @audit (valid but excluded finding)
37:       event Burn(address indexed from, address indexed target, uint256 value, uint256 index);

/// @audit (valid but excluded finding)
46:       event BalanceTransfer(address indexed from, address indexed to, uint256 value, uint256 index);

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol#L15
