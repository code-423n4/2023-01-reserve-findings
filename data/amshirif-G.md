# Wasteful for loops

# Summary

Generally speaking it is best to avoid iterating over datasets, especially in this case where the information is being iterated over for the benefit of an external contract. This is wasteful in two ways. One is the gas needed to iterate over the array, the other is that the entire array must be written into memory, though this is not as costly as writing to storage it is far less efficient than making a direct call. In `AssetRegistry.sol`, function `erc20s()` the sole purpose is to convert the `_erc20s` to an array of IERC20s. The same is true in getRegistry() with IERC20 and IAsset. These steps are unnecessary and should be left for the receiving contract to cast the values if and when you need. 

# Code Snippet

https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/AssetRegistry.sol#L122-L142

# Recommendation

There are a few different approaches depending on desired outcome. The library being used `EnumerableSet` allows for the entire set to be returned through `function values(UintSet storage set)`. 

```solidity
    function erc20s() external view returns (address[]) {
        return _erc20s.values(X);
    }
```
The downside to this approach is that is still copies the entire array into memory. 

The best result would come from using a shared storage contract. A single contract could still be restricted to write access while other contracts would simply need the reference to its storage location and could make the same call for far less.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Require in view function

# Summary

Certain opcodes incur a memory expansion cost in addition to their otherwise static gas cost (https://github.com/wolflo/evm-opcodes/blob/main/gas.md#a0-1-memory-expansion). View functions can oftentimes accomplish their goals without `require` statements and return data should be used to verify data validity. This pertains to `AssetRegistry.sol` and `BasketHandler.so`.

# Code Snippet

https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/AssetRegistry.sol#L107-L114

In this instance, if the ERC20 is not registered or collateralized the function will revert, consuming additional gas, only to be caught in the `try{}catch{}` statement on the calling function.

https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/BasketHandler.sol#L296-L311

# Recommendation

```solidity
    function quantity(IERC20 erc20) public view returns (uint192) {
        ICollateral iCollateral = assetRegistry.toColl(erc20);
        if (address(iCollateral) == address(0) || iCollateral.status() == CollateralStatus.DISABLED) {
            return FIX_ZERO;
        }

        uint192 refPerTok = coll.refPerTok();
        if (refPerTok > 0) {
            return basket.refAmts[erc20].div(refPerTok, CEIL);
        } else {
            return FIX_MAX;
        }
    }
```
Here is an example of eliminating the wasteful revert. If a revert is desired then also leave that up to the calling contract to throw an error. If it is imperative that the BasketHandlerP1 contact throws an exception, different error codes should be thrown instead of just a revert. This will allow for the receiving contract to have a strong catch case.

-------------------------------------------------------------------------------------------------------------------------------------------------------------------------

Token call placement

# Summary

Many of the ERC20 calls should be placed elsewhere in the flow in the event of transaction reverts. Each of these calls are small in themselves, but together should have a noticeable effect on gas consumption during failures. 

# Code Snippet / Recommendation

1. Foremost is the placement/usage of the `_afterTokenTransfer`. 
https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L755-L761

`require` calls should be placed as high as possible in the function call. In this case, it would be best to not use the `_afterTokenTransfer` function at all. Instead, I would place all of the zero checks in a modifier that makes the call immediately. 

```solidity
modifier noZeros() {
    require(from != address(0), "ERC20: transfer from the zero address");
    require(to != address(0), "ERC20: transfer to the zero address");
    require(to != address(this), "StRSR transfer to self");
    _;
}
```

This is just an example. Not all of your functions require the same zero checks. It is also fine to simply place each of these checks in the top lines of the `external` or `public` functions, it just smacks of code smell. 

2. Another modification would be to do an allowance check or move the transfer calls higher in the `stake(uint256)` call. The same could be said for `withdraw(address, uint256)` and `seizeRSR(uint256)` calls, however since the tokens are originating from the contract itself there is a far smaller chance of this happening. 

https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L212-L237

```solidity
    function stake(uint256 rsrAmount) external {
        require(rsrAmount > 0, "Cannot stake zero");
        IERC20Upgradeable(address(rsr)).safeTransferFrom(account, address(this), rsrAmount);
        if (!main.pausedOrFrozen()) _payoutRewards();

        // Compute stake amount
        // This is not an overflow risk according to our expected ranges:
        //   rsrAmount <= 1e29, totalStaked <= 1e38, 1e29 * 1e38 < 2^256.
        // stakeAmount: how many stRSR the user shall receive.
        // pick stakeAmount as big as we can such that (newTotalStakes <= newStakeRSR * stakeRate)
        uint256 newStakeRSR = stakeRSR + rsrAmount;
        // newTotalStakes: {qStRSR} = D18{qStRSR/qRSR} * {qRSR} / D18
        uint256 newTotalStakes = (stakeRate * newStakeRSR) / FIX_ONE;
        uint256 stakeAmount = newTotalStakes - totalStakes;

        // Update staked
        address account = _msgSender();
        stakeRSR += rsrAmount;
        _mint(account, stakeAmount);

        // Transfer RSR from account to this contract
        emit Staked(era, account, rsrAmount, stakeAmount);
    }
```

This also occurs in the Broker.sol contract.

https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/Broker.sol#L85-L118

3. Another `require` call to be moved up are that at the end of `setRewardPeriod(uint48)` and `function setUnstakingDelay(uint48)`. 

https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/StRSR.sol#L812-L825

Simply move the calls up.

```solidity
    function setUnstakingDelay(uint48 val) public governance {
        require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
        require(rewardPeriod * 2 <= val, "unstakingDelay/rewardPeriod incompatible");
        emit UnstakingDelaySet(unstakingDelay, val);
        unstakingDelay = val;
    }

    function setRewardPeriod(uint48 val) public governance {
        require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
        require(rewardPeriod * 2 <= val, "unstakingDelay/rewardPeriod incompatible");
        emit RewardPeriodSet(rewardPeriod, val);
        rewardPeriod = val;
    }
```