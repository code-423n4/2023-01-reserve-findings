## IMPROVEMENT WHILE SUM

In line:

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L229

It can be improved to:

Changes: 

       stakeRSR += rsrAmount;   //before
       stakeRSR = stakeRSR  + rsrAmount; //after


## UNCHECKED IN FOR LOOP

In line