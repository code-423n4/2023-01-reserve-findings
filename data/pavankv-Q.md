## 1.Re-arange the struct to save slot and gas :-

Code snippet:-
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L116

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
