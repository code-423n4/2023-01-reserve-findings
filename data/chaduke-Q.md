QA1. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L210-L212
Use openZeppelin's ReentrancyGuard to prevent reentrancy attack for the issue() function:
https://docs.openzeppelin.com/contracts/4.x/api/security

QA2. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L801
The documentation should be "Require the BU to RToken exchange rate to be in [1e-9, 1e9], but represented as 
[FIX_ONE_256*1e-9, FIX_ONE_256*1e9] =  [1e9, 1e27]". Otherwise, it might not appear to be consistent with the implementation.
 
QA3. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L230-L236
It is safer to perform the RSR tranfer first before the minting of StRSR so that a user will not own StRST first before transfering the RST to the contract.

QA4. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L234
We need to use ``RoundingMode.CEIL`` here:
```
totalSupply() > 0 ? mulDiv(basketsNeeded, amtRToken, totalSupply(), RoundingMode.CEIL)

``` 

QA5. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L76
Wrong implementation, ``lastIssRate`` is {rTok/block} not D18{rTok/block}! See the implementation below: 
```
lastIssRate = uint192((issuanceRate * totalSupply()) / FIX_ONE);

```

QA6. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L655-L659
Range check is necessary for ``left`` and ``right`` so that their values are proper
```
If(left < queue.left || right > queue.right || left > right) revert LeftRightOutOfRange();

```

