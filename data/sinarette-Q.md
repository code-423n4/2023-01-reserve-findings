### Wrong comment
The comment explaining the rounding logic should be 'fair' rather than 'unfair', considering the context.

```solidity
File: p1/BackingManager.sol

231:                // initial division is intentional here! We'd rather save the dust than be unfair
232:                toRSR[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rsrTotal;
233:                toRToken[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rTokenTotal;
```

### Unnecessary type conversion
No need to upcast ```lastPayout``` to ```uint64```, since all other components are ```uint48```

```solidity
File: p1/Furnace.sol

71:        if (uint48(block.timestamp) < uint64(lastPayout) + period) return;
```

### Unnecessary checking statement
No need to check if ```status``` is SOUND, if it wasn't then it would have already reverted

```solidity
File: p1/RTokenP1.sol

217:        require(status == CollateralStatus.SOUND, "basket unsound");

251:        if (
252:            // D18{blocks} <= D18{1} * {blocks}
253:            vestingEnd <= FIX_ONE_256 * block.number &&
254:            queue.left == queue.right &&
255:            status == CollateralStatus.SOUND // always true
256:        )
```