## Summary
### Low Risk Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
|[L-01]| In event `BasketSet` `nonce` type i is `uint256`, but in emit type `nonce` is `uint48`| 1 |
|[L-02]| There is a risk that the ` ratio ` variable is accidentally execute to 0 with ` setRatio` function| 1 |
|[L-03]| The nonReentrant modifier should occur before all other modifiers| 1 |
|[L-04]| No Storage Gap for `ComponentP1`| 1 |
|[L-05]| init() functions can be called by anybody| 11 |
|[L-06]|Allows malleable SECP256K1 signatures |3 |
|[L-07]|Signature Malleability of EVM's ecrecover() | 3 |
|[L-08]|Keccak Constant values should used to immutable rather than constant| 8 |
|[L-09]|Need Fuzzing test| 7 |
|[L-10]|Loss of precision due to rounding| 1 |

Total 10 issues


### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01]|Missing Event for critical parameters init|9|
| [N-02] |Insufficient coverage  ||
| [N-03] |NatSpec comments should be increased in contracts| All contracts |
| [N-04] |`Function writing` that does not comply with the `Solidity Style Guide` | All contracts |
| [N-05] |Add a timelock to critical functions | 1|
| [N-06] |Include return parameters in NatSpec comments | All contracts  |
| [N-07] |Generate perfect code headers every time|  |
| [N-08] |Mark visibility of initialize(...) functions as ``external``  | 1 |
| [N-09] |Use underscores for number literals | 9 |
| [N-10] | `Empty blocks` should be _removed_ or _Emit_ something | 2 |
| [N-11] |Implement some type of version counter that will be incremented automatically for contract upgrades | 1 |
| [N-12] |Tokens accidentally sent to the contract cannot be recovered|  |
| [N-13] |Use a single file for all system-wide constants|45  |
| [N-14] |Take advantage of Custom Error's return value property| 5 |
| [N-15] |Repeated validation logic can be converted into a function modifier | 13 |
| [N-16] |Avoid _shadowing_ `inherited state variables` | 1 |
| [N-17] |Use a more recent version of Chainlink dependencies| 1 |
| [N-18] |Use `require` instead of `assert` | 1 |
| [N-19] |For modern and more readable code; update import usages| All Contracts |
| [N-20] |Compliance with Solidity Style rules in Constant expressions | 1 |
| [N-21] |Use a more recent version of Solidity | All Contracts |
| [N-22] |For functions, follow Solidity standard naming conventions (internal function style rule) | 25 |
| [N-23] |Project Upgrade and Stop Scenario should be |  |
| [N-24] |Use descriptive names for Contracts and Libraries| All Contracts |
| [N-25] |Use SMTChecker|  |
| [N-26] |Make the Test Context with Solidity | |
| [N-27] |Use of bytes.concat() instead of abi.encodePacked() | |
| [N-28] |`emit` names should be made more understandable|1 |

Total 28 issues


### [L-01] In event `BasketSet` `nonce` type i is `uint256`, but in emit type `nonce` is `uint48`

The different type in Event and Emit may cause some problems
```solidity
contracts/interfaces/IBasketHandler.sol:
28:     event BasketSet(uint256 indexed nonce, IERC20[] erc20s, uint192[] refAmts, bool disabled);

contracts/p1/BasketHandler.sol:
  130:     uint48 public override nonce; // A unique identifier for this basket instance

contracts/p1/BasketHandler.sol:
  162      // effects: disabled' = true
  163:     function disableBasket() external {
  164:         require(_msgSender() == address(assetRegistry), "asset registry only");
  165:         uint192[] memory refAmts = new uint192[](basket.erc20s.length);
  166:         emit BasketSet(nonce, basket.erc20s, refAmts, true);
  167:         disabled = true;
  168:     }
```


### [L-02] There is a risk that the ` ratio ` variable is accidentally execute to 0 with ` setRatio` function



The ` setRatio ` function stores a ratio definition that is stored in the `ratio`state variable

There is no protection against accidentally setting 0 charges in the ` setRatio` function, adding a bool check i can prevent `ratio` from being set to 0


