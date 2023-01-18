
# QA report

## Author: rotcivegaf

## Non-critical

| N-N    |Issue|Instances|
|:------:|:----|:-------:|
| [N&#x2011;01] | Typo | 28 |
| [N&#x2011;02] | Unused imports | 43 |
| [N&#x2011;03] | Use `delete` | 1 |
| [N&#x2011;04] | Use OZ libraries and don't copy/paste | 1 |
| [N&#x2011;05] | Unused interface | 11 |
| [N&#x2011;06] | Library inside interface file | 1 |
| [N&#x2011;07] | Event is missing `indexed` fields | 30 |
| [N&#x2011;08] | Remove mocks | 1 |
| [N&#x2011;09] | Use the same pragma | 9 |
| [N&#x2011;10] | Non-library/interface files should use fixed compiler versions, not floating ones | 6 |

## Non-critical

### [N‑01] Typo

```solidity
File: protocol/contracts/interfaces/IFacadeAct.sol

/// @audit: remove `v`
12:v */
```

```solidity
File: protocol/contracts/interfaces/IFacadeRead.sol

/// @audit: remove `v`
14:v */
```

```solidity
File: protocol/contracts/plugins/trading/GnosisTrade.sol

/// @audit: `acution` to `auction`
 47:    // from this trade's acution will all eventually go to origin.

/// @audit: `balancess` to `balance`
166:    //   transfer all balancess of `buy` and `sell` at this address to `origin`
```

```solidity
File: protocol/contracts/plugins/assets/FiatCollateral.sol

/// @audit: `mulfunction` to `malfunction`
21:    uint48 delayUntilDefault; // {s} The number of seconds an oracle can mulfunction
```

```solidity
File: protocol/contracts/plugins/aave/StaticATokenLM.sol

/// @audit: `amound` to `amount`
524:     * @return The amound of pending rewards in RAY
```

```solidity
File: protocol/contracts/mixins/Auth.sol

/// @audit: `predetemined` to `predetermined`
22:     * Typically freezing thaws on its own in a predetemined number of blocks.
```

```solidity
File: protocol/contracts/interfaces/IStRSR.sol

/// @audit: `paids` to `paid`
71:    /// Emitted whenever RSR are paids out

/// @audit: `unstakin` to `unstaking`
76:    /// Emitted if all the RSR in the unstakin pool is seized, and all ongoing unstaking is voided.
```

```solidity
File: protocol/contracts/interfaces/IRToken.sol

/// @audit: `redeeeming` to `redeeming`
 74:    /// @param redeemer The address of the account redeeeming RTokens

/// @audit: `reedemed` to `redeemed`
178:    /// Set the fraction of the RToken supply that can be reedemed at once
```

```solidity
File: protocol/contracts/interfaces/IFurnace.sol

/// @audit: `permisionlessly` to `permissionlessly`
8: * @notice A helper contract to burn RTokens slowly and permisionlessly.
```

```solidity
File: protocol/contracts/interfaces/IFacadeWrite.sol

/// @audit: `interactin` to `interacting`
68: * @notice A UX-friendly layer for interactin with the protocol
```

```solidity
File: protocol/contracts/interfaces/IFacadeRead.sol

/// @audit: `collaterazation` to `collateralization`
100:    /// @return backing The worst-case collaterazation % the protocol will have after done trading
```

```solidity
File: protocol/contracts/interfaces/IDeployer.sol

/// @audit: `parametrizations` to `parameterizations`
17: * meaning that after deployment there is freedom to allow parametrizations to deviate.
```

```solidity
File: protocol/contracts/interfaces/IAsset.sol

/// @audit: `implemntation` to `implementation`
86:    /// VERY IMPORTANT: In any valid implemntation, status() MUST become DISABLED in refresh() if
```

```solidity
File: protocol/contracts/p1/mixins/TradeLib.sol

/// @audit: `CollateralizatlionLib` to `CollateralizationLib`
43:        // checked for in RevenueTrader / CollateralizatlionLib
```

```solidity
File: protocol/contracts/p1/StRSRVotes.sol

/// @audit: `accountt` to `account`
30:    // _delegates[account] is the address of the delegate that `accountt` has specified
```

```solidity
File: protocol/contracts/p1/StRSR.sol

/// @audit: `appeneded` to `appended`
541:    //   r'.queue is r.queue with a new entry appeneded for (totalDrafts' - totalDraft) drafts

/// @audit: `Overriden` to `Overridden`
570:    /// Overriden in StRSRVotes to handle rebases
```

```solidity
File: protocol/contracts/p1/RToken.sol

/// @audit: `adjaacent` to `adjacent`
109:    // issuances, and so any particular issuance is actually the _difference_ between two adjaacent

/// @audit: `striaght` to `straight`
112:    // The way to keep an IssueQueue striaght in your head is to think of each TotalIssue item as a
```

```solidity
File: protocol/contracts/p1/Furnace.sol

/// @audit: `permisionlessly` to `permissionlessly`
10: * @notice A helper to melt RTokens slowly and permisionlessly.

/// @audit: `leriod` to `period`
66:    //   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay leriod)
```

```solidity
File: protocol/contracts/p1/BasketHandler.sol

/// @audit: `setPrimeBakset` to `setPrimeBasket`
127:    // event. config is only modified by governance through setPrimeBakset and setBackupConfig

/// @audit: `interepreted` to `interpreted`
537:        // As such, they're each interepreted as a map from target name -> target weight
```

```solidity
File: protocol/contracts/p1/BackingManager.sol

/// @audit: `Mointain` to `Maintain`
 89:    /// Mointain the overall backing policy; handout assets otherwise

/// @audit: `recollateralize` to `recollateralized`
247:    /// Compromise on how many baskets are needed in order to recollateralize-by-accounting
```


### [N‑02] Unused imports

```solidity
File: protocol/contracts/p1/BackingManager.sol

10:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/BasketHandler.sol

12:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/Broker.sol

10:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/Distributor.sol

9:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/Furnace.sol

4:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/Main.sol

6:import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
```

```solidity
File: protocol/contracts/p1/RToken.sol

8:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/StRSR.sol

 4:import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";

 6:import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";

13:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/mixins/RecollateralizationLib.sol

8:import "../../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/mixins/TradeLib.sol

4:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

6:import "../../interfaces/IAssetRegistry.sol";

8:import "../../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/p1/mixins/Trading.sol

10:import "../../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/libraries/RedemptionBattery.sol

4:import "../interfaces/IRToken.sol";
```

```solidity
File: protocol/contracts/interfaces/IAsset.sol

6:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/interfaces/IDeployer.sol

6:import "./IDistributor.sol";
```

```solidity
File: protocol/contracts/interfaces/IFurnace.sol

4:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/interfaces/IMain.sol

10:import "./IGnosis.sol";

16:import "./ITrading.sol";
```

```solidity
File: protocol/contracts/interfaces/IRewardable.sol

5:import "./IComponent.sol";
6:import "./IMain.sol";
```

```solidity
File:

7:import "../libraries/Fixed.sol";

8:import "./IAsset.sol";
```

```solidity
File: protocol/contracts/interfaces/IStRSR.sol

7:import "../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/interfaces/ITrading.sol

5:import "../libraries/Fixed.sol";

6:import "./IAsset.sol";
```

```solidity
File: protocol/contracts/mixins/ComponentRegistry.sol

5:import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";

6:import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

7:import "../interfaces/IMain.sol";
```

```solidity
File: protocol/contracts/plugins/assets/ATokenFiatCollateral.sol

5:import "../../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/plugins/assets/CTokenFiatCollateral.sol

7:import "../../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol

4:import "../../libraries/Fixed.sol";

6:import "./ICToken.sol";
```

```solidity
File: protocol/contracts/plugins/assets/EURFiatCollateral.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:import "../../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/plugins/assets/FiatCollateral.sol

6:import "../../libraries/Fixed.sol";

7:import "./FiatCollateral.sol";
```

```solidity
File: protocol/contracts/plugins/assets/NonFiatCollateral.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:import "../../libraries/Fixed.sol";
```

```solidity
File: protocol/contracts/plugins/assets/SelfReferentialCollateral.sol

4:import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

5:import "../../libraries/Fixed.sol";
```

### [N‑03] Use `delete`

Use `delete` instead of assign a default value(0, address(0), ...)

`a[b] = address(0);` => `delete(a[b]);`

```solidity
File: protocol/contracts/p1/AssetRegistry.sol

93:        assets[asset.erc20()] = IAsset(address(0));
```

### [N‑04] Use OZ libraries and don't copy/paste

The **protocol/contracts/plugins/aave/ReentrancyGuard.sol** contract can be imported directly from OZ

### [N‑05] Unused interface

The following interfaces are not used by the protocol:
- **TestIAsset** defined in **protocol/contracts/interfaces/IAsset.sol**
- **TestIBackingManager** defined in **protocol/contracts/interfaces/IBackingManager.sol**
- **TestIBroker** defined in **protocol/contracts/interfaces/IBroker.sol**
- **TestIDeployer** defined in **protocol/contracts/interfaces/IDeployer.sol**
- **TestIDistributor** defined in **protocol/contracts/interfaces/IDistributor.sol**
- **TestIFurnace** defined in **protocol/contracts/interfaces/IFurnace.sol**
- **TestIMain** defined in **protocol/contracts/interfaces/IRevenueTrader.sol**
- **TestIRevenueTrader** defined in **protocol/contracts/interfaces/IRevenueTrader.sol**
- **TestIRToken** defined in **protocol/contracts/interfaces/IRToken.sol**
- **TestIStRSR** defined in **protocol/contracts/interfaces/IStRSR.sol**
- **TestITrading** defined in **protocol/contracts/interfaces/ITrading.sol**


### [N‑06] Library inside interface file

Move the `CollateralStatusComparator` library on **protocol/contracts/interfaces/IAsset.sol** to separate file and to **libraries** folder

### [N‑07] Event is missing `indexed` fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

```solidity
File: protocol/contracts/interfaces/IBasketHandler.sol

21:    event PrimeBasketSet(IERC20[] erc20s, uint192[] targetAmts, bytes32[] targetNames);

28:    event BasketSet(uint256 indexed nonce, IERC20[] erc20s, uint192[] refAmts, bool disabled);

34:    event BackupConfigSet(bytes32 indexed targetName, uint256 indexed max, IERC20[] erc20s);
```

```solidity
File: protocol/contracts/interfaces/IDeployerRegistry.sol

7:    event DeploymentUnregistered(string version, IDeployer deployer);

8:    event DeploymentRegistered(string version, IDeployer deployer);

9:    event LatestChanged(string version, IDeployer deployer);
```

```solidity
File: protocol/contracts/interfaces/IDistributor.sol

28:    event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);

34:    event RevenueDistributed(IERC20 indexed erc20, address indexed source, uint256 indexed amount);
```

```solidity
File: protocol/contracts/interfaces/IRToken.sol

78:    event Redemption(address indexed redeemer, uint256 indexed amount, uint192 baskets);

83:    event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);

87    event Melted(uint256 amount);
```

```solidity
File: protocol/contracts/plugins/aave/IAaveIncentivesController.sol

6:    event RewardsAccrued(address indexed user, uint256 amount);

8:    event RewardsClaimed(address indexed user, address indexed to, uint256 amount);
```

```solidity
File: protocol/contracts/plugins/aave/IAToken.sol

15:    event Mint(address indexed from, uint256 value, uint256 index);

37:    event Burn(address indexed from, address indexed target, uint256 value, uint256 index);

46:    event BalanceTransfer(address indexed from, address indexed to, uint256 value, uint256 index);
```

```solidity
File: protocol/contracts/plugins/aave/IAToken.sol

15:    event Mint(address indexed from, uint256 value, uint256 index);

37:    event Burn(address indexed from, address indexed target, uint256 value, uint256 index);

46:    event BalanceTransfer(address indexed from, address indexed to, uint256 value, uint256 index);
```

```solidity
File: protocol/contracts/plugins/mocks/EasyAuction.sol

55:    event NewSellOrder(
56:        uint256 indexed auctionId,
57:        uint64 indexed userId,
58:        uint96 buyAmount,
59:        uint96 sellAmount
60:    );

61:    event CancellationSellOrder(
62:        uint256 indexed auctionId,
63:        uint64 indexed userId,
64:        uint96 buyAmount,
65:        uint96 sellAmount
66:    );

67:    event ClaimedFromOrder(
68:        uint256 indexed auctionId,
69:        uint64 indexed userId,
70:        uint96 buyAmount,
71:        uint96 sellAmount
72:    );

88:    event AuctionCleared(
89:        uint256 indexed auctionId,
90:        uint96 soldAuctioningTokens,
91:        uint96 soldBiddingTokens,
92:        bytes32 clearingPriceOrder
93:    );

94:    event UserRegistration(address indexed user, uint64 userId);
```

```solidity
File: protocol/contracts/plugins/mocks/WETH.sol

25:    event Approval(address indexed src, address indexed guy, uint256 wad);

26:    event Transfer(address indexed src, address indexed dst, uint256 wad);

27:    event Deposit(address indexed dst, uint256 wad);

28:    event Withdrawal(address indexed src, uint256 wad);
```

```solidity
File: protocol/contracts/plugins/mocks/vendor/EasyAuction.sol

 95:    event Transfer(address indexed from, address indexed to, uint256 value);

101:    event Approval(address indexed owner, address indexed spender, uint256 value);
```

### [N‑08] Remove mocks and tests

Move the mocks to a separate folder and don't send to auditing, this are mocks and only are useful to test the protocol
Also move the test files

- protocol/contracts/libraries/test/*
- protocol/contracts/plugins/mocks/*


### [N‑09] Update old pragma

Some contracts use an old pragma version update the version:

```solidity
File: protocol/contracts/plugins/aave/RayMathNoRounding.sol

2:pragma solidity 0.6.12;
```

```solidity
File: protocol/contracts/plugins/aave/StaticATokenErrors.sol

2:pragma solidity 0.6.12;
```

```solidity
File: protocol/contracts/plugins/mocks/vendor/EasyAuction.sol

2:pragma solidity 0.6.12;
```

```solidity
File: protocol/contracts/plugins/aave/StaticATokenLM.sol

2:pragma solidity 0.6.12;
```

```solidity
File: protocol/contracts/plugins/mocks/EasyAuction.sol

2:pragma solidity 0.6.12;
```

```solidity
File: protocol/contracts/plugins/aave/ERC20.sol

3:pragma solidity ^0.6.0;
```

```solidity
File: protocol/contracts/plugins/aave/IStaticATokenLM.sol

2:pragma solidity 0.6.12;
```

```solidity
File: protocol/contracts/plugins/aave/IAToken.sol

2:pragma solidity 0.6.12;
```

```solidity
File: protocol/contracts/plugins/aave/IAaveIncentivesController.sol

2:pragma solidity 0.6.12;
```

### [N‑10] Non-library/interface files should use fixed compiler versions, not floating ones

```solidity
File: protocol/contracts/plugins/aave/ERC20.sol

3:pragma solidity ^0.6.0;
```

```solidity
File: protocol/contracts/libraries/test/StringCallerMock.sol

2:pragma solidity ^0.8.9;
```

```solidity
File: protocol/contracts/libraries/test/FixedCallerMock.sol

2:pragma solidity ^0.8.9;
```

```solidity
File: protocol/contracts/libraries/test/ArrayCallerMock.sol

2:pragma solidity ^0.8.9;
```

```solidity
File: protocol/contracts/plugins/mocks/ChainlinkMock.sol

2:pragma solidity ^0.8.9;
```

```solidity
File: protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol

2:pragma solidity ^0.8.0;
```
