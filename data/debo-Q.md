## [L-01] 
```
SWC-103 Floating Pragma
```

URL
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L3
```

Case
```
Check for circumflex or greater than and less than operators in pragma version
```

Description
```
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
```

PoC
```
pragma solidity ^0.8.9;
```

Remediation
```
// remove circumflex or GT & LT operators
pragma solidity 0.8.9;
```