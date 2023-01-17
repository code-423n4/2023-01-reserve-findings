

### [L01] require() should be used instead of assert()


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





### [L02] Missing checks for `address(0x0)` when assigning values to `address` state variables



#### Findings:
```
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::32 => decimals = _decimals;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::37 => latestAnswer = _answer;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::62 => latestRound = _roundId;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::63 => latestAnswer = _answer;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::64 => latestTimestamp = _timestamp;
```



### [L03] `initialize` functions can be front-run

#### Impact
See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details
#### Findings:
```
reserve-protocol/contracts/p1/AssetRegistry.sol::34 => function init(IMain main_, IAsset[] calldata assets_) external initializer {
reserve-protocol/contracts/p1/BackingManager.sol::50 => ) external initializer {
reserve-protocol/contracts/p1/BasketHandler.sol::151 => function init(IMain main_) external initializer {
reserve-protocol/contracts/p1/Broker.sol::56 => ) external initializer {
reserve-protocol/contracts/p1/Distributor.sol::41 => function init(IMain main_, RevenueShare calldata dist) external initializer {
reserve-protocol/contracts/p1/Furnace.sol::37 => ) external initializer {
reserve-protocol/contracts/p1/Main.sol::23 => constructor() initializer {}
reserve-protocol/contracts/p1/Main.sol::31 => ) public virtual initializer {
reserve-protocol/contracts/p1/RToken.sol::155 => ) external initializer {
reserve-protocol/contracts/p1/RevenueTrader.sol::28 => ) external initializer {
reserve-protocol/contracts/p1/StRSR.sol::167 => ) external initializer {
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::31 => ) public initializer {
```



### [L04] `safeApprove()` is deprecated

#### Impact
Deprecated in favor of safeIncreaseAllowance() and safeDecreaseAllowance()
#### Findings:
```
reserve-protocol/contracts/p1/BackingManager.sol::75 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
reserve-protocol/contracts/p1/BackingManager.sol::76 => IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
reserve-protocol/contracts/p1/RevenueTrader.sol::61 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), 0);
reserve-protocol/contracts/p1/RevenueTrader.sol::62 => IERC20Upgradeable(address(erc20)).safeApprove(address(distributor), bal);
reserve-protocol/contracts/p1/mixins/Trading.sol::116 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), 0);
reserve-protocol/contracts/p1/mixins/Trading.sol::117 => IERC20Upgradeable(address(sell)).safeApprove(address(broker), req.sellAmount);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::101 => ASSET.safeApprove(address(pool), type(uint256).max);
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::136 => IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), 0);
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::137 => IERC20Upgradeable(address(sell)).safeApprove(address(gnosis), initBal);
```

### [L05] Unused nonce used for multiple purposes

#### Impact
The nonce mapping used for permit() calls is the same as the one used for delegateBySig().
 This should at the very least be documented so signers know that the 
order of operations between the two functions matters, and so that multicall()s can be organized appropriately
#### Findings:
```
reserve-protocol/contracts/p1/StRSRVotes.sol::133 => require(nonce == _useNonce(signer), "ERC20Votes: invalid nonce");
```




### [L06] `pragma experimental ABIEncoderV2` is deprecated

