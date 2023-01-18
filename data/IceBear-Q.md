## [L-01] OWNER CAN RENOUNCE OWNERSHIP

### Description
Ownable used in this project contract implements renounceOwnership . This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

```onlyRole``` functions;

[Main.sol#L64](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L64)

```solidity=
    64: function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
```

### Recommended Mitigation Steps
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.
## [L-02] LOSS OF PRECISION DUE TO ROUNDING
[BackingManager#L232](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L232)
[BackingManager#L233](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L233)

```solidity=
BackingManager.sol
    232: toRSR[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rsrTotal;
    233: toRToken[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rTokenTotal;
```

## [L-03] REQUIRE MESSAGES UNCLEAR

### Description
The correct and clear error description explains to the user why the function reverts.

[Fixed.sol#L317](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L317)

```solidity=
317: require(x_ <= FIX_ONE);
```

## [N-01] NOT USING THE LATEST VERSION OF OPENZEPPELIN FROM DEPENDENCIES

### Description
The package.json configuration file says that the project is using 4.6.0 of OpenZeppelin which has a not last update version.

```solidity=
package.json:
    52:"@openzeppelin/contracts": "^4.7.3",
    53:"@openzeppelin/contracts-upgradeable": "^4.7.3",
```

### Recommended Mitigation Steps
Use patched versions.

## [N-02] SOLIDITY COMPILER OPTIMIZATIONS CAN BE PROBLEMATIC
[hardhat.config.ts#L25](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/hardhat.config.ts#L25)

```typescript=
25: const settings = useEnv('NO_OPT') ? {} : { optimizer: { enabled: true, runs: 200 } }
```

### Description
Protocol has enabled optional compiler optimizations in Solidity. There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them.

Therefore, it is unclear how well they are being tested and exercised. High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG.

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported. A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe. It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

### Recommended Mitigation Steps
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.

## [N-03] ADD PARAMETER TO EVENT-EMIT

### Description
Some event-emit description hasn’t parameter. Add to parameter for front-end website or client app , they can has that something has happened on the blockchain.

[IMain.sol#L154](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L154)

```solidity=
154: event MainInitialized();
```

## [N-04] INCLUDE RETURN PARAMETERS IN NATSPEC COMMENTS

### Description
https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

If Return parameters are declared, you must prefix them with ”/// @return”.

Some code analysis programs do analysis by reading NatSpec details, if they can’t see the “@return” tag, they do incomplete analysis.

[Main.sol#L57](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L57)
[Broker.sol#L85](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L85)


### Recommended Mitigation Steps
Include return parameters in NatSpec comments.

## [N-05] SOLIDITY COMPILER VERSIONS MISMATCH
### Description
The project is compiled with different versions of Solidity, which is not recommended because it can lead to undefined behaviors.

## [S-01] GENERATE PERFECT CODE HEADERS EVERY TIME
### Description
I recommend using header for Solidity code layout and readability.

https://github.com/transmissions11/headers
