# Low

## COMP rewards claiming can bypass `requireNotPausedOrFrozen()`

`claimComp(address holder)` in Comptroller can be called by anyone, sending the COMP rewards to the holder specified.

In the claiming functions for RToken.sol 
```sol
function claimRewardsSingle(IERC20 erc20) external {
	requireNotPausedOrFrozen();
	RewardableLibP1.claimRewardsSingle(assetRegistry.toAsset(erc20));
}
```
and Trading.sol
```sol
function claimRewardsSingle(IERC20 erc20) external notPausedOrFrozen {
	RewardableLibP1.claimRewardsSingle(main.assetRegistry().toAsset(erc20));
}
```
The protocol expects that `claimRewards(...)` cannot be called when the protocol is paused or frozen. But a user could directly call `claimComp` on the comptroller contract, to send COMP rewards to `RToken`, `BackingManager` and `RevenueTrader` contracts.

Also, the event emitted will report _wrong_ values if `claimComp` was called directly. 
```sol
function claimRewards() external virtual override {
	IERC20 comp = IERC20(comptroller.getCompAddress());
	uint256 oldBal = comp.balanceOf(address(this));
	comptroller.claimComp(address(this));
	emit RewardsClaimed(comp, comp.balanceOf(address(this)) - oldBal);
}
```
_"wrong"_ as in the total sum of all amounts from the `RewardsClaimed` events will not total the actual amount of rewards claimed by the contracts.
```sol
event RewardsClaimed(IERC20 indexed erc20, uint256 indexed amount);
```
Affected: 
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L82-L92
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L520-L530
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L52

### Impact
It is unexpected that COMP rewards can still be claimed when the protocol is paused/frozen but I could not find a path in which this will lead to a bigger issue.

### Recommendations
I don't think this can really be prevented, it is just important to keep in mind that COMP balances of contracts which inherit IRewardableComponent could be changed without permission/knowledge of the protocol.

Therefore, if `RewardsClaimed` event should be consumed by an external program, know that the values could be misrepresented.

This issue will likely also apply to other Compound-fork protocols. For AAVE, this is not an issue because AAVE reward claiming is permissioned (msg.sender must be authorised claimer).


## Transaction ordering of setting unstakingDelay and rewardPeriod may cause reverts

### Details
In both the `setUnstakingDelay(...)` and `setRewardPeriod(...)` governance functions, there exists a common check for `rewardPeriod * 2 <= unstakingDelay`.
```sol
function setUnstakingDelay(uint48 val) public governance {
	require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
	emit UnstakingDelaySet(unstakingDelay, val);
	unstakingDelay = val;
	require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
}

function setRewardPeriod(uint48 val) public governance {
	require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
	emit RewardPeriodSet(rewardPeriod, val);
	rewardPeriod = val;
	require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
}
```
Assume governance decides to change these parameters.
Even if the final `unstakingDelay` and `rewardPeriod` are compatible, the governance transaction may still revert due to a bad ordering of function calls.

Say that the current `rewardPeriod` is $w$ weeks, and the `unstakingDelay` is $2w$ weeks in seconds.

If updating $\text{rewardPeriod} \gt w$, then `setUnstakingDelay(...)` must be called first such that $\text{unstakingDelay} \ge 2 \times \text{newRewardPeriod}$. 

If updating $\text{unstakingDelay} < 2w$, then `setRewardPeriod(...)` must be called first such that $rewardPeriod \le \frac{\text{newUnstakingDelay}}{2}$

Otherwise, the transactions will revert at the `require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");` check.

Affected:
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812-L825

### Impact
Since this is a **governance** function, there is a higher risk.

The reason is that these functions require a voting process to take place, which incurs significant gas costs for all governance participants (on Ethereum) - think voting process, queueing, execution, cancelling and retrying.

Additionally, the default Governor Alexios implementation takes 7 days end-to-end (from Proposal to Execution) due to voting delay and timelock.

Availability is impacted and there is significant gas wasted. It is also a plausible mistake to not consider the transaction order when setting new **compatible** parameters.

### Recommendations
One way to prevent this is to combine both `setUnstakingDelay(...)` and `setRewardPeriod(...)` into one function, performing the check only after both values are set so such a transaction ordering mistake cannot be made.


## stRSR `permit(...)` does not account for era
stRSR has a `permit(...)` function
```sol
function permit(...) public virtual {
	require(block.timestamp <= deadline, "ERC20Permit: expired deadline");

	bytes32 structHash = keccak256(
		abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)
	);

	PermitLib.requireSignature(owner, _hashTypedDataV4(structHash), v, r, s);

	_approve(owner, spender, value);
}
```
A permit could be signed off-chain, allowing some user to later set allowance according to the signed permit. 

Because `era` is not included in the hash struct, if `era` changed, an unused, signed permit would still be valid. That is, allowance will be set for the new `era` in `_approve(...)`
```sol
_allowances[era][owner][spender] = amount;
```
If `deadline` is set to a reasonable value, this is partially mitigated. Also, in most cases, if a user permits another user to spend tokens, they usually already trust that user, therefore submitting as Low.

This issue also applies to vote delegation - `delegateBySig(...)` in `stRSRVotes.sol`.

