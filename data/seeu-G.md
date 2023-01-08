## Cache array length outside of loop

### Description
If the array's length is not changed during a loop, caching it outside the loop saves reading it on each iteration

### Findings
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L523 => while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
```

### References

- [G002 - Cache Array Length Outside of Loop](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)
- [Avoid unnecessary read of array length in for loops can save gas](https://github.com/code-423n4/2021-11-badgerzaps-findings/issues/36)



## Missing implementation Shift Right/Left for division and multiplication

### Description

The `SHR` opcode only utilizes 3 gas, compared to the 5 gas used by the `DIV` opcode. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

### Findings

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L427 => return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L453 => test = (left + right) / 2; // left < test < right because left < right - 1
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L430 => return (a / 2) + (b / 2) + (((a % 2) + (b % 2)) / 2);
```

### References
- [G008 - Use Shift Right/Left instead of Division/Multiplication if possible](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
- [EVM Opcodes](https://www.evm.codes/)



## Make function external instead of public

### Description

Version 0.6.9 removed the restriction on public functions accepting calldata arguments. Public functions for Solidity versions 0.6.9 have to transfer the parameters to memory.

### Findings
```
reserve/protocol/contracts/plugins/aave/ERC20.sol::188 =>     function increaseAllowance(address spender, uint256 addedValue) public virtual returns (bool) { || pragma solidity ^0.6.0;
reserve/protocol/contracts/plugins/mocks/WETH.sol::59 =>     function transfer(address dst, uint256 wad) public returns (bool) { || pragma solidity >=0.4.22 <0.6;
```

### Resources

- [G009 - Make Function external instead of public](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g009---make-function-external-instead-of-public)
- [StackOverflow answer re `solc >=0.6.9`](https://ethereum.stackexchange.com/questions/107578/does-using-external-over-public-in-a-library-reduce-any-gas-costs/107939#107939)
- [Public vs External Functions in Solidity | Gustavo (Gus) Guimaraes post](https://gus-tavo-guim.medium.com/public-vs-external-functions-in-solidity-b46bcf0ba3ac)
- [StackOverflow answer](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices?answertab=active#tab-top)