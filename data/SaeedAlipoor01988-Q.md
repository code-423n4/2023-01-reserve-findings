0 address Check
0 address control should be done in these functions;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L468
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L162

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