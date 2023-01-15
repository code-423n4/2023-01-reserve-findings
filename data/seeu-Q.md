## Compiler version Pragma is non-specific

### Description

For non-library contracts, floating pragmas may be a security risk for application implementations, since a known vulnerable compiler version may accidentally be selected or security tools might fallback to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

### Findings

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L3 => pragma solidity ^0.6.0;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L2 => pragma solidity ^0.8.0;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L2 => pragma solidity ^0.8.0;
```

### Resources

- [L003 - Unspecific Compiler Version Pragma](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)
- [Version Pragma | Solidity documents](https://docs.soliditylang.org/en/latest/layout-of-source-files.html#version-pragma)
- [4.6 Unspecific compiler version pragma | Consensys Audit of 1inch Liquidity Protocol](https://consensys.net/diligence/audits/2020/12/1inch-liquidity-protocol/#unspecific-compiler-version-pragma)


## Unsafe ERC20 Operation: _mint

_SafeMint is a better option than the original _mint function, as it helps to prevent unauthorized token minting.

### Findings

- [contracts/p1/StRSRVotes.sol#L138](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L138) => `super._mint(account, amount);`

### Resources

- [_mint](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_mint-address-uint256-) vs [_safeMint](https://docs.openzeppelin.com/contracts/2.x/api/token/erc721#ERC721-_safeMint-address-uint256-bytes-) | OpenZeppelin Docs



## require()/revert() statements should have descriptive reason strings

To increase overall code clarity and aid in debugging whenever a need is not met, think about adding precise, informative error messages to all `require` and `revert` statements.

### Findings

```Solidity
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol
::102 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
::116 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
::133 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol
::149 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol
::78 =>             if (errData.length == 0) revert(); // solhint-disable-line reason-string
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadCollateralPlugin.sol
::51 =>                 if (errData.length == 0) revert(); // solhint-disable-line reason-string
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol
::32 =>             revert(); // Revert with no reason
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol
::43 =>         require(balanceOf[msg.sender] >= wad);
::68 =>         require(balanceOf[src] >= wad);
::71 =>             require(allowance[src][msg.sender] >= wad);
```

### Resources

- [Error handling: Assert, Require, Revert and Exceptions](https://docs.soliditylang.org/en/v0.8.17/control-structures.html#error-handling-assert-require-revert-and-exceptions)
- [Missing error messages in require statements | Opyn Bull Strategy Contracts Audit](https://blog.openzeppelin.com/opyn-bull-strategy-contracts-audit/#missing-error-messages-in-require-statements)