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

QA7. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L24
Wrong documentation here. It should be D18{1/hour} instead of {1/hour}. 


QA8. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L67-L69
``sweepRewards()`` cannot sweep excessive ERC20 rewards if one of them has a low balance that is smaller then their corresponding liabilities. This is a weakness. We should allow ``sweepRewards()`` to sweepthe rest of the ERC tokens if they have excessive balances even though one ERC20 has a low balance. The fix is below:
```
// Calculate deltas
        for (uint256 i = 0; i < erc20sLen; ++i) {
             if(erc20s[i].balanceOf(address(this))  >   liabilities[erc20s[i]])          // skipping those who might underflow
                        deltas[i] = erc20s[i].balanceOf(address(this)) - liabilities[erc20s[i]]; // {qTok}; 
        }

```

QA9. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L571
Revise it to to incude the ``from`` field:
```
emit Melted(msg.sender, amtRToken);
```

QA10. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L615-L618
``val`` cannot be exceeding ``totalSupply()`` to be effective, this is because: for function ``currentCharge()``, the returned ``charge = min(totalSupply(), amtPerHour, calculatedCharge)``, and ``amtPerHour >= battery.redemptionRateFloor``. In summary, ``val`` cannot be exceeding ``totalSupply()`` to be effective, otherwise, it will become useless. To fix, we add a check below: 

```
function setRedemptionRateFloor(uint256 val) public governance {
       if(val > totalSupply()} revert RedemptionRateFloorTooLarge(); // @audit

        emit RedemptionRateFloorSet(battery.redemptionRateFloor, val);
        battery.redemptionRateFloor = val;
    }
```

QA11. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L622
The name ``item`` is misleading here, it should be renamed to ``queue``. 

QA12. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L183
Better to be called ``switchBasket()``. 

QA13. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L38
Maybe it is more reasonable to revert here; otherwise a documentation is needed for this behavior. 

QA14. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L70
Zero check of ``_auctionLength`` is needed. 

QA15. https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L102-L108
The ``deploy`` function needs to have an access control so that malicious users cannot call it or front-run it. 