```solidity


1 results - 1 files

contracts/p1/Furnace.sol:
   95      /// @custom:governance
   96:     function setRatio(uint192 ratio_) public governance {
   97:         require(ratio_ <= MAX_RATIO, "invalid ratio");
   98:         // The ratio can safely be set to 0
   99:         emit RatioSet(ratio, ratio_);
  100:         ratio = ratio_;
  101:     }

```

### Recommended Mitigation Steps

With the `isZeroCheck` bool, both `setRatio` can be set to 0 and it can be checked


```diff
+ error ZeroFeeError();

contracts/p1/Furnace.sol:
 63      /// @custom:governance
- 64:     function setRatio(uint192 ratio_) public governance {
+ 64:     function setRatio(uint192 ratio_, bool isZeroCheck) public governance {
  65:         require(ratio_ <= MAX_RATIO, "invalid ratio");
+             if (ratio_ == 0 && !isZeroCheck) {
+	 revert ZeroRatioError()
+	}
  66:         // The ratio can safely be set to 0
  67:         emit RatioSet(ratio, ratio_);
  68:         ratio = ratio_;
  69:     }
  70  }

```
### [L-03] The nonReentrant modifier should occur before all other modifiers

Best practice for the re-entry issue is to have non-entrancy come first in the modifier order.

This rule is not applied in the following one function;

```solidity
1 result - 1 file

contracts/p1/mixins/Trading.sol:
  66:     function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {
```



### [L-04] No Storage Gap for `ComponentP1`

```solidity
contracts/p0/AssetRegistry.sol:
  12: contract AssetRegistryP1 is ComponentP1, IAssetRegistry {
```
### Impact
For upgradeable contracts, inheriting contracts may introduce new variables. In order to be able to add new variables to the upgradeable contract without causing storage collisions, a storage gap should be added to the upgradeable contract.

If no storage gap is added, when the upgradable contract introduces new variables, 
it may override the variables in the inheriting contract.

Storage gaps are a convention for reserving storage slots in a base contract, allowing future versions of that contract to use up those slots without affecting the storage layout of child contracts.
To create a storage gap, declare a fixed-size array in the base contract with an initial number of slots. 
This can be an array of uint256 so that each element reserves a 32 byte slot. Use the naming convention __gap so that OpenZeppelin Upgrades will recognize the gap:


```js
contract Base {
    uint256 base1;
    uint256[49] __gap;
}

contract Child is Base {
    uint256 child;
}
```

Openzeppelin Storage Gaps notification:
```js
Storage Gaps
This makes the storage layouts incompatible, as explained in Writing Upgradeable Contracts. 
The size of the __gap array is calculated so that the amount of storage used by a contract 
always adds up to the same number (in this case 50 storage slots).
```



### Recommended Mitigation Steps
Consider adding a storage gap at the end of the upgradeable abstract contract
```js
uint256[50] private __gap;
```
### [L-05] init() functions can be called by anybody

`init()` functions can be called anybody when the contract is not initialized.


Here is a definition of `init()` function.

```solidity

11 results - 11 files

contracts/p1/AssetRegistry.sol:
  34:     function init(IMain main_, IAsset[] calldata assets_) external initializer {

contracts/p1/BackingManager.sol:
  44:     function init(

contracts/p1/BasketHandler.sol:
  151:     function init(IMain main_) external initializer {

contracts/p1/Broker.sol:
  51:     function init(

contracts/p1/Distributor.sol:
  41:     function init(IMain main_, RevenueShare calldata dist) external initializer {

contracts/p1/Furnace.sol:
  33:     function init(

contracts/p1/Main.sol:
  26:     function init(

contracts/p1/RevenueTrader.sol:
  23:     function init(

contracts/p1/RToken.sol:
  147:     function init(

contracts/p1/StRSR.sol:
  160:     function init(

contracts/plugins/trading/GnosisTrade.sol:
  81:     function init(

```


### Recommended Mitigation Steps

Add a control that makes `init()` only call the Deployer Contract or EOA;

```js
if (msg.sender != DEPLOYER_ADDRESS) {
            revert NotDeployer();
        }
```


### [L-06] Allows malleable SECP256K1 signatures

Here, the ecrecover() method doesn't check the s range.

