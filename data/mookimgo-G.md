# ChainlinkFeed AggregatorV3Interface's decimals should be cached as an immutable variable at constructor

This `chainlinkFeed.decimals()` is called each time when query a price, which is unnecessary, as Chainlink won't change decimals.

Refactor this code to cache decaimls as an immutable variable should reduce a function call, saving gas.

https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/plugins/assets/OracleLib.sol#L30