0 address check
0 address control should be done in these functions;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L468

////////////////////////////////////////////// ***** //////////////////////////////////////////////

initialize function can be called by everyone and front-run can get happen

Given the public access, this is susceptible to front-running by an attacker who can initialize this with arbitrary data before the deployer. Reinitialization will require contract redeployment because initialization can be done only once (thanks to the OpenZeppelin initializer modifier).

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/AssetRegistry.sol#L21
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/BackingManager.sol#L29
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Broker.sol#L35
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Furnace.sol#L23
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Main.sol#L23
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L86

source:
https://github.com/code-423n4/2022-01-livepeer-findings/issues/112

the same problem solved here :
https://github.com/code-423n4/2023-01-astaria/blob/1bfc58b42109b839528ab1c21dc9803d663df898/src/CollateralToken.sol#L86