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

[FILE : Broker.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol)

      import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
      import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
      import "@openzeppelin/contracts/proxy/Clones.sol";
      import "../interfaces/IBroker.sol";
      import "../interfaces/IMain.sol";
      import "../interfaces/ITrade.sol";
      import "../libraries/Fixed.sol";
      import "./mixins/Component.sol";
      import "../plugins/trading/GnosisTrade.sol";

[FILE : Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol)

      import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
      import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
      import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
      import "../interfaces/IDistributor.sol";
      import "../interfaces/IMain.sol";
      import "../libraries/Fixed.sol";
      import "./mixins/Component.sol";

[FILE : StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol)

    import "@openzeppelin/contracts-upgradeable/interfaces/IERC1271Upgradeable.sol";
    import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
    import "@openzeppelin/contracts-upgradeable/utils/cryptography/SignatureCheckerUpgradeable.sol";
    import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";
    import "@openzeppelin/contracts-upgradeable/utils/CountersUpgradeable.sol";
    import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

    import "../interfaces/IStRSR.sol";
    import "../interfaces/IMain.sol";
    import "../libraries/Fixed.sol";
    import "../libraries/Permit.sol";
    import "./mixins/Component.sol";

##

### [L-2]  AVOID HARDCODED VALUES

It is not good practice to hardcode values, but if you are dealing with addresses much less, these can change between implementations, networks or projects, so it is convenient to remove these values from the source code

[FILE : Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol)

        165 :   require(share.rsrDist <= 10000, "RSR distribution too high");

        166:    require(share.rTokenDist <= 10000, "RToken distribution too high");      

##

### [L-3]  LACK OF CHECKS THE INTEGER RANGES

The following methods lack checks on the following integer arguments, you can see the recommendations above.

Affected Source Code
issuanceRate_,scalingRedemptionRate_,redemptionRateFloor_ is not checked to be != 0 during the init, nevertheless it’s checked in setIssuanceRate, setScalingRedemptionRate, setRedemptionRateFloor


[FILE : RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol)

       function init(
        IMain main_,
        string calldata name_,
        string calldata symbol_,
        string calldata mandate_,
        uint192 issuanceRate_,  //@audit  LACK OF CHECKS THE INTEGER RANGES
        uint192 scalingRedemptionRate_,    //@audit  LACK OF CHECKS THE INTEGER RANGES
        uint256 redemptionRateFloor_      //@audit  LACK OF CHECKS THE INTEGER RANGES
        ) external initializer {
        require(bytes(name_).length > 0, "name empty");
        require(bytes(symbol_).length > 0, "symbol empty");
        require(bytes(mandate_).length > 0, "mandate empty");
        __Component_init(main_);
        __ERC20_init(name_, symbol_);
        __ERC20Permit_init(name_);

        assetRegistry = main_.assetRegistry();
        basketHandler = main_.basketHandler();
        backingManager = main_.backingManager();
        furnace = main_.furnace();

        mandate = mandate_;
        setIssuanceRate(issuanceRate_);
        setScalingRedemptionRate(scalingRedemptionRate_);
        setRedemptionRateFloor(redemptionRateFloor_);
        }

Recommended Mitigation :

Need to make sure integer values not zero and not exceeding with its minimum or maximum values .

##

### [L-4]  abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()
Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). "Unless there is a compelling reason, abi.encode should be preferred". If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead. If all arguments are strings and or bytes, bytes.concat() should be used instead

The abi.encodePacked function was meant to be simpler and more compact for encoding data. The abi.encode function can be useful when it comes to preventing collisions in hash functions.

(AAA, BBB) -> AAABBB         
(AA, ABBB) -> AAABBB

function collisionExample(string memory _string1, string memory _string2)
public pure returns (bytes32) {
     return keccak256(abi.encodePacked(_string1, _string2));
}

Let’s use a simple example (Example 1):

_string1 = AAA

_string2 = BBB

The result when we concatenate the strings and apply the hash function is:

0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358
Now let us change the values to the following (Example 2):

_string1 = AA

_string2 = ABBB

Here is the result:

0xf6568e65381c5fc6a447ddf0dcb848248282b798b2121d9944a87599b7921358

Now let us use the same function, but this time with abi.encode.

function collisionExample2(string memory _string1, string memory _string2)
public pure returns (bytes32) {
     return keccak256(abi.encode(_string1, _string2));
}
With the first example (Example 1) the result is:

0xd6da8b03238087e6936e7b951b58424ff2783accb08af423b2b9a71cb02bd18b
This is much different than using abi.encodePacked. Now the moment of truth (Example 2), will it result in the same hash?

0x54bc5894818c61a0ab427d51905bc936ae11a8111a8b373e53c8a34720957abb

[FILE : StaticATokenLM.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol)

      (https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L146-L154)
  
      (https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L174-L191)

      (https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L214-L231)

# NON-CRITICAL FINDINGS 

##

### [NC-1] USE A MORE RECENT VERSION OF SOLIDITY

> Instances (25)

 Use a solidity version of at least 0.8.12 to get string.concat() to be used instead of abi.encodePacked(<str>,<str>)

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

    2:  pragma solidity 0.8.9;

[FILE : BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol)

   2:  pragma solidity 0.8.9;

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

    2:  pragma solidity 0.8.9;

[FILE : Broker.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol)

       2 :  pragma solidity 0.8.9;

[FILE : RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol)

      2 : pragma solidity 0.8.9;

[FILE : StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol)

    2 :  pragma solidity 0.8.9;

## [NC- 2]  NATSPEC COMMENTS SHOULD BE ADDED IN CONTRACTS

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation.
In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

(https://docs.soliditylang.org/en/v0.8.15/natspec-format.html)

Recommendation
NatSpec comments should be added in Contracts

##

### [NC-3]  TYPOS

> Instances (13)


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

[FILE : Furnace.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol)

        // @audit permisionlessly= > meaning less word used 

     10 :    * @notice A helper to melt RTokens slowly and permisionlessly.

[FILE : RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol)

          // @audit adjaacent= > adjacent

      109 :  // issuances, and so any particular issuance is actually the _difference_ between two adjaacent

       // @audit IssueQueue ,striaght ,TotalIssue 

      112:       // The way to keep an IssueQueue striaght in your head is to think of each TotalIssue item as a

[FILE : StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol)

      // @audit zereod  => Zeroed

      25 :    *   If this happens, users balances are zereod out and StRSR is re-issued at a 1:1 exchange rate

      // @audit appeneded => Appended

      541 :   //   r'.queue is r.queue with a new entry appeneded for (totalDrafts' - totalDraft) drafts

// @audit Overriden => Overridden 

      570 :   /// Overriden in StRSRVotes to handle rebases

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

##

### [NC-5]   EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

> Instances (3)

[FILE : Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol)

       31:  address public constant FURNACE = address(1);
    
       32:  address public constant ST_RSR = address(2);

[FILE : StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol)

         bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );











