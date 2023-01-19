## Gas Optimizaiton

| G-O    |Issue|
|:------:|:----|
| [G&#x2011;01] | Using private rather than public for constants, saves gas | 2 |
| [G&#x2011;02] | Division by two should use bit shifting | 6 |
| [G&#x2011;03] | USE REQUIRE INSTEAD OF ASSERT | 11 |
| [G&#x2011;04] | x = x - y costs less gas than x -= y, same for addition | 19 |
| [G&#x2011;05] | REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS | 31 |
| [G&#x2011;06] | THERE’S NO NEED TO SET DEFAULT VALUES FOR VARIABLES | 2 |
| [G&#x2011;07] | Splitting require() statements that use && saves gas | 1 |
| [G&#x2011;08] | Use x != 0 instead of x > 0 for uint types | 1 |
| [G&#x2011;09] | Change the variable to immutable | 1 |
| [G&#x2011;10] | USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS -3 | 1 |

Total: 10 issues

### [G-01] Using private rather than public for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table.

File: `BackingManager.sol`

```solidity
uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
uint192 public constant MAX_BACKING_BUFFER = FIX_ONE; // {1} 100%
```

Lines of code: 

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L34

### [G-02] Division by two should use bit shifting

<x>/ 2 is the same as <x>>\> 1\. While the compiler uses the SHR opcode to accomplish both, the version that uses division incurs an overhead of 20 gas due to JUMPs to and from a compiler utility function that introduces checks which can be avoided by using unchecked {} around the division by two</x></x>

Files: `BasketHandler.sol` and `StRSR.sol`

```solidity
uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
```

```solidity
return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
```

```solidity
test = (left + right) / 2; // left < test < right because left < right - 1
```

Lines of code:

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L372 

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L427

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L453

### [G-03] USE REQUIRE INSTEAD OF ASSERT
The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error. They are quite similar as both check for conditions and if they are not met, would throw an error. The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

File: `BackingManager`, `BasketHandler.sol` and `StRSR.sol`

```solidity
assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
```

```solidity
assert(targetIndex < targetsLength);
```

```solidity
assert(totalStakes + amount < type(uint224).max);
```

Lines of code:

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696

### [G-04]  x = x - y costs less gas than x -= y, same for addition
You can replace all -= and += occurrences to save gas

Example in `BasketHandler.sol`

```solidity
low256 += quantityMulPrice(qty, lowP);
high256 += quantityMulPrice(qty, highP);
```

### [G-05] REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas.

File: `BasketHandler.sol`

```solidity
require(0 < targetAmts[i], "invalid target amount; must be nonzero");
```

Lines of code:

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L231

### [G-06]] THERE’S NO NEED TO SET DEFAULT VALUES FOR VARIABLES
If a variable is not set/initialized, the default value is assumed (0, false, 0x0 … depending on the data type). You are simply wasting gas if you directly initialize it with its default value.

File: `BasketHandler.sol`

```solidity
uint256 assigned = 0;
```

Lines of code:

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L606


### [G-07]  Splitting require() statements that use && saves gas
See this issue which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by 3 gas

Files: `Furnace.sol` and `Broker.sol`

```solidity
require(newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH, "invalid auctionLength");
```

```solidity
require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
```

Lines of code:

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L135

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89

### [G-08] Use x != 0 instead of x > 0 for uint types
The != operator costs less gas than > and for uint types you can use it to check for non-zero values to save gas

Files: `Furnace.sol` and `StRSR.sol`

```solidity
if (amount > 0) rToken.melt(amount);
```

```solidity
require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");
```

In `StRSR.sol` there are 13 instances of this issue.

Lines of code:

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L83

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89

### [G-09] Change the variable to immutable

In `RevenueTrader.sol` comment for `tokenTobuy` describes the variable as immutable and its value is set in the init function so it can be marked as immutable to save some gas every time the variables are used. , also `mandate` in `RToken.sol`

```solidity
// Immutable after init()
IERC20 public tokenToBuy;
```

```solidity
/// immutable data store.
string public mandate;
```
Lines of code:

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L19

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L44



### [G-10] USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS -3

 When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.

```solidity
TradeInfo memory trade = TradeInfo({
```

```solidity
TradingRules memory rules = TradingRules({
```

```solidity
uint192[] memory refAmts = new uint192[](basket.erc20s.length);
```

```solidity
bytes32[] memory names = new bytes32[](erc20s.length);
```


Lines of code:

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L74

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L82

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L169

- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L225
