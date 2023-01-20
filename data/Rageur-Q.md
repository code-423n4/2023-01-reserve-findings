## LOW-1 Outdated compiler version

### Affected file

* ERC20.sol (Line 3)
* EasyAuction.sol (Line 2)
* IAToken.sol (Line 2)
* IAaveIncentivesController.sol (Line 2)
* IStaticATokenLM.sol (Line 2)
* RayMathNoRounding.sol (Line 2)
* ReentrancyGuard.sol (Line 3)
* StaticATokenErrors.sol (Line 2)
* StaticATokenLM.sol (Line 2)
* WETH.sol (Line 16)

## LOW-2 State variable visibility not set

### Code

uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;

### Affected file

* Fixed.sol (Line 310)