## [L-0] Abuse Allowance thru DistributorP1.distribute()

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L87

DistributorP1.distribute() could abuse the token allowance from any account. It should check `from == msg.sender` to avoid that.