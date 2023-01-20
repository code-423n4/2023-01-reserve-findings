## Low

###

## Non-Critical

### Inaccurate constant naming

MIN_TRADE_VOLUME is actually MAX_MIN_TRADE_VOLUME
MAX_TRADE_SLIPPAGE is actually MAX_MAX_TRADE_SLIPPAGE

protocol/contracts/p1/mixins/Trading.sol:L20-21
```
    uint192 public constant MIN_TRADE_VOLUME = 1e29; // {UoA}
    uint192 public constant MAX_TRADE_SLIPPAGE = 1e18; // {%}
```

It is also technically not the max becuase < is used instead of <=

```
        require(val < MAX_TRADE_SLIPPAGE, "invalid maxTradeSlippage");
```

