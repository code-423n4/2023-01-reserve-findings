## [G-01] Cache array length outside of loop

### Description
If the array's length is not changed during a loop, caching it outside the loop saves reading it on each iteration

### Findings
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
::523 => while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
```

### References

- [G002 - Cache Array Length Outside of Loop](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)
- [Avoid unnecessary read of array length in for loops can save gas](https://github.com/code-423n4/2021-11-badgerzaps-findings/issues/36)



## [G-02] Missing implementation Shift Right/Left for division and multiplication

### Description

The `SHR` opcode only utilizes 3 gas, compared to the 5 gas used by the `DIV` opcode. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

### Findings

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
::372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
::427 => return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
::453 => test = (left + right) / 2; // left < test < right because left < right - 1
::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol
::430 => return (a / 2) + (b / 2) + (((a % 2) + (b % 2)) / 2);
```

### References
- [G008 - Use Shift Right/Left instead of Division/Multiplication if possible](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
- [EVM Opcodes](https://www.evm.codes/)



## [G-03] Make function external instead of public

### Description

Version 0.6.9 removed the restriction on public functions accepting calldata arguments. Public functions for Solidity versions 0.6.9 have to transfer the parameters to memory.

Note: valid only for solidity versions `<0.6.9`

### Findings
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol
::66 =>     function name() public view returns (string memory) { || pragma solidity ^0.6.0;
::74 =>     function symbol() public view returns (string memory) { || pragma solidity ^0.6.0;
::91 =>     function decimals() public view returns (uint8) { || pragma solidity ^0.6.0;
::98 =>     function totalSupply() public view override returns (uint256) { || pragma solidity ^0.6.0;
::105 =>     function balanceOf(address account) public view override returns (uint256) { || pragma solidity ^0.6.0;
::117 =>     function transfer(address recipient, uint256 amount) public virtual override returns (bool) { || pragma solidity ^0.6.0;
::142 =>     function approve(address spender, uint256 amount) public virtual override returns (bool) { || pragma solidity ^0.6.0;
::188 =>     function increaseAllowance(address spender, uint256 addedValue) public virtual returns (bool) { || pragma solidity ^0.6.0;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol
::37 =>     function deposit() public payable { || pragma solidity >=0.4.22 <0.6;
::42 =>     function withdraw(uint256 wad) public { || pragma solidity >=0.4.22 <0.6;
::49 =>     function totalSupply() public view returns (uint256) { || pragma solidity >=0.4.22 <0.6;
::53 =>     function approve(address guy, uint256 wad) public returns (bool) { || pragma solidity >=0.4.22 <0.6;
::59 =>     function transfer(address dst, uint256 wad) public returns (bool) { || pragma solidity >=0.4.22 <0.6;
```

### Resources

- [G009 - Make Function external instead of public](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g009---make-function-external-instead-of-public)
- [Public vs External Functions in Solidity | Gustavo (Gus) Guimaraes post](https://gus-tavo-guim.medium.com/public-vs-external-functions-in-solidity-b46bcf0ba3ac)
- [StackOverflow answer](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices?answertab=active#tab-top)



## [G-04] Using bools for storage incurs overhead

### Description

Use uint256 for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

### Findings
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol
::17 =>     bool minFundingThresholdNotReached;
::18 =>     bool isAtomicClosureAllowed;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol
::93 =>     bool isRSR = erc20 == rsr; // if false: isRToken
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol
::500 =>    bool allZero = true;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol
::107 =>    bool minFundingThresholdNotReached;
::108 =>    bool isAtomicClosureAllowed;
::319 =>    bool success = sellOrders[auctionId].removeKeepHistory(_sellOrders[i]);
::523 =>    bool minFundingThresholdNotReached = auctionData[auctionId].minFundingThresholdNotReached;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol
::321 =>    bool result = removeKeepHistory(self, elementToRemove);
```

### References

- [[GAS-1] Using bools for storage incurs overhead](https://gist.github.com/Picodes/ab2df52379e4b4993709be1b91aab651#gas-1-using-bools-for-storage-incurs-overhead)
- [OpenZeppelin Contracts](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)


## [G-05] Use "require" instead of "assert" when possible

If `assert()` returns a false assertion, it compiles to the invalid opcode `0xfe`, which eats up all the gas left and completely undoes the changes. `require()` compiles to `0xfd`, which is the opcode for a `REVERT`, indicating that it will return the remaining gas if it returns a false assertion.

### Findings

```Solidity
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol
::249 =>         assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
::556 =>             assert(targetIndex < targetsLength);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
::696 =>         assert(totalStakes + amount < type(uint224).max);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol
::110 =>         assert(doTrade);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol
::78 =>                 assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
::102 =>             assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol
::44 =>         assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
::108 =>         assert(
::168 =>             assert(errorCode == 0x11 || errorCode == 0x12);
::170 =>             assert(keccak256(reason) == UIntOutofBoundsHash);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol
::114 =>         assert(address(trades[sell]) == address(0));
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol
::345 =>             assert(amt == amountToWithdraw);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol
::98 =>                 assert(low == 0);
::112 =>             assert(low <= high);
::147 =>         assert(lotLow <= lotHigh);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol
::137 =>                 assert(low == 0);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol
::74 =>             assert(low <= high);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol
::63 =>         assert(status == TradeStatus.PENDING);
::98 =>         assert(origin_ != address(0));
::182 =>             assert(isAuctionCleared());
```

### Reference

- [Assert() vs Require() in Solidity – Key Difference & What to Use](https://codedamn.com/news/solidity/assert-vs-require-in-solidity)