#### Impact
Use pragma abicoder v2 [instead](https://github.com/ethereum/solidity/blob/69411436139acf5dbcfc5828446f18b9fcfee32c/docs/080-breaking-changes.rst#silent-changes-of-the-semantics)

#### Findings:
```
reserve-protocol/contracts/plugins/aave/IAaveIncentivesController.sol::3 => pragma experimental ABIEncoderV2;
reserve-protocol/contracts/plugins/aave/IStaticATokenLM.sol::3 => pragma experimental ABIEncoderV2;
reserve-protocol/contracts/plugins/aave/RayMathNoRounding.sol::3 => pragma experimental ABIEncoderV2;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::3 => pragma experimental ABIEncoderV2;
```



### [L07] `Math.max(<x>,0)` used with `int` cast to `uint`

#### Impact
The code casts an `int` to a `uint` before passing it to `Math.max()`. It seems as though the `Math.max()` call is attempting to prevent values from being negative, but since the `int` is being cast to `uint`,
 the value will never be negative, and instead will overflow if either 
the multiplication involving the slope and timestamp is positive. I 
wasn’t able to find a scenario where this is the case, but this seems 
very dangerous, and the `Math.max()` call is sending misleading signals, so I suggest moving it to inside the cast to `uint`
#### Findings:
```
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::121 => uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::123 => uint256 minBuyAmtPerOrder = Math.max(
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::199 => uint256 adjustedSoldAmt = Math.max(soldAmt - 1, 1);
```



### [L08] Unspecific Compiler Version Pragma

#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.
#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::3 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/test/ArrayCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/test/StringCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/plugins/aave/ERC20.sol::3 => pragma solidity ^0.6.0;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::2 => pragma solidity ^0.8.0;
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::2 => pragma solidity ^0.8.0;
```

### Non-Critical Issues


### [N01] Adding a return statement when the function defines a named return variable, is redundant



#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::173 => return result;
reserve-protocol/contracts/libraries/Fixed.sol::541 => return result;
reserve-protocol/contracts/p1/RToken.sol::279 => return amtRToken;
reserve-protocol/contracts/p1/StRSR.sol::457 => return right;
reserve-protocol/contracts/p1/StRSRVotes.sol::56 => return era;
reserve-protocol/contracts/p1/mixins/TradeLib.sol::161 => return result;
reserve-protocol/contracts/p1/mixins/TradeLib.sol::172 => return FIX_MAX;
reserve-protocol/contracts/plugins/aave/ERC20.sol::67 => return _name;
reserve-protocol/contracts/plugins/aave/ERC20.sol::75 => return _symbol;
reserve-protocol/contracts/plugins/aave/ERC20.sol::92 => return _decimals;
reserve-protocol/contracts/plugins/aave/ERC20.sol::99 => return _totalSupply;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::204 => return _whenDefault;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::211 => return FIX_ONE;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::216 => return FIX_ONE;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::22 => return _underlyingAsset;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::55 => return _exchangeRate;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::68 => return aToken;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::73 => return aaveToken;
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::29 => return _exchangeRate;
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::33 => return _exchangeRate;
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::42 => return _underlyingToken;
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::18 => return _targetPerRef;
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::88 => return FIX_ONE;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::325 => return result;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::378 => return nextElement;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::908 => return _owner;
```



### [N02] `require()`/`revert()` statements should have descriptive reason strings



#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::317 => require(x_ <= FIX_ONE);
reserve-protocol/contracts/plugins/aave/RayMathNoRounding.sol::15 => require(a <= type(uint256).max / b, Errors.MATH_MULTIPLICATION_OVERFLOW);
reserve-protocol/contracts/plugins/aave/RayMathNoRounding.sol::20 => require(b != 0, Errors.MATH_DIVISION_BY_ZERO);
reserve-protocol/contracts/plugins/aave/RayMathNoRounding.sol::21 => require(a <= (type(uint256).max) / RAY, Errors.MATH_MULTIPLICATION_OVERFLOW);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::142 => require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::144 => require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::155 => require(owner == ecrecover(digest, v, r, s), StaticATokenErrors.INVALID_SIGNATURE);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::170 => require(depositor != address(0), StaticATokenErrors.INVALID_DEPOSITOR);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::172 => require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::210 => require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::212 => require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::294 => require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::317 => require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);
reserve-protocol/contracts/plugins/assets/Asset.sol::102 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
reserve-protocol/contracts/plugins/assets/Asset.sol::116 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
reserve-protocol/contracts/plugins/assets/Asset.sol::133 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
reserve-protocol/contracts/plugins/mocks/WETH.sol::43 => require(balanceOf[msg.sender] >= wad);
reserve-protocol/contracts/plugins/mocks/WETH.sol::68 => require(balanceOf[src] >= wad);
reserve-protocol/contracts/plugins/mocks/WETH.sol::71 => require(allowance[src][msg.sender] >= wad);
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::557 => require(b > 0, errorMessage);
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::597 => require(b != 0, errorMessage);
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::848 => revert(add(32, returndata), returndata_size)
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::851 => revert(errorMessage);
```



### [N03] constants should be defined rather than using magic numbers



#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::17 => toFix(1) == 1e18
reserve-protocol/contracts/libraries/Fixed.sol::36 => uint256 constant FIX_ONE_256 = 1e18;
reserve-protocol/contracts/libraries/Fixed.sol::37 => uint8 constant FIX_DECIMALS = 18;
reserve-protocol/contracts/libraries/Fixed.sol::41 => uint64 constant FIX_SCALE = 1e18;
reserve-protocol/contracts/libraries/Fixed.sol::44 => uint128 constant FIX_SCALE_SQ = 1e36;
reserve-protocol/contracts/libraries/Fixed.sol::101 => if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5
reserve-protocol/contracts/libraries/Fixed.sol::102 => if (40 <= shiftLeft) revert UIntOutOfBounds(); // 10**56 < FIX_MAX < 10**57
reserve-protocol/contracts/libraries/Fixed.sol::104 => shiftLeft += 18;
reserve-protocol/contracts/libraries/Fixed.sol::106 => uint256 coeff = 10**abs(shiftLeft);
reserve-protocol/contracts/libraries/Fixed.sol::107 => uint256 shifted = (shiftLeft >= 0) ? x * coeff : _divrnd(x, coeff, rounding);
reserve-protocol/contracts/libraries/Fixed.sol::212 => if (decimals <= -59) return (rounding == CEIL ? 1 : 0); // 59, because 1e58 > 2**192
reserve-protocol/contracts/libraries/Fixed.sol::213 => if (58 <= decimals) revert UIntOutOfBounds(); // 58, because x * 1e58 > 2 ** 192 if x != 0
reserve-protocol/contracts/libraries/Fixed.sol::215 => uint256 coeff = uint256(10**abs(decimals));
reserve-protocol/contracts/libraries/Fixed.sol::392 => decimals -= 18; // shift so that toUint happens at the same time.
reserve-protocol/contracts/libraries/Fixed.sol::394 => uint256 coeff = uint256(10**abs(decimals));
reserve-protocol/contracts/p1/BasketHandler.sol::117 => uint192 public constant MAX_TARGET_AMT = 1e3 * FIX_ONE; // {target/BU} max basket weight
reserve-protocol/contracts/p1/BasketHandler.sol::367 => uint256 rawDelta = uint256(p) * qty; // {D36} = {D18} * {D18}
reserve-protocol/contracts/p1/BasketHandler.sol::388 => return uint192(shiftDelta / FIX_ONE); // {D18} = {D36} / {D18}
reserve-protocol/contracts/p1/Distributor.sol::165 => require(share.rsrDist <= 10000, "RSR distribution too high");
reserve-protocol/contracts/p1/Distributor.sol::166 => require(share.rTokenDist <= 10000, "RToken distribution too high");
reserve-protocol/contracts/p1/RToken.sol::360 => uint192 nowStart = uint192(FIX_ONE * (block.number - 1)); // D18{block} = D18{1} * {block}
reserve-protocol/contracts/p1/StRSR.sol::45 => uint8 public constant decimals = 18;
reserve-protocol/contracts/p1/StRSR.sol::65 => uint192 private constant MAX_STAKE_RATE = 1e9 * FIX_ONE; // 1e9 D18{qStRSR/qRSR}
reserve-protocol/contracts/p1/StRSR.sol::87 => uint192 private constant MAX_DRAFT_RATE = 1e9 * FIX_ONE; // 1e9 D18{qDrafts/qRSR}
reserve-protocol/contracts/p1/mixins/Trading.sol::21 => uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::46 => int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::49 => int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;
reserve-protocol/contracts/plugins/governance/Governance.sol::156 => return (bal * 1e8) / totalSupply;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::51 => return 18;
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::36 => return 18;
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::24 => return (1e18, 1e18);
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::35 => return 2e18;
reserve-protocol/contracts/plugins/mocks/WETH.sol::23 => uint8 public decimals = 18;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::216 => 0x0000000000000000000000000000000000000000000000000000000000000001;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::219 => 0xffffffffffffffffffffffffffffffffffffffff000000000000000000000001;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::657 => require(value < 2**96, "SafeCast: value doesn't fit in 96 bits");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::662 => require(value < 2**64, "SafeCast: value doesn't fit in 64 bits");
```



