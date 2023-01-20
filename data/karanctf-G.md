## [G-1] In Furnance.sol variables are not pracked properly packed
Try to write them in accending order

```solidity
contract FurnaceP1 is ComponentP1, IFurnace {
    using FixLib for uint192;

    uint192 public constant MAX_RATIO = FIX_ONE; // {1} 100%
    uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year

    uint192 public ratio; // {1} What fraction of balance to melt each period
    uint48 public period; // {seconds} How often to melt
    uint256 public lastPayoutBal; // {qRTok} The balance of RToken at the last payout
    uint48 public lastPayout; // {seconds} The last time we did a payout

    IRToken private rToken;
```
