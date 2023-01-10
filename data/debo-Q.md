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

## [L-02] 
```
SWC-100 Function Default Visibility 
```

URL
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L72
```

Case 
```
Check for functions that do not have the visibility set with a value i.e., public, internal, and external.
```

Description
```
Functions that do not have a function visibility type specified are public by default. This could lead to a vulnerability if a developer forgot to set the visibility and a malicious user is able to make unauthorized or unintended state changes.
```

PoC
```
function _safeWrap(uint256 x) pure returns (uint192) {
```

Remediation
```
// Add visibility e.g., internal
function _safeWrap(uint256 x) internal pure returns (uint192) {
```

## [L-03]
```
SWC-100 Function Default Visibility 
```

URL
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L80
```

Case 
```
Check for functions that do not have the visibility set with a value i.e., public, internal, and external.
```

Description
```
Functions that do not have a function visibility type specified are public by default. This could lead to a vulnerability if a developer forgot to set the visibility and a malicious user is able to make unauthorized or unintended state changes.
```

PoC
```
function toFix(uint256 x) pure returns (uint192) {
```

Remediation
```
// Add visibility e.g., internal
function toFix(uint256 x) internal pure returns (uint192) {
```

## [L-04]
```
SWC-100 Function Default Visibility 
```

URL
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L88
```

Case 
```
Check for functions that do not have the visibility set with a value i.e., public, internal, and external.
```

Description
```
Functions that do not have a function visibility type specified are public by default. This could lead to a vulnerability if a developer forgot to set the visibility and a malicious user is able to make unauthorized or unintended state changes.
```

PoC
```
function shiftl_toFix(uint256 x, int8 shiftLeft) pure returns (uint192) {
```

Remediation
```
// Add visibility e.g., internal
function shiftl_toFix(uint256 x, int8 shiftLeft) internal pure returns (uint192) {
```

## [L-05]
```
SWC-100 Function Default Visibility 
```

URL
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L94-L98
```

Case 
```
Check for functions that do not have the visibility set with a value i.e., public, internal, and external.
```

Description
```
Functions that do not have a function visibility type specified are public by default. This could lead to a vulnerability if a developer forgot to set the visibility and a malicious user is able to make unauthorized or unintended state changes.
```

PoC
```
function shiftl_toFix(
    uint256 x,
    int8 shiftLeft,
    RoundingMode rounding
) pure returns (uint192) {
```

Remediation
```
// Add visibility e.g., internal
function shiftl_toFix(
    uint256 x,
    int8 shiftLeft,
    RoundingMode rounding
) internal pure returns (uint192) {
```