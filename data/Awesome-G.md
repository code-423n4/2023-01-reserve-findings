# 1. Splitting `require()` Statements That Use `&&` Saves Gas - (Saves ~`3` Gas per `&&`)

Instead of using the `&&` operator in a single require statement to check multiple conditions, using various require statements with 1 condition per require statement will save ~3 GAS per `&&`.

Affected line of code:

- [Broker.sol#L134-L137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L134-L137)
- [Auth.sol#L181](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181)
- [Auth.sol#L188](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188)
- [Deployer.sol#L48-L65](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48-L65)
- [Deployer.sol#L109](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109)
- [Furnace.sol#L89](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89)
- [RevenueTrader.sol#L72](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72)
- [RToken.sol#L410](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410)
- [RToken.sol#L590](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590)
- [RToken.sol#L623](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L623)
- [RToken.sol#L741](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L741)
- [RToken.sol#L813](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813)
- [StRSR.sol#L813](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813)
- [StRSR.sol#L821](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L821)

# 2. Regular addition/subtraction assignment saves gas

Using standard addition or subtraction assignment (`x = x + y` or `x = x - y`) rather than the shorthand versions (`x += y` or `x -= y`) saves gas. This can save approximately 22 gas per occurrence.

Affected line of code:

- [Fixed.sol#L104](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/libraries/Fixed.sol#L104)
- [BasketHandler.sol#L345-L346](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L345-L346)
- [BasketHandler.sol#L636](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L636)

# 3. Uncheck arithmetics operations that can’t underflow/overflow

Solidity version 0.8+ has an implicit overflow and underflow check on unsigned integers.

When an overflow or an underflow isn’t possible, some gas can be saved using an unchecked block.

For example, the code at [RToken.sol#L270-L276](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270-L276)
can be refactored to as:
```solidity
File: /contracts/p1/RToken.sol
    for (uint256 i = 0; i < erc20s.length;) {
        IERC20Upgradeable(erc20s[i]).safeTransferFrom(
            issuer,
            address(backingManager),
            deposits[i]
        );

        unchecked {
            ++i;
        }
    }
```

This is affected in multiple files:

- [Array.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/libraries/Array.sol)
- [RewardableLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/p1/mixins/RewardableLib.sol)
- [RecollateralizationLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/p1/mixins/RecollateralizationLib.sol)
- [RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/p1/RToken.sol)
- [Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/p1/Distributor.sol)
- [BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/p1/BasketHandler.sol)
- [BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/p1/BackingManager.sol)
- [AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2b/contracts/p1/AssetRegistry.sol)