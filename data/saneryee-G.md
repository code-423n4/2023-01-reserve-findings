# Gas Optimizations Report

|         | Issue                                                                                                                              | Instances |
| ------- | :--------------------------------------------------------------------------------------------------------------------------------- | :-------: |
| [G-001] | x += y or x -= y costs more gas than x = x + y or x = x - y for state variables                                                    |    18     |
| [G-002] | Splitting require() statements that use `&&` saves gas                                                                             |    15     |
| [G-003] | `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping` |    22     |
| [G-004] | Functions guaranteed to revert when called by normal users can be marked payable                                                   |     8     |
| [G-005] | Don't use `_msgSender()` if not supporting EIP-2771                                                                                |    13     |
| [G-006] | Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate                 |     4     |
| [G-007] | Using `calldata` instead of memory for read-only arguments in external functions saves gas                                         |    15     |
| [G-008] | Multiple accesses of a `mapping/array` should use a local variable cache                                                           |     7     |
| [G-009] | internal functions only called once can be inlined to save gas                                                                     |     5     |
| [G-010] | Use a more recent version of solidity                                                                                              |     7     |
| [G-011] | Replace modifier with function                                                                                                     |     4     |
| [G-012] | Use named returns for local variables where it is possible.                                                                        |     6     |

## [G-001] x += y or x -= y costs more gas than x = x + y or x = x - y for state variables

### Impact

Using the addition operator instead of plus-equals saves 113 gas. Usually does not work with struct and mappings.

### Findings

Total:18

