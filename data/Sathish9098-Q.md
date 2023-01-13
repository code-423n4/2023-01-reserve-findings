# LOW FINDINGS

##

### [L-1]  USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

      import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
      import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
      import "../interfaces/IAssetRegistry.sol";
      import "../interfaces/IMain.sol";
      import "./mixins/Component.sol";

[FILE : BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol)

      import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
      import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
      import "../interfaces/IAsset.sol";
       import "../interfaces/IBackingManager.sol";
       import "../interfaces/IMain.sol";
      import "../libraries/Array.sol";
      import "../libraries/Fixed.sol";
      import "./mixins/Trading.sol";
      import "./mixins/RecollateralizationLib.sol";

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

      import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
      import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
      import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
      import "../interfaces/IAssetRegistry.sol";
      import "../interfaces/IBasketHandler.sol";
      import "../interfaces/IMain.sol";
      import "../libraries/Array.sol";
       import "./mixins/Component.sol";


# NON-CRITICAL FINDINGS 

##

### [NC-1] USE A MORE RECENT VERSION OF SOLIDITY

 Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

    2:  pragma solidity 0.8.9;

[FILE : BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol)

   2:  pragma solidity 0.8.9;

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

    2:  pragma solidity 0.8.9;

## [NC- 2]  NATSPEC COMMENTS SHOULD BE ADDED IN CONTRACTS

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

Recommendation
NatSpec comments should be added in Contracts

##

### [NC-3]  TYPOS

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

 //@audit unregistering => meaning less word

  159 :  /// Register an asset, unregistering any previous asset with the same ERC20.

[FILE : BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol)

// @audit Mointain = > Maintain

    89 :  /// Mointain the overall backing policy; handout assets otherwise

// @audit undercollateralized= > under collateralized

   130 :   * If we run out of capital and are still undercollateralized, we compromise

// @audit collateralized= > meaning less word

   157 :   *   - Fully collateralized. All collateral meet balance requirements.

// @audit collateralized= > meaning less word

   210 :  // - We're fully collateralized

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

// @audit collateralized= > meaning less word

       272 :     /// ie, whether the protocol is currently fully collateralized

// @audit interepreted = > interpreted

      537 :      // As such, they're each interepreted as a map from target name -> target weight   

##

### [NC-4]  FOR FUNCTIONS, FOLLOW SOLIDITY STANDARD NAMING CONVENTIONS

Description

The above codes don’t follow Solidity’s standard naming convention,

internal and private functions : the mixedCase format starting with an underscore (_mixedCase starting with an underscore)

public and external functions : only mixedCase

constant variable : UPPERCASEWITH_UNDERSCORES (separated by uppercase and underscore)

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

   75 : function setFrom(Basket storage self, Basket storage other) internal {

    87 : function add(
        Basket storage self,
        IERC20 tok,
        uint192 weight
    ) internal {


  650 :   function requireValidCollArray(IERC20[] calldata erc20s) internal view {













NC-1	Return values of approve() not checked	12
NC-2	Constants should be defined rather than using magic numbers	3

L-1	Do not use deprecated library functions	10
L-2	Unsafe ERC20 operation(s)	1
L-3	Unspecific compiler version pragma	2

M-1	Centralization Risk for trusted owners	21