## Fit Checkpoint into 1 storage slot

This can be fitted into 1 slot by changing fromBlock to uint32 which should still last long enough

protocol/contracts/p1/StRSRVotes.sol:L22
```
    struct Checkpoint {
        uint48 fromBlock;
        uint224 val;
    }
```

## Cache variable in memory

basketsNeeded can be cached here

protocol/contracts/p1/RToken.sol:L809-L810
```
        uint256 low = (FIX_ONE_256 * basketsNeeded) / supply; // D18{BU/rTok}
        uint256 high = (FIX_ONE_256 * basketsNeeded + (supply - 1)) / supply; // D18{BU/rTok}
```

## Use unchecked

For example this is safe becuase supply is checked to be != 0 and `(FIX_ONE_256 * uint192) < uint256`

protocol/contracts/p1/RToken.sol:L809-L810
```
        uint256 low = (FIX_ONE_256 * basketsNeeded) / supply; // D18{BU/rTok}
        uint256 high = (FIX_ONE_256 * basketsNeeded + (supply - 1)) / supply; // D18{BU/rTok}
```

## Unnecessary transfer to self check

This check prevent unlikely transfer to the token contract at the expense of every transfer

protocol/contracts/p1/RToken.sol:L833
```
        require(to != address(this), "RToken transfer to self");
```