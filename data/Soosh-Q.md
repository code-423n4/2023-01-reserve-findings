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