Affected:
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L766-L784
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L118-L135


### Impact
It is possible to hold a signed `permit()` across different eras.
It is possible to hold a signed `delegate()` across different eras.

### Recommendations
- Accept the risk
- OR include `era` in the `structHash`
- OR follow the recommendation in a seperate High risk issue titled "Bridged stRSR can lead to loss of funds" submitted by me.


## `setRedemptionRateFloor(...)` should be checked for reasonable value

The function does not currently have a bound for acceptable values
```sol
function setRedemptionRateFloor(uint256 val) public governance {
	emit RedemptionRateFloorSet(battery.redemptionRateFloor, val);
	battery.redemptionRateFloor = val;
}
```
refer to `system-design.md` - RedemptionBattery:
_"Either of the two variables can be defined to be zero to disable them"_
...
```md
RedemptionBattery.redemptionRateFloor
Dimension: {qRTok/hour}

The redemption rate floor is the minimum quantity of RToken to allow redemption of per-hour, and thereby the rate to charge the redemption battery at.

Default value: 1e24 = 1,000,000 RToken Mainnet reasonable range: 1e23 to 1e27
```
I think it is important that `redemptionRateFloor` cannot be set to zero, but instead require it to be `> 1e23`. If only `scalingRedemptionRate` is set, and `redemptionRateFloor` is disabled (zero), users can never redeem all RTokens since the amount redeemable is a percentage of totalSupply.

^ The same problem as trying to reach the end of a room, but each step taken must be half the distance of the previous step.

Note that the issue is that both values can be set to zero at the same time. The above is reason for making `redmeptionRateFloor` non-zero, rather than enforcing `scalingRedemptionRate` be non-zero.

Affected:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L615-L618

### Impact
This **contradicts** a known ~~issue~~ behavior on the contest page

_"The governance has the ability to freeze, and with long freezing enabled (default) it is possible for governance to soft-lock the backing for the duration of the freezing period by preventing the RToken holders from redeeming. **The idea is to turn off freezing capabilities couple months into RToken existence.**"_

The last sentence suggests that governance should eventually not be able to soft-lock the backing.

The ability to set `redemptionRateFloor` to zero/a small amount allows governance another way to soft-lock the backing without freezing capabilities. (governance sets both to zero, no amount can be redeemed)

### Recommendations
```sol
function setRedemptionRateFloor(uint256 val) public governance {
+	require(val >= 1e23, "invalid amount");
	emit RedemptionRateFloorSet(battery.redemptionRateFloor, val);
	battery.redemptionRateFloor = val;
}
```

## Silent error with `melt()` in `redeem(...)`
### Details
In `redeem(...)`,
```sol
	// Failure to melt results in a lower redemption price, so we can allow it when paused
	// solhint-disable-next-line no-empty-blocks
	try main.furnace().melt() {} catch {}
```
`main.furnace().melt()` will call `RToken.melt()`:
```sol
    function melt(uint256 amtRToken) external notPausedOrFrozen {
        _burn(_msgSender(), amtRToken);
        emit Melted(amtRToken);
        requireValidBUExchangeRate();
    }
```
The intention behind the `try-catch` as stated by the comment is so that even if `melt()` reverts due to being paused (`notPausedOrFrozen` modifier), it should still allow the user to `redeem(...)` at a lower redemption price.

However, this also means that `requireValidBUExchangeRate()` check is bypassed. If the `requireValidBUExchangeRate()` does not hold true, the external tx is reverted, and execution continues at the empty catch block.

`melt()` can silently fail (furnace does not actually burn any tokens) due to the `try-catch` when redeeming.

Affected:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L452

### Impact
Even if redeeming when not paused, there could be a silent failure to `melt()` resulting in a lower redemption price for the redeemer.

### Recommendations
```sol
if (!main.pausedOrFrozen()) {
	main.furnace().melt()
}
```
This will make it so that `redeem(...)` will revert if `requireValidBUExchangeRate()` does not hold after `melt()`. (should create a `paused()` function to check if paused. Above recommendation is based on existing `pausedOrFrozen()` function).

## Inherent DoS risk from battery capacity

system-design.md - RedemptionBattery:
```md
In order to restrict the system to organic patterns of behavior, we have the concept of a Redemption Battery. When redemption occurs, it uses up stored battery charge. The battery has a natural charging rate, which is controlled by the combination of two variables: _redemptionRateFloor_ and _scalingRedemptionRate_.
```
Issue is that someone can grief with this functionality, causing a DoS since there isn't really a penalty for continuously  `issue(...) -> vest(...) -> redeem(...)` to drain the battery, only gas costs. The attacker uses up the battery charge so other users cannot redeem.

This is amplified if issuance rate is set higher than redeem rate, since issuances for the attacker can be instantly vested and redeemed in a single transaction.

Issuance can also be DOS, but that requires capital lockup for the griefer (Since they will also have to wait to vest)

Affected:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/docs/system-design.md?plain=1#L269-L291

### Impact
Prevent other users from redeeming RTokens

Would submit as higher risk if I had a decent recommendation.
### Recommendations
Inherent risk due to design/purpose of this functionality.