# QA report

## Low Risk
| L-N    |Issue|
|:------:|:----|
| [L&#x2011;01] | decimals() not part of ERC20 standard in `BackingManager` | 9 |
| [L&#x2011;02] | Use of deprecated safeApprove() function  | 1 |

Total: 2 issues

## Non-critical

| N-C    |Issue|
|:------:|:----|
| [NC&#x2011;01] | Use latest Solidity version  | 2 |
| [NC&#x2011;02] | Use solidity's native units for dealing with time | 6 |
| [NC&#x2011;03] | Missing NatSpec | 11 |
| [NC&#x2011;04] | Structs declaration | 19 |
| [NC&#x2011;05] | Constants should be defined rather than using magic numbers | 31 |
| [NC&#x2011;06] | Forgot import | 2 |
| [NC&#x2011;07] | Missing override keyword | 1 |
| [NC&#x2011;08] | Typos | 1 |

Total: 8 issues

## Low Risk

### [L-01] decimals() not part of ERC20 standard in `BackingManager`

In `handoutExcessAssets` function, the protocol wants to fetch all surplus asset balances (including collateral and RToken(which decimals are know but now for the collateral))'s decimals. However decimals() is not part of the official ERC20 standard and might fall for tokens that do not implement it. While in practice it is very unlikely, as usually most of the tokens implement it, this should still be considered as a potential issue.

### [L-02] Use of deprecated safeApprove() function 
In BackingManager.sol the grantRTokenAllowance() function makes use of safeApprove() two times. OpenZeppelin's safeApprove implementation is deprecated. Using this deprecated function can lead to unintended reverts and potentially the locking of funds.

## Non-critical


### [NC-01]  Use latest Solidity version 

Use latest Solidity version to get all compiler features, bugfixes and optimizations

### [NC-02] Use solidity's native units for dealing with time

Solidity provides some native units for dealing with time. We can use the following units: seconds, minutes, hours, days, weeks and years. These units will convert to a uint of the number of seconds in that specific length of time.

File: `BackingManager.sol`, `Furnace.sol`, `StRSR.sol` and `Broker.sol`

```solidity
uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
```

```solidity
uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
```

```solidity
uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
```

```solidity
uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
```

Lines of code:

- https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/BackingManager.sol#L33

- https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/Furnace.sol#L16

- https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/StRSR.sol#L36

- https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/StRSR.sol#L37

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24

### [NC-03] Missing NatSpec

NatSpec documentation is essential for better understanding of the code by developers and auditors and is strongly recommended. Most of the functions in the contract are missing only a couple of params and returns documented. Consider adding natspec documentation for all the code.

### [NC-04]  Structs declaration

It is a best practice to be declared in the interface not in the implementation contract.

Files: `BasketHandler.sol`, `Distributor.sol`, `RToken.sol`, `StRSRVotes.sol`

### [NC-05] Constants should be defined rather than using magic numbers 

Files: `BasketHandler.sol` and `Distributor.sol`

```solidity
uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
```

```solidity
require(share.rsrDist <= 10000, "RSR distribution too high");
require(share.rTokenDist <= 10000, "RToken distribution too high");
```

Lines of code:

- https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/BasketHandler.sol#L364

- https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/Distributor.sol#L165

- https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/Distributor.sol#L166


### [NC-06] Forgot import

In `RevenueTrader`,  `IRevenueTrader` is inherited but not imported, add an import statement


### [NC-07] Missing override keyword

Functions in the contracts are missing the override keyword although they are inheriting their interfaces where the functions are described. 

### [NC-08] Typos

`Mointain` -> `Maintain` in `BackingManager.sol`
`setPrimeBakset` -> `setPrimeBasket` in `BasketHandler.sol`
`interepreted` -> `interpreted` in `BasketHandler.sol`
`adjaacent` -> `adjacent` in `RToken.sol`
