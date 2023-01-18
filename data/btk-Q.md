### Total L issues

| Number | Issues Details                                                                     | Context       |
|--------|------------------------------------------------------------------------------------|---------------|
| [L-01] | Low level calls with solidity version 0.8.14 and lower can result in optimiser bug | 3             |
| [L-02] | Loss of precision due to rounding                                                  | 3             |
| [L-03] | `call()` should be used instead of `transfer()`                                    | 1             |
| [L-04] | `init()` function can be called by anyone                                          | 12            |
| [L-05] | Payout may be too soon                                                             | 1             |
| [L-06] | Integer overflow by unsafe casting                                                 | 11            |
| [L-07] | Allows malleable `SECP256K1` signatures                                            | 3             |
| [L-08] | Inconsistent validation of input                                                   | 1             |

### Total NC issues

| Number  | Issues Details                                                                   | Context       |
|---------|----------------------------------------------------------------------------------|---------------|
| [NC-01] | Use `require()` instead of `assert()`                                            | 20            |
| [NC-02] | Constants in comparisons should appear on the left side                          | 10            |
| [NC-03] | Non-usage of specific imports                                                    | All Contracts |
| [NC-04] | The `nonReentrant` modifier should occur before all other modifiers              | 1             |
| [NC-05] | Use a more recent version of solidity                                            | All Contracts |
| [NC-06] | Typos                                                                            | 4             |
| [NC-07] | Include `@return` parameters in NatSpec comments                                 | All Contracts |
| [NC-08] | Contracts should have full test coverage                                         | All Contracts |
| [NC-09] | Function writing does not comply with the `Solidity Style Guide`                 | All Contracts |
| [NC-10] | Use `bytes.concat()` and `string.concat()`                                       | 5             |
| [NC-11] | Solidity compiler optimizations can be problematic                               | 1             |
| [NC-12] | Mark visibility of `init()` functions as external                                | 2             |  
| [NC-13] | Value is not validated to be different than the existing one                     | 13            |
| [NC-14] | Lack of event emit                                                               | 2             |
| [NC-15] | Signature Malleability of EVM's `ecrecover()`                                    | 3             |
| [NC-16] | Critical changes should use-two step procedure                                   | 1             |
| [NC-17] | Add a timelock to critical functions                                             | 15            |
| [NC-18] | Use `immutable` instead of `constant` for values such as a call to keccak256()   | 6             |
| [NC-19] | Avoid shadowing inherited state variables                                        | 1             |
| [NC-20] | `revert()` Statements Should Have Descriptive Reason Strings                     | 5             |


## [L-01] Low level calls with solidity version 0.8.14 and lower can result in optimiser bug

The protocol is using low level calls with solidity version less then 0.8.14 which can result in optimizer bug.

> Ref: https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d

### Lines of code

- [StaticATokenLM.sol:264](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L264)
- [EasyAuction.sol:695](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L695)
- [EasyAuction.sol:846](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L846)

### Recommended Mitigation Steps

Consider upgrading to solidity 0.8.17

## [L-02] Loss of precision due to rounding

Loss of precision due to rounding in [unstake](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L257) and [withdraw](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L300) functions.

### Lines of code

- [StRSR.sol:224](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L224)
- [StRSR.sol:270](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L270)
- [StRSR.sol:324](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L324)

## [L-03] `call()` should be used instead of `transfer()`

The use of the deprecated ```transfer()``` function for a payable address will certainly make the transaction fail when:

1. The claimer smart contract does not implement a payable fallback function.

2. The claimer smart contract does implement a payable fallback which uses more than 2300 gas unit.

3. The claimer smart contract implements a payable fallback function that needs less than 2300 gas units but is called through proxy, raising the call's gas usage above 2300.

4. Some multisig wallets might also require higher gas than 2300.

