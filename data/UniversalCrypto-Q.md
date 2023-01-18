Low:

1. User can burn gas unintentionally 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L378-L397
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L395
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L739
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L413-L418

If the user submits `endId` in this case `if (queue.left == endId) return;` the function will run & not revert however no state will be changed. This will mean the transaction will still go through but it will not revert but the user will still pay gas despite no state change.

Non Critical:

1. Use msg.sender instead _msgSender() if the code does not implement EIP-2771 trusted forwarder support.