[contracts/libraries/Fixed.sol#L537](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L537)

```solidity
537:    if (mm > 0) result += 1;
```

[contracts/libraries/Fixed.sol#L504](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L504)

```solidity
504:    if (mm > lo) hi -= 1;
```

[contracts/libraries/Fixed.sol#L555](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L555)

```solidity
555:    if (mm < lo) hi -= 1;
```

[contracts/libraries/Fixed.sol#L505](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L505)

```solidity
505:    lo -= mm;
```

[contracts/libraries/Fixed.sol#L509](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L509)

```solidity
509:    lo += hi * ((0 - pow2) / pow2 + 1);
```

[contracts/libraries/Fixed.sol#L539](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L539)

```solidity
539:    if (mm > ((z - 1) / 2)) result += 1;
```

[contracts/libraries/Fixed.sol#L392](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L392)

```solidity
392:    decimals -= 18;
```

[contracts/libraries/Fixed.sol#L104](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L104)

```solidity
104:    shiftLeft += 18;
```

[contracts/p1/StRSR.sol#L387](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L387)

```solidity
387:    stakeRSR -= stakeRSRToTake;
```

[contracts/p1/StRSR.sol#L412](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L412)

```solidity
412:    seizedRSR += draftRSR;
```

[contracts/p1/StRSR.sol#L403](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L403)

```solidity
403:    seizedRSR += draftRSRToTake;
```

[contracts/p1/StRSR.sol#L699](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L699)

```solidity
699:    totalStakes += amount;
```

[contracts/p1/StRSR.sol#L229](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L229)

```solidity
229:    stakeRSR += rsrAmount;
```

[contracts/p1/StRSR.sol#L516](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L516)

```solidity
516:    payoutLastPaid += numPeriods * rewardPeriod;
```

[contracts/p1/StRSR.sol#L721](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L721)

```solidity
721:    totalStakes -= amount;
```

[contracts/p1/StRSR.sol#L417](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L417)

```solidity
417:    seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;
```

[contracts/p1/Furnace.sol#L81](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Furnace.sol#L81)

```solidity
81:    lastPayout += numPeriods * period;
```

[contracts/p1/BasketHandler.sol#L636](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L636)

```solidity
636:    nonce += 1;
```

## [G-002] Splitting require() statements that use `&&` saves gas

### Impact

When using `&&` in a `require()` statement, the entire statement will be evaluated before the transaction reverts. If the first condition is false, the second condition will not be evaluated. This means that if the first condition is false, the second condition will not be evaluated, which is a waste of gas. Splitting the `require()` statement into two separate statements will save gas.

### Findings

Total:15

[contracts/mixins/Auth.sol#L181](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L181)

```solidity
181:    require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");
```

[contracts/mixins/Auth.sol#L188](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L188)

```solidity
188:    require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");
```

[contracts/plugins/assets/Asset.sol#L50](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/assets/Asset.sol#L50)

```solidity
50:    require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");
```

[contracts/p1/StRSR.sol#L821](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L821)

```solidity
821:    require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
```

[contracts/p1/StRSR.sol#L813](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L813)

```solidity
813:    require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
```

[contracts/p1/Broker.sol#L134-L137](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Broker.sol#L134-L137)

```solidity
134:    require(
135:                newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
136:                "invalid auctionLength"
137:            );
```

[contracts/p1/Furnace.sol#L89](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Furnace.sol#L89)

```solidity
89:    require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
```

[contracts/p1/RToken.sol#L623](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L623)

```solidity
623:    require(index >= item.left && index < item.right, "out of range");
```

[contracts/p1/RToken.sol#L813](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L813)

```solidity
813:    require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");
```

[contracts/p1/RToken.sol#L410](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L410)

```solidity
410:    require(queue.left <= endId && endId <= queue.right, "out of range");
```

[contracts/p1/RToken.sol#L741](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L741)

```solidity
741:    require(queue.left <= endId && endId <= queue.right, "out of range");
```

[contracts/p1/RToken.sol#L590](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L590)

```solidity
590:    require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");
```

[contracts/p1/RevenueTrader.sol#L72](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RevenueTrader.sol#L72)

```solidity
72:    require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");
```

[contracts/p1/Deployer.sol#L109](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Deployer.sol#L109)

```solidity
109:    require(owner != address(0) && owner != address(this), "invalid owner");
```

[contracts/p1/Deployer.sol#L48-L65](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Deployer.sol#L48-L65)

```solidity
48:    require(
49:                address(rsr_) != address(0) &&
50:                    address(gnosis_) != address(0) &&
51:                    address(rsrAsset_) != address(0) &&
52:                    address(implementations_.main) != address(0) &&
53:                    address(implementations_.trade) != address(0) &&
54:                    address(implementations_.components.assetRegistry) != address(0) &&
55:                    address(implementations_.components.backingManager) != address(0) &&
56:                    address(implementations_.components.basketHandler) != address(0) &&
57:                    address(implementations_.components.broker) != address(0) &&
58:                    address(implementations_.components.distributor) != address(0) &&
59:                    address(implementations_.components.furnace) != address(0) &&
60:                    address(implementations_.components.rsrTrader) != address(0) &&
61:                    address(implementations_.components.rTokenTrader) != address(0) &&
62:                    address(implementations_.components.rToken) != address(0) &&
63:                    address(implementations_.components.stRSR) != address(0),
64:                "invalid address"
65:            );
```

## [G-003] `storage` pointer to a structure is cheaper than copying each value of the structure into `memory`, same for `array` and `mapping`

### Impact

It may not be obvious, but every time you copy a storage `struct/array/mapping` to a `memory` variable, you are literally copying each member by reading it from `storage`, which is expensive. And when you use the `storage` keyword, you are just storing a pointer to the storage, which is much cheaper.

### Findings

Total:22

[contracts/plugins/aave/StaticATokenLM.sol#L389](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/StaticATokenLM.sol#L389)

```solidity
389:    address[] memory assets = new address[](1);
```

[contracts/plugins/aave/StaticATokenLM.sol#L411](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/StaticATokenLM.sol#L411)

```solidity
411:    address[] memory assets = new address[](1);
```

[contracts/plugins/aave/StaticATokenLM.sol#L545](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/StaticATokenLM.sol#L545)

```solidity
545:    address[] memory assets = new address[](1);
```

[contracts/plugins/aave/StaticATokenLM.sol#L582](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/StaticATokenLM.sol#L582)

```solidity
582:    address[] memory assets = new address[](1);
```

[contracts/p1/BackingManager.sol#L219](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BackingManager.sol#L219)

```solidity
219:    uint256[] memory toRSR = new uint256[](length);
```

[contracts/p1/BackingManager.sol#L220](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BackingManager.sol#L220)

```solidity
220:    uint256[] memory toRToken = new uint256[](length);
```

[contracts/p1/BasketHandler.sol#L642](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L642)

```solidity
642:    uint192[] memory refAmts = new uint192[](basketLength);
```

[contracts/p1/BasketHandler.sol#L541](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L541)

```solidity
541:    uint192[] memory goodWeights = new uint192[](targetsLength);
```

[contracts/p1/BasketHandler.sol#L545](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L545)

```solidity
545:    uint192[] memory totalWeights = new uint192[](targetsLength);
```

[contracts/p1/BasketHandler.sol#L225](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L225)

```solidity
225:    bytes32[] memory names = new bytes32[](erc20s.length);
```

[contracts/p1/BasketHandler.sol#L169](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L169)

```solidity
169:    uint192[] memory refAmts = new uint192[](basket.erc20s.length);
```

[contracts/p1/Distributor.sol#L105](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Distributor.sol#L105)

```solidity
105:    Transfer[] memory transfers = new Transfer[](destinations.length());
```

[contracts/p1/RToken.sol#L666](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L666)

```solidity
666:    uint256[] memory amt = new uint256[](tokensLen);
```

[contracts/p1/RToken.sol#L750](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L750)

```solidity
750:    uint256[] memory amtDeposits = new uint256[](tokensLen);
```

[contracts/p1/RToken.sol#L465](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L465)

```solidity
465:    (address[] memory erc20s, uint256[] memory amounts) = basketHandler.quote(baskets, FLOOR);
```

[contracts/p1/Deployer.sol#L230](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Deployer.sol#L230)

```solidity
230:    IAsset[] memory assets = new IAsset[](2);
```

[contracts/p1/mixins/RewardableLib.sol#L62](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/RewardableLib.sol#L62)

```solidity
62:    IERC20[] memory erc20s = reg.erc20s();
```

[contracts/p1/mixins/RewardableLib.sol#L64](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/RewardableLib.sol#L64)

```solidity
64:    uint256[] memory deltas = new uint256[](erc20sLen);
```

[contracts/p1/mixins/RecollateralizationLib.sol#L433](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/RecollateralizationLib.sol#L433)

```solidity
433:    IERC20[] memory basketERC20s = components.bh.basketTokens();
```

[contracts/interfaces/IBasketHandler.sol#L88](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/interfaces/IBasketHandler.sol#L88)

```solidity
88:    returns (address[] memory erc20s, uint256[] memory quantities);
```

[contracts/interfaces/IFacadeRead.sol#L90](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/interfaces/IFacadeRead.sol#L90)

```solidity
90:    returns (IERC20[] memory erc20s, uint256 max);
```

[contracts/interfaces/IFacadeRead.sol#L27](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/interfaces/IFacadeRead.sol#L27)

```solidity
27:    returns (address[] memory tokens, uint256[] memory deposits);
```

### Recommendation

Using `storage` instead of `memory`

## [G-004] Functions guaranteed to revert when called by normal users can be marked payable

### Impact

If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are `CALLVALUE(2)`,`DUP1(3)`,`ISZERO(3)`,`PUSH2(3)`,`JUMPI(10)`,`PUSH1(3)`,`DUP1(3)`,`REVERT(0)`,`JUMPDEST(1)`,`POP(2)`, which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost.

### Findings

Total:8

[contracts/mixins/Auth.sol#L157](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L157)

```solidity
157:    function unfreeze() external onlyRole(OWNER) {
```

[contracts/mixins/Auth.sol#L180](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L180)

```solidity
180:    function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
```

[contracts/mixins/Auth.sol#L165](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L165)

```solidity
165:    function pause() external onlyRole(PAUSER) {
```

[contracts/mixins/Auth.sol#L187](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L187)

```solidity
187:    function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
```

[contracts/mixins/Auth.sol#L120](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L120)

```solidity
120:    function freezeShort() external onlyRole(SHORT_FREEZER) {
```

[contracts/mixins/Auth.sol#L148](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L148)

```solidity
148:    function freezeForever() external onlyRole(OWNER) {
```

[contracts/mixins/Auth.sol#L172](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L172)

```solidity
172:    function unpause() external onlyRole(PAUSER) {
```

[contracts/mixins/Auth.sol#L135](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L135)

```solidity
135:    function freezeLong() external onlyRole(LONG_FREEZER) {
```

### Recommendation

Mark the function as payable.

## [G-005] Don't use `_msgSender()` if not supporting EIP-2771

### Impact

The use of `_msgSender()` when there is no implementation of a meta transaction mechanism that uses it, such as EIP-2771, very slightly increases gas consumption.

### Findings

Total:13

[contracts/mixins/Auth.sol#L139](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L139)

```solidity
139:    if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());
```

[contracts/mixins/Auth.sol#L136](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L136)

```solidity
136:    longFreezes[_msgSender()] -= 1;
```

[contracts/plugins/aave/ERC20.sol#L215](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/ERC20.sol#L215)

```solidity
215:    _allowances[_msgSender()][spender].sub(
```

[contracts/plugins/aave/ERC20.sol#L189](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/ERC20.sol#L189)

```solidity
189:    _approve(_msgSender(), spender, _allowances[_msgSender()][spender].add(addedValue));
```

[contracts/plugins/aave/ERC20.sol#L118](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/ERC20.sol#L118)

```solidity
118:    _transfer(_msgSender(), recipient, amount);
```

[contracts/plugins/aave/ERC20.sol#L143](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/ERC20.sol#L143)

```solidity
143:    _approve(_msgSender(), spender, amount);
```

[contracts/plugins/aave/ERC20.sol#L168](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/ERC20.sol#L168)

```solidity
168:    _allowances[sender][_msgSender()].sub(
```

[contracts/p1/StRSR.sol#L633](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L633)

```solidity
633:    _approve(_msgSender(), spender, amount);
```

[contracts/p1/StRSR.sol#L421](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L421)

```solidity
421:    IERC20Upgradeable(address(rsr)).safeTransfer(_msgSender(), seizedRSR);
```

[contracts/p1/StRSRVotes.sol#L115](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSRVotes.sol#L115)

```solidity
115:    _delegate(_msgSender(), delegatee);
```

[contracts/p1/Broker.sol#L125](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Broker.sol#L125)

```solidity
125:    require(trades[_msgSender()], "unrecognized trade contract");
```

[contracts/p1/RToken.sol#L178](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L178)

```solidity
178:    issue(_msgSender(), amtRToken);
```

[contracts/p1/RToken.sol#L570](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L570)

```solidity
570:    _burn(_msgSender(), amtRToken);
```

### Recommendation

Replace `_msgSender()` with `msg.sender` if there is no mechanism to support meta-transactions like EIP-2771 implemented.

## [G-006] Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Impact

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### Findings

Total:4

[contracts/plugins/aave/StaticATokenLM.sol#L74-L76](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/StaticATokenLM.sol#L74-L76)

```solidity
74:    mapping(address => uint256) private _userSnapshotRewardsPerToken;
75:        // user => unclaimedRewards (in RAYs)
76:        mapping(address => uint256) private _unclaimedRewards;
```

[contracts/p1/StRSR.sol#L81-L82](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L81-L82)

```solidity
81:    mapping(uint256 => mapping(address => CumulativeDraft[])) public draftQueues; // {drafts}
82:        mapping(uint256 => mapping(address => uint256)) public firstRemainingDraft; // draft index
```

[contracts/p1/StRSRVotes.sol#L38-L40](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSRVotes.sol#L38-L40)

```solidity
38:    mapping(uint256 => mapping(address => Checkpoint[])) private _checkpoints; // {qStRSR}
39:        // `_totalSupplyCheckpoints[era]` is the history of totalSupply values during era `era`
40:        mapping(uint256 => Checkpoint[]) private _totalSupplyCheckpoints; // {qStRSR}
```

[contracts/p1/BasketHandler.sol#L40-L42](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L40-L42)

```solidity
40:    mapping(IERC20 => uint192) targetAmts;
41:        // Cached view of the target unit for each erc20 upon setup
42:        mapping(IERC20 => bytes32) targetNames;
```

## [G-007] Using `calldata` instead of memory for read-only arguments in external functions saves gas

### Impact

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a `for-loop` to copy each index of the `calldata` to the `memory` index. Each iteration of this `for-loop` costs at least 60 gas (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead.<br><br> If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one.

### Findings

Total:15

[contracts/plugins/governance/Governance.sol#L85-L88](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L85-L88)

```solidity
85:    function propose(
86:            address[] memory targets,
87:            uint256[] memory values,
88:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L95-L98](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L95-L98)

```solidity
95:    function propose(
96:            address[] memory targets,
97:            uint256[] memory values,
98:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L105-L108](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L105-L108)

```solidity
105:    function propose(
106:            address[] memory targets,
107:            uint256[] memory values,
108:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L116-L119](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L116-L119)

```solidity
116:    function propose(
117:            address[] memory targets,
118:            uint256[] memory values,
119:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L126-L129](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L126-L129)

```solidity
126:    function propose(
127:            address[] memory targets,
128:            uint256[] memory values,
129:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L85-L88](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L85-L88)

```solidity
85:    function queue(
86:            address[] memory targets,
87:            uint256[] memory values,
88:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L95-L98](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L95-L98)

```solidity
95:    function queue(
96:            address[] memory targets,
97:            uint256[] memory values,
98:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L105-L108](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L105-L108)

```solidity
105:    function queue(
106:            address[] memory targets,
107:            uint256[] memory values,
108:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L116-L119](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L116-L119)

```solidity
116:    function queue(
117:            address[] memory targets,
118:            uint256[] memory values,
119:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L126-L129](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L126-L129)

```solidity
126:    function queue(
127:            address[] memory targets,
128:            uint256[] memory values,
129:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L85-L88](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L85-L88)

```solidity
85:    function cancel(
86:            address[] memory targets,
87:            uint256[] memory values,
88:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L95-L98](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L95-L98)

```solidity
95:    function cancel(
96:            address[] memory targets,
97:            uint256[] memory values,
98:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L105-L108](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L105-L108)

```solidity
105:    function cancel(
106:            address[] memory targets,
107:            uint256[] memory values,
108:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L116-L119](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L116-L119)

```solidity
116:    function cancel(
117:            address[] memory targets,
118:            uint256[] memory values,
119:            bytes[] memory calldatas,
```

[contracts/plugins/governance/Governance.sol#L126-L129](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/governance/Governance.sol#L126-L129)

```solidity
126:    function cancel(
127:            address[] memory targets,
128:            uint256[] memory values,
129:            bytes[] memory calldatas,
```

## [G-008] Multiple accesses of a `mapping/array` should use a local variable cache

### Impact

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local storage or calldata variable when the value is accessed multiple times, saves ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into `memory/calldata` (**与[G-001]类似**)

### Findings

Total:1

[contracts/mixins/Auth.sol#L135-L141](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L135-L141)

```solidity
    function freezeLong() external onlyRole(LONG_FREEZER) {
        longFreezes[_msgSender()] -= 1; // reverts on underflow


        // Revoke on 0 charges as a cleanup step
        if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());
        freezeUntil(uint48(block.timestamp) + longFreeze);
    }
```

## [G-009] internal functions only called once can be inlined to save gas

### Impact

Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.

### Findings

Total:5

[contracts/p1/StRSR.sol#L795](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L795)

```solidity
795:    function _useNonce(address owner) internal returns (uint256 current) {
```

[contracts/p1/StRSR.sol#L694](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L694)

```solidity
694:    function _mint(address account, uint256 amount) internal virtual {
```

[contracts/p1/StRSR.sol#L708](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L708)

```solidity
708:    function _burn(address account, uint256 amount) internal virtual {
```

[contracts/p1/StRSRVotes.sol#L137](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSRVotes.sol#L137)

```solidity
137:    function _mint(address account, uint256 amount) internal override {
```

[contracts/p1/StRSRVotes.sol#L142](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSRVotes.sol#L142)

```solidity
142:    function _burn(address account, uint256 amount) internal override {
```

### Recommendation

Same as description

## [G-010] Use a more recent version of solidity

### Impact

- Use a solidity version of at least `0.8.2` to get simple compiler automatic inlining
- Use a solidity version of at least `0.8.3` to get better struct packing and cheaper multiple storage reads
- Use a solidity version of at least `0.8.4` to get custom errors, which are cheaper at deployment than `revert()/require()` strings
- Use a solidity version of at least `0.8.10` to have external calls skip contract existence checks if the external call has a return value

### Findings

Total:7

[contracts/plugins/aave/ERC20.sol#L3](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/ERC20.sol#L3)

```solidity
3:    pragma solidity ^0.6.0;
```

[contracts/plugins/aave/RayMathNoRounding.sol#L2](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/RayMathNoRounding.sol#L2)

```solidity
2:    pragma solidity 0.6.12;
```

[contracts/plugins/aave/IAaveIncentivesController.sol#L2](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/IAaveIncentivesController.sol#L2)

```solidity
2:    pragma solidity 0.6.12;
```

[contracts/plugins/aave/StaticATokenErrors.sol#L2](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/StaticATokenErrors.sol#L2)

```solidity
2:    pragma solidity 0.6.12;
```

[contracts/plugins/aave/IAToken.sol#L2](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/IAToken.sol#L2)

```solidity
2:    pragma solidity 0.6.12;
```

[contracts/plugins/aave/IStaticATokenLM.sol#L2](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/IStaticATokenLM.sol#L2)

```solidity
2:    pragma solidity 0.6.12;
```

[contracts/plugins/aave/StaticATokenLM.sol#L2](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/StaticATokenLM.sol#L2)

```solidity
2:    pragma solidity 0.6.12;
```

### Recommendation

Current version: 0.8.17 (2022-11)

## [G-011] Replace modifier with function

### Impact

Modifiers make code more elegant, but cost more than normal functions.

### Findings

Total:4

[contracts/plugins/aave/ReentrancyGuard.sol#L49](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/ReentrancyGuard.sol#L49)

```solidity
49:    modifier nonReentrant() {
```

[contracts/p1/mixins/Component.sol#L46](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/Component.sol#L46)

```solidity
46:    modifier notFrozen() {
```

[contracts/p1/mixins/Component.sol#L41](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/Component.sol#L41)

```solidity
41:    modifier notPausedOrFrozen() {
```

[contracts/p1/mixins/Component.sol#L51](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/Component.sol#L51)

```solidity
51:    modifier governance() {
```

## [G-012] Use named returns for local variables where it is possible.

### Impact

It is not necessary to have both a named return and a return statement.

### Findings

Total:6

[contracts/plugins/aave/StaticATokenLM.sol#L304](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/plugins/aave/StaticATokenLM.sol#L304)

```solidity
304:    uint256 amountToMint = _dynamicToStaticAmount(amount, rate());
```

[contracts/libraries/Fixed.sol#L159](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L159)

```solidity
159:    uint256 result = numerator / divisor;
```

[contracts/libraries/Fixed.sol#L321](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L321)

```solidity
321:    uint256 result = FIX_SCALE_SQ;
```

[contracts/libraries/Fixed.sol#L532](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/libraries/Fixed.sol#L532)

```solidity
532:    uint256 result = mulDiv256(x, y, z);
```

[contracts/p1/StRSR.sol#L455](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L455)

```solidity
455:    else right = test;
```

[contracts/p1/Broker.sol#L97](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Broker.sol#L97)

```solidity
97:    GnosisTrade trade = GnosisTrade(address(tradeImplementation).clone());
```