Homestead (EIP-2) added this limitation, however the precompile remained unaltered. The majority of libraries, including OpenZeppelin, do this check.
Since an order can only be confirmed once and its hash is saved, there doesn't seem to be a serious danger in existing use cases.

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7201e6707f6631d9499a569f492870ebdd4133cf/contracts/utils/cryptography/ECDSA.sol#L138-L149


```solidity

3 results - 1 file

contracts/plugins/aave/StaticATokenLM.sol:
  135              abi.encodePacked(
  136:                 "\x19\x01",
  137                  getDomainSeparator(),

  163              abi.encodePacked(
  164:                 "\x19\x01",
  165                  getDomainSeparator(),

  203              abi.encodePacked(
  204:                 "\x19\x01",
  205                  getDomainSeparator(),
```
### [L-07] Signature Malleability of EVM's ecrecover()

**Context:**

```solidity
3 results - 1 file

contracts/plugins/aave/StaticATokenLM.sol:
  142          );
  143:         require(owner == ecrecover(digest, v, r, s), StaticATokenErrors.INVALID_SIGNATURE);
  144          _nonces[owner] = currentValidNonce.add(1);

  180          require(
  181:             depositor == ecrecover(digest, sigParams.v, sigParams.r, sigParams.s),
  182              StaticATokenErrors.INVALID_SIGNATURE

  221          require(
  222:             owner == ecrecover(digest, sigParams.v, sigParams.r, sigParams.s),
  223              StaticATokenErrors.INVALID_SIGNATURE
```
**Description:**
Description: The function calls the Solidity ecrecover() function directly to verify the given signatures. However, the ecrecover() EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin's ECDSA library.

*Recommendation:**
Use the ecrecover function from OpenZeppelin's ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

### [L-08] Keccak Constant values should used to immutable rather than constant

There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand.



```solidity
8 results - 5 files

contracts/libraries/Fixed.sol:
  33: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));

contracts/p1/StRSR.sol:
  126:     bytes32 private constant _PERMIT_TYPEHASH = keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");

contracts/p1/StRSRVotes.sol:
  27:     bytes32 private constant _DELEGATE_TYPEHASH = keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

contracts/plugins/aave/StaticATokenLM.sol:
  41:     bytes32 internal constant EIP712_DOMAIN =keccak256("EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)");
  42:     bytes32 public constant PERMIT_TYPEHASH =keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");
  43:     bytes32 public constant METADEPOSIT_TYPEHASH =keccak256("Deposit(address depositor,address recipient,uint256 value,uint16 referralCode,bool fromUnderlying,uint256 nonce,uint256 deadline)");
  44:     bytes32 public constant METAWITHDRAWAL_TYPEHASH =keccak256("Withdraw(address owner,address recipient,uint256 staticAmount,uint256 dynamicAmount,bool toUnderlying,uint256 nonce,uint256 deadline)");

contracts/vendor/ERC20PermitUpgradeable.sol:
  39:     bytes32 private constant _PERMIT_TYPEHASH =keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");


```
### [L-09] Need Fuzzing test

```solidity

7 results - 3 files

contracts/libraries/Fixed.sol:
  499  ) pure returns (uint256 result) {
  500:     unchecked {
  501          (uint256 hi, uint256 lo) = fullMul(x, y);

  550  function fullMul(uint256 x, uint256 y) pure returns (uint256 hi, uint256 lo) {
  551:     unchecked {
  552          uint256 mm = mulmod(x, y, uint256(0) - uint256(1));

contracts/p1/BasketHandler.sol:
  360          // return FIX_MAX instead of throwing overflow errors.
  361:         unchecked {
  362              // p and mul *are* Fix values, so have 18 decimals (D18)

contracts/p1/StRSR.sol:
  654          require(currentAllowance >= subtractedValue, "ERC20: decreased allowance below zero");
  655:         unchecked {
  656              _approve(owner, spender, currentAllowance - subtractedValue);

  674          require(fromBalance >= amount, "ERC20: transfer amount exceeds balance");
  675:         unchecked {
  676              eraStakes[from] = fromBalance - amount;

  711          require(accountBalance >= amount, "ERC20: burn amount exceeds balance");
  712:         unchecked {
  713              eraStakes[account] = accountBalance - amount;

  740              require(currentAllowance >= amount, "ERC20: insufficient allowance");
  741:             unchecked {
  742                  _approve(owner, spender, currentAllowance - amount);

```

