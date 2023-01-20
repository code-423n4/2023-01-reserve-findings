
## [NAZ-L1] Inconsistent Use of `requireNotPausedOrFrozen()` Function
**Severity**: Low
**Context**: [`RToken.sol#L557`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L557), [`RToken.sol#L580`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L580)

**Description**:
According to the natspec of `requireNotPausedOrFrozen()` function:
> @dev Used in reward claim functions to save on contract size

However, it is used across several other functions on top of the reward claim functions causing an inconsistency in the use of `requireNotPausedOrFrozen()` function.

**Recommendation**:
Consider either of the following:
1. Changing the natspec to describe the use of `requireNotPausedOrFrozen()` function 
2. Remain consistent in the use by removing it from both `mint() && setBasketsNeeded()` and adding the `notPausedOrFrozen` modifier to them.


## [NAZ-L2] Unneeded Function According To Video Walkthrough
**Severity**: Low
**Context**: [`Main.sol#L43`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L43)

**Description**:
According to the walkthrough video at [10:30](https://youtu.be/341MhkOWsJE?t=631). The poke function was for testing and to prove equivalence between P1 and P0.

**Recommendation**:
Consider removing this function for production use since it can confuse some users.


## [NAZ-L3] Missing Check If `setLongFreeze()` is Larger Than `shortFreeze`
**Severity**: Low
**Context**: [`Auth.sol#L187`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L187)

**Description**:
`setLongFreeze()` can be set to be less than `shortFreeze` defeating the purpose of having both. 

**Recommendation**:
Consider adding a check so that `longFreeze` is always larger than `shortFreeze` or that it is larger than `MAX_SHORT_FREEZE`.