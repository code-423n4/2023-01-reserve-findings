# Report


## Gas Optimizations

```solidity
File: p1/StRSRVotes.sol

#L22-L25: 
 struct Checkpoint {
        uint48 fromBlock;
        uint224 val;
    }
```
**The struct occupies 2 storage slots for each struct**

Modifying it to 
```solidity
    struct Checkpoint {
        uint48 fromBlock;
        uint208 val;
    }
```
**reduces read and write to 1 storage slots**
