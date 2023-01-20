## [L-01] Initializers can be frontrunnable.

Some contracts have an initializing function that has no owner modifier. Anyone can call the initializer and anyone can frontrun the protocol's initializer. The protocol has to check

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L167
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L41

## [L-02] Require() should be used instead of assert()

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do. 

assert() should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249

## [N-01] Numeric values having to do with time should use time units for readability

There are units for seconds, minutes, hours, days, and weeks

Use 1 years instead of 31536000 for better readability

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33

## [N-02] Large multiples of ten should use scientific notation (eg 1e6) rahter than decimal literals (eg 1000000), for readability

Use 1e4 instead of 10,000 or use a constant / variable.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165-L166

## [N-03] Use a more recent version of solidity

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)

Noted version 0.8.9 used here - for example:

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L2

## [N-04] Best practice is to prevent signature malleability

Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L155