**Description:**
In total 3 contracts, 7 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

### [L-10] Loss of precision due to rounding

Add scalar so roundings are negligible

[StRSR.sol#L386](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L386)

```solidity

1 result - 1 file

contracts/p1/StRSR.sol:
  385          // Remove RSR from stakeRSR
  386:         uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;

```


### [N-01] Missing Event for critical parameters init

**Context:**
```solidity


contracts/p1/AssetRegistry.sol:
  34:     function init(IMain main_, IAsset[] calldata assets_) external initializer {

contracts/p1/BackingManager.sol:
  44:     function init(

contracts/p1/BasketHandler.sol:
  151:     function init(IMain main_) external initializer {

contracts/p1/Broker.sol:
  51:     function init(

contracts/p1/Distributor.sol:
  41:     function init(IMain main_, RevenueShare calldata dist) external initializer {

contracts/p1/Furnace.sol:
  33:     function init(

contracts/p1/Main.sol:
  26:     function init(

contracts/p1/RevenueTrader.sol:
  23:     function init(

contracts/p1/RToken.sol:
  147:     function init(

contracts/p1/StRSR.sol:
  160:     function init(
```

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit


### [N-02] Insufficient coverage

**Description:**
The test coverage rate of the project is ~95%. Testing all functions is best practice in terms of security criteria.


https://github.com/code-423n4/2023-01-reserve/blob/main/README.md#scoping-details
```js
- What is the overall line coverage percentage provided by your tests?: 95
```

Test coverage is expected to be 100%

### [N-03] NatSpec comments should be increased in contracts

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
NatSpec comments should be increased in contracts

### [N-04] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
All Contracts

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

 constructor
receive function (if exists)
fallback function (if exists)
external
public
internal
private
within a grouping, place the view and pure functions last

### [N-05] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).
Consider adding a timelock to:

```solidity

contracts/p1/BackingManager.sol:
  255      /// @custom:governance
  256:     function setTradingDelay(uint48 val) public governance {
  257:         require(val <= MAX_TRADING_DELAY, "invalid tradingDelay");
  258:         emit TradingDelaySet(tradingDelay, val);
  259:         tradingDelay = val;
  260:     }
```
### [N-06] Include return parameters in NatSpec comments

**Context:**
All Contracts

**Description:**
It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

**Recommendation:**
Include return parameters in NatSpec comments

_Recommendation  Code Style: (from Uniswap3)_
```js
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```

### [N-07] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```

### [N-08] Mark visibility of initialize(...) functions as ``external``

```solidity

contracts/p1/Main.sol:
  24  
  25:     /// Initializer
  26:     function init(
  27:         Components memory components,
  28:         IERC20 rsr_,
  29:         uint48 shortFreeze_,
  30:         uint48 longFreeze_
  31:     ) public virtual initializer {
  32:         require(address(rsr_) != address(0), "invalid RSR address");
  33:         __Auth_init(shortFreeze_, longFreeze_);
  34:         __ComponentRegistry_init(components);
  35:         __UUPSUpgradeable_init();
  36: 
  37:         rsr = rsr_;
  38:         emit MainInitialized();
  39:     }

```

**Description:**
If someone wants to extend via inheritance, it might make more sense that the overridden initialize(...) function calls the internal {...}_init function, not the parent public initialize(...) function.

External instead of public would give more the sense of the initialize(...) functions to behave like a constructor (only called on deployment, so should only be called externally)

Security point of view, it might be safer so that it cannot be called internally by accident in the child contract 

It might cost a bit less gas to use external over public 


It is possible to override a function from external to public (= "opening it up") ✅
but it is not possible to override a function from public to external (= "narrow it down"). ❌

For above reasons you can change initialize(...) to external


https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750

### [N-09] Use underscores for number literals

```diff
9 results

contracts/p1/Distributor.sol:
- 165:         require(share.rsrDist <= 10000, "RSR distribution too high");
+ 165:	 require(share.rsrDist <= 10_000, "RSR distribution too high");
-  166:         require(share.rTokenDist <= 10000, "RToken distribution too high");
+  166:         require(share.rTokenDist <= 10_000, "RToken distribution too high");

contracts/mixins/Auth.sol:
-   9: uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month
+  9: uint48 constant MAX_SHORT_FREEZE = 2_592_000; // 1 month
- 10: uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year
+ 10: uint48 constant MAX_LONG_FREEZE = 31_536_000; // 1 year


contracts/p1/BackingManager.sol:
- 33:     uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
+ 33:     uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year

contracts/p1/Furnace.sol:
-  16:     uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
+ 16:     uint48 public constant MAX_PERIOD = 31_536_000; // {s} 1 year

contracts/p1/StRSR.sol:
- 37:     uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
+ 37:     uint48 public constant MAX_UNSTAKING_DELAY = 31_536_000; // {s} 1 year

- 38:     uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
+ 38:    uint48 public constant MAX_REWARD_PERIOD = 31_536_000; // {s} 1 year

contracts/plugins/trading/GnosisTrade.sol:
- 27:     uint256 public constant FEE_DENOMINATOR = 1000;
+ 27:     uint256 public constant FEE_DENOMINATOR = 1_000;

```
**Description:**
 There is   occasions where certain number have been hardcoded, either in variable or in the code itself. Large numbers can become hard to read.

**Recommendation:**
Consider using underscores for number literals to improve its readability.

### [N-10] `Empty blocks` should be _removed_ or _Emit_ something

**Description:**
Code contains empty block

```solidity

2 results - 2 files

contracts/plugins/assets/Asset.sol:
  163      /// @dev Use delegatecall
  164:     function claimRewards() external virtual {}
  165  

contracts/plugins/assets/RTokenAsset.sol:
  118      /// @dev Use delegatecall
  119:     function claimRewards() external virtual {}

```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### [N-11] Implement some type of version counter that will be incremented automatically for contract upgrades

As part of the upgradeability of  Proxies , the contract can be upgraded multiple times, where it is a systematic approach to record the version of each upgrade.

```js
1 result - 1 file

contracts/p1/Main.sol:
  63      // solhint-disable-next-line no-empty-blocks
  64:     function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
  65  


```
I suggest implementing some kind of version counter that will be incremented automatically when you upgrade the contract.

**Recommendation:**
```js

uint256 public authorizeUpgradeCounter;

function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {
	authorizeUpgradeCounter+=1;
}


    }
```

### [N-12] Tokens accidentally sent to the contract cannot be recovered


It can't be recovered if the tokens accidentally arrive at the contract address, which has happened to many popular projects, so I recommend adding a recovery code to your critical contracts.

### Recommended Mitigation Steps

Add this code:

```solidity
 /**
  * @notice Sends ERC20 tokens trapped in contract to external address
  * @dev Onlyowner is allowed to make this function call
  * @param account is the receiving address
  * @param externalToken is the token being sent
  * @param amount is the quantity being sent
  * @return boolean value indicating whether the operation succeeded.
  *
 */
  function rescueERC20(address account, address externalToken, uint256 amount) public onlyOwner returns (bool) {
    IERC20(externalToken).transfer(account, amount);
    return true;
  }
}

```

### [N-13] Use a single file for all system-wide constants

There are many addresses and constants used in the system. It is recommended to put the most used ones in one file (for example constants.sol, use inheritance to access these values)

  This will help with readability and easier maintenance for future changes. This also helps with any issues, as some of these hard-coded values are admin addresses.

constants.sol
Use and import this file in contracts that require access to these values. This is just a suggestion, in some use cases this may result in higher gas usage in the distribution.

```solidity
45 results - 12 files

contracts/libraries/Fixed.sol:
   33: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
   36: uint256 constant FIX_ONE_256 = 1e18;
   37: uint8 constant FIX_DECIMALS = 18;
   41: uint64 constant FIX_SCALE = 1e18;
   44: uint128 constant FIX_SCALE_SQ = 1e36;
   48: uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;
   50: uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.
   51: uint192 constant FIX_ONE = FIX_SCALE; // The uint192 representation of one.
   52: uint192 constant FIX_MAX = type(uint192).max; // The largest uint192. (Not an integer!)
   53: uint192 constant FIX_MIN = 0; // The smallest uint192.
   62: RoundingMode constant FLOOR = RoundingMode.FLOOR;
   63: RoundingMode constant ROUND = RoundingMode.ROUND;
   64: RoundingMode constant CEIL = RoundingMode.CEIL;
  310:     uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;

contracts/libraries/RedemptionBattery.sol:
  11: uint48 constant BLOCKS_PER_HOUR = 300; // {blocks/hour}

contracts/mixins/Auth.sol:
   7: uint256 constant LONG_FREEZE_CHARGES = 6; // 6 uses
   8: uint48 constant MAX_UNFREEZE_AT = type(uint48).max;
   9: uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month
  10: uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year
  27:     bytes32 public constant OWNER_ROLE = OWNER;
  28:     bytes32 public constant SHORT_FREEZER_ROLE = SHORT_FREEZER;
  29:     bytes32 public constant LONG_FREEZER_ROLE = LONG_FREEZER;
  30:     bytes32 public constant PAUSER_ROLE = PAUSER;

contracts/p1/BackingManager.sol:
   33:     uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
   34:     uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%
  167:          *       roughly constant, and strictly does not decrease,

contracts/p1/BasketHandler.sol:
  117:     uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight

contracts/p1/Broker.sol:
  15: uint256 constant GNOSIS_MAX_TOKENS = 7e28;
  24:     uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week

contracts/p1/Deployer.sol:
  31:     string public constant ENS = "reserveprotocol.eth";

contracts/p1/Distributor.sol:
  31:     address public constant FURNACE = address(1);
  32:     address public constant ST_RSR = address(2);
  34:     uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;

contracts/p1/Furnace.sol:
  15:     uint192 public constant MAX_RATIO = FIX_ONE; // {1} 100%
  16:     uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year

contracts/p1/RToken.sol:
  15: uint192 constant MIN_BLOCK_ISSUANCE_LIMIT = 10_000 * FIX_ONE;
  18: uint192 constant MAX_ISSUANCE_RATE = FIX_ONE; // {1}

contracts/p1/StRSR.sol:
   37:     uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
   38:     uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
   39:     uint192 public constant MAX_REWARD_RATIO = FIX_ONE; // {1} 100%
   45:     uint8 public constant decimals = 18;
   65:     uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}
   87:     uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}
  126:     bytes32 private constant _PERMIT_TYPEHASH = keccak256("Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)");

contracts/p1/StRSRVotes.sol:
  27:     bytes32 private constant _DELEGATE_TYPEHASH = keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

```

### [N-14] Take advantage of Custom Error's return value property

An important feature of Custom Error is that values such as address, tokenID, msg.value can 
be written inside the `()` sign, this kind of approach provides a serious advantage in 
debugging and examining the revert details of dapps such as tenderly.


```solidity
5 results - 3 files

contracts/plugins/assets/Asset.sol:
  102:             if (errData.length == 0) revert(); // solhint-disable-line reason-string
  116:             if (errData.length == 0) revert(); // solhint-disable-line reason-string
  133:             if (errData.length == 0) revert(); // solhint-disable-line reason-string

contracts/plugins/assets/FiatCollateral.sol:
  149:             if (errData.length == 0) revert(); // solhint-disable-line reason-string

contracts/plugins/assets/RTokenAsset.sol:
  78:             if (errData.length == 0) revert(); // solhint-disable-line reason-string

```

### [N-15] Repeated validation logic can be converted into a function modifier

If a query or logic is repeated over many lines, using a modifier improves the readability and reusability of the code


```solidity
13 results - 3 files

contracts/p1/mixins/RecollateralizationLib.sol:
   94:         if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {
  401:         if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {

contracts/p1/mixins/Trading.sol:
   68:         if (address(trade) == address(0)) return;
  114:         assert(address(trades[sell]) == address(0));

contracts/plugins/aave/StaticATokenLM.sol:
  351:         if (address(INCENTIVES_CONTROLLER) == address(0)) {
  366:         if (address(INCENTIVES_CONTROLLER) == address(0)) {
  392:         if (address(INCENTIVES_CONTROLLER) == address(0)) {
  461:         if (address(INCENTIVES_CONTROLLER) == address(0)) {
  473:         if (address(INCENTIVES_CONTROLLER) == address(0)) {
  480:         if (address(INCENTIVES_CONTROLLER) == address(0)) {
  519:         if (address(INCENTIVES_CONTROLLER) == address(0)) {
  566:         if (address(INCENTIVES_CONTROLLER) == address(0)) {
```

### [N-16] Avoid _shadowing_ `inherited state variables`

```solidity
contracts/vendor/ERC20PermitUpgradeable.sol:
  57       */
  58:     function __ERC20Permit_init(string memory name) internal onlyInitializing {
  59:         __EIP712_init_unchained(name, "1");
  60:     }


node_modules/@openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol:
  66       */
  67:     function name() public view virtual override returns (string memory) {
  68:         return _name;
  69:     }
```

**Description:**
In ```ERC20PermitUpgradeable.sol #L52``` , there is a local variable named ```name``` , but is are a state varible named ```name``` in the inherited ``` ERC20Upgradeable.sol ``` with the same name. 
This use causes compilers to issue warnings, negatively affecting checking and code readability. 


```js
contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol:
  77     */
  78:   function name() public view virtual override returns (string memory) {
  79:     return _name;
  80:   }


contracts/cash/external/openzeppelin/contracts-upgradeable/token/ERC20/ERC20Upgradeable.sol:
  85     */
  86:   function symbol() public view virtual override returns (string memory) {
  87:     return _symbol;
  88:   }

```

**Recommendation:**
Avoid using variables with the same name, including inherited in the same contract, if used, it must be specified in the NatSpec comments.

There is no danger here, but definitions with the same name are dangerous, especially with upgradeable contracts, which may cause problems in future versions.
The way to avoid this; Adding the _ sign to the beginning of local variable names
### [N-17] Use a more recent version of Chainlink dependencies

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Chainlink version.


```js
1 result - 1 file

package.json:
  45:   "devDependencies": {
  47:     "@chainlink/contracts": "^0.4.1",
```



**Recommendation:**
Old version of OZ is used `(0.4.1)`, newer version can be used `(0.6.0)` 

https://www.npmjs.com/package/@chainlink/contracts?activeTab=versions

### [N-18] Use `require` instead of `assert`

**Context:**

```solidity
11 results - 7 files

contracts/p1/BackingManager.sol:
  249:         assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());

contracts/p1/BasketHandler.sol:
  556:             assert(targetIndex < targetsLength);

contracts/p1/StRSR.sol:
  693:         assert(totalStakes + amount < type(uint224).max);

contracts/p1/mixins/RecollateralizationLib.sol:
  110:         assert(doTrade);

contracts/p1/mixins/RewardableLib.sol:
   78:                 assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
  102:             assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);

contracts/p1/mixins/TradeLib.sol:
   44:         assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
  108:         assert(
  168:             assert(errorCode == 0x11 || errorCode == 0x12);
  170:             assert(keccak256(reason) == UIntOutofBoundsHash);

contracts/p1/mixins/Trading.sol:
  114:         assert(address(trades[sell]) == address(0));

```
**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

assert() and ruqire();
The big difference between the two is that the `assert()`function when false, uses up all the remaining gas and reverts all the changes made. Meanwhile, a  `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### [N-19] For modern and more readable code; update import usages

**Context:**
All contracts

**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

### [N-20] Compliance with Solidity Style rules in Constant expressions

**Context:**
```solidity


contracts/libraries/Fixed.sol:
  33: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));

