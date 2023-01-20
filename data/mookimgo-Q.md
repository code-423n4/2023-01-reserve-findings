# StRSR's withdraw function should place emit UnstakingCompleted before `if (rsrAmount == 0) return;`

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L327

When rsrAmount equals to zero, there is indeed no need to call `rsr.safeTransfer`.

But this function has modified state variable `firstRemainingDraft[draftEra][account]`, 
so the event `UnstakingCompleted` emitting is meaningful, to inform that a withdraw has been processed, and `endId` has changed.

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

# event UnstakingCompleted should place `staker` address as the first parameter

`eth_getLogs` RPC call can only filter by topics in order, like specifying topics0 and topics1, while it cannot search by topics3 without specifying topics1 and topic2.

So in order to facilitate event searching for a specific staker using `eth_getLogs` RPC call, it's recommended to move staker to the first event parameter.

**Suggestion**: change the event definition to:

```
    event UnstakingCompleted(
        address indexed staker,
        uint256 indexed firstId,
        uint256 indexed endId,
        uint256 draftEra,
        uint256 rsrAmount
    );
```

And correspondingly update the code.

-----

# docs @param should be changed to @return in CTokenNonFiatCollateral.sol's tryPrice function

https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L36

low, high, pegPrice is return value, should not use `@param` in docs

Similar issues also in `SelfReferentialCollateral.sol`, `RTokenAsset.sol`, `CTokenNonFiatCollateral.sol`, `Asset.sol`, `FiatCollateral.sol`, `EURFiatCollateral.sol`, `NonFiatCollateral.sol`

Suggestion: change to:

```
    /// Can revert, used by other contract functions in order to catch errors
    /// @return low {UoA/tok} The low price estimate
    /// @return high {UoA/tok} The high price estimate
    /// @return pegPrice {target/ref}
    function tryPrice()
```
