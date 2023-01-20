## Summary

### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| Doesn't check paused status and Possible for refactoring function of pause and unpause | 1 |
|[L-02]| `require` can be removed in `poke()` | 1 |
|[L-03]| Add a Timelock to Critical Parameter Change | - |
|[L-04]| Remove Test Interface | 11 |

Total 4 issues

### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [NC-01] | Order of contract layout | - |
| [NC-02] | Not using named import |~|
| [NC-03] | Not using latest library |2|
| [NC-04] | Not using latest solidity | 1 |
| [NC-05] | Time Units | - |
| [NC-06] | No emit for storage variable change set | 2 |
| [NC-07] | Typos | 11 |

Total 7 issues

---

# [L-01] Doesn't check paused status and Possible for refactoring function of pause and unpause

There is no check if current is paused or not, for example:
if a pauser A already set the paused to `true`, then the pauser B doesn't have to call it again.
we might save some gas if we add `require` block by merging pause & unpause in one function.

```solidity
File: Auth.sol
165:     function pause() external onlyRole(PAUSER) {
166:         emit PausedSet(paused, true);
167:         paused = true;
168:     }

172:     function unpause() external onlyRole(PAUSER) {
173:         emit PausedSet(paused, false);
174:         paused = false;
175:     }
```

can be rewrite to check paused status and refactored into:

```solidity
function setPaused(bool _paused) external onlyRole(PAUSER) {
  require(paused != _paused, "No Changes");
  emit PausedSet(paused, _paused);
  paused = _paused;
}
```

# [L-02] `require` can be removed in `poke()`

```solidity
File: Main.sol
43:     function poke() external {
44:         // == Refresher ==
45:         assetRegistry.refresh();
46:
47:         // == CE block ==
48:         require(!pausedOrFrozen(), "paused or frozen");
49:         furnace.melt();
50:         stRSR.payoutRewards();
51:     }
```

The `require(!pausedOrFrozen(), "paused or frozen");` is redundant, we can remove it since the `melt()` and `payoutRewards()` function contains `notPausedOrFrozen` modifier.

# [L-03] Add a Timelock to Critical Parameter Change

It is a good practice to give time for users to react and adjust to critical changes with a mandatory time window between them. The first step merely broadcasts to users that a particular change is coming, and the second step commits that change after a suitable waiting period. 

This allows users that do not accept the change to withdraw within the grace period. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious Owner making any malicious or ulterior intention). 

Specifically, privileged roles could use front running to make malicious changes just ahead of incoming transactions, or purely accidental negative effects could occur due to the unfortunate timing of changes.

some function that might suitable to add a timelock:
- `function setRewardPeriod(uint48 val) public governance`
- `function setRewardRatio(uint192 val) public governance`

# [L-04] Remove Test interface

Remove any test related contract for when deploying for production.
- TestIAsset
- TestIBackingManager
- TestIBroker
- TestIDeployer
- TestIDistributor
- TestIFurnace
- TestIMain
- TestIRevenueTrader
- TestIRToken
- TestIStRSR
- TestITrading

# [L-05] Use of floating pragma

https://swcregistry.io/docs/SWC-103

`Fixed.sol` have floating pragma and its been used in many P1 contracts

```solidity
File: Fixed.sol
1: // SPDX-License-Identifier: BlueOak-1.0.0
2: // solhint-disable func-name-mixedcase func-visibility
3: pragma solidity ^0.8.9;
```

Recommend using fixed solidity version

# [NC-01] Order of contract layout

https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-layout

Inside each contract, library or interface, use the following order:

1. Type declarations
2. State variables
3. Events
4. Modifiers
5. Functions

but some of contracts in Reserve codebase are not following this.
for example in `Distributor.sol`, the `struct Transfer` written between functions, and `__gap` variable put on the bottom of contract declaration.

Another example, `ComponentRegistry.sol` the state variable of `rToken`, `stRSR`, `assetRegistry`, `basketHandler`, `backingManager`, `distributor`, `rsrTrader`, `rTokenTrader`, `furnace`, `broker` and `__gap` is not located on top of contract. It's best to stick to standard like:

```solidity
abstract contract ComponentRegistry is Initializable, Auth, IComponentRegistry {
  IRToken public rToken;
  IStRSR public stRSR;
  IAssetRegistry public assetRegistry;
  IBasketHandler public basketHandler;
  IBackingManager public backingManager;
  IDistributor public distributor;
  IRevenueTrader public rsrTrader;
  IRevenueTrader public rTokenTrader;
  IFurnace public furnace;
  IBroker public broker;

  uint256[40] private __gap;

  function _setRToken(IRToken val) private {
      require(address(val) != address(0), "invalid RToken address");
      emit RTokenSet(rToken, val);
      rToken = val;
  }

  function _setStRSR(IStRSR val) private {
      require(address(val) != address(0), "invalid StRSR address");
      emit StRSRSet(stRSR, val);
      stRSR = val;
  }

  ...
```

