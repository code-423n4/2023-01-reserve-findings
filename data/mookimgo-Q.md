# StRSR's withdraw function should place emit UnstakingCompleted before `if (rsrAmount == 0) return;`

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L327

When rsrAmount equals to zero, there is indeed no need to call `rsr.safeTransfer`.

But this function has modified state variable `firstRemainingDraft[draftEra][account]`, 
so the event `UnstakingCompleted` emitting is meaningful, to inform that a withdraw has been processed.

**Suggestion**: move `if (rsrAmount == 0) return;` to after the event emit, like:

```
...
        uint256 rsrAmount = draftRSR - newDraftRSR;

        // ==== Transfer RSR from the draft pool
        totalDrafts = newTotalDrafts;
        draftRSR = newDraftRSR;

        emit UnstakingCompleted(firstId, endId, draftEra, account, rsrAmount);

        if (rsrAmount == 0) return;

        // == Interaction ==
        IERC20Upgradeable(address(rsr)).safeTransfer(account, rsrAmount);
```