0 address Check
0 address control should be done in these functions;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L468
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L162

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

////////////////////////////////////////////// ***** //////////////////////////////////////////////


What I found out is that the vault can be based on any erc20 token. So if I'm not mistaken, what is your strategy to manage deposits with fee-on-transfer/inflationary/deflationary/rebasing tokens?

in broker contract you transfer erc20 amount to GnosisTrade contract;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Broker.sol#L73

and then in GnosisTrade and init function, you set the sell variable to req.sell; 
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L91

if erc20 is fee-on-transfer/inflationary/deflationary/rebasing, you should set sell variable to balance of GnosisTrade for erc20 token.

////////////////////////////////////////////// ***** //////////////////////////////////////////////

AaveV2 approves the lending pool in the constructor

in the StaticATokenLM contract and constructor, we approve an unlimited amount to the lending pool address.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L101

now, what if in the future, the lending pool address in aave project gets changed? 
deposit function in the StaticATokenLM contract will not work anymore! and there is no function in StaticATokenLM  contract to set approve an unlimited amount to the new lending pool address.

////////////////////////////////////////////// ***** //////////////////////////////////////////////