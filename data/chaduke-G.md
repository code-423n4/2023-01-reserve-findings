G1. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L682
Enclosing this inside unchecked can save gas since underflow is impossible due to the way the queue is created.
```
unchecked{
    amtRToken = rightItem.amtRToken - leftItem.amtRToken;
}
```

G2. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L711-L713
Using ``tokenslen`` can save gas here:
```
for (uint256 i = 0; i < tokenslen; ++i) {
            IERC20Upgradeable(queue.tokens[i]).safeTransfer(account, amt[i]);
}
```

G3. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L765-L766
Enclosing it into unchecked to save gas. Underflow is impossible due to the way a queue is created.
```
unchecked{
    amtRToken = rightItem.amtRToken - leftItem.amtRToken;
    amtBaskets = rightItem.amtBaskets - leftItem.amtBaskets;
}
```

G4.  https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L768-L771
Enclosing it into unchecked to save gas. Underflow is impossible here.
```
unchecked{
                amtDeposits[i] = rightItem.deposits[i] - leftItem.deposits[i];

                // Decrement liabilities
                liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];
}
```

G5. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L822-L823
Caching state variable ``rewardPeriod`` can save gas since it is used twice.

G6. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L824
Using ``val`` instead of reading state variable ``rewardPeriod`` here can save gas.
```
require(val * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");

```
G7. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L497-L498
Caching state variable ``rewardPeriod`` can save gas here as it has been accessed multiple times.

G8. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L225
Enclosing this inside unchecked can save gas since underflow is impossible here.
```
uint256 stakeAmount = newTotalStakes - totalStakes;

```

G9. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L229
Just assigning it with ``newStakeRSR`` can save gas here:
```
stakeRSR = newStakeRSR;
```

G10. 
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L271
Enclosing this inside ``unchecked`` can save gas since underflow is inposible.
```
unchecked{
    uint256 rsrAmount = stakeRSR - newStakeRSR;
}
```

G11. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L316
Enclosing it inside unchecked can save gas since underflow is impossible due to the way the queue is created.
```
unchecked{
   uint192 draftAmount = queue[endId - 1].drafts - oldDrafts;
}
```

G12. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L322-L326
Enclosing it inside unchecked can save gas since underflow is impossible here.
```
unchecked{
     uint256 newTotalDrafts = totalDrafts - draftAmount;
        // newDraftRSR: {qRSR} = {qDrafts} * D18 / D18{qDrafts/qRSR}
        uint256 newDraftRSR = (newTotalDrafts * FIX_ONE_256 + (draftRate - 1)) / draftRate;
        uint256 rsrAmount = draftRSR - newDraftRSR;
}
```