A more detailed explanation can be found [here](https://consensys.net/diligence/blog/2019/09/stop-using-soliditys-transfer-now/).

```solidity
    function withdraw(uint256 wad) public {
        require(balanceOf[msg.sender] >= wad);
        balanceOf[msg.sender] -= wad;
        msg.sender.transfer(wad);
        emit Withdrawal(msg.sender, wad);
    }
```

### Lines of code

- [WETH.sol:45](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L45)

### Recommended Mitigation Steps

Consider using `call()` instead with the CEI pattern implemented correctly.

## [L-04] `init()` function can be called by anybody

`init()` function can be called anybody when the contract is not initialized.

```solidity
    function init(
        IMain main_,
        uint48 tradingDelay_,
        uint192 backingBuffer_,
        uint192 maxTradeSlippage_,
        uint192 minTradeVolume_
    ) external initializer {
        __Component_init(main_);
        __Trading_init(main_, maxTradeSlippage_, minTradeVolume_);

        assetRegistry = main_.assetRegistry();
        basketHandler = main_.basketHandler();
        distributor = main_.distributor();
        rsr = main_.rsr();
        rsrTrader = main_.rsrTrader();
        rTokenTrader = main_.rTokenTrader();
        rToken = main_.rToken();
        stRSR = main_.stRSR();

        setTradingDelay(tradingDelay_);
        setBackingBuffer(backingBuffer_);
    }
```

### Lines of code

- [GnosisTrade.sol:81](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L81)
- [InvalidBrokerMock.sol:26](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L26)
- [StRSR.sol:160](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L160)
- [RToken.sol:147](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L147)
- [RevenueTrader.sol:23](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L23)
- [Main.sol:26](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L26)
- [Furnace.sol:33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L33)
- [Distributor.sol:4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L41)
- [Broker.sol:51](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L51)
- [BasketHandler.sol:151](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L151)
- [BackingManager.sol:44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L44)
- [AssetRegistry.sol:34](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L34)

### Recommended Mitigation Steps

Add a `DEPLOYER` address and require that only him can call the `initialize()` function.

## [L-05] Payout may be too soon

The first timestamp should be based on when rewards are first payed, so that an appropriate duration of the cycle occurs, rather than during deployment.

```solidity
    payoutLastPaid = uint48(block.timestamp);
```

### Lines of code 
 
- [StRSR.sol:180](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L180)

## [L-06] Integer overflow by unsafe casting

Keep in mind that the version of solidity used, despite being greater than 0.8, does not prevent integer overflows during casting, it only does so in mathematical operations.

It is necessary to safely convert between the different numeric types.

```solidity
    endTime = uint48(block.timestamp) + auctionLength;
```

### Lines of code 

- [GnosisTrade.sol:103](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L103)
- [FiatCollateral.sol:134](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L134)
- [FiatCollateral.sol:195](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L195)
- [Asset.sol:95](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L95)
- [Asset.sol:137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L137)
- [StRSR.sol:180](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L180)
- [StRSR.sol#L561](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L561)
- [Furnace.sol:42](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L42)
- [Furnace.sol:71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L71)
- [Furnace.sol:74](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L74)
- [BasketHandler.sol:637](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L637)

### Recommended Mitigation Steps

Use OpenZeppelin [safeCast](https://docs.openzeppelin.com/contracts/3.x/api/utils#SafeCast) library.

## [L-07] Allows malleable `SECP256K1` signatures

Here, the `ecrecover()` method doesn't check the `bytes32 s` range.

Homestead [(EIP-2)](https://eips.ethereum.org/EIPS/eip-2) added this limitation, however the precompile remained unaltered. The majority of libraries, including OpenZeppelin, do this check.

> Ref: https://github.com/OpenZeppelin/openzeppelin-contracts/blob/7201e6707f6631d9499a569f492870ebdd4133cf/contracts/utils/cryptography/ECDSA.sol#L138-L149

### Lines of code

- [StaticATokenLM.sol:155](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L155)
- [StaticATokenLM.sol:193](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L193)
- [StaticATokenLM.sol:234](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L234)

### Recommended Mitigation Steps

Use OpenZeppelin's `ECDSA` library for signature verification.

## [L-08] Inconsistent validation of input    

While the `setPrimeBasket()` function check that `erc20s.length > 0`, the `setBackupConfig()` does not.

if the caller calls `setBackupConfig()` with `erc20s.length = 0 ` by mistake, it will update the `targetName` and `config.max` and delete the `conf.erc20s` array without updating it to a new value.  

```solidity
    function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts)
        external
        governance
    {
        require(erc20s.length > 0, "cannot empty basket");
        require(erc20s.length == targetAmts.length, "must be same length");
        requireValidCollArray(erc20s);

        // Clean up previous basket config
        for (uint256 i = 0; i < config.erc20s.length; ++i) {
            delete config.targetAmts[config.erc20s[i]];
            delete config.targetNames[config.erc20s[i]];
        }
        delete config.erc20s;

        // Set up new config basket
        bytes32[] memory names = new bytes32[](erc20s.length);

        for (uint256 i = 0; i < erc20s.length; ++i) {
            // This is a nice catch to have, but in general it is possible for
            // an ERC20 in the prime basket to have its asset unregistered.
            require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
            require(0 < targetAmts[i], "invalid target amount; must be nonzero");
            require(targetAmts[i] <= MAX_TARGET_AMT, "invalid target amount; too large");

            config.erc20s.push(erc20s[i]);
            config.targetAmts[erc20s[i]] = targetAmts[i];
            names[i] = assetRegistry.toColl(erc20s[i]).targetName();
            config.targetNames[erc20s[i]] = names[i];
        }

        emit PrimeBasketSet(erc20s, targetAmts, names);
    }

    function setBackupConfig(
        bytes32 targetName,
        uint256 max,
        IERC20[] calldata erc20s
    ) external governance {
        requireValidCollArray(erc20s);
        BackupConfig storage conf = config.backups[targetName];
        conf.max = max;
        delete conf.erc20s;

        for (uint256 i = 0; i < erc20s.length; ++i) {
            // This is a nice catch to have, but in general it is possible for
            // an ERC20 in the backup config to have its asset altered.
            require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
            conf.erc20s.push(erc20s[i]);
        }
        emit BackupConfigSet(targetName, max, erc20s);
    }

```

### Lines of code 

- [BasketHandler.sol:209](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L209-L241)
- [BasketHandler.sol:252](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L252-L269)

### Recommended Mitigation Steps

Add this check to `setBackupConfig()` function:

```solidity
        require(erc20s.length > 0);
```

## [NC-01] Use `require()` instead of `assert()`

Assert should not be used except for tests, require should be used. Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as `request()/revert()` did.

The big difference between the two is that the `assert()` function when false, uses up all the remaining gas and reverts all the changes made. Meanwhile, a `require()` statement when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

Assertion should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type `Panic(uint256)`. Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### Lines of code

- [GnosisTrade.sol:63](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L63)
- [GnosisTrade.sol:98](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L98)
- [GnosisTrade.sol:182](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L182)
- [RTokenAsset.sol:74](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L74)
- [FiatCollateral.sol:137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L137)
- [Asset.sol:98](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L98)
- [Asset.sol:112](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L112)
- [Asset.sol:147](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L147)
- [StaticATokenLM.sol:345](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L345)
- [Trading.sol:114](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L114)
- [TradeLib.sol:44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44)
- [TradeLib.sol:108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L108-L113)
- [TradeLib.sol:168](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L168)
- [TradeLib.sol:170](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L170)
- [RewardableLib.sol:78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78)
- [RewardableLib.sol:102](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L102)
- [RecollateralizationLib.sol:110](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110)
- [StRSR.sol:696](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696)
- [BasketHandler.sol:556](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556)
- [BackingManager.sol:249](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249)

### Recommended Mitigation Steps

Use `require()` instead of `assert()`

## [NC-02] Constants in comparisons should appear on the left side

Doing so will prevent [typo bug](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html).

```solidity
    assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
```

### Lines of code 
 
- [BackingManager.sol:249](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249)
- [Distributor.sol:16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L164)
- [Distributor.sol:114](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L114)
- [Distributor.sol:163](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L163)
- [Distributor.sol:168](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L168)
- [RevenueTrader.sol:57](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L57)
- [RToken.sol:502](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L502)
- [RToken.sol:672](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L672)
- [RToken.sol:754](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L754)
- [RToken.sol:804](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L804)

### Recommended Mitigation Steps

```solidity
    assert(0 == tradesOpen && !basketHandler.fullyCollateralized());
```

## [NC-03] Non-usage of specific imports 

Using import declarations of the form import `{<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation. 

The Solidity docs recommend specifying imported symbols explicitly.

> Ref: https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

### Lines of code

- **All contract**

### Recommended Mitigation Steps
Use specific imports syntax per [solidity docs](https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files) recommendation.

## [NC-04] The `nonReentrant` modifier should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers

```solidity
    function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {
        ITrade trade = trades[sell];
        if (address(trade) == address(0)) return;
        require(trade.canSettle(), "cannot settle yet");

        delete trades[sell];
        tradesOpen--;

        // == Interactions ==
        (uint256 soldAmt, uint256 boughtAmt) = trade.settle();
        emit TradeSettled(trade, trade.sell(), trade.buy(), soldAmt, boughtAmt);
    }
```

### Lines of code

- [Trading.sol:66](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L66)

### Recommended Mitigation Steps

Use the `nonReentrant` modifier first.

## [NC-05] Use a more recent version of solidity

Use a solidity version of at least 0.8.12 to get `string.concat()` instead of `abi.encodePacked(<string>,<string>)`.

> https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/

### Lines of code 

- **All Contract**

### Recommended Mitigation Steps

Consider upgrading to 0.8.12.

## [NC-06] Typos   

```solidity
/// @audit: lowLow

       /// lowLow should be nonzero when the asset might be worth selling
```
> [BasketHandler.sol:321](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L321)

```solidity
/// @audit: Mointain

       /// Mointain the overall backing policy; handout assets otherwise
```
> [BackingManager.sol:89](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L89)

```solidity
/// @audit: iff

       //     queue.right == left  iff  there are no more pending issuances in this queue
```
> [RToken.sol:106](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L106)

```solidity
/// @audit: adjaacent

       // issuances, and so any particular issuance is actually the _difference_ between two adjaacent
```
> [RToken.sol:109](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L109)

## [NC-07] Include return parameters in NatSpec comments

If Return parameters are declared, you must prefix them with `/// @return`. Some code analysis programs do analysis by reading [NatSpec](https://docs.soliditylang.org/en/v0.8.15/natspec-format.html) details, if they can't see the `@return` tag, they do incomplete analysis.

### Lines of code

- **All Contracts**

### Recommended Mitigation Steps

Include the `@return` argument in the NatSpec comments.

## [NC-08] Contracts should have full test coverage

While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.

### Lines of code 

- **All Contracts**

### Recommended Mitigation Steps

Line coverage percentage should be 100%.

## [NC-09] Function writing does not comply with the `Solidity Style Guide`

Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

Functions should be grouped according to their visibility and ordered:

- `constructor()`
- `receive()`  
- `fallback()`  
- `external / public / internal / private`
- `view / pure`

### Recommended Mitigation Steps

Follow Solidity Style Guide.

## [NC-10] Use `bytes.concat()` and `string.concat()`

- `bytes.concat()` vs `abi.encodePacked(<bytes>,<bytes>)`
- `string.concat()` vs `abi.encodePacked(<string>,<string>)`

 > https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=bytes.concat#the-functions-bytes-concat-and-string-concat

### Lines of code 

- [Deployer.sol:206](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L206)
- [Deployer.sol:207](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L207)
- [StaticATokenLM.sol:147](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L147)
- [StaticATokenLM.sol:175](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L175)
- [StaticATokenLM.sol:215](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L215)

### Recommended Mitigation Steps

Use `bytes.concat()` and `string.concat()`.

## [NC-011] Solidity compiler optimizations can be problematic

Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

```solidity
    const settings = useEnv('NO_OPT') ? {} : { optimizer: { enabled: true, runs: 200 } }
```

### Lines of code

- [hardhat.config.ts:25](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/hardhat.config.ts#L25)

### Recommended Mitigation Steps

Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug. Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## [NC-12] Mark visibility of `init()` functions as external  

- If someone wants to extend via inheritance, it might make more sense that the overridden `init()` function calls the internal {...}_init function, not the parent public `init()` function.

- External instead of public would give more the sense of the `init()` functions to behave like a constructor (only called on deployment, so should only be called externally)

- Security point of view, it might be safer so that it cannot be called internally by accident in the child contract

- It might cost a bit less gas to use external over public

- It is possible to override a function from external to public ("opening it up") but it is not possible to override a function from public to external ("narrow it down").

> Ref: https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3750

### Lines of code

- [InvalidBrokerMock.sol:31](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L31)
- [Main.sol:31](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L31)

### Recommended Mitigation Steps

Change the visibility of `init()` functions to external

## [NC-13] Value is not validated to be different than the existing one

While the value is validated to be in the constant bounds, it is not validated to be different than the existing one. Queueing the same value will cause multiple abnormal events to be emitted, will ultimately result in a no-op situation.

### Lines of code

- [Trading.sol:128](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L128)
- [Trading.sol:135](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L135)
- [StRSR.sol:812](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812)
- [StRSR.sol:820](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L820)
- [StRSR.sol:828](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L828)
- [RToken.sol:579](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L579)
- [RToken.sol#L589](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L589)
- [RToken.sol:602](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L602)
- [RToken.sol:615](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L615)
- [Furnace.sol:88](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L88)
- [Furnace.sol:96](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L96)
- [BackingManager.sol:256](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256)
- [BackingManager.sol:263](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L263)

### Recommended Mitigation Steps

Add a `require()` statement to check that the new value is different than the current one.

## [NC-14] Lack of event emit   

The below methods do not emit an event when the state changes, something that it's very important for dApps and users.

### Lines of code 

- [StRSR.sol:80](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L803)
- [StRSR.sol:807](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L807)

## [NC-15] Signature Malleability of EVM's `ecrecover()`

The function calls the Solidity `ecrecover()` function directly to verify the given signatures. However, the `ecrecover()` EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for this contract, I recommend using the battle-tested OpenZeppelin's ECDSA library.

### Lines of code 

- [StaticATokenLM.sol:155](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L155)
- [StaticATokenLM.sol:193](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L193)
- [StaticATokenLM.sol:234](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L234)

### Recommended Mitigation Steps

Use the ecrecover function from OpenZeppelin's ECDSA library for signature verification. (Ensure using a version > 4.7.3 for there was a critical bug >= 4.1.0 < 4.7.3).

## [NC-16] Critical changes should use-two step procedure  

The `Main.sol` inherits Openzeppelin `OwnableUpgradeable.sol` contract, which does not have a two-step procedure for critical changes.

```solidity
    /**
     * @dev Transfers ownership of the contract to a new account (`newOwner`).
     * Can only be called by the current owner.
     */
    function transferOwnership(address newOwner) public virtual onlyOwner {
        require(newOwner != address(0), "Ownable: new owner is the zero address");
        _transferOwnership(newOwner);
    }

    /**
     * @dev Transfers ownership of the contract to a new account (`newOwner`).
     * Internal function without access restriction.
     */
    function _transferOwnership(address newOwner) internal virtual {
        address oldOwner = _owner;
        _owner = newOwner;
        emit OwnershipTransferred(oldOwner, newOwner);
    }
```

### Lines of code 

- [Main.sol:6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L6)

### Recommended Mitigation Steps

Consider adding two step procedure on the critical functions where the first is announcing a pending new owner and the new address should then claim its ownership.

## [NC-17] Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate.

### Lines of code

- [Trading.sol:128](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L128)
- [Trading.sol:135](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L135)
- [StRSR.sol:812](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812)
- [StRSR.sol:820](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L820)
- [StRSR.sol:828](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L828)
- [RToken.sol:579](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L579)
- [RToken.sol#L589](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L589)
- [RToken.sol:602](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L602)
- [RToken.sol:615](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L615)
- [Furnace.sol:88](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L88)
- [Furnace.sol:96](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L96)
- [BackingManager.sol:256](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256)
- [BackingManager.sol:263](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L263)
- [StRSR.sol:80](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L803)
- [StRSR.sol:807](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L807)

### Recommended Mitigation Steps

Consider adding a timelock to the critical changes.

## [NC-18] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

### Lines of code 

- [StaticATokenLM.sol:41](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L41-L44)
- [StaticATokenLM.sol:45](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L45-L48)
- [StaticATokenLM.sol:49](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L49-L52)
- [StaticATokenLM.sol:53](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L53-L56)
- [StRSRVotes.sol:27](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSRVotes.sol#L27-L28)
- [StRSR.sol:126](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L126-L129)

### Recommended Mitigation Steps

Use `immutable` instead of `constants` 

## [NC-19] Avoid shadowing inherited state variables  

In `CTokenFiatCollateral .sol` there is a local variable named `erc20`, but there is a state variable named `erc20` in the inherited `Asset .sol` with the same name. This use causes compilers to issue warnings, negatively affecting checking and code readability.

```solidity
        ICToken erc20 = ICToken(address(config.erc20));
        referenceERC20Decimals = IERC20Metadata(erc20.underlying()).decimals();
```

```solidity
        IERC20Metadata public immutable erc20;
```

### Lines of code 

- [CTokenFiatCollateral.sol:27](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/assets/CTokenFiatCollateral.sol#L27-L28)
- [Asset.sol:15](https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/assets/Asset.sol#L15)

### Recommended Mitigation Steps

Avoid using variables with the same name.

## [NC-20] `revert()` Statements Should Have Descriptive Reason Strings

The correct and clear error description explains to the user why the function reverts, but the error descriptions below in the project are not self-explanatory. These error descriptions are very important in the debug features of DApps like Tenderly.

### Lines of code

- [RTokenAsset.sol:78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L78)
- [FiatCollateral.sol:149](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L149)
- [Asset.sol:102](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L102)
- [Asset.sol:116](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L116)
- [Asset.sol:133](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L133)

### Recommended Mitigation Steps

Error definitions should be added to the `revert("...")` block
