# Number 1: BYTES CONSTANTS ARE MORE EFFICIENT THAN STRING CONSTANTS

Vulnerability details

### Impact

If data can fit into 32 bytes, then you should use bytes32 data type rather than bytes or strings as it is cheaper in solidity.

## Proof of Concept

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L31 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L5 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L6 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L7 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L8 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L9 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L10 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L11 

## Tools Used

Manual Analysis

### Recommended Mitigation Steps

Replace string constants by byte32 constants to save gas.
