## Summary<a name="Summary">

### Gas Optimizations
| |Issue|Contexts|Estimated Gas Saved|
|-|:-|:-|:-:|
| [GAS&#x2011;1](#GAS&#x2011;1) | `abi.encode()` is less efficient than `abi.encodepacked()` | 7 | 700 |
| [GAS&#x2011;2](#GAS&#x2011;2) | Use calldata instead of memory for function parameters | 9 | 2700 |
| [GAS&#x2011;3](#GAS&#x2011;3) | State variables only set in the `constructor` should be declared immutable | 5 | - |
| [GAS&#x2011;4](#GAS&#x2011;4) | Setting the `constructor` to `payable` | 17 | 221 |
| [GAS&#x2011;5](#GAS&#x2011;5) | Do not calculate constants | 6 | - |
| [GAS&#x2011;6](#GAS&#x2011;6) | Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function | 26 | 728 |
| [GAS&#x2011;7](#GAS&#x2011;7) | Empty Blocks Should Be Removed Or Emit Something | 9 | - |
| [GAS&#x2011;8](#GAS&#x2011;8) | Using fixed bytes is cheaper than using `string` | 10 | - |
| [GAS&#x2011;9](#GAS&#x2011;9) | Using `delete` statement can save gas | 11 | - |
| [GAS&#x2011;10](#GAS&#x2011;10) | Use `assembly` to write address storage values | 24 | - |
| [GAS&#x2011;11](#GAS&#x2011;11) | `internal` functions only called once can be inlined to save gas | 29 | - |
| [GAS&#x2011;12](#GAS&#x2011;12) | Optimize names to save gas | 32 | 704 |
| [GAS&#x2011;13](#GAS&#x2011;13) | `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables | 36 | - |
| [GAS&#x2011;14](#GAS&#x2011;14-) | Public Functions To External | 43 | - |
| [GAS&#x2011;15](#GAS&#x2011;15) | `require()` Should Be Used Instead Of `assert()` | 18 | - |
| [GAS&#x2011;16](#GAS&#x2011;16) | Splitting `require()` Statements That Use `&&` Saves Gas | 12 | 108 |
| [GAS&#x2011;17](#GAS&#x2011;17) | Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It | 10 | - |
| [GAS&#x2011;18](#GAS&#x2011;18) | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 209 | - |
| [GAS&#x2011;19](#GAS&#x2011;19) | `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops | 50 | 1750 |
| [GAS&#x2011;20](#GAS&#x2011;20) | Using `unchecked` blocks to save gas | 16 | 2176 |
| [GAS&#x2011;21](#GAS&#x2011;21) | Use v4.8.0 OpenZeppelin contracts | 2 | - |
| [GAS&#x2011;22](#GAS&#x2011;22) | Use solidity version 0.8.17 to gain some gas boost | 75 | 6600 |
| [GAS&#x2011;23](#GAS&#x2011;23) | Using `storage` instead of `memory` saves gas | 1 | 350 |
| [GAS&#x2011;24](#GAS&#x2011;24) | Using `10**X` for constants isn't gas efficient  | 2 | - |
| [GAS&#x2011;25](#GAS&#x2011;25) | Struct packing | 1 | - |

Total: 660 contexts over 25 issues

## Gas Optimizations

### <a href="#Summary">[GAS&#x2011;1]</a><a name="GAS&#x2011;1"> `abi.encode()` is less efficient than `abi.encodepacked()`

See for more information: https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison 

#### <ins>Proof Of Concept</ins>


```solidity
778: abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L778

```solidity
128: _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L128

```solidity
151: abi.encode(PERMIT_TYPEHASH, owner, spender, value, currentValidNonce, deadline)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L151

```solidity
179: abi.encode(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L179

```solidity
219: abi.encode(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L219

```solidity
269: abi.encode(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L269

```solidity
84: abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L84






### <a href="#Summary">[GAS&#x2011;2]</a><a name="GAS&#x2011;2"> Use calldata instead of memory for function parameters

In some cases, having function arguments in calldata instead of
memory is more optimal.

Consider the following generic example:
```
contract C {
    function add(uint[] memory arr) external returns (uint sum) {
        uint length = arr.length;
        for (uint i = 0; i < arr.length; i++) {
            sum += arr[i];
        }
    }
}
```
In the above example, the dynamic array arr has the storage location
memory. When the function gets called externally, the array values are
kept in calldata and copied to memory during ABI decoding (using the
opcode calldataload and mstore). And during the for loop, arr[i]
accesses the value in memory using a mload. However, for the above
example this is inefficient. Consider the following snippet instead:
```
contract C {
    function add(uint[] calldata arr) external returns (uint sum) {
        uint length = arr.length;
        for (uint i = 0; i < arr.length; i++) {
            sum += arr[i];
        }
    }
}
```
In the above snippet, instead of going via memory, the value is directly
read from calldata using calldataload. That is, there are no
intermediate memory operations that carries this value.

Gas savings: In the former example, the ABI decoding begins with
copying value from calldata to memory in a for loop. Each iteration
would cost at least 60 gas. In the latter example, this can be
completely avoided. This will also reduce the number of instructions and
therefore reduces the deploy time cost of the contract.

In short, use calldata instead of memory if the function argument
is only read.

Note that in older Solidity versions, changing some function arguments
from memory to calldata may cause "unimplemented feature error".
This can be avoided by using a newer (0.8.*) Solidity compiler.

Examples
Note: The following pattern is prevalent in the codebase:
```
function f(bytes memory data) external {
    (...) = abi.decode(data, (..., types, ...));
}
```
Here, changing to bytes calldata will decrease the gas. The total
savings for this change across all such uses would be quite
significant.

#### <ins>Proof Of Concept</ins>


```solidity
function allUnique(IERC20[] memory arr) internal pure returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L9

```solidity
function sortedAndAllUnique(IERC20[] memory arr) internal pure returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L21

```solidity
function allUnique(IERC20[] memory arr) public pure returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol#L8

```solidity
function sortedAndAllUnique(IERC20[] memory arr) public pure returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol#L12

```solidity
function propose(
        address[] memory targets,
        uint256[] memory values,
        bytes[] memory calldatas,
        string memory description
    ) public override(Governor, IGovernor) returns (uint256 proposalId) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L85

```solidity
function queue(
        address[] memory targets,
        uint256[] memory values,
        bytes[] memory calldatas,
        bytes32 descriptionHash
    ) public override returns (uint256 proposalId) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L95

```solidity
function cancel(
        address[] memory targets,
        uint256[] memory values,
        bytes[] memory calldatas,
        bytes32 descriptionHash
    ) external {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L105

```solidity
function _execute(
        uint256 proposalId,
        address[] memory targets,
        uint256[] memory values,
        bytes[] memory calldatas,
        bytes32 descriptionHash
    ) internal override(Governor, GovernorTimelockControl) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L115

```solidity
function _cancel(
        address[] memory targets,
        uint256[] memory values,
        bytes[] memory calldatas,
        bytes32 descriptionHash
    ) internal override(Governor, GovernorTimelockControl) returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L126






### <a href="#Summary">[GAS&#x2011;3]</a><a name="GAS&#x2011;3"> State variables only set in the `constructor` should be declared immutable

Avoids a Gsset (20000 gas) in the constructor, and replaces each Gwarmacces (100 gas) with a PUSH32 (3 gas).

#### <ins>Proof Of Concept</ins>

```solidity
70: implementations = implementations_
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L70

```solidity
84: LENDING_POOL = pool
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L84

```solidity
85: ATOKEN = IERC20(aToken)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L85

```solidity
95: INCENTIVES_CONTROLLER = incentivesController
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L95

```solidity
96: REWARD_TOKEN = IERC20(INCENTIVES_CONTROLLER.REWARD_TOKEN())
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L96



### <a href="#Summary">[GAS&#x2011;4]</a><a name="GAS&#x2011;4"> Setting the `constructor` to `payable`

Saves ~13 gas per instance

#### <ins>Proof Of Concept</ins>

```solidity
42: constructor(
        IERC20Metadata rsr_,
        IGnosis gnosis_,
        IAsset rsrAsset_,
        Implementations memory implementations_
    )
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L42

```solidity
23: constructor() initializer
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L23

```solidity
25: constructor() initializer
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L25

```solidity
57: constructor(string memory name, string memory symbol) public
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L57

```solidity
38: constructor() internal
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L38

```solidity
78: constructor(
        ILendingPool pool,
        address aToken,
        string memory staticATokenName,
        string memory staticATokenSymbol
    ) public
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L78

```solidity
40: constructor(
        uint48 priceTimeout_,
        AggregatorV3Interface chainlinkFeed_,
        uint192 oracleError_,
        IERC20Metadata erc20_,
        uint192 maxTradeVolume_,
        uint48 oracleTimeout_
    )
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L40

```solidity
40: constructor(CollateralConfig memory config) FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L40

```solidity
25: constructor(CollateralConfig memory config, IComptroller comptroller_) FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L25

```solidity
23: constructor(
        CollateralConfig memory config,
        AggregatorV3Interface targetUnitChainlinkFeed_,
        IComptroller comptroller_
    ) CTokenFiatCollateral(config, comptroller_)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L23

```solidity
24: constructor(
        CollateralConfig memory config,
        uint8 referenceERC20Decimals_,
        IComptroller comptroller_
    ) SelfReferentialCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L24

```solidity
21: constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
        FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L21

```solidity
63: constructor(CollateralConfig memory config)
        Asset(
            config.priceTimeout,
            config.chainlinkFeed,
            config.oracleError,
            config.erc20,
            config.maxTradeVolume,
            config.oracleTimeout
        )
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L63

```solidity
21: constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
        FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L21

```solidity
27: constructor(IRToken erc20_, uint192 maxTradeVolume_)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L27

```solidity
18: constructor(CollateralConfig memory config) FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L18

```solidity
31: constructor(
        IStRSRVotes token_,
        TimelockController timelock_,
        uint256 votingDelay_, // in blocks
        uint256 votingPeriod_, // in blocks
        uint256 proposalThresholdAsMicroPercent_, // e.g. 1e4 for 0.01%
        uint256 quorumPercent // e.g 4 for 4%
    )
        Governor("Governor Alexios")
        GovernorSettings(votingDelay_, votingPeriod_, proposalThresholdAsMicroPercent_)
        GovernorVotes(IVotes(address(token_)))
        GovernorVotesQuorumFraction(quorumPercent)
        GovernorTimelockControl(timelock_)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L31





### <a href="#Summary">[GAS&#x2011;5]</a><a name="GAS&#x2011;5"> Do not calculate constants

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

#### <ins>Proof Of Concept</ins>

```solidity
48: uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L48

```solidity
310: uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L310

```solidity
117: uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L117


```solidity
65: uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L65

```solidity
87: uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L87


```solidity
34: uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100; // {tok}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L34





#### <a href="#Summary">[GAS&#x2011;6]</a><a name="GAS&#x2011;6"> Duplicated `require()`/`revert()` Checks Should Be Refactored To A Modifier Or Function

Saves deployment costs

#### <ins>Proof Of Concept</ins>

```solidity
103: require(_erc20s.contains(address(erc20)), "erc20 unregistered");
111: require(_erc20s.contains(address(erc20)), "erc20 unregistered");

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L103

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L111



```solidity
215: requireValidCollArray(erc20s);
257: requireValidCollArray(erc20s);
230: require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
265: require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L215

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L257

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L230

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L265



```solidity
217: require(status == CollateralStatus.SOUND, "basket unsound");
384: require(status == CollateralStatus.SOUND, "basket unsound");
410: require(queue.left <= endId && endId <= queue.right, "out of range");
741: require(queue.left <= endId && endId <= queue.right, "out of range");
558: require(_msgSender() == address(backingManager), "not backing manager");
581: require(_msgSender() == address(backingManager), "not backing manager");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L217

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L384

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L741

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L558

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L581




```solidity
816: require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
824: require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L816

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L824



```solidity
77: require(blockNumber < block.number, "ERC20Votes: block not yet mined");
83: require(blockNumber < block.number, "ERC20Votes: block not yet mined");
89: require(blockNumber < block.number, "ERC20Votes: block not yet mined");

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L77

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L83

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L89



```solidity
142: require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
210: require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
144: require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
172: require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
212: require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
294: require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);
317: require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L142

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L210

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L144

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L172

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L212

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L294

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L317



```solidity
102: require(startedInSameEra(proposalId), "new era");
123: require(startedInSameEra(proposalId), "new era");

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L102

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L123








### <a href="#Summary">[GAS&#x2011;7]</a><a name="GAS&#x2011;7"> Empty Blocks Should Be Removed Or Emit Something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

#### <ins>Proof Of Concept</ins>

```solidity
64: function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L64

```solidity
452: try main.furnace().melt() {} catch {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L452

```solidity
838: function requireNotPausedOrFrozen() private notPausedOrFrozen {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L838

```solidity
57: function _authorizeUpgrade(address newImplementation) internal view override governance {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L57

```solidity
346: ) internal virtual {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L346

```solidity
164: function claimRewards() external virtual {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L164

```solidity
40: constructor(CollateralConfig memory config) FiatCollateral(config) {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L40

```solidity
119: function claimRewards() external virtual {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L119

```solidity
67: function __ERC20Permit_init_unchained(string memory) internal onlyInitializing {}
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L67






### <a href="#Summary">[GAS&#x2011;8]</a><a name="GAS&#x2011;8"> Using fixed bytes is cheaper than using `string`

As a rule of thumb, use `bytes` for arbitrary-length raw byte data and string for arbitrary-length `string` (UTF-8) data. If you can limit the length to a certain number of bytes, always use one of `bytes1` to `bytes32` because they are much cheaper.

#### <ins>Proof Of Concept</ins>


```solidity
31: string public constant ENS = "reserveprotocol.eth";
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L31

```solidity
206: string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L206

```solidity
207: string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L207

```solidity
5: string public constant INVALID_OWNER = "1";
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L5

```solidity
6: string public constant INVALID_EXPIRATION = "2";
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L6

```solidity
7: string public constant INVALID_SIGNATURE = "3";
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L7

```solidity
8: string public constant INVALID_DEPOSITOR = "4";
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L8

```solidity
9: string public constant INVALID_RECIPIENT = "5";
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L9

```solidity
10: string public constant INVALID_CLAIMER = "6";
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L10

```solidity
11: string public constant ONLY_ONE_AMOUNT_FORMAT_ALLOWED = "7";
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L11






### <a href="#Summary">[GAS&#x2011;9]</a><a name="GAS&#x2011;9"> Using `delete` statement can save gas

#### <ins>Proof Of Concept</ins>

```solidity
606: uint256 assigned = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L606

```solidity
693: queue.left = 0;
694: queue.right = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L693-L694

```solidity
784: queue.left = 0;
785: queue.right = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L784-L785

```solidity
575: stakeRSR = 0;
576: totalStakes = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L575-L576

```solidity
587: draftRSR = 0;
588: totalDrafts = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L587-L588

```solidity
102: uint256 low = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L102

```solidity
462: _unclaimedRewards[onBehalfOf] = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L462




### <a href="#Summary">[GAS&#x2011;10]</a><a name="GAS&#x2011;10"> Use `assembly` to write address storage values

#### <ins>Proof Of Concept</ins>

```solidity
58: _name = name;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L58

```solidity
59: _symbol = symbol;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L59

```solidity
325: _decimals = decimals_;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L325

```solidity
39: _status = _NOT_ENTERED;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L39

```solidity
60: _status = _NOT_ENTERED;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L60

```solidity
54: _status = _ENTERED;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L54

```solidity
87: _name = staticATokenName;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L87

```solidity
88: _symbol = staticATokenSymbol;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L88

```solidity
399: _lifetimeRewards = lifetimeRewards;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L399

```solidity
429: _lifetimeRewards = lifetimeRewards;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L429

```solidity
432: _lifetimeRewardsClaimed = lifetimeRewards;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L432

```solidity
48: _whenDefault = NEVER;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L48

```solidity
186: _whenDefault = NEVER;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L186

```solidity
189: _whenDefault = NEVER;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L189







### <a href="#Summary">[GAS&#x2011;11]</a><a name="GAS&#x2011;11"> `internal` functions only called once can be inlined to save gas

#### <ins>Proof Of Concept</ins>

```solidity
66: function worseThan

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L66


```solidity
19: function __ComponentRegistry_init

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L19

```solidity
75: function setFrom

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L75

```solidity
64: function _authorizeUpgrade

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L64

```solidity
828: function _beforeTokenTransfer

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L828

```solidity
543: function pushDraft

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L543

```solidity
694: function _mint

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L694

```solidity
708: function _burn

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L708

```solidity
739: function _spendAllowance

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L739

```solidity
795: function _useNonce

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L795

```solidity
49: function beginEra

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L49

```solidity
137: function _mint

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L137

```solidity
142: function _burn

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L142

```solidity
147: function _afterTokenTransfer

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L147

```solidity
33: function __Component_init

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L33

```solidity
57: function _authorizeUpgrade

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L57

```solidity
140: function isEnoughToSell

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L140

```solidity
154: function safeMulDivCeil

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L154

```solidity
44: function __Trading_init

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L44

```solidity
111: function tryTrade

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L111


```solidity
19: function rayDivNoRounding

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol#L19

```solidity
25: function rayToWadNoRounding

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol#L25

```solidity
358: function _beforeTokenTransfer

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L358

```solidity
199: function alreadyDefaulted

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L199

```solidity
115: function _execute

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L115

```solidity
145: function _getVotes

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L145

```solidity
61: function __ERC20Permit_init
67: function __ERC20Permit_init

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L61

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L67



```solidity
67: function __ERC20Permit_init_unchained

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L67

```solidity
116: function _useNonce

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L116










### <a href="#Summary">[GAS&#x2011;12]</a><a name="GAS&#x2011;12"> Optimize names to save gas

Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. 

See more <a href="https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92">here</a>

#### <ins>Proof Of Concept</ins>

All in-scope contracts

#### <ins>Recommended Mitigation Steps</ins>
Find a lower method ID name for the most called functions for example Call() vs. Call1() is cheaper by 22 gas
For example, the function IDs in the Gauge.sol contract will be the most used; A lower method ID may be given.



### <a href="#Summary">[GAS&#x2011;13]</a><a name="GAS&#x2011;13"> `<x> += <y>` Costs More Gas Than `<x> = <x> + <y>` For State Variables

#### <ins>Proof Of Concept</ins>


```solidity
104: shiftLeft += 18;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L104

```solidity
392: decimals -= 18;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L392

```solidity
504: if (mm > lo) hi -= 1;
505: lo -= mm;
509: lo += hi * ((0 - pow2) / pow2 + 1);
537: if (mm > 0) result += 1;
539: if (mm > ((z - 1) / 2)) result += 1;
555: if (mm < lo) hi -= 1;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L504

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L505

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L509

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L537

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L539

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L555



```solidity
136: longFreezes[_msgSender()] -= 1;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L136

```solidity
345: low256 += quantityMulPrice(qty, lowP);
346: high256 += quantityMulPrice(qty, highP);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L345-L346

```solidity
636: nonce += 1;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L636

```solidity
145: revTotals.rTokenTotal += share.rTokenDist;
146: revTotals.rsrTotal += share.rsrDist;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L145-L146

```solidity
81: lastPayout += numPeriods * period;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L81

```solidity
330: liabilities[IERC20(erc20s[i])] += deposits[i];

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L330

```solidity
678: liabilities[IERC20(queue.tokens[i])] -= amt[i];
678: liabilities[IERC20(queue.tokens[i])] -= amt[i];

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L678

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L678



```solidity
761: liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];
761: liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L761

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L761



```solidity
229: stakeRSR += rsrAmount;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L229

```solidity
387: stakeRSR -= stakeRSRToTake;
396: seizedRSR += stakeRSR;
402: draftRSR -= draftRSRToTake;
403: seizedRSR += draftRSRToTake;
412: seizedRSR += draftRSR;
417: seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L387

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L396

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L402

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L403

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L412

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L417



```solidity
513: stakeRSR += payout;
516: payoutLastPaid += numPeriods * rewardPeriod;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L513

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L516



```solidity
549: draftRSR += rsrAmount;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L549

```solidity
684: eraStakes[to] += amount;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L684

```solidity
698: stakes[era][account] += amount;
699: totalStakes += amount;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L698-L699

```solidity
721: totalStakes -= amount;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L721

```solidity
264: assetsLow += low.mul(bal, FLOOR);
271: else assetsHigh += val;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L264

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L271









### <a href="#Summary">[GAS&#x2011;14]</a><a name="GAS&#x2011;14"> Public Functions To External

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

#### <ins>Proof Of Concept</ins>

```solidity
function pausedOrFrozen() public view returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L107

```solidity
function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L180

```solidity
function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L187

```solidity
function version() public pure virtual override returns (string memory) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Versioned.sol#L11

```solidity
function refresh() public {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L46

```solidity
function setTradingDelay(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256

```solidity
function setBackingBuffer(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L263

```solidity
function status() public view returns (CollateralStatus status_) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L279

```solidity
function quantity(IERC20 erc20) public view returns (uint192) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L296

```solidity
function basketsHeldBy(address account) public view returns (uint192 baskets) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L432

```solidity
function setAuctionLength(uint48 newAuctionLength) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L133

```solidity
function totals() public view returns (RevenueTotals memory revTotals) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L141

```solidity
function setPeriod(uint48 period_) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L88

```solidity
function setRatio(uint192 ratio_) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L96

```solidity
function init(
        Components memory components,
        IERC20 rsr_,
        uint48 shortFreeze_,
        uint48 longFreeze_
    ) public virtual initializer {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L26

```solidity
function setIssuanceRate(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L589

```solidity
function setScalingRedemptionRate(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L602

```solidity
function setRedemptionRateFloor(uint256 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L615

```solidity
function exchangeRate() public view returns (uint192) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L425

```solidity
function totalSupply() public view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L604

```solidity
function balanceOf(address account) public view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L608

```solidity
function transfer(address to, uint256 amount) public returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L622

```solidity
function approve(address spender, uint256 amount) public returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L632

```solidity
function transferFrom(
        address from,
        address to,
        uint256 amount
    ) public returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L641

```solidity
function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L651

```solidity
function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L657

```solidity
function permit(
        address owner,
        address spender,
        uint256 value,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L766

```solidity
function nonces(address owner) public view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L786

```solidity
function setUnstakingDelay(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812

```solidity
function setRewardPeriod(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L820

```solidity
function setRewardRatio(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L828

```solidity
function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L59

```solidity
function numCheckpoints(address account) public view returns (uint48) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L63

```solidity
function delegates(address account) public view returns (address) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L67

```solidity
function getVotes(address account) public view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L71

```solidity
function getPastVotes(address account, uint256 blockNumber) public view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L76

```solidity
function getPastTotalSupply(uint256 blockNumber) public view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L82

```solidity
function getPastEra(uint256 blockNumber) public view returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L88

```solidity
function delegate(address delegatee) public {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L114

```solidity
function delegateBySig(
        address delegatee,
        uint256 nonce,
        uint256 expiry,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L118

```solidity
function setMaxTradeSlippage(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L128

```solidity
function setMinTradeVolume(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L135

```solidity
function nonces(address owner) public view virtual override returns (uint256) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L99






### <a href="#Summary">[GAS&#x2011;15]</a><a name="GAS&#x2011;15"> `require()` Should Be Used Instead Of `assert()`

#### <ins>Proof Of Concept</ins>


```solidity
249: assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249

```solidity
556: assert(targetIndex < targetsLength);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556

```solidity
696: assert(totalStakes + amount < type(uint224).max);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696

```solidity
110: assert(doTrade);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110

```solidity
78: assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78

```solidity
102: assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L102

```solidity
44: assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44

```solidity
168: assert(errorCode == 0x11 || errorCode == 0x12);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L168

```solidity
170: assert(keccak256(reason) == UIntOutofBoundsHash);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L170

```solidity
114: assert(address(trades[sell]) == address(0));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L114

```solidity
345: assert(amt == amountToWithdraw);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L345

```solidity
98: assert(low == 0);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L98

```solidity
112: assert(low <= high);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L112

```solidity
147: assert(lotLow <= lotHigh);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L147

```solidity
137: assert(low == 0);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L137

```solidity
74: assert(low <= high);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L74

```solidity
98: assert(origin_ != address(0));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L98

```solidity
182: assert(isAuctionCleared());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L182






### <a href="#Summary">[GAS&#x2011;16]</a><a name="GAS&#x2011;16"> Splitting `require()` statements that use `&&` saves gas

Instead of using operator `&&` on a single `require`. Using a two `require` can save more gas.

i.e.
for `require(version == 1 && _bytecodeHash[1] == bytes1(0), "zf");` use:

```
    require(version == 1);
    require(_bytecodeHash[1] == bytes1(0));
```

#### <ins>Proof Of Concept</ins>

```solidity
181: require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181

```solidity
188: require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188

```solidity
109: require(owner != address(0) && owner != address(this), "invalid owner");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109

```solidity
89: require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89

```solidity
72: require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72

```solidity
410: require(queue.left <= endId && endId <= queue.right, "out of range");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410

```solidity
590: require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590

```solidity
623: require(index >= item.left && index < item.right, "out of range");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L623

```solidity
741: require(queue.left <= endId && endId <= queue.right, "out of range");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L741

```solidity
813: require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813

```solidity
813: require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813

```solidity
821: require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L821





### <a href="#Summary">[GAS&#x2011;17]</a><a name="GAS&#x2011;17"> Help The Optimizer By Saving A Storage Variable's Reference Instead Of Repeatedly Fetching It

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

#### <ins>Proof Of Concept</ins>


```solidity
258: BackupConfig storage conf = config.backups[targetName];
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L258

```solidity
562: totalWeights[targetIndex] = totalWeights[targetIndex].plus(targetWeight);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L562

```solidity
565: goodWeights[targetIndex] = goodWeights[targetIndex].plus(targetWeight);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L565

```solidity
723: BackupConfig storage backup = config.backups[targetName];
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L723

```solidity
112: ? distribution[addrTo].rsrDist
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L112

```solidity
113: : distribution[addrTo].rTokenDist;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L113

```solidity
199: IssueQueue storage queue = issueQueues[recipient];
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L199

```solidity
199: queue = issueQueues[recipient];
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L199

```solidity
662: IssueQueue storage queue = issueQueues[account];
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L662

```solidity
738: IssueQueue storage queue = issueQueues[account];
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L738






### <a href="#Summary">[GAS&#x2011;18]</a><a name="GAS&#x2011;18"> Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract's gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra 22-28 gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

#### <ins>Proof Of Concept</ins>


```solidity
24: uint192 minTradeVolume;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L24

```solidity
25: uint192 rTokenMaxTradeVolume;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L25

```solidity
28: uint48 shortFreeze;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L28

```solidity
29: uint48 longFreeze;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L29

```solidity
32: uint192 rewardRatio;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L32

```solidity
33: uint48 rewardPeriod;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L33

```solidity
36: uint48 unstakingDelay;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L36

```solidity
39: uint48 tradingDelay;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L39

```solidity
40: uint48 auctionLength;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L40

```solidity
41: uint192 backingBuffer;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L41

```solidity
42: uint192 maxTradeSlippage;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L42

```solidity
45: uint192 issuanceRate;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L45

```solidity
46: uint192 scalingRedemptionRate;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L46

```solidity
8: uint16 rTokenDist;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L8

```solidity
9: uint16 rsrDist;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L9

```solidity
14: uint24 rTokenTotal;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L14

```solidity
15: uint24 rsrTotal;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L15

```solidity
28: uint192[] weights;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeWrite.sol#L28

```solidity
16: uint96 volumeClearingPriceOrder;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol#L16

```solidity
50: uint192 constant FIX_ZERO = 0;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L50

```solidity
310: uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L310

```solidity
364: uint192 diff = x <= y ? y - x : x - y;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L364

```solidity
24: uint192 scalingRedemptionRate;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L24

```solidity
27: uint48 lastBlock;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L27

```solidity
64: uint48 blocks = uint48(block.number) - battery.lastBlock;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L64

```solidity
36: uint48 public unfreezeAt;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L36

```solidity
37: uint48 public shortFreeze;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L37

```solidity
38: uint48 public longFreeze;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L38

```solidity
90: uint192 quantity = basketHandler.quantity(asset.erc20());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L90

```solidity
33: uint48 public constant MAX_TRADING_DELAY = 31536000;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33

```solidity
34: uint192 public constant MAX_BACKING_BUFFER = FIX_ONE;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L34

```solidity
36: uint48 public tradingDelay;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L36

```solidity
37: uint192 public backingBuffer;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L37

```solidity
113: uint48 basketTimestamp = basketHandler.timestamp();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L113

```solidity
188: uint192 needed;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L188

```solidity
191: uint192 held = basketHandler.basketsHeldBy(address(this));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L191

```solidity
194: uint192 totalSupply = _safeWrap(rToken.totalSupply());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L194

```solidity
197: uint192 extraBUs = held.minus(needed);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L197

```solidity
200: uint192 rTok = (needed > 0) ? extraBUs.mulDiv(totalSupply, needed) : extraBUs;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L200

```solidity
224: uint192 req = needed.mul(basketHandler.quantity(erc20s[i]), CEIL);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L224

```solidity
117: uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L117

```solidity
134: uint48 public override nonce;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L134

```solidity
135: uint48 public override timestamp;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L135

```solidity
169: uint192[] memory refAmts = new uint192[](basket.erc20s.length);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L169

```solidity
441: uint192 refPerTok = coll.refPerTok();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L441

```solidity
338: uint192 qty = quantity(basket.erc20s[i]);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L338

```solidity
447: uint192 bal = coll.bal(account);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L447

```solidity
450: uint192 q = basket.refAmts[basket.erc20s[i]].div(refPerTok, CEIL);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L450

```solidity
541: uint192[] memory goodWeights = new uint192[](targetsLength);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L541

```solidity
545: uint192[] memory totalWeights = new uint192[](targetsLength);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L545

```solidity
561: uint192 targetWeight = config.targetAmts[erc20];
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L561

```solidity
608: uint192 needed = totalWeights[i].minus(goodWeights[i]);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L608

```solidity
642: uint192[] memory refAmts = new uint192[](basketLength);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L642

```solidity
24: uint48 public constant MAX_AUCTION_LENGTH = 604800;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24

```solidity
37: uint48 public auctionLength;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L37

```solidity
34: uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L34

```solidity
15: uint192 public constant MAX_RATIO = FIX_ONE;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L15

```solidity
16: uint48 public constant MAX_PERIOD = 31536000;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L16

```solidity
18: uint192 public ratio;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L18

```solidity
19: uint48 public period;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L19

```solidity
21: uint48 public lastPayout;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L21

```solidity
74: uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L74

```solidity
77: uint192 payoutRatio = FIX_ONE.minus(FIX_ONE.minus(ratio).powu(numPeriods));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L77

```solidity
50: uint192 public issuanceRate;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L50

```solidity
67: uint192 public basketsNeeded;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L67

```solidity
72: uint192 private allVestAt;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L72

```solidity
76: uint192 private lastIssRate;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L76

```solidity
81: uint192 when;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L81

```solidity
745: uint192 amtBaskets;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L745

```solidity
387: uint48 basketNonce = basketHandler.nonce();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L387

```solidity
243: uint192 vestingEnd = whenFinished(amtRToken);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L243

```solidity
259: uint192 newBasketsNeeded = basketsNeeded + amtBaskets;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L259

```solidity
358: uint192 before = allVestAt;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L358

```solidity
360: uint192 nowStart = uint192(FIX_ONE * (block.number - 1));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L360

```solidity
454: uint192 basketsNeeded_ = basketsNeeded;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L454

```solidity
462: uint192 baskets = uint192(mulDiv256(basketsNeeded_, amount, supply));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L462

```solidity
475: uint192 prorate = uint192((FIX_ONE_256 * amount) / supply);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L475

```solidity
37: uint48 public constant MAX_UNSTAKING_DELAY = 31536000;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L37

```solidity
38: uint48 public constant MAX_REWARD_PERIOD = 31536000;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L38

```solidity
39: uint192 public constant MAX_REWARD_RATIO = FIX_ONE;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L39

```solidity
45: uint8 public constant decimals = 18;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L45

```solidity
63: uint192 public stakeRate;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L63

```solidity
65: uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L65

```solidity
77: uint176 drafts;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L77

```solidity
78: uint64 availableAt;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L78

```solidity
85: uint192 public draftRate;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L85

```solidity
87: uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L87

```solidity
134: uint48 public unstakingDelay;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L134

```solidity
135: uint48 public rewardPeriod;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L135

```solidity
136: uint192 public rewardRatio;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L136

```solidity
147: uint48 public payoutLastPaid;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L147

```solidity
315: uint192 oldDrafts = firstId > 0 ? queue[firstId - 1].drafts : 0;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L315

```solidity
316: uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L316

```solidity
500: uint192 initRate = exchangeRate();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L500

```solidity
498: uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L498

```solidity
509: uint192 payoutRatio = FIX_ONE - FixLib.powu(FIX_ONE - rewardRatio, numPeriods);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L509

```solidity
559: uint192 oldDrafts = index > 0 ? queue[index - 1].drafts : 0;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L559

```solidity
560: uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L560

```solidity
23: uint48 fromBlock;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L23

```solidity
24: uint224 val;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L24

```solidity
25: uint192 minTradeVolume;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L25

```solidity
26: uint192 maxTradeSlippage;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L26

```solidity
32: uint192 sellAmount;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L32

```solidity
33: uint192 buyAmount;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L33

```solidity
34: uint192 sellPrice;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L34

```solidity
35: uint192 buyPrice;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L35

```solidity
117: uint192 top;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L117

```solidity
118: uint192 bottom;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L118

```solidity
172: uint192 basketsHigh = components.bm.safeMulDivCeil(FIX_ONE, assetsHigh, basketPriceLow);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L172

```solidity
185: uint192 shortfall = collateralShortfall(components, range.top);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L185

```solidity
236: uint192 potentialDustLoss;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L236

```solidity
332: uint192 bal = reg.assets[i].bal(address(components.bm));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L332

```solidity
269: uint192 val = components.bm.safeMulDivCeil(high, bal, FIX_ONE);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L269

```solidity
286: uint192 surplus;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L286

```solidity
287: uint192 deficit;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L287

```solidity
336: uint192 needed = range.top.mul(components.bh.quantity(reg.erc20s[i]), CEIL);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L336

```solidity
338: uint192 low;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L338

```solidity
342: uint192 high;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L342

```solidity
352: uint192 delta = bal.minus(needed).mul(lotLow, FLOOR);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L352

```solidity
382: uint192 amtShort = needed.minus(bal);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L382

```solidity
386: uint192 delta = amtShort.mul(high, CEIL);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L386

```solidity
442: uint192 needed = basketsTop.mul(components.bh.quantity(basketERC20s[i]), CEIL);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L442

```solidity
445: uint192 held = coll.bal(address(components.bm));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L445

```solidity
54: uint192 maxSell = maxTradeSize(trade.sell, lotHigh);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L54

```solidity
55: uint192 s = trade.sellAmount > maxSell ? maxSell : trade.sellAmount;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L55

```solidity
122: uint192 exactSellAmount = trade.buyAmount.mulDiv(trade.buyPrice, trade.sellPrice, CEIL);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L122

```solidity
182: uint192 size = price == 0 ? FIX_MAX : minTradeVolume.div(price, CEIL);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L182

```solidity
191: uint192 size = price == 0 ? FIX_MAX : asset.maxTradeVolume().div(price, FLOOR);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L191

```solidity
20: uint192 public constant MIN_TRADE_VOLUME = 1e29;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L20

```solidity
21: uint192 public constant MAX_TRADE_SLIPPAGE = 1e18;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L21

```solidity
28: uint48 public tradesOpen;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L28

```solidity
31: uint192 public maxTradeSlippage;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L31

```solidity
33: uint192 public minTradeVolume;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L33

```solidity
46: uint8 private _decimals;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L46

```solidity
11: uint8 v;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L11

```solidity
17: uint8 public immutable erc20Decimals;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L17

```solidity
19: uint192 public immutable override maxTradeVolume;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L19

```solidity
21: uint48 public immutable oracleTimeout;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L21

```solidity
23: uint192 public immutable oracleError;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L23

```solidity
27: uint48 public immutable priceTimeout;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L27

```solidity
29: uint192 public savedLowPrice;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L29

```solidity
31: uint192 public savedHighPrice;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L31

```solidity
33: uint48 public lastSave;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L33

```solidity
79: uint192 p = chainlinkFeed.price(oracleTimeout);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L79

```solidity
80: uint192 delta = p.mul(oracleError);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L80

```solidity
137: uint48 delta = uint48(block.timestamp) - lastSave;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L137

```solidity
141: uint192 lotMultiplier = divuu(priceTimeout - delta, priceTimeout);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L141

```solidity
20: uint8 public immutable referenceERC20Decimals;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L20

```solidity
46: int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L46

```solidity
51: uint192 pricePerTarget = targetUnitChainlinkFeed.price(oracleTimeout);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L51

```solidity
54: uint192 p = pricePerTarget.mul(pegPrice).mul(refPerTok());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L54

```solidity
57: uint192 delta = p.mul(oracleError);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L57

```solidity
18: uint8 public immutable referenceERC20Decimals;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L18

```solidity
49: int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L49

```solidity
42: uint192 refPrice = chainlinkFeed.price(oracleTimeout);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L42

```solidity
43: uint192 targetPrice = uoaPerTargetFeed.price(oracleTimeout);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L43

```solidity
50: uint192 delta = refPrice.mul(oracleError);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L50

```solidity
12: uint48 priceTimeout;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L12

```solidity
14: uint192 oracleError;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L14

```solidity
16: uint192 maxTradeVolume;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L16

```solidity
17: uint48 oracleTimeout;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L17

```solidity
19: uint192 defaultThreshold;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L19

```solidity
21: uint48 delayUntilDefault;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L21

```solidity
47: uint48 private constant NEVER = type(uint48).max;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L47

```solidity
48: uint48 private _whenDefault = NEVER;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L48

```solidity
50: uint48 public immutable delayUntilDefault;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L50

```solidity
55: uint192 public immutable pegBottom;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L55

```solidity
57: uint192 public immutable pegTop;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L57

```solidity
60: uint192 public prevReferencePrice;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L60

```solidity
82: uint192 peg = targetPerRef();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L82

```solidity
85: uint192 delta = peg.mul(config.defaultThreshold);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L85

```solidity
111: uint192 p = pegPrice.mul(refPerTok());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L111

```solidity
112: uint192 delta = p.mul(oracleError);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L112

```solidity
154: uint192 referencePrice = refPerTok();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L154

```solidity
43: uint192 pricePerTarget = uoaPerTargetFeed.price(oracleTimeout);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L43

```solidity
46: uint192 p = pricePerTarget.mul(pegPrice).mul(refPerTok());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L46

```solidity
49: uint192 delta = p.mul(oracleError);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L49

```solidity
26: uint48 secondsSince = uint48(block.timestamp - updateTime);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L26

```solidity
22: uint8 public immutable erc20Decimals;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L22

```solidity
24: uint192 public immutable override maxTradeVolume;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L24

```solidity
92: uint192 supply = _safeWrap(IRToken(address(erc20)).totalSupply());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L92

```solidity
37: uint192 p = chainlinkFeed.price(oracleTimeout).mul(refPerTok());
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L37

```solidity
38: uint192 delta = p.mul(oracleError);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L38

```solidity
31: uint96 public constant MAX_ORDERS = 1e5;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L31

```solidity
34: uint192 public constant DEFAULT_MIN_BID = FIX_ONE / 100;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L34

```solidity
52: uint48 public endTime;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L52

```solidity
53: uint192 public worstCasePrice;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L53

```solidity
121: uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L121






### <a href="#Summary">[GAS&#x2011;19]</a><a name="GAS&#x2011;19"> `++i`/`i++` Should Be `unchecked{++i}`/`unchecked{i++}` When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

#### <ins>Proof Of Concept</ins>


```solidity
11: for (uint256 i = 1; i < arrLen; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11

```solidity
12: for (uint256 j = 0; j < i; ++j) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L12

```solidity
23: for (uint256 i = 1; i < arrLen; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23

```solidity
14: for (uint256 i = 0; i < bStr.length; i++) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14

```solidity
38: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L38

```solidity
49: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L49

```solidity
127: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L127

```solidity
138: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L138

```solidity
221: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L221

```solidity
78: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L78

```solidity
218: for (uint256 i = 0; i < config.erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L218

```solidity
227: for (uint256 i = 0; i < erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L227

```solidity
262: for (uint256 i = 0; i < erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L262

```solidity
286: for (uint256 i = 0; i < size; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L286

```solidity
337: for (uint256 i = 0; i < len; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L337

```solidity
397: for (uint256 i = 0; i < len; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L397

```solidity
416: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L416

```solidity
437: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L437

```solidity
530: for (uint256 i = 0; i < basketLength; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L530

```solidity
553: for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L553

```solidity
586: for (uint256 i = 0; i < targetsLength; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L586

```solidity
597: for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L597

```solidity
611: for (uint256 j = 0; j < backupLength && assigned < size; ++j) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L611

```solidity
653: for (uint256 i = 0; i < erc20s.length; i++) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L653

```solidity
707: for (uint256 i = 0; i < erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L707

```solidity
725: for (uint256 i = 0; i < erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L725

```solidity
108: for (uint256 i = 0; i < destinations.length(); ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L108

```solidity
133: for (uint256 i = 0; i < numTransfers; i++) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L133

```solidity
143: for (uint256 i = 0; i < length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L143

```solidity
270: for (uint256 i = 0; i < erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270

```solidity
303: for (uint256 i = 0; i < basketSize; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L303

```solidity
478: for (uint256 i = 0; i < erc20length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L478

```solidity
674: for (uint256 i = 0; i < tokensLen; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L674

```solidity
711: for (uint256 i = 0; i < queue.tokens.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L711

```solidity
757: for (uint256 i = 0; i < tokensLen; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L757

```solidity
242: for (uint256 i = 0; i < reg.erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L242

```solidity
329: for (uint256 i = 0; i < reg.erc20s.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L329

```solidity
437: for (uint256 i = 0; i < len; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437

```solidity
27: for (uint256 i = 0; i < registry.assets.length; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27

```solidity
67: for (uint256 i = 0; i < erc20sLen; ++i) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L67





### <a href="#Summary">[GAS&#x2011;20]</a><a name="GAS&#x2011;20"> Using `unchecked` blocks to save gas

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isnâ€™t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block

#### <ins>Proof Of Concept</ins>

```solidity
48: battery.lastCharge = charge - amount;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L48

```solidity
141: if (req.sellAmount > bal) stRSR.seizeRSR(req.sellAmount - bal);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L141


```solidity
493: basketsNeeded = basketsNeeded_ - baskets;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L493

```solidity
682: amtRToken = rightItem.amtRToken - leftItem.amtRToken;
765: amtRToken = rightItem.amtRToken - leftItem.amtRToken;
766: amtBaskets = rightItem.amtBaskets - leftItem.amtBaskets;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L682

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L765

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L766



```solidity
810: uint256 high = (FIX_ONE_256 * basketsNeeded + (supply - 1)) / supply;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L810

```solidity
225: uint256 stakeAmount = newTotalStakes - totalStakes;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L225

```solidity
271: uint256 rsrAmount = stakeRSR - newStakeRSR;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L271

```solidity
322: uint256 newTotalDrafts = totalDrafts - draftAmount;
325: uint256 rsrAmount = draftRSR - newDraftRSR;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L322

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L325



```solidity
662: _approve(owner, spender, currentAllowance - subtractedValue);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L662

```solidity
682: eraStakes[from] = fromBalance - amount;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L682

```solidity
719: eraStakes[account] = accountBalance - amount;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L719

```solidity
748: _approve(owner, spender, currentAllowance - amount);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L748

```solidity
196: soldAmt = initBal - sellBal;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L196








### <a href="#Summary">[GAS&#x2011;21]</a><a name="GAS&#x2011;21"> Use v4.8.0 OpenZeppelin contracts

The upcoming v4.8.0 version of OpenZeppelin provides many small gas optimizations.
https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.8.0-rc.0

#### <ins>Proof Of Concept</ins>

```solidity
    "@openzeppelin/contracts": "^4.7.3"
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/package.json#L52

```solidity
    "@openzeppelin/contracts-upgradeable": "^4.7.3"
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/package.json#L53



#### <ins>Recommended Mitigation Steps</ins>

Update OpenZeppelin Contracts Usage in package.json and require at least version of 4.8.0




### <a href="#Summary">[GAS&#x2011;22]</a><a name="GAS&#x2011;22"> Use solidity version 0.8.17 to gain some gas boost

Upgrade to the latest solidity version 0.8.17 to get additional gas savings.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAssetRegistry.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBroker.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IComponent.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeAct.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeRead.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeTest.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeWrite.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRevenueTrader.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRewardable.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSRVotes.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrade.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IVersioned.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L3

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/StringCallerMock.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Versioned.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L2

```solidity
pragma solidity ^0.6.0;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L3

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L2

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol#L2

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L2

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol#L2

```solidity
pragma solidity >=0.6.0 <0.8.0;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L3

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L2

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ICToken.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L6





### <a href="#Summary">[GAS&#x2011;23]</a><a name="GAS&#x2011;23"> Using `storage` instead of `memory` saves gas

When fetching data from a `storage` location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from `storage`, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another `memory` array/struct

#### <ins>Proof Of Concept</ins>


```solidity
134: Transfer memory t = transfers[i];

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L134





### <a href="#Summary">[GAS&#x2011;24]</a><a name="GAS&#x2011;24"> Using `10**X` for constants isn't gas efficient 

In Solidity, a constant expression in a variable will compute the expression everytime the variable is called. It's not the result of the expression that is stored, but the expression itself.

As Solidity supports the scientific notation, constants of form 10**X can be rewritten as 1eX to save the gas cost from the calculation with the exponentiation operator **.

#### <ins>Proof Of Concept</ins>


```solidity
101: if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L101

```solidity
102: if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L102



#### <ins>Recommended Mitigation Steps</ins>

Replace 10**X with 1eX



### <a href="#Summary">[GAS&#x2011;25]</a><a name="GAS&#x2011;25"> Struct packing

In `IGnosis.sol`:

```solidity
struct GnosisAuctionData {
    IERC20 auctioningToken;
    IERC20 biddingToken;
    uint256 orderCancellationEndDate;
    uint256 auctionEndDate;
    bytes32 initialAuctionOrder;
    uint256 minimumBiddingAmountPerOrder;
    uint256 interimSumBidAmount;
    bytes32 interimOrder;
    bytes32 clearingPriceOrder;
    uint96 volumeClearingPriceOrder;
    bool minFundingThresholdNotReached;
    bool isAtomicClosureAllowed;
    uint256 feeNumerator;
    uint256 minFundingThreshold;
}
```

Since it is unlikely `EndDate` to ever reach uint256.max, it can be changed from `uint256` to a lower `uint` such as `uint128` to save a storage slot.
Can save storage slots by changing to:

```solidity
struct GnosisAuctionData {
    IERC20 auctioningToken;
    IERC20 biddingToken;
    uint128 orderCancellationEndDate;
    uint128 auctionEndDate;
    bytes32 initialAuctionOrder;
    uint256 minimumBiddingAmountPerOrder;
    uint256 interimSumBidAmount;
    bytes32 interimOrder;
    bytes32 clearingPriceOrder;
    uint96 volumeClearingPriceOrder;
    bool minFundingThresholdNotReached;
    bool isAtomicClosureAllowed;
    uint256 feeNumerator;
    uint256 minFundingThreshold;
}
```

