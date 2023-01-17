QA1. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L210-L212
Use openZeppelin's ReentrancyGuard to prevent reentrancy attack for the issue() function:
https://docs.openzeppelin.com/contracts/4.x/api/security

QA2. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L801
The documentation should be "Require the BU to RToken exchange rate to be in [1e-9, 1e9], but represented as 
[FIX_ONE_256*1e-9, FIX_ONE_256*1e9] =  [1e9, 1e27]". Otherwise, it might not appear to be consistent with the implementation.
 