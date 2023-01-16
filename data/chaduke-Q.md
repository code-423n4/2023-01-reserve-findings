QA1. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L210-L212
Use openZeppelin's ReentrancyGuard to prevent reentrancy attack for the issue() function:
https://docs.openzeppelin.com/contracts/4.x/api/security
