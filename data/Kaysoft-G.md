## [GAS-01] x = x + y cost less gas than x +=y for state variables. Same for x = x -y.

 files: 
- [contracts/p1/Furnace.sol#L81](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L81)
- [contracts/p1/RToken.sol#L330](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L330)


## [GAS-02] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for-loop and while-loops.

Files:

- [contracts/p1/BasketHandler.sol#L653](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L653)

- [contracts/p1/Distributor.sol#L133](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L133)

