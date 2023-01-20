## Private function with embedded modifier reduces contract size
Consider having the logic of a modifier embedded through a private function to reduce contract size if need be. A private visibility that saves more gas on function calls than the internal visibility is adopted because the modifier will only be making this call inside the contract.

For instance, the modifier instance below may be refactored as follows:

[File: Component.sol#L41-L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L41-L44)

```diff
+    function _notPausedOrFrozen() private view {
+        require(!main.pausedOrFrozen(), "paused or frozen");
+    }

    modifier notPausedOrFrozen() {
-        require(!main.pausedOrFrozen(), "paused or frozen");
+        _notPausedOrFrozen();
        _;
    }
```
## Use storage instead of memory for structs/arrays
A storage pointer is cheaper since copying a state struct in memory would incur as many SLOADs and MSTOREs as there are slots. In another words, this causes all fields of the struct/array to be read from storage, incurring a Gcoldsload (2100 gas) for each field of the struct/array, and then further incurring an additional MLOAD rather than a cheap stack read. As such, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables will be much cheaper, involving only Gcoldsload for all associated field reads. Read the whole struct/array into a memory variable only when it is being returned by the function, passed into a function that requires memory, or if the array/struct is being read from another memory array/struct.

Here are some of the instances entailed:

[File: RecollateralizationLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol)

```solidity
70:        ComponentCache memory components = ComponentCache({

78:        TradingRules memory rules = TradingRules({

83:        Registry memory reg = components.reg.getRegistry();

              [ ... ]
```
## Merging of if blocks
In `price()` of OracleLib.sol, the two if blocks can be merged as follows to save gas both on contract size and function calls:

[File: OracleLib.sol#L14-L31](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L14-L31)

```diff
    function price(AggregatorV3Interface chainlinkFeed, uint48 timeout)
        internal
        view
        returns (uint192)
    {
        (uint80 roundId, int256 p, , uint256 updateTime, uint80 answeredInRound) = chainlinkFeed
            .latestRoundData();

-        if (updateTime == 0 || answeredInRound < roundId) {
+        if (uint48(block.timestamp - updateTime) > timeOut || answeredInRound < roundId) {
            revert StalePrice();
        }
        // Downcast is safe: uint256(-) reverts on underflow; block.timestamp assumed < 2^48
-        uint48 secondsSince = uint48(block.timestamp - updateTime);
-        if (secondsSince > timeout) revert StalePrice();

        // {UoA/tok}
        return shiftl_toFix(uint256(p), -int8(chainlinkFeed.decimals()));
    }
```
## `||` costs less gas than its equivalent `&&`
Rule of thumb: `(x && y)` is `(!(!x || !y))`

Even with the 10k Optimizer enabled: `||`, OR conditions cost less than their equivalent `&&`, AND conditions.

As an example, the instance below may be refactored as follows:

[File: RecollateralizationLib.sol#L401](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L401)

```diff
-        if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {
+        if (!(address(trade.sell) != address(0) || address(trade.buy) == address(0))) {
```
## Early if block
In `melt()` of Furnace.sol, the last if block should be executed before `lastPayoutBal` is updated. In the event `amount == 0` due to `ratio == 0`, the function could end with a return and save some gas.

[File: Furnace.sol#L70-L84](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L70-L84)

```diff
    function melt() external notPausedOrFrozen {
        if (uint48(block.timestamp) < uint64(lastPayout) + period) return;

        // # of whole periods that have passed since lastPayout
        uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;

        // Paying out the ratio r, N times, equals paying out the ratio (1 - (1-r)^N) 1 time.
        uint192 payoutRatio = FIX_ONE.minus(FIX_ONE.minus(ratio).powu(numPeriods));

        uint256 amount = payoutRatio.mulu_toUint(lastPayoutBal);

        lastPayout += numPeriods * period;

+        if (amount == 0) {
+            return;
+        else {
+            rToken.melt(amount);
+        }

        lastPayoutBal = rToken.balanceOf(address(this)) - amount;
-        if (amount > 0) rToken.melt(amount);
    }
```
## Unchecked SafeMath saves gas
"Checked" math, which is default in ^0.8.0 is not free. The compiler will add some overflow checks, somehow similar to those implemented by `SafeMath`. While it is reasonable to expect these checks to be less expensive than the current `SafeMath`, one should keep in mind that these checks will increase the cost of "basic math operation" that were not previously covered. This particularly concerns variable increments in for loops. When no arithmetic overflow/underflow is going to happen, `unchecked { ++i ;}` to use the previous wrapping behavior further saves gas just as in the for loop below as an example:

[File: RecollateralizationLib.sol#L437-L454](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437-L454)

```diff
-        for (uint256 i = 0; i < len; ++i) {
+        for (uint256 i = 0; i < len;) {
              ICollateral coll = components.reg.toColl(basketERC20s[i]);

              [ ... ]

+            unchecked {
+                ++i;
+            }
          }
```
## Non-strict inequalities are cheaper than strict ones
In the EVM, there is no opcode for non-strict inequalities (>=, <=) and two operations are performed (> + = or < + =).

As an example, the following `>=` inequality instance may be refactored as follows:

[File: RewardableLib.sol#L78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78)

```diff
-                assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
+                assert(erc20s[i].balanceOf(address(this)) > liabilities[erc20s[i]] - 1);
```
## `break` or `continue` in for loop
`for` loop entailing large array with reverting logic should incorporate `break` or `continue` to cater for element(s) failing to get through the iteration(s). This will tremendously save gas on instances where the loop specifically fails to execute at the end of the iterations.

Here is an instance entailed where `refresh()` is associated with try catch reverting code:

[File: AssetRegistry.sol#L46-L52](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L46-L52)

```solidity
    function refresh() public {
        // It's a waste of gas to require notPausedOrFrozen because assets can be updated directly
        uint256 length = _erc20s.length();
        for (uint256 i = 0; i < length; ++i) {
            assets[IERC20(_erc20s.at(i))].refresh();
        }
    }
```
## Split require statements using &&
Instead of using the `&&` operator in a single require statement to check multiple conditions, using multiple require statements with 1 condition per require statement will save 3 GAS per `&&`.

Here is a sample instance entailed:

[File: Broker.sol#L134-L137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L134-L137)

```solidity
        require(
            newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
            "invalid auctionLength"
        );
```
## Use of named returns for local variables saves gas
You can have further advantages in term of gas cost by simply using named return values as temporary local variable.

For instance, the code block below may be refactored as follows:

[File: Deployer.sol#L102-L249](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L102-L249)

```diff
    function deploy(
        string memory name,
        string memory symbol,
        string calldata mandate,
        address owner,
        DeploymentParams memory params
-    ) external returns (address) {
+    ) external returns (address rTokenAddr) {

        [ ... ]

-        return (address(components.rToken));
+        rTokenAddr = address(components.rToken);
    }
```
## Unneeded logic
In Auth.sol, a LONG_FREEZER role is given 6 charges whereas the SHORT_FREEZER role is only entitled to a one time use. And when they are run out of the assigned privileges, they can/will be recharged via `grantRole()`. In my opinion, the logic entailed is unnecessary since these assignees are trusted partners and/or associates to the protocol. If need be, the owner can always revoke their roles via the inherited `revokeRole()`.

Consider removing the following lines of codes to save gas both on contract size and function calls:

[File: Auth.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol)

```diff
- 7: uint256 constant LONG_FREEZE_CHARGES = 6; // 6 uses

- 121:        // Revoke short freezer role after one use
- 122:        _revokeRole(SHORT_FREEZER, _msgSender());

- 138:        // Revoke on 0 charges as a cleanup step
- 139:        if (longFreezes[_msgSender()] == 0) _revokeRole(LONG_FREEZER, _msgSender());
```
## += and -= cost more gas
`+=` and `-=` generally cost 22 more gas than writing out the assigned equation explicitly. The amount of gas wasted can be quite sizable when repeatedly operated in a loop.

For instance, the `+=` instance below may be refactored as follows:

[File: RToken.sol#L330](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L330)

```diff
-            liabilities[IERC20(erc20s[i])] += deposits[i];
+            liabilities[IERC20(erc20s[i])] = liabilities[IERC20(erc20s[i])] + deposits[i];
```
## Ternary over `if ... else`
Using ternary operator instead of the if else statement saves gas.

For instance, the code block below may be refactored as follows:

[File: RToken.sol#L392-L396](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L392-L396)

```solidity
 earliest
     ? refundSpan(account, queue.left, endId)
     : refundSpan(account, endId, queue.right);
```    
## Local variable not fully utilized
The input parameter, `val`, in `setUnstakingDelay()` and `setRewardPeriod()` of StRSR.sol could have been used in the second require statement to separately save 1 SLOAD on `unstakingDelay`:

[File: StRSR.sol#L812-L825](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812-L825)

```diff
    function setUnstakingDelay(uint48 val) public governance {
        require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
        emit UnstakingDelaySet(unstakingDelay, val);
        unstakingDelay = val;
-        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
+        require(rewardPeriod * 2 <= val, "unstakingDelay/rewardPeriod incompatible");
    }

    /// @custom:governance
    function setRewardPeriod(uint48 val) public governance {
        require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
        emit RewardPeriodSet(rewardPeriod, val);
        rewardPeriod = val;
-        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
+        require(val * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
    }
```
## Early checks
Checks in a function logic should be as early as possible to minimize gas waste in the event of a revert.

For instance, the same instances in the preceding issue with the input parameter, `val`, fully utilized should have been refactored as follows:

[File: StRSR.sol#L812-L825](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812-L825) 

```diff
    function setUnstakingDelay(uint48 val) public governance {
        require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");
+        require(rewardPeriod * 2 <= val, "unstakingDelay/rewardPeriod incompatible");
        emit UnstakingDelaySet(unstakingDelay, val);
        unstakingDelay = val;
-        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
    }

    /// @custom:governance
    function setRewardPeriod(uint48 val) public governance {
        require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");
+        require(val * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
        emit RewardPeriodSet(rewardPeriod, val);
        rewardPeriod = val;
-        require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
    }
```
## Payable access control functions costs less gas
Consider marking functions with access control as `payable`. This will save 20 gas on each call by their respective permissible callers for not needing to have the compiler check for `msg.value`.

For instance, the function below may be refactored as follows:

[File: BackingManager.sol#L256](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256)

```diff
-    function setTradingDelay(uint48 val) public governance {
+    function setTradingDelay(uint48 val) public payable governance {
```
## Function order affects gas consumption
The order of function will also have an impact on gas consumption. Because in smart contracts, there is a difference in the order of the functions. Each position will have an extra 22 gas. The order is dependent on method ID. So, if you rename the frequently accessed function to more early method ID, you can save gas cost. Please visit the following site for further information:

https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92

## Activate the optimizer
Before deploying your contract, activate the optimizer when compiling using “solc --optimize --bin sourceFile.sol”. By default, the optimizer will optimize the contract assuming it is called 200 times across its lifetime. If you want the initial contract deployment to be cheaper and the later function executions to be more expensive, set it to “ --optimize-runs=1”. Conversely, if you expect many transactions and do not care for higher deployment cost and output size, set “--optimize-runs” to a high number.

```
module.exports = {
solidity: {
version: "0.8.16",
settings: {
  optimizer: {
    enabled: true,
    runs: 1000,
  },
},
},
};
```
Please visit the following site for further information:

https://docs.soliditylang.org/en/v0.5.4/using-the-compiler.html#using-the-commandline-compiler

Here's one example of instance on opcode comparison that delineates the gas saving mechanism:

```
for !=0 before optimization
PUSH1 0x00
DUP2
EQ
ISZERO
PUSH1 [cont offset]
JUMPI

after optimization
DUP1
PUSH1 [revert offset]
JUMPI
```
Disclaimer: There have been several bugs with security implications related to optimizations. For this reason, Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past . A high-severity bug in the emscripten -generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. Please measure the gas savings from optimizations, and carefully weigh them against the possibility of an optimization-related bug. Also, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## Use assembly to check for address(0)
Using assembly to check for address(0) saves 6 gas for each instance.

As an example, the following instance may be refactored as follows:

[File: ComponentRegistry.sol#L36-L40](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L36-L40)

```diff
    function _setRToken(IRToken val) private {
-        require(address(val) != address(0), "invalid RToken address");

+        assembly {
+            if iszero(address(val)) {
+                mstore(0x00, "invalid RToken address")
+                revert(0x00, 0x20)
+            }

        emit RTokenSet(rToken, val);
        rToken = val;
    }
```
## Unneeded if block
In BackingManager.sol, the second check in the if block of `handoutExcessAssets()` is unneeded because `basketHandler.fullyCollateralized()` has already confirmed that `basketsHeldBy(address(backingManager)) >= rToken.basketsNeeded()` before `handoutExcessAssets()` is privately invoked:

[File: BasketHandler.sol#L273-L275](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L273-L275)

```solidity
    function fullyCollateralized() external view returns (bool) {
        return basketsHeldBy(address(backingManager)) >= rToken.basketsNeeded();
    }
```
As such consider refactoring the following code line to save gas on contract size and function calls:

[File: BackingManager.sol#L192-L206](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L192-L206) 

```diff
-            if (held.gt(needed)) {
                // gas-optimization: RToken is known to have 18 decimals, the same as FixLib
                uint192 totalSupply = _safeWrap(rToken.totalSupply()); // {rTok}

                // {BU} = {BU} - {BU}
                uint192 extraBUs = held.minus(needed);

                // {rTok} = {BU} * {rTok / BU} (if needed == 0, conv rate is 1 rTok/BU)
                uint192 rTok = (needed > 0) ? extraBUs.mulDiv(totalSupply, needed) : extraBUs;

                // gas-optimization: RToken is known to have 18 decimals, same as FixLib
                rToken.mint(address(this), uint256(rTok));
                rToken.setBasketsNeeded(held);
                needed = held;
-            }
```
## The first condition check is unneeded
In `quantityMulPrice()` of BasketHandler.sol, the comment before the first if block already mentioned qty will never = 0 here because of the check in _price(). For this reason, `qty == 0` is always going to return false, making this check a waste of gas.

Consider refactoring the affected code line as follows:

[File: BasketHandler.sol#L358-L359](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L358-L359)

```diff
        //      qty will never = 0 here because of the check in _price()
-        if (qty == 0 || p == 0) return 0;
+        if (p == 0) return 0;
```