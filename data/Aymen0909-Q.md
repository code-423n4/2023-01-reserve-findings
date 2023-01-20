
# QA Report

## Summary

|               | Issue         | Risk     | Instances     |
| :-------------: |:-------------|:-------------:|:-------------:|
| 1  | `delegateBySig` doesn't revert when `signer` is `address(0)` | Low | 1 |
| 2  | `require()` checks for the inputs should be placed first | NC | 2 |
| 3  | Duplicated `require()` checks should be refactored to a modifier  | NC |  7 |
| 4  | Use solidity time-based units | NC | 5 |


## Findings

### 1- `delegateBySig` doesn't revert when `signer` is `address(0)` :

### Risk : Low

The function `delegateBySig` from the StRSRVotes contract is used to allow the users to delegate by signatures and it uses the `ECDSAUpgradeable.recover` function to get the `signer` address, this function can in some cases return `address(0)` but the `delegateBySig` does not check the `signer` address and thus the function will not revert in case of `signer == address(0)`.

### Proof of Concept

the isssue occurs in the `delegateBySig` function below :

File: contracts/p1/StRSRVotes.sol [Line 118-135](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L118-L135)

```
function delegateBySig(
    address delegatee,
    uint256 nonce,
    uint256 expiry,
    uint8 v,
    bytes32 r,
    bytes32 s
) public {
    require(block.timestamp <= expiry, "ERC20Votes: signature expired");
    address signer = ECDSAUpgradeable.recover(
        _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),
        v,
        r,
        s
    );
    require(nonce == _useNonce(signer), "ERC20Votes: invalid nonce");
    _delegate(signer, delegatee);
}
```

As you can see the returned `signer` address is used without checking its value and thus the call will not revert if its equla to `address(0)`.


### Mitigation

Add a non zero address check on the returned `signer` address


### 2- `require()` checks on inputs should be placed first :

`require()` checks statement that are used to check the input values should be placed at the beginnning of the functions to avoid unecessary runtime.

### Risk : NON CRITICAL

### Proof of Concept

There are 2 instances of this issue :

File: contracts/p1/StRSR.sol [Line 812-817](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812-L817)
```
function setUnstakingDelay(uint48 val) public governance {
    require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
    emit UnstakingDelaySet(unstakingDelay, val);
    unstakingDelay = val;
    require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
}
```

These function can be rewritten as follow : 

```
function setUnstakingDelay(uint48 val) public governance {
    require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
    require(rewardPeriod * 2 <= val, "unstakingDelay/rewardPeriod incompatible");
    emit UnstakingDelaySet(unstakingDelay, val);
    unstakingDelay = val;
}
```

File: contracts/p1/StRSR.sol [Line 820-825](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L820-L825)
```
function setRewardPeriod(uint48 val) public governance {
    require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
    emit RewardPeriodSet(rewardPeriod, val);
    rewardPeriod = val;
    require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
}
```

The function can be rewritten as follow : 

```
function setRewardPeriod(uint48 val) public governance {
    require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
    require(val * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
    emit RewardPeriodSet(rewardPeriod, val);
    rewardPeriod = val;
}
```

### 3- Duplicated `require()` checks should be refactored to a modifier :

`require()` checks statement used multiple times inside a contract should be refactored to a modifier for better readability.

### Risk : NON CRITICAL

### Proof of Concept

There are 4 instances of this issue in `AssetRegistry`:

```
File: contracts/p1/AssetRegistry.sol

74      require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");
88      require(_erc20s.contains(address(asset.erc20())), "no asset to unregister");
103     require(_erc20s.contains(address(erc20)), "erc20 unregistered");
111     require(_erc20s.contains(address(erc20)), "erc20 unregistered");
```

Those checks should be replaced by a `registred` modifier as follow :

```
modifier registred(address erc20){
    require(_erc20s.contains(erc20), "erc20 unregistered");
    _;
}
```

```
File: contracts/p1/StRSRVotes.sol

77      require(blockNumber < block.number, "ERC20Votes: block not yet mined");
83      require(blockNumber < block.number, "ERC20Votes: block not yet mined");
89      require(blockNumber < block.number, "ERC20Votes: block not yet mined");
```

Those checks should be replaced by a `isMinted` modifier as follow :

```
modifier isMinted(uint256 blockNumber){
    require(blockNumber < block.number, "ERC20Votes: block not yet mined");
    _;
}
```

### 4- Use solidity time-based units :

Solidity contains time-based units (seconds, minutes, hours, days and weeks) which should be used when declaring time based variables/constants instead of using literal numbers, this is done for better readability and to avoid mistakes.

### Risk : NON CRITICAL

### Proof of Concept
Instances include:

File: contracts/p1/BackingManager.sol [Line 33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33)

```
uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
```

File: contracts/p1/Broker.sol [Line 24](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24)

```
uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
```

File: contracts/p1/Furnace.sol [Line 16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L16)

```
uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
```

File: contracts/p1/StRSR.sol [Line 37-38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L37-L38)

```
uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
```

### Mitigation
Use time units when declaring time-based variables.
