| Index | issue                                                                                    |
|-------|------------------------------------------------------------------------------------------|
| 1     | Using calldata instead of memory for read-only arguments in external functions saves gas |
| 2     | Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead                     |
| 3     | Add unchecked {} for operation that can not overflow/underflow to save gas               |
| 4     | Use of Bit shift operators                                                               |
| 5     | Use nested if and, avoid multiple check combinations                                     |
| 6     | Use of Multiple require(s)                                                               |
| 7     | Mappings are cheaper than arrays                                                         |
| 8     | Functions guaranteed to revert when called by normal users can be marked payable         |
| 9     | ++i is more efficient then i +=1                                                         |

## 1.Using calldata instead of memory for read-only arguments in external functions saves gas
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having memory arguments, it's still valid for implementation contracs to use calldata arguments instead.

If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it's still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one
```
function basketTokens() external view returns (IERC20[] memory erc20s) {
```
[BasketHandler.sol#L394](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L394)
[BasketHandler.sol#L410](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L410)
[BasketHandler.sol#L694-L700](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L694-L700)
[BasketHandler.sol#L721](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L721)

## 2.Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead                     
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
```
uint8 constant FIX_DECIMALS = 18;
```
[Fixed.sol#L37](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L37)
[Fixed.sol#L41](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L41)
[Fixed.sol#L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L44)
[Fixed.sol#L48](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L48)
[Fixed.sol#L50-L53](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L50-L53)
[Fixed.sol#L72](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L72)
[Fixed.sol#L88](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L88)
[Fixed.sol#L98](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L98)
[Fixed.sol#L116](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L116)
[RedemptionBattery.sol#L11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L11)
[RedemptionBattery.sol#L24](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L24)
[RedemptionBattery.sol#L27](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L27)
[RedemptionBattery.sol#L64](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L64)
Note there are a lot of amount of this issue in whole codebase i haven't mentioned all of them .

## 3.Add unchecked {} for operation that can not overflow/underflow to save gas
you can add unchecked block for i++/++i in for loop because its not likely they are going to overflow/underflow
```
for (uint256 i = 1; i < arrLen; ++i) {
```
[Array.sol#L11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11)
[Array.sol#L12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L12)
[Array.sol#L23](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23)
[AssetRegistry.sol#L38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L38)
[AssetRegistry.sol#L49](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L49)
[AssetRegistry.sol#L127](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L127)
[AssetRegistry.sol#L138](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L138)
[BackingManager.sol#L221](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L221)
[BackingManager.sol#L238](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L238)

## 4.Use of Bit shift operators
you can use bit shift operators it cheaper than normal operation for example you can use bit shift if you want to divide by 2 using " >> 1" and for 4 use " >> 2" 
```
uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
```
[BasketHandler.sol#L372](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L372)
[StRSR.sol#L427](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L427)
[StRSR.sol#L453](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L453)
[EasyAuction.sol#L430](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L430)

## 5.Use nested if and, avoid multiple check combinations
Using nested is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.
```
if (battery.redemptionRateFloor == 0 && battery.scalingRedemptionRate == 0) return;
```
[RedemptionBattery.sol#L38](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L38)
[String.sol#L16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L16)
[Distributor.sol#L168](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L168)
[RToken.sol#L202](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L202)
[RToken.sol#L251-L256](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L251-L256)

## 6.Use of Multiple require(s)
Using mutiple require statements is cheaper than using && multiple check combinations. There are more advantages, such as easier to read code and better coverage reports.
```
require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range")
```
[Auth.sol#L181](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181)
[Auth.sol#L188](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188)
[Deployer.sol#L49-L63](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L49-L63)
[Deployer.sol#L109](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109)
[Furnace.sol#L89](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89)
[RevenueTrader.sol#L72](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72)
[RToken.sol#L410](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410)
[RToken.sol#L590](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590)
[StRSR.sol#L813](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813)
[StRSR.sol#L821](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L821)

## 7.Mappings are cheaper than arrays
Solidity provides only two data types to represents list of data: arrays and maps. Mappings are cheaper, while arrays are packable and iterable.

In order to save gas, it is recommended to use mappings to manage lists of data, unless there is a need to iterate or it is possible to pack data types. This is useful both for Storage and Memory. You can manage an ordered list with a mapping using an integer index as a key.

## 8.Functions guaranteed to revert when called by normal users can be marked payable
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are
CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
```
 function freezeShort() external onlyRole(SHORT_FREEZER) {
```
[Auth.sol#L120](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L120)
[Auth.sol#L135](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L135)
[Auth.sol#L148](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L148)
[Auth.sol#L157](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L157)
[Auth.sol#L165](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L165)
[Auth.sol#L172](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L172)
[Auth.sol#L180](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L180)
[Auth.sol#L187](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L187)

## 9.`++i` is more efficient then `i +=1`
```
if (mm > 0) result += 1;
```
[Fixed.sol#L537](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L537)
[Fixed.sol#L539](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L539)
[BasketHandler.sol#L636](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L636)
