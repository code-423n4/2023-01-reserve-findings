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

G12. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L351-L353
Introducing a variable ``newLastIssRate`` here can save gas so that we do not need to read the state variable ``lastIssRate`` multiple times. 

```
            uint192 private newLastIssRate;

            newLastIssRate = uint192((issuanceRate * totalSupply()) / FIX_ONE);
            // uint192(<) is equivalent to Fix.lt
            if (newLastIssRate < MIN_BLOCK_ISSUANCE_LIMIT) lastIssRate = MIN_BLOCK_ISSUANCE_LIMIT;
            else lastIssRate = newLastIssRate;

```

G13. 
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L64
Enclosing it inside unchecked as underflow is impossible here:
```
uint48 blocks = uint48(block.number) - battery.lastBlock;
```

G14. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L69-L70
There is no need to compare amtPerHour and supply since we always have amtPerHour <= supply according to L59. 
So the following code is sufficient and will save gas:
```
if (charge > amtPerHour) charge = amtPerHour;
```

G15. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L48
Enclosing it inside unchecked to save gas since there is a check already at L44.
```
battery.lastCharge = charge - amount;

```

G16. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L491
Moving this line to an earlier line (L462) can save gas to ensure ``amount`` is not too big. 

G17. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L500-L513
This all-zero-test is used to revert the function when all amounts[i] =0. To save gas, this check can be done in the previous for loop when amounts[i] is first calculated. Such short-circuit can save gas.
```
bool allZero = true;
for (uint256 i = 0; i < erc20length; ++i) {
            // {qTok}
            uint256 bal = IERC20Upgradeable(erc20s[i]).balanceOf(address(backingManager));

            // gas-optimization: only do the full mulDiv256 if prorate is 0
            uint256 prorata = (prorate > 0)
                ? (prorate * bal) / FIX_ONE // {qTok} = D18{1} * {qTok} / D18
                : mulDiv256(bal, amount, supply); // {qTok} = {qTok} * {qRTok} / {qRTok}

            if (prorata < amounts[i]) amounts[i] = prorata;
            if(amounts[i] != 0) allZero = false; // audit: check all-zero            
}

if (allZero) revert("Empty redemption"); // short-circuit, no need to call _burn()
             
```

G18. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L173-L179
Caching ``rsr.balanceOf(address(this)`` can save gas.

```
uint256 rsrBal = rsr.balanceOf(address(this);
if (rsrBal > 0) {
            // For CEI, this is an interaction "within our system" even though RSR is already live
            IERC20Upgradeable(address(rsr)).safeTransfer(
                address(rsrTrader),
                rsrBal)
            );
        }
```

G19. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L169-L170
These two lines can be deleted, might be useless.

G20. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L703
Caching ``basket.erc20s.length`` can save gas here.

G21. 
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L718-L729
Caching ``backup.erc20s.length`` can save gas

G22. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L81
Enclosing it inside unchecked can save gas, overflow/underflow is not possible
```
return (p - delta, p + delta, 0);
```

G23. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L123-L128
There is no need to create the ``transfer[]`` data structure and this code, we can replace it by the subsequence ``safeTransferFrom()`` to avoid another for-loop and book keeping.
```
IERC20Upgradeable(address(erc20)).safeTransferFrom(from, addrTo, transferAmt);
```