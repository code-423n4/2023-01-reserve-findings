# QA Report

## Use `unchecked` for index incrementation in for loops
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11-L15
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23-L25

<br>

## Use Solidity's built in constants
For example, use `1 year` instead of `31536000` to improve readability and reduce margin for error
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L37
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L38
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33

<br>

## Replace deprecated OZ draft
As described in the source file, OpenZeppelin's `draft-EIP712Upgradeable.sol` is deprecated and simply points to `EIP712Upgradeable.sol`, and so the import statement ought to be updated.
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L7

<br>

## Single step role assignment introduces risk
If an erroneous address is passed as a parameter for `grantRole`, a malicious actor may gain privileges to the protocol (or if `renounceRole` is called straight after, the role may be irreversibly lost). Two-step transfers are considered to be safer.
- https://github.com/reserve-protocol/protocol/blob/master/contracts/mixins/Auth.sol#L89-L97