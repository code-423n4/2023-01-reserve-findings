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

## [L-06]
```
SWC-108 State Variable Default Visibility
```

URL
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L33-L53
```

Case 
```
Check for variables that do not have the visibility set with a value i.e., public, internal, and external.
```

Description
```
Labeling the visibility explicitly makes it easier to catch incorrect assumptions about who can access the variable.
```

PoC
```
bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));

// Used by P1 implementation for easier casting
uint256 constant FIX_ONE_256 = 1e18;
uint8 constant FIX_DECIMALS = 18;

// If a particular uint192 is represented by the uint192 n, then the uint192 represents the
// value n/FIX_SCALE.
uint64 constant FIX_SCALE = 1e18;

// FIX_SCALE Squared:
uint128 constant FIX_SCALE_SQ = 1e36;

// The largest integer that can be converted to uint192 .
// This is a bit bigger than 3.1e39
uint192 constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

uint192 constant FIX_ZERO = 0; // The uint192 representation of zero.
uint192 constant FIX_ONE = FIX_SCALE; // The uint192 representation of one.
uint192 constant FIX_MAX = type(uint192).max; // The largest uint192. (Not an integer!)
uint192 constant FIX_MIN = 0; // The smallest uint192.
```

Remediation
```
// Add visibility e.g., public
bytes32 public constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));

// Used by P1 implementation for easier casting
uint256 public constant FIX_ONE_256 = 1e18;
uint8 public constant FIX_DECIMALS = 18;

// If a particular uint192 is represented by the uint192 n, then the uint192 represents the
// value n/FIX_SCALE.
uint64 public constant FIX_SCALE = 1e18;

// FIX_SCALE Squared:
uint128 public constant FIX_SCALE_SQ = 1e36;

// The largest integer that can be converted to uint192 .
// This is a bit bigger than 3.1e39
uint192 public constant FIX_MAX_INT = type(uint192).max / FIX_SCALE;

uint192 public constant FIX_ZERO = 0; // The uint192 representation of zero.
uint192 public constant FIX_ONE = FIX_SCALE; // The uint192 representation of one.
uint192 public constant FIX_MAX = type(uint192).max; // The largest uint192. (Not an integer!)
uint192 public constant FIX_MIN = 0; // The smallest uint192.
```

## [L-07]
```
SWC-100 Function Default Visibility 
```

URL
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L116
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
function divFix(uint256 x, uint192 y) pure returns (uint192) {
```

Remediation
```
// Add visibility e.g., internal
function divFix(uint256 x, uint192 y) internal pure returns (uint192) {
```
