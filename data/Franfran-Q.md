## StRSR endIdForWithdraw returned ID may be wrong if the cached draft id was set

In the `StRSR` contract there is a function called `endIdForWithdraw` which is used to find the first draft ID which makes the withdraw possible considering the locking time.

```solidity
    function endIdForWithdraw(address account) external view returns (uint256) {
        uint256 time = block.timestamp;
        CumulativeDraft[] storage queue = draftQueues[draftEra][account];

        // Bounds our search for the current cumulative draft
        (uint256 left, uint256 right) = (firstRemainingDraft[draftEra][account], queue.length);

        // If there are no drafts to be found, return 0 drafts
        if (left >= right) return right;
        if (queue[left].availableAt > time) return left;

        // Otherwise, there *are* drafts with left <= index < right and availableAt <= time.
        // Binary search:
        uint256 test;
        while (left < right - 1) {
            // Loop invariants, because without great care a binary search is usually wrong:
            // - queue[left].availableAt <= time
            // - either right == queue.length or queue[right].availableAt > time
            test = (left + right) / 2; // left < test < right because left < right - 1
            if (queue[test].availableAt <= time) left = test;
            else right = test;
        }
        return right;
    }
```

The issue lies in the `if (queue[left].availableAt > time) return left;`
The queue elements are accessed by removing 1 to the ID, which is not done here.
Instead, it should be:

```solidity
if (left != 0) {
	if (queue[left - 1].availableAt > time) return left;
}
```