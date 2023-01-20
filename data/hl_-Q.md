# Table of Contents

- [L-01] Require() should be used instead of assert()
- [L-02] Initialize function can be front-run
- [NC-01] Use a more recent version of solidity
- [NC-02] Best practice is to prevent signature malleability
- [NC-03] Best practice is to use Solidity time instead of big numbers

## [L-01] Require() should be used instead of assert()

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as require()/revert() do. 

assert() should be avoided even past solidity version 0.8.0 as its documentation states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.

For example: 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249

## [L-02] Initialize function can be front-run

The initialize function that initializes important contract state can be called by anyone. The attacker can initialize the contract before the legitimate deployer. In the best case for the victim, they notice it and have to redeploy their contract costing gas.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L167

## [NC-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)

Noted version 0.8.9 used here - for example: 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L2

## [NC-02] Best practice is to prevent signature malleability

Use OpenZeppelin’s ECDSA contract rather than calling ecrecover() directly.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L155

## [NC-03] Best practice is to use Solidity time instead of big numbers

As best practice and for better readability, write 1 years instead of 31536000. For example: 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33