# [NC-02] Not using named import

All import using plain `import 'file.sol`' instead use named import.

This global import syntax is not recommended, as it makes it hard to understand where modules come from or where modules are defined.

From solidity documentation: https://docs.soliditylang.org/en/v0.8.17/layout-of-source-files.html#syntax-and-semantics
> This form (global import) is not recommended for use, because it unpredictably pollutes the namespace. If you add new top-level items inside “filename”, they automatically appear in all files that import like this from “filename”. It is better to import specific symbols explicitly.

# [NC-03] Not using latest library

- OpenZeppelin using 4.7.3 not 4.8.1
- Chainlink using ^0.4.1 not 0.6.0

# [NC-04] Not using latest solidity

Most of contract is using pragma solidity 0.8.9, while latest solidity version is 0.8.17.

One of reason, we can use a solidity version of at least 0.8.12 to get `string.concat()` to be used instead of `abi.encodePacked(<str>,<str>)`.

```solidity
File: Deployer.sol
206:             string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));
207:             string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));
```

# [NC-05] Time Units

https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html#time-units

suffixes like seconds, minutes, hours, days and weeks after literal numbers can be used to specify units of time where seconds are the base unit and units are considered naively in the following way:

1 == 1 seconds
1 minutes == 60 seconds
1 hours == 60 minutes
1 days == 24 hours
1 weeks == 7 days

To avoid human error while making the assignment more verbose, better to use time units provided.

some instances from Reserve protocol codebase:

```solidity
File: Auth.sol
09: uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month
10: uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year

File: Broker.sol
24:     uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
```

# [NC-06] No emit for storage variable change set

When changing storage variable commonly a setter function it's recommended to emit an event, meanwhile the protocol most of the setter function didn't emit an event. Emitting events allows monitoring activities with off-chain monitoring tools.

For example:

```solidity
File: StRSR.sol
803:     function setName(string calldata name_) external governance {
804:         name = name_;
805:     }
806:
807:     function setSymbol(string calldata symbol_) external governance {
808:         symbol = symbol_;
809:     }
```

# [NC-07] Typos

- implemntation
  File: IAsset.sol
  86: /// VERY IMPORTANT: In any valid implemntation, status() MUST become DISABLED in refresh() if

- setPrimeBakset
  File: BasketHandler.sol
  127: // event. config is only modified by governance through setPrimeBakset and setBackupConfig

- interepreted
  File: BasketHandler.sol
  537: // As such, they're each interepreted as a map from target name -> target weight

- iff
  File: IDeployerRegistry.sol
  14: /// @param makeLatest True iff this deployment should be promoted to be the latest deployment

  File: IRToken.sol
  47: /// That is, id was cancelled iff firstId <= id < endId

  File: Fixed.sol
  21: // Every function should revert iff its result is out of bounds.

  File: BasketHandler.sol
  361: // qty = FIX_MAX iff p = 0

  File: Broker.sol
  47: // (trades[addr] == true) iff this contract has created an ITrade clone at addr

  File: RToken.sol
  106: // queue.right == left iff there are no more pending issuances in this queue

  124: // We define a (partial) ordering on IssueItems: item1 < item2 iff the following all hold:

  File: Trading.sol
  37: // trades[sell] != 0 iff trade[sell] has been opened and not yet settled

- adjaacent
  File: RToken.sol
  109: // issuances, and so any particular issuance is actually the _difference_ between two adjaacent

- zereod
  File: StRSR.sol
  25: \* If this happens, users balances are zereod out and StRSR is re-issued at a 1:1 exchange rate

- transferrable
  File: StRSR.sol
  74: // Drafts: share of the withdrawing tokens. Not transferrable and not revenue-earning.

- accountt
  File: StRSRVotes.sol
  30: // \_delegates[account] is the address of the delegate that `accountt` has specified

- mulfunction
  File: FiatCollateral.sol
  21: uint48 delayUntilDefault; // {s} The number of seconds an oracle can mulfunction

- interactin
  File: IFacadeWrite.sol
  68: \* @notice A UX-friendly layer for interactin with the protocol

- unstakin
  File: IStRSR.sol
  76: /// Emitted if all the RSR in the unstakin pool is seized, and all ongoing unstaking is voided.