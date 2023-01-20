Using compound assignment operators for state variables (ex. x += 3) is more expensive than using operator assignment (ex. x = x + 3).

Affected source code:
+ libraries/Fixed.sol:104
+ libraries/Fixed.sol:392
+ libraries/Fixed.sol:504
+ libraries/Fixed.sol:505
+ libraries/Fixed.sol:507
+ libraries/Fixed.sol:508
+ libraries/Fixed.sol:509
+ libraries/Fixed.sol:511
+ libraries/Fixed.sol:512
+ libraries/Fixed.sol:513
+ libraries/Fixed.sol:514
+ libraries/Fixed.sol:515
+ libraries/Fixed.sol:516
+ libraries/Fixed.sol:517
+ libraries/Fixed.sol:518
+ libraries/Fixed.sol:537
+ libraries/Fixed.sol:539
+ libraries/Fixed.sol:555
+ mixins/Auth.sol:136
+ p1/BasketHandler.sol:345
+ p1/BasketHandler.sol:346
+ p1/BasketHandler.sol:636
+ p1/Distributor.sol:145
+ p1/Distributor.sol:146
+ p1/Furnace.sol:81
+ p1/RToken.sol:330
+ p1/RToken.sol:678
+ p1/RToken.sol:687
+ p1/RToken.sol:761
+ p1/RToken.sol:771
+ p1/StRSR.sol:229
+ p1/StRSR.sol:387
+ p1/StRSR.sol:396
+ p1/StRSR.sol:402
+ p1/StRSR.sol:403
+ p1/StRSR.sol:412
+ p1/StRSR.sol:417
+ p1/StRSR.sol:513
+ p1/StRSR.sol:516
+ p1/StRSR.sol:549
+ p1/StRSR.sol:684
+ p1/StRSR.sol:698
+ p1/StRSR.sol:699
+ p1/StRSR.sol:721