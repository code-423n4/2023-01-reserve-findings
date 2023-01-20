## [NC-01] USE LATEST OPENZZEPELIN VERSION

The project used openzzepelin version 4.7.3 while the latest openzzepelin version is 4.8.1
File: package.json
```
 "@openzeppelin/contracts": "^4.7.3",
 "@openzeppelin/contracts-upgradeable": "^4.7.3",
```

It is best practice to always use latest stable versions of softwares especially when security is important as many updates and bug fixes would have been made on the latest version.

## Use NatSpec comments
Some functions in the codebase lack Natspec comments.
See: https://docs.soliditylang.org/en/v0.8.17/natspec-format.html
Files: 
- [contracts/p1/StRSRVotes.sol#L118](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L118)
- [contracts/p1/StRSRVotes.sol#L137](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L137)
- [contracts/p1/StRSRVotes.sol#L142](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L142)
- [contracts/p1/StRSRVotes.sol#L147](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L147)

## [NC-02] Use named imports

Files: all files

Use
```
import {IERC20 } from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```
instead of 

```
import  "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

## [NC-03] REMOVE EMPTY BLOCKS OR EMIT EVENT
FILES: 
- [contracts/plugins/assets/RTokenAsset.sol#L119](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L119)
- [contracts/plugins/governance/Governance.sol#L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L44)



## [L-01] USE LATEST STABLE COMPILER VERSION 0.8.17

Use latest solidity pragma version 0.8.17.

Files: all files

```
pragma solidity 0.8.9;
```

#### Recommended Mitigation Steps

Use latest solidity pragma version 0.8.17.




