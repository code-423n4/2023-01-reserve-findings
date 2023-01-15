Avoid default assignment - 3 gas per instance - 51 gas saved

Declaring uint256 i = 0; means doing an MSTORE of the value 0 Instead you could just declare uint256 i to declare the variable without assigning it’s default value, saving 3 gas per declaration

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L

lines:
70,78,218, 227,262,286,337,397, 416,437,530,548,586, 648,653,707, 725
