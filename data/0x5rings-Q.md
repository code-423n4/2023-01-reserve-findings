Findings: Check that  ECRecover address ≠ 0

In some instances the ecrecover can return a zero address.

Code should check if the ecrecover address is not 0.

Code: [https://github.com/reserve-protocol/protocol/blob/d224c14c398d2727d39d133aa7511e1e6b161833/contracts/p1/StRSR.sol#L781](https://github.com/reserve-protocol/protocol/blob/d224c14c398d2727d39d133aa7511e1e6b161833/contracts/p1/StRSR.sol#L781)

Source: [https://github.com/rsksmart/rskj/issues/1418](https://github.com/rsksmart/rskj/issues/1418)

Mitigation: check if ecrecover address != 0.