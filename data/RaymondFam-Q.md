## Typo mistakes
[File: IMain.sol#L32](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L32)

```diff
- *   - usually (but not always) contain sizeable state that require a proxy
+ *   - usually (but not always) contain sizable state that requires a proxy
```
## Modularity on import usages
For cleaner Solidity code in conjunction with the rule of modularity and modular programming, use named imports with curly braces instead of adopting the global import approach.

For instance, the import instances below could be refactored as follows:

[File: Component.sol#L4-L9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L4-L9)

```diff
- import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
+ import {Initializable} from "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
- import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
+ import {UUPSUpgradeable.sol} from "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
    [ ... ]
```
## Interfaces should be imported
Some contracts have interface(s) showing up in its/their entirety at the top/bottom of the contract facilitating an ease of references on the same file page. This has, in certain instances, made the already large contract size to house over a thousand code lines. Consider saving the interfaces entailed as `Ixxx.sol` respectively, and have them imported like all other files.

Here are some of the instances entailed:

[File: IMain.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol)

## Inconsistency in interface naming
Some interfaces in the code bases are named without the prefix `I` that could cause confusion to developers and readers referencing or interacting with the protocol. Consider conforming to Solidity's naming conventions by having the instances below refactored as follow:

[File: IMain.sol#L166](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L166)

```diff
- interface TestIMain is IMain {
+ interface ITestIMain is IMain {
```
## Abbreviations
Abbreviations should be documented or provided with their definitions to help users/developers pick up the intended meaning and avoid guessing/confusion.

Here are some of the instances entailed:

[File: RecollateralizationLib.sol#L25](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L25) 

```solidity
    uint192 minTradeVolume; // {UoA} @ audit Unit of Accounting/Action/Assessment/???
```
[File: RecollateralizationLib.sol#L87](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L87)

```solidity
        // Compute basket range -  {BU} @ audit Basket Unit/Business Unit/???
```
## `require()` over `assert()`
As documented by [Solidity Documentations](https://docs.soliditylang.org/en/v0.8.17/control-structures.html):

"Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Language analysis tools can evaluate your contract to identify the conditions and function calls which will cause a Panic."

The protocol uses `assert()` in multiple places of the code bases. Despite the strip of gas saving benefits for Solidity version ^0.8.0, `require()` should still be used for checking error conditions on inputs and return values.

Here are some of the instances entailed:

[File: RecollateralizationLib.sol#L110](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110)

```solidity
        assert(doTrade); // @ audit Associated with the return value, `doTrade`
```
[File: TradeLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol)

```solidity
            // @ audit Associated with the input parameter, `trade`
44:        assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX); 

            // @ audit Associated with the input parameter, `trade`
108:        assert( 
109:            trade.sellPrice > 0 &&
110:                trade.sellPrice < FIX_MAX && 
111:                trade.buyPrice > 0 &&
112:                trade.buyPrice < FIX_MAX
113:        );

168:            assert(errorCode == 0x11 || errorCode == 0x12); // @ audit Associated with try catch

170:            assert(keccak256(reason) == UIntOutofBoundsHash); // audit Associated with try catch
```
## Descriptive and Verbose Options
Consider making the naming of local variables more verbose and descriptive so all other peer developers would better be able to comprehend the intended statement logic, significantly enhancing the code readability.

Here are some of the instances entailed:

[File: TradeLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol)

```solidity
55:         uint192 s = trade.sellAmount > maxSell ? maxSell : trade.sellAmount; // {sellTok}

59:        uint192 b = safeMulDivCeil(
```