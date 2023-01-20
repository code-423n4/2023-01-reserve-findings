# QA Report

## [NC-1] Unused imports are used
There are 8 instances of it & there permalinks are below
it is  recommend to remove unused imports for better readability of the code
[BackingManager.sol#L10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L10),
[Broker.sol#L10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L10),
[Distributor.sol#L9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L9),
[Furnace.sol#L4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L4),
[Main.sol#L6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L6),
[StRSR.sol#L4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L4),
[StRSR.sol#L6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L6),
[StRSR.sol#L13](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L13)

## [NC- 2] Use a more recent version of solidity
For now all the contracts are at 0.8.9 
For security, it is best practice to use the latest Solidity version.
for more recent verison can look at ref - https://github.com/ethereum/solidity/blob/develop/Changelog.md

