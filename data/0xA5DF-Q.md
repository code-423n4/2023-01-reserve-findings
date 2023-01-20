## Div before multiply at `Distributor.distribute()`
[Here](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L99) there's a div before [multiplication](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L115) distribution that might cause the amount distributed to be lower than it should.
This would mean that value up to the amount of total shares (1e7 wei) can be locked in the sender without being able to distribute it.
