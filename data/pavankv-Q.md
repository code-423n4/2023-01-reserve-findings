## 1.Re-arange the struct to save slot and gas :-

Code snippet:-
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L116
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L75

Old:-
  struct BasketRange {
        uint192 top; // {BU}
        uint192 bottom; // {BU}
    }

## 2 .Update to latest version of pragma :-
In new version features added and issues are fixed .

Code snippet:-
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L2

and all files in scope
Can be change like this :-
  struct BasketRange {
        uint128 top; // {BU}
        uint128 bottom; // {BU}
    }

Or :-
  struct BasketRange {
        uint256 top; // {BU}
        uint256 bottom; // {BU}
    }

## 3. Don't leave empty Brackets :-

code snippet:-
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L25
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L23

## 4 .Use require() instead of assert() :-

Use require instead of asset because assert function should only be used to examine invariants and test for internal problems.The require function should be used to check return values from calls to external contracts or to guarantee that valid conditions, such as inputs or contract state variables, are satisfied.

code snippet:-
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44 

## 5 .Front-runnable inti() function :-


code snippet:-
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L160
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L34 
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L44
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L151
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L51
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L41
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L33
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L26
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L147
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L23

Recommedation :-
Add access control to init() function .

## 6. When using _msg.sender() , import context.sol from openzeppelin :-

code snippet:-
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L168
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L187
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L88
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L125
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L178
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L407
 

## 7 . Arrange the variables to save slot and gas :-

 Old:-
uint192 public ratio; // {1} What fraction of balance to melt each period
    uint48 public period; // {seconds} How often to melt
    uint256 public lastPayoutBal; // {qRTok} The balance of RToken at the last payout
    uint48 public lastPayout; // {seconds} The last time we did a payout

New :-
uint192 public ratio; // {1} What fraction of balance to melt each period
    uint32 public period; // {seconds} How often to melt
    uint32 public lastPayout; // {seconds} The last time we did a payout
  uint256 public lastPayoutBal; // {qRTok} The balance of RToken at the last payout

## 8. Missing approve(0) before approve

Its consider a good practice to reset the approve before changing it. Mainly because some tokens (like USDT) do not work when changing the allowance from an existing non-zero allowance value.They must first be approved by zero and then the actual allowance must be approved.

code snippet:-
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L653