```

**Recommendation:**
Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.

### [N-21] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used `(0.8.9)`, newer version can be used `(0.8.17)` 

### [N-22] For functions, follow Solidity standard naming conventions (internal function style rule)

**Context:**
```solidity

25 results - 9 files

contracts/p1/BasketHandler.sol:
   68:     function empty(Basket storage self) internal {
   75:     function setFrom(Basket storage self, Basket storage other) internal {
   87:     function add(Basket storage self,IERC20 tok,uint192 weight ) internal {
  352:     function quantityMulPrice(uint192 qty, uint192 p) internal pure returns (uint192) {
  646:     function requireValidCollArray(IERC20[] calldata erc20s) internal view {

contracts/p1/Distributor.sol:
  17:     EnumerableSet.AddressSet internal destinations;

contracts/p1/StRSR.sol:
   56:     uint256 internal era;
   61:     uint256 internal totalStakes; // Total of all stakes {qStRSR}
   62:     uint256 internal stakeRSR; // Amount of RSR backing all stakes {qRSR}
   73:     uint256 internal draftEra;
   83:     uint256 internal totalDrafts; // Total of all drafts {qDrafts}
   84:     uint256 internal draftRSR; // Amount of RSR backing all drafts {qRSR}
  147:     uint256 internal rsrRewardsAtLastPayout;
  540:     function pushDraft(address account, uint256 rsrAmount) internal returns (uint256 index, uint64 availableAt) {
  568:     function beginEra() internal virtual {
  580:     function beginDraftEra() internal virtual {
  590:     function rsrRewards() internal view returns (uint256) {

contracts/p1/StRSRVotes.sol:
  48:     function beginEra() internal override {

contracts/p1/mixins/RecollateralizationLib.sol:
  152:     function basketRange( ComponentCache memory components, TradingRules memory rules, Registry memory reg ) internal 

contracts/p1/mixins/TradeLib.sol:
  140:     function isEnoughToSell() internal view returns (bool) {
  149:     function safeMulDivCeil() internal pure returns (uint192) {

contracts/p1/mixins/Trading.sol:
  111:     function tryTrade(TradeRequest memory req) internal nonReentrant {

contracts/plugins/assets/FiatCollateral.sol:
  180:     function markStatus(CollateralStatus status_) internal {
  199:     function alreadyDefaulted() internal view returns (bool) {

contracts/plugins/assets/OracleLib.sol:
  14:     function price(AggregatorV3Interface chainlinkFeed, uint48 timeout)internal


```


**Description:**
The above codes don't follow Solidity's standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)


### [N-23] Project Upgrade and Stop Scenario should be

At the start of the project, the system may need to be stopped or upgraded, I suggest you have a script beforehand and add it to the documentation.
This can also be called an " EMERGENCY STOP (CIRCUIT BREAKER) PATTERN ".

https://github.com/maxwoe/solidity_patterns/blob/master/security/EmergencyStop.sol


### [N-24] Use descriptive names for Contracts and Libraries


This codebase will be difficult to navigate, as there are no descriptive naming conventions that specify which files should contain meaningful logic.

Prefixes should be added like this by filing:

Interface I_
absctract contracts Abs_
Libraries Lib_

We recommend that you implement this or a similar agreement.

### [N-25] Use SMTChecker

The *highest* tier of smart contract behavior assurance is formal mathematical verification. All assertions that are made are guaranteed to be true across all inputs → The quality of your asserts is the quality of your verification.

https://twitter.com/0xOwenThurm/status/1614359896350425088?t=dbG9gHFigBX85Rv29lOjIQ&s=19

### [N-26] Make the Test Context with Solidity

It's crucial to write tests with possibly 100% coverage for smart contract systems.

It is recommended to write appropriate tests for all possible code streams and especially for extreme cases.

But the other important point is the test context, tests written with solidity are safer, it is recommended to focus on tests with Foundry

https://www.paradigm.xyz/2021/12/introducing-the-foundry-ethereum-development-toolbox

Testing in JS instead of Solidity means that you operate 1 level of abstraction away from what you actually want to test, requiring you to be familiar with Mocha and Ethers.js or Web3.js at a minimum. This increases the barrier to entry for Solidity developers.

### [N-27] Use of bytes.concat() instead of abi.encodePacked()

```solidity

2 results - 1 file

contracts/p1/Deployer.sol:
  206:             string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));
  207:             string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));

```
Rather than using `abi.encodePacked` for appending bytes, since version 0.8.4, bytes.concat() is enabled

Since version 0.8.4 for appending bytes, bytes.concat() can be used instead of abi.encodePacked(,)

### [N-28] `emit` names should be made more understandable



```diff

contracts/p1/Furnace.sol:
  87      /// @custom:governance
-  88:     function setPeriod(uint48 period_) public governance {
+  88:     function setPeriod(uint48 ewPeriod_public governance {

-  89:         require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
+ 89:         require(newPeriod_); > 0 && period_ <= MAX_PERIOD, "invalid period");
-  90:         emit PeriodSet(period, period_);
+ 90:         emit PeriodSet(period, newPeriod_);
-  91:         period = period_;
+  91:         period = newPeriod_);

  92:     }
```

