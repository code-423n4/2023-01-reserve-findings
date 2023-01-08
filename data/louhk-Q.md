In RevenueTrader.sol - https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RevenueTrader.sol#L34-#L76

The `manageToken` function does not check the return value of `prepareTradeSell` when it calls tryTrade. If `prepareTradeSell` returns false, the result of `calling` tryTrade will in an exception being thrown.