### [N04] Use a more recent version of solidity




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



### [N05] Variable names that consist of all capital letters should be reserved for `const`/`immutable `variables

#### Impact
If the variable needs to be different based on which class it comes from, a view/pure function should be used instead (e.g. like [this](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/76eee35971c2541585e05cbf258510dda7b2fbc6/contracts/token/ERC20/extensions/draft-IERC20Permit.sol#L59)).
#### Findings:
```
reserve-protocol/contracts/p1/Distributor.sol::17 => EnumerableSet.AddressSet internal destinations;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::30 => ATokenMock internal aToken;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::951 => IterableOrderedOrderSet.Data internal data;
```



### [N06] Use a more recent version of solidity

#### Impact
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions
#### Findings:
```
reserve-protocol/contracts/libraries/RedemptionBattery.sol::20 => using FixLib for uint192;
reserve-protocol/contracts/p1/AssetRegistry.sol::13 => using EnumerableSet for EnumerableSet.AddressSet;
reserve-protocol/contracts/p1/BackingManager.sol::21 => using FixLib for uint192;
reserve-protocol/contracts/p1/BackingManager.sol::22 => using SafeERC20Upgradeable for IERC20Upgradeable;
reserve-protocol/contracts/p1/BasketHandler.sol::62 => using BasketLibP1 for Basket;
reserve-protocol/contracts/p1/BasketHandler.sol::63 => using FixLib for uint192;
reserve-protocol/contracts/p1/BasketHandler.sol::111 => using BasketLibP1 for Basket;
reserve-protocol/contracts/p1/BasketHandler.sol::112 => using CollateralStatusComparator for CollateralStatus;
reserve-protocol/contracts/p1/BasketHandler.sol::113 => using EnumerableSet for EnumerableSet.AddressSet;
reserve-protocol/contracts/p1/BasketHandler.sol::114 => using EnumerableSet for EnumerableSet.Bytes32Set;
reserve-protocol/contracts/p1/BasketHandler.sol::115 => using FixLib for uint192;
reserve-protocol/contracts/p1/Broker.sol::19 => using EnumerableSet for EnumerableSet.AddressSet;
reserve-protocol/contracts/p1/Broker.sol::20 => using FixLib for uint192;
reserve-protocol/contracts/p1/Broker.sol::21 => using SafeERC20Upgradeable for IERC20Upgradeable;
reserve-protocol/contracts/p1/Broker.sol::22 => using Clones for address;
reserve-protocol/contracts/p1/Deployer.sol::29 => using Clones for address;
reserve-protocol/contracts/p1/Distributor.sol::13 => using SafeERC20Upgradeable for IERC20Upgradeable;
reserve-protocol/contracts/p1/Distributor.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/p1/Distributor.sol::15 => using EnumerableSet for EnumerableSet.AddressSet;
reserve-protocol/contracts/p1/Furnace.sol::13 => using FixLib for uint192;
reserve-protocol/contracts/p1/RToken.sol::25 => using RedemptionBatteryLib for RedemptionBatteryLib.Battery;
reserve-protocol/contracts/p1/RToken.sol::26 => using SafeERC20Upgradeable for IERC20Upgradeable;
reserve-protocol/contracts/p1/RevenueTrader.sol::15 => using FixLib for uint192;
reserve-protocol/contracts/p1/RevenueTrader.sol::16 => using SafeERC20Upgradeable for IERC20Upgradeable;
reserve-protocol/contracts/p1/StRSR.sol::34 => using CountersUpgradeable for CountersUpgradeable.Counter;
reserve-protocol/contracts/p1/StRSR.sol::35 => using SafeERC20Upgradeable for IERC20Upgradeable;
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::50 => using FixLib for uint192;
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::51 => using TradeLib for TradeInfo;
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::52 => using TradeLib for IBackingManager;
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::16 => using Address for address;
reserve-protocol/contracts/p1/mixins/RewardableLib.sol::17 => using SafeERC20 for IERC20;
reserve-protocol/contracts/p1/mixins/TradeLib.sol::19 => using FixLib for uint192;
reserve-protocol/contracts/p1/mixins/Trading.sol::17 => using FixLib for uint192;
reserve-protocol/contracts/p1/mixins/Trading.sol::18 => using SafeERC20Upgradeable for IERC20Upgradeable;
reserve-protocol/contracts/plugins/aave/ERC20.sol::35 => using SafeMath for uint256;
reserve-protocol/contracts/plugins/aave/ERC20.sol::36 => using Address for address;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::35 => using SafeERC20 for IERC20;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::36 => using SafeMath for uint256;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::37 => using WadRayMath for uint256;
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::38 => using RayMathNoRounding for uint256;
reserve-protocol/contracts/plugins/assets/ATokenFiatCollateral.sol::35 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/ATokenFiatCollateral.sol::36 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/Asset.sol::10 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/Asset.sol::11 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::15 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::16 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::15 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol::16 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::13 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/EURFiatCollateral.sol::15 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::39 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::40 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/NonFiatCollateral.sol::15 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::12 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::13 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::14 => using FixLib for uint192;
reserve-protocol/contracts/plugins/assets/SelfReferentialCollateral.sol::15 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::28 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::7 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::8 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::9 => using Address for address;
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::10 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::9 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::9 => using SafeERC20 for IERC20;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::10 => using SafeMath for uint64;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::11 => using SafeMath for uint96;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::12 => using SafeMath for uint256;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::13 => using SafeCast for uint256;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::14 => using IterableOrderedOrderSet for IterableOrderedOrderSet.Data;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::15 => using IterableOrderedOrderSet for bytes32;
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::16 => using IdToAddressBiMap for IdToAddressBiMap.Data;
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::47 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::48 => using SafeERC20 for IERC20;
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::13 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::14 => using SafeERC20 for IERC20;
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::15 => using EnumerableSet for EnumerableSet.AddressSet;
reserve-protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::16 => using SafeERC20 for IERC20Metadata;
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::7 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::7 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::19 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::10 => using FixLib for uint192;
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::11 => using OracleLib for AggregatorV3Interface;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::114 => using SafeMath for uint256;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::115 => using Address for address;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::211 => using SafeMath for uint96;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::212 => using IterableOrderedOrderSet for bytes32;
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::949 => using IterableOrderedOrderSet for IterableOrderedOrderSet.Data;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::23 => using FixLib for uint192;
reserve-protocol/contracts/plugins/trading/GnosisTrade.sol::24 => using SafeERC20Upgradeable for IERC20Upgradeable;
```



### [N07] Event is missing `indexed` fields

#### Impact
Each event should use three indexed fields if there are three or more fields
#### Findings:
```
reserve-protocol/contracts/interfaces/IBasketHandler.sol::21 => event PrimeBasketSet(IERC20[] erc20s, uint192[] targetAmts, bytes32[] targetNames);
reserve-protocol/contracts/interfaces/IBasketHandler.sol::28 => event BasketSet(uint256 indexed nonce, IERC20[] erc20s, uint192[] refAmts, bool disabled);
reserve-protocol/contracts/interfaces/IBasketHandler.sol::34 => event BackupConfigSet(bytes32 indexed targetName, uint256 indexed max, IERC20[] erc20s);
reserve-protocol/contracts/interfaces/IDeployerRegistry.sol::7 => event DeploymentUnregistered(string version, IDeployer deployer);
reserve-protocol/contracts/interfaces/IDeployerRegistry.sol::8 => event DeploymentRegistered(string version, IDeployer deployer);
reserve-protocol/contracts/interfaces/IDeployerRegistry.sol::9 => event LatestChanged(string version, IDeployer deployer);
reserve-protocol/contracts/interfaces/IDistributor.sol::28 => event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);
reserve-protocol/contracts/interfaces/IRToken.sol::78 => event Redemption(address indexed redeemer, uint256 indexed amount, uint192 baskets);
reserve-protocol/contracts/interfaces/IRToken.sol::83 => event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);
reserve-protocol/contracts/plugins/aave/IAToken.sol::15 => event Mint(address indexed from, uint256 value, uint256 index);
reserve-protocol/contracts/plugins/aave/IAToken.sol::37 => event Burn(address indexed from, address indexed target, uint256 value, uint256 index);
reserve-protocol/contracts/plugins/aave/IAToken.sol::46 => event BalanceTransfer(address indexed from, address indexed to, uint256 value, uint256 index);
reserve-protocol/contracts/plugins/aave/IAaveIncentivesController.sol::8 => event RewardsClaimed(address indexed user, address indexed to, uint256 amount);
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::95 => event Transfer(address indexed from, address indexed to, uint256 value);
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::101 => event Approval(address indexed owner, address indexed spender, uint256 value);
```



### [N08] Unused file


#### Findings:
```
reserve-protocol/contracts/plugins/aave/ERC20.sol::1 => // SPDX-License-Identifier: MIT
reserve-protocol/contracts/plugins/aave/ReentrancyGuard.sol::1 => // SPDX-License-Identifier: MIT
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::1 => // SPDX-License-Identifier: MIT
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::1 => // SPDX-License-Identifier: MIT
```



### [N09] `2**<n> - 1` should be re-written as `type(uint<n>).max`

#### Impact
Earlier versions of solidity can use uint<n>(-1) instead. Expressions not including the - 1 can often be re-written to accomodate the change (e.g. by using a > rather than a >=, which will also save some gas)
#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::212 => if (decimals <= -59) return (rounding == CEIL ? 1 : 0); // 59, because 1e58 > 2**192
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::657 => require(value < 2**96, "SafeCast: value doesn't fit in 96 bits");
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::662 => require(value < 2**64, "SafeCast: value doesn't fit in 64 bits");
```



### [N10] `public` functions not called by the contract should be declared `external` instead

#### Impact
Contracts are allowed to override their parents’ functions and change the visibility from public to external .
#### Findings:
```
reserve-protocol/contracts/libraries/test/ArrayCallerMock.sol::8 => function allUnique(IERC20[] memory arr) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/ArrayCallerMock.sol::12 => function sortedAndAllUnique(IERC20[] memory arr) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::9 => function toFix_(uint256 x) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::12 => function shiftl_toFix_(uint256 x, int8 d) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::15 => function shiftl_toFix_Rnd(uint256 x, int8 d, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::19 => function divFix_(uint256 x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::22 => function divuu_(uint256 x, uint256 y) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::26 => function fixMin_(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::29 => function fixMax_(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::32 => function abs_(int256 x) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::35 => function divrnd_(uint256 n, uint256 d, RoundingMode rnd) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::38 => function toUint(uint192  x) public pure returns (uint256 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::41 => function toUintRnd(uint192  x, RoundingMode rnd) public pure returns (uint256 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::44 => function shiftl(uint192  x, int8 decimals) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::47 => function shiftlRnd(uint192  x, int8 decimals, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::50 => function plus(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::53 => function plusu(uint192  x, uint256 y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::56 => function minus(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::59 => function minusu(uint192  x, uint256 y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::62 => function mul(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::65 => function mulRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::68 => function mulu(uint192  x, uint256 y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::71 => function div(uint192  x, uint192  y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::74 => function divRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::77 => function divu(uint192  x, uint256 y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::80 => function divuRnd(uint192  x, uint256 y, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::83 => function powu(uint192  x, uint48 y) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::86 => function lt(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::89 => function lte(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::92 => function gt(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::95 => function gte(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::98 => function eq(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::101 => function neq(uint192  x, uint192  y) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::104 => function near(uint192  x, uint192  y, uint192  epsilon) public pure returns (bool) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::109 => function shiftl_toUint(uint192  x, int8 d) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::112 => function shiftl_toUintRnd(uint192  x, int8 d, RoundingMode rnd) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::115 => function mulu_toUint(uint192  x, uint256 y) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::118 => function mulu_toUintRnd(uint192  x, uint256 y, RoundingMode rnd) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::121 => function mul_toUint(uint192  x, uint192  y) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::124 => function mul_toUintRnd(uint192  x, uint192  y, RoundingMode rnd) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::127 => function muluDivu(uint192  x, uint256 y, uint256 z) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::130 => function muluDivuRnd(uint192  x, uint256 y, uint256 z, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::133 => function mulDiv(uint192  x, uint192  y, uint192  z) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::136 => function mulDivRnd(uint192  x, uint192  y, uint192  z, RoundingMode rnd) public pure returns (uint192 ) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::141 => function mulDiv256_(uint256 x, uint256 y, uint256 z) public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::145 => public pure returns (uint256) {
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::148 => function fullMul_(uint256 x, uint256 y) public pure returns (uint256 h, uint256 l) {
reserve-protocol/contracts/mixins/Auth.sol::107 => function pausedOrFrozen() public view returns (bool) {
reserve-protocol/contracts/mixins/Auth.sol::180 => function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
reserve-protocol/contracts/mixins/Auth.sol::187 => function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
reserve-protocol/contracts/mixins/Versioned.sol::11 => function version() public pure virtual override returns (string memory) {
reserve-protocol/contracts/p1/AssetRegistry.sol::46 => function refresh() public {
reserve-protocol/contracts/p1/BackingManager.sol::256 => function setTradingDelay(uint48 val) public governance {
reserve-protocol/contracts/p1/BackingManager.sol::263 => function setBackingBuffer(uint192 val) public governance {
reserve-protocol/contracts/p1/BasketHandler.sol::279 => function status() public view returns (CollateralStatus status_) {
reserve-protocol/contracts/p1/BasketHandler.sol::296 => function quantity(IERC20 erc20) public view returns (uint192) {
reserve-protocol/contracts/p1/BasketHandler.sol::432 => function basketsHeldBy(address account) public view returns (uint192 baskets) {
reserve-protocol/contracts/p1/Broker.sol::133 => function setAuctionLength(uint48 newAuctionLength) public governance {
reserve-protocol/contracts/p1/Distributor.sol::141 => function totals() public view returns (RevenueTotals memory revTotals) {
reserve-protocol/contracts/p1/Furnace.sol::88 => function setPeriod(uint48 period_) public governance {
reserve-protocol/contracts/p1/Furnace.sol::96 => function setRatio(uint192 ratio_) public governance {
reserve-protocol/contracts/p1/RToken.sol::589 => function setIssuanceRate(uint192 val) public governance {
reserve-protocol/contracts/p1/RToken.sol::602 => function setScalingRedemptionRate(uint192 val) public governance {
reserve-protocol/contracts/p1/RToken.sol::615 => function setRedemptionRateFloor(uint256 val) public governance {
reserve-protocol/contracts/p1/StRSR.sol::425 => function exchangeRate() public view returns (uint192) {
reserve-protocol/contracts/p1/StRSR.sol::604 => function totalSupply() public view returns (uint256) {
reserve-protocol/contracts/p1/StRSR.sol::608 => function balanceOf(address account) public view returns (uint256) {
reserve-protocol/contracts/p1/StRSR.sol::622 => function transfer(address to, uint256 amount) public returns (bool) {
reserve-protocol/contracts/p1/StRSR.sol::632 => function approve(address spender, uint256 amount) public returns (bool) {
reserve-protocol/contracts/p1/StRSR.sol::645 => ) public returns (bool) {
reserve-protocol/contracts/p1/StRSR.sol::651 => function increaseAllowance(address spender, uint256 addedValue) public returns (bool) {
reserve-protocol/contracts/p1/StRSR.sol::657 => function decreaseAllowance(address spender, uint256 subtractedValue) public returns (bool) {
reserve-protocol/contracts/p1/StRSR.sol::774 => ) public {
reserve-protocol/contracts/p1/StRSR.sol::786 => function nonces(address owner) public view returns (uint256) {
reserve-protocol/contracts/p1/StRSR.sol::812 => function setUnstakingDelay(uint48 val) public governance {
reserve-protocol/contracts/p1/StRSR.sol::820 => function setRewardPeriod(uint48 val) public governance {
reserve-protocol/contracts/p1/StRSR.sol::828 => function setRewardRatio(uint192 val) public governance {
reserve-protocol/contracts/p1/StRSRVotes.sol::59 => function checkpoints(address account, uint48 pos) public view returns (Checkpoint memory) {
reserve-protocol/contracts/p1/StRSRVotes.sol::63 => function numCheckpoints(address account) public view returns (uint48) {
reserve-protocol/contracts/p1/StRSRVotes.sol::67 => function delegates(address account) public view returns (address) {
reserve-protocol/contracts/p1/StRSRVotes.sol::71 => function getVotes(address account) public view returns (uint256) {
reserve-protocol/contracts/p1/StRSRVotes.sol::76 => function getPastVotes(address account, uint256 blockNumber) public view returns (uint256) {
reserve-protocol/contracts/p1/StRSRVotes.sol::82 => function getPastTotalSupply(uint256 blockNumber) public view returns (uint256) {
reserve-protocol/contracts/p1/StRSRVotes.sol::88 => function getPastEra(uint256 blockNumber) public view returns (uint256) {
reserve-protocol/contracts/p1/StRSRVotes.sol::114 => function delegate(address delegatee) public {
reserve-protocol/contracts/p1/StRSRVotes.sol::125 => ) public {
reserve-protocol/contracts/p1/mixins/Trading.sol::128 => function setMaxTradeSlippage(uint192 val) public governance {
reserve-protocol/contracts/p1/mixins/Trading.sol::135 => function setMinTradeVolume(uint192 val) public governance {
reserve-protocol/contracts/plugins/aave/ERC20.sol::66 => function name() public view returns (string memory) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::74 => function symbol() public view returns (string memory) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::91 => function decimals() public view returns (uint8) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::98 => function totalSupply() public view override returns (uint256) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::105 => function balanceOf(address account) public view override returns (uint256) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::117 => function transfer(address recipient, uint256 amount) public virtual override returns (bool) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::142 => function approve(address spender, uint256 amount) public virtual override returns (bool) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::163 => ) public virtual override returns (bool) {
reserve-protocol/contracts/plugins/aave/ERC20.sol::188 => function increaseAllowance(address spender, uint256 addedValue) public virtual returns (bool) {
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::83 => ) public {
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::257 => function rate() public view override returns (uint256) {
reserve-protocol/contracts/plugins/aave/StaticATokenLM.sol::262 => function getDomainSeparator() public view override returns (bytes32) {
reserve-protocol/contracts/plugins/assets/ATokenFiatCollateral.sol::45 => function refPerTok() public view override returns (uint192) {
reserve-protocol/contracts/plugins/assets/Asset.sol::86 => function refresh() public virtual override {
reserve-protocol/contracts/plugins/assets/Asset.sol::110 => function price() public view virtual returns (uint192, uint192) {
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::34 => function refresh() public virtual override {
reserve-protocol/contracts/plugins/assets/CTokenFiatCollateral.sol::44 => function refPerTok() public view override returns (uint192) {
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::37 => function refresh() public virtual override {
reserve-protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol::47 => function refPerTok() public view override returns (uint192) {
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::121 => function refresh() public virtual override(Asset, IAsset) {
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::168 => function status() public view returns (CollateralStatus) {
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::210 => function refPerTok() public view virtual returns (uint192) {
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::215 => function targetPerRef() public view virtual returns (uint192) {
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::63 => function refresh() public virtual override {
reserve-protocol/contracts/plugins/assets/RTokenAsset.sol::72 => function price() public view virtual returns (uint192, uint192) {
reserve-protocol/contracts/plugins/governance/Governance.sol::48 => function votingDelay() public view override(IGovernor, GovernorSettings) returns (uint256) {
reserve-protocol/contracts/plugins/governance/Governance.sol::52 => function votingPeriod() public view override(IGovernor, GovernorSettings) returns (uint256) {
reserve-protocol/contracts/plugins/governance/Governance.sol::90 => ) public override(Governor, IGovernor) returns (uint256 proposalId) {
reserve-protocol/contracts/plugins/governance/Governance.sol::100 => ) public override returns (uint256 proposalId) {
reserve-protocol/contracts/plugins/mocks/ATokenMock.sol::50 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::10 => bool public checkSoftDefault = true; // peg
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::11 => bool public checkHardDefault = true; // defi invariant
reserve-protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::26 => function refresh() public virtual override {
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::31 => function decimals() public view override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::39 => function transfer(address to, uint256 amount) public virtual override returns (bool) {
reserve-protocol/contracts/plugins/mocks/BadERC20.sol::52 => ) public virtual override returns (bool) {
reserve-protocol/contracts/plugins/mocks/CTokenMock.sol::23 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::36 => function updateAnswer(int256 _answer) public {
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::47 => function setInvalidTimestamp() public {
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::52 => function setInvalidAnsweredRound() public {
reserve-protocol/contracts/plugins/mocks/ERC1271Mock.sol::31 => function isValidSignature(bytes32, bytes memory) public view returns (bytes4 magicValue) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::605 => function registerUser(address user) public returns (uint64 userId) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::612 => function getUserId(address user) public returns (uint64 userId) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::621 => function getSecondsRemainingInBatch(uint256 auctionId) public view returns (uint256) {
reserve-protocol/contracts/plugins/mocks/EasyAuction.sol::628 => function containsOrder(uint256 auctionId, bytes32 order) public view returns (bool) {
reserve-protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol::15 => function price() public view virtual returns (uint192, uint192) {
reserve-protocol/contracts/plugins/mocks/MockableCollateral.sol::17 => function targetPerRef() public view override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::12 => function targetPerRef() public view virtual override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::23 => function targetPerRef() public view virtual override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::34 => function targetPerRef() public view virtual override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol::45 => function targetPerRef() public view virtual override returns (uint192) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::51 => function price() public view override(RTokenAsset, IAsset) returns (uint192 low, uint192 high) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::55 => function refresh() public virtual override(RTokenAsset, IAsset) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::69 => function status() public view virtual override returns (CollateralStatus) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::85 => function refPerTok() public view virtual returns (uint192) {
reserve-protocol/contracts/plugins/mocks/RTokenCollateral.sol::92 => function targetPerRef() public view virtual returns (uint192) {
reserve-protocol/contracts/plugins/mocks/USDCMock.sol::10 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/WBTCMock.sol::10 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/WETH.sol::37 => function deposit() public payable {
reserve-protocol/contracts/plugins/mocks/WETH.sol::42 => function withdraw(uint256 wad) public {
reserve-protocol/contracts/plugins/mocks/WETH.sol::49 => function totalSupply() public view returns (uint256) {
reserve-protocol/contracts/plugins/mocks/WETH.sol::53 => function approve(address guy, uint256 wad) public returns (bool) {
reserve-protocol/contracts/plugins/mocks/WETH.sol::59 => function transfer(address dst, uint256 wad) public returns (bool) {
reserve-protocol/contracts/plugins/mocks/ZeroDecimalMock.sol::10 => function decimals() public pure override returns (uint8) {
reserve-protocol/contracts/plugins/mocks/upgrades/AssetRegistryV2.sol::13 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/BackingManagerV2.sol::14 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/BasketHandlerV2.sol::13 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/BrokerV2.sol::13 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/DistributorV2.sol::13 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/FurnaceV2.sol::13 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/MainV2.sol::14 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/RTokenV2.sol::14 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/RevenueTraderV2.sol::14 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/upgrades/StRSRV2.sol::13 => function version() public pure override returns (string memory) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::907 => function owner() public view returns (address) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::926 => function renounceOwnership() public virtual onlyOwner {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::935 => function transferOwnership(address newOwner) public virtual onlyOwner {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::953 => function initializeEmptyList() public {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::957 => function insert(bytes32 value) public returns (bool) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::961 => function insertAt(bytes32 value, bytes32 at) public returns (bool) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::965 => function remove(bytes32 value) public returns (bool) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::969 => function removeKeepHistory(bytes32 value) public returns (bool) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::973 => function contains(bytes32 value) public view returns (bool) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::977 => function isEmpty() public view returns (bool) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::981 => function first() public view returns (bytes32) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::985 => function next(bytes32 value) public view returns (bytes32) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::989 => function nextMap(bytes32 value) public view returns (bytes32) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::993 => function prevMap(bytes32 value) public view returns (bytes32) {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::1017 => function smallerThan(bytes32 orderLeft, bytes32 orderRight) public pure returns (bool) {
```



### [N11] Numeric values having to do with time should use time units for readability

#### Impact
There are units for seconds, minutes, hours, days, and weeks
#### Findings:
```
reserve-protocol/contracts/interfaces/IDeployer.sol::46 => uint192 scalingRedemptionRate; // {1/hour} max fraction of supply that can be redeemed hourly
reserve-protocol/contracts/interfaces/IDeployer.sol::47 => uint256 redemptionRateFloor; // {qRTok/hour} the lowest possible hourly redemption limit
reserve-protocol/contracts/interfaces/IFacadeWrite.sol::63 => uint256 timelockDelay; // in seconds (used for timelock)
reserve-protocol/contracts/interfaces/IRToken.sol::181 => /// @return {1/hour} The maximum fraction of the RToken supply that can be redeemed at once
reserve-protocol/contracts/interfaces/IRToken.sol::187 => /// @return {qRTok/hour} The lowest possible hourly redemption limit
reserve-protocol/contracts/libraries/RedemptionBattery.sol::11 => uint48 constant BLOCKS_PER_HOUR = 300; // {blocks/hour}
reserve-protocol/contracts/libraries/RedemptionBattery.sol::14 => /// Models a "battery" which "recharges" linearly block by block, over roughly 1 hour.
reserve-protocol/contracts/libraries/RedemptionBattery.sol::23 => uint256 redemptionRateFloor; // {qRTok/hour} the floor of the battery charging rate
reserve-protocol/contracts/libraries/RedemptionBattery.sol::24 => uint192 scalingRedemptionRate; // {1/hour} charging rate as a fraction of supply
reserve-protocol/contracts/libraries/RedemptionBattery.sol::58 => // {qRTok/hour} = {qRTok} * D18{1/hour} / D18
reserve-protocol/contracts/libraries/RedemptionBattery.sol::66 => // {qRTok} = {qRTok} + {qRTok/hour} * {blocks} / {blocks/hour}
reserve-protocol/contracts/p1/Broker.sol::24 => uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
reserve-protocol/contracts/p1/Deployer.sol::81 => /// - Capital preservation first. Spending power preservation second. Permissionless
reserve-protocol/contracts/p1/Furnace.sol::19 => uint48 public period; // {seconds} How often to melt
reserve-protocol/contracts/p1/Furnace.sol::21 => uint48 public lastPayout; // {seconds} The last time we did a payout
reserve-protocol/contracts/p1/RToken.sol::34 => /// - Capital preservation first. Spending power preservation second. Permissionless
reserve-protocol/contracts/p1/RToken.sol::595 => /// @return {1/hour} The max redemption charging rate
reserve-protocol/contracts/p1/RToken.sol::600 => /// @param val {1/hour}
reserve-protocol/contracts/p1/RToken.sol::608 => /// @return {qRTok/hour} The min redemption charging rate, in {qRTok}
reserve-protocol/contracts/p1/RToken.sol::613 => /// @param val {qRTok/hour}
reserve-protocol/contracts/p1/StRSR.sol::135 => uint48 public rewardPeriod; // {s} The number of seconds between revenue payout events
reserve-protocol/contracts/p1/StRSR.sol::146 => // {seconds} The last time when rewards were paid out
reserve-protocol/contracts/p1/StRSR.sol::522 => //      the second half of the OR comparison is untestable because of the invariant:
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::297 => ///   sellPrice {UoA/sellTok} The worst-case price of the sell token on secondary markets
reserve-protocol/contracts/p1/mixins/RecollateralizationLib.sol::298 => ///   buyPrice {UoA/sellTok} The worst-case price of the buy token on secondary markets
reserve-protocol/contracts/plugins/aave/IAaveIncentivesController.sol::22 => * @return The asset index, the emission per second and the last updated timestamp
reserve-protocol/contracts/plugins/assets/Asset.sol::35 => /// @param priceTimeout_ {s} The number of seconds over which savedHighPrice decays to 0
reserve-protocol/contracts/plugins/assets/Asset.sol::39 => /// @param oracleTimeout_ {s} The number of seconds until a oracle value becomes invalid
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::12 => uint48 priceTimeout; // {s} The number of seconds over which saved prices decay
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::17 => uint48 oracleTimeout; // {s} The number of seconds until a oracle value becomes invalid
reserve-protocol/contracts/plugins/assets/FiatCollateral.sol::21 => uint48 delayUntilDefault; // {s} The number of seconds an oracle can mulfunction
reserve-protocol/contracts/plugins/assets/OracleLib.sol::12 => /// @param timeout The number of seconds after which oracle values should be considered stale
reserve-protocol/contracts/plugins/assets/OracleLib.sol::26 => uint48 secondsSince = uint48(block.timestamp - updateTime);
reserve-protocol/contracts/plugins/assets/OracleLib.sol::27 => if (secondsSince > timeout) revert StalePrice();
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::15 => /// @param priceTimeout_ {s} The number of seconds over which savedHighPrice decays to 0
reserve-protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol::19 => /// @param oracleTimeout_ {s} The number of seconds until a oracle value becomes invalid
reserve-protocol/contracts/plugins/mocks/WETH.sol::157 => permission, would make you directly or secondarily liable for
reserve-protocol/contracts/plugins/mocks/WETH.sol::429 => prior to 60 days after the cessation.
reserve-protocol/contracts/plugins/mocks/WETH.sol::434 => copyright holder, and you cure the violation prior to 30 days after
```



### [N12] NC-library/interface files should use fixed compiler versions, not floating ones


#### Findings:
```
reserve-protocol/contracts/libraries/Fixed.sol::3 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/test/ArrayCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/test/FixedCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/libraries/test/StringCallerMock.sol::2 => pragma solidity ^0.8.9;
reserve-protocol/contracts/plugins/aave/ERC20.sol::3 => pragma solidity ^0.6.0;
reserve-protocol/contracts/plugins/mocks/ChainlinkMock.sol::2 => pragma solidity ^0.8.0;
reserve-protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::2 => pragma solidity ^0.8.0;
```

### [N13] Interfaces should be moved to separate files

#### Impact
Most of the other interfaces in this project are in their own file in
 the interfaces directory. The interfaces below do not follow this 
pattern
#### Findings:
```
reserve-protocol/contracts/plugins/aave/IAToken.sol::8 => interface IAToken is IERC20, IScaledBalanceToken {
reserve-protocol/contracts/plugins/aave/IAaveIncentivesController.sol::5 => interface IAaveIncentivesController {
reserve-protocol/contracts/plugins/aave/IStaticATokenLM.sol::9 => interface IStaticATokenLM is IERC20 {
reserve-protocol/contracts/plugins/assets/ATokenFiatCollateral.sol::10 => interface IStaticAToken is IERC20Metadata {
reserve-protocol/contracts/plugins/assets/ICToken.sol::8 => interface ICToken is IERC20Metadata {
reserve-protocol/contracts/plugins/assets/ICToken.sol::20 => interface IComptroller {
reserve-protocol/contracts/plugins/mocks/AaveLendingPoolMock.sol::5 => interface IAaveLendingPool {
reserve-protocol/contracts/plugins/mocks/AaveLendingPoolMock.sol::9 => interface ILendingPoolAddressesProvider {
reserve-protocol/contracts/plugins/mocks/AaveLendingPoolMock.sol::13 => interface IAaveOracle {
reserve-protocol/contracts/plugins/mocks/GnosisMock.sol::11 => interface IBiddable {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::16 => interface AllowListVerifier {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::29 => interface IERC20 {
reserve-protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::942 => interface IWETH is IERC20 {
```




