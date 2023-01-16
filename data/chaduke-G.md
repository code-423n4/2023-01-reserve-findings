G1. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L682
Enclosing this inside unchecked can save gas since underflow is impossible due to the way the queue is created.
```
unchecked{
    amtRToken = rightItem.amtRToken - leftItem.amtRToken;
}
```