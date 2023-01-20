## 1. Use assembly to check for zero address

Using assembly to check for `address(0)` saves around **6 gas**.

For example:

File: `Auth.sol` [Line 94](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L94)

```solidity
        require(account != address(0), "cannot grant role to address 0");
```

The above zero address check can be written in assembly to save gas:

```solidity
        assembly {
            if iszero(address(account)) {
                mstore(0x00, "cannot grant role to address 0")
                revert(0x00, 0x20)
            }
```

Other instances of this issue:

- File: `ComponentRegistry.sol` [Line 37](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L37)
- File: `ComponentRegistry.sol` [Line 45](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L45)
- File: `ComponentRegistry.sol` [Line 53](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L53)
- File: `ComponentRegistry.sol` [Line 61](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L61)
- File: `ComponentRegistry.sol` [Line 69](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L69)
- File: `ComponentRegistry.sol` [Line 77](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L77)
- File: `ComponentRegistry.sol` [Line 85](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L85)
- File: `ComponentRegistry.sol` [Line 93](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L93)
- File: `ComponentRegistry.sol` [Line 101](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L101)
- File: `ComponentRegistry.sol` [Line 109](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L109)

## 2. Functions that can only be called by users with correct permissions/roles can be marked `payable`

Functions with access control will cost less gas when marked as `payable` (assuming the caller has correct permissions). This is because the compiler doesn't need to check for `msg.value`, which saves approximately **20 gas** per call.

Here are some instances of this issue:

- File: `Auth.sol` [Line 148](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L148)
- File: `Auth.sol` [Line 157](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L157)
- File: `Auth.sol` [Line 165](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L165)

## 3. Parameter `uint48 val` should be made full use of

In the following code, at [line 816](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L816) (second `require` statement), instead of reading from `unstakingDelay` (state variable), you could read from the parameter `uint48 val` (local). This will save 1 SLOAD.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812-L817

```solidity
812:    function setUnstakingDelay(uint48 val) public governance {
813:        require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
814:        emit UnstakingDelaySet(unstakingDelay, val);
815:        unstakingDelay = val;
816:        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
817:    }
```

For example, the above may be refactored to:

```solidity
812:    function setUnstakingDelay(uint48 val) public governance {
813:        require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
814:        emit UnstakingDelaySet(unstakingDelay, val);
815:        require(rewardPeriod * 2 <= val, "unstakingDelay/rewardPeriod incompatible");
816:        unstakingDelay = val;
817:    }
```

Note: the `require` statement is put before `unstakingDelay = val;` to decrease gas wastage if a revert were to happen.

Here is another instance of this issue:

File: `StRSR.sol` [Line 820-825](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L820-L825)

```solidity
    function setRewardPeriod(uint48 val) public governance {
        require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
        emit RewardPeriodSet(rewardPeriod, val);
        rewardPeriod = val;
        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
    }
```

In the above function, at the second `require` statement, instead of reading from `rewardPeriod` (state variable), you could read from the parameter `uint48 val` (local). This will save 1 SLOAD.

## 4. Unchecked math saves gas

Use the `unchecked` keyword to avoid unnecessary arithmetic checks and save gas when an underflow/overflow will not happen.

File: `BasketHandler.sol` [#L70](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L70), [#L78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L78), [#L218](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L218), [#L227](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L227), [#L262](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L262), [#L286](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L286), [#L337](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L337), [#L397](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L397), [#L416](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L416), [#L437](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L437), [#L530](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L530), [#L548](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L548), [#L586](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L586), [#L643](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L643), [#L707](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L707), [#L725](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L725)

```solidity
70:        for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;

78:        for (uint256 i = 0; i < length; ++i) {

218:        for (uint256 i = 0; i < config.erc20s.length; ++i) {

227:        for (uint256 i = 0; i < erc20s.length; ++i) {

262:        for (uint256 i = 0; i < erc20s.length; ++i) {

286:        for (uint256 i = 0; i < size; ++i) {

337:        for (uint256 i = 0; i < len; ++i) {

397:        for (uint256 i = 0; i < len; ++i) {

416:        for (uint256 i = 0; i < length; ++i) {

437:        for (uint256 i = 0; i < length; ++i) {

530:        for (uint256 i = 0; i < basketLength; ++i) {

548:        for (uint256 i = 0; i < basketLength; ++i) {

586:        for (uint256 i = 0; i < targetsLength; ++i) {

643:        for (uint256 i = 0; i < basketLength; ++i) {

707:        for (uint256 i = 0; i < erc20s.length; ++i) {

725:        for (uint256 i = 0; i < erc20s.length; ++i) {
```

## 5. Splitting `require()` statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per &&.

Here are some examples of this issue:

- File: `Auth.sol` [Line 181](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181)
- File: `Auth.sol` [Line 188](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188)
- File: `Broker.sol` [Line 135](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L135)

## 6. Non-strict equalities (`<=`, `>=`) is less gas efficient than strict equalities (`<`, `>`)

Strict equalities will save gas (less opcodes)

For example:

File: `Fixed.sol` [Line 101](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L101)

```solidity
    if (shiftLeft <= -96) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5
```

In the code above, `<=` is used when `<` could be used instead:

```solidity
    if (shiftLeft < -97) return (rounding == CEIL ? 1 : 0); // 0 < uint.max / 10**77 < 0.5
```

## 7. Using `storage` instead of `memory` for structs/arrays saves gas

When retrieving data from a `storage` location, assigning the data to a `memory` variable will cause all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new `memory` variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declaring the variable with the memory keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incurring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

For example:

File: `BackingManager.sol` [Line 219-220](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L219-L220)

## 8. Revert as early as possible

Functions should revert as early as possible to prevent unnecessary gas wastage.

File: `StRSR.sol` [Line 257-260](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L257-L260)

```solidity
257:    function unstake(uint256 stakeAmount) external notPausedOrFrozen {
258:        address account = _msgSender();
259:        require(stakeAmount > 0, "Cannot withdraw zero");
260:        require(stakes[era][account] >= stakeAmount, "Not enough balance");
```

In the code above, `address account` is unnecessarily declared before `require(stakeAmount > 0)`.

Consider swapping line 259 and line 258:

```solidity
257:    function unstake(uint256 stakeAmount) external notPausedOrFrozen {
258:        require(stakeAmount > 0, "Cannot withdraw zero");
259:        address account = _msgSender();
260:        require(stakes[era][account] >= stakeAmount, "Not enough balance");
```
