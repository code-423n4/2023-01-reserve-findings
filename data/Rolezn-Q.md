## Summary<a name="Summary">

### Low Risk Issues
| |Issue|Contexts|
|-|:-|:-:|
| [LOW&#x2011;1](#LOW&#x2011;1) | Draft Import Dependencies | 5 |
| [LOW&#x2011;2](#LOW&#x2011;2) | Use of `ecrecover` is susceptible to signature malleability | 3 |
| [LOW&#x2011;3](#LOW&#x2011;3) | Init functions are susceptible to front-running | 4 |
| [LOW&#x2011;4](#LOW&#x2011;4) | Loss of precision due to rounding | 3 |
| [LOW&#x2011;5](#LOW&#x2011;5) | Use `_safeMint` instead of `_mint` | 5 |
| [LOW&#x2011;6](#LOW&#x2011;6) | Missing parameter validation in `constructor` | 1 |
| [LOW&#x2011;7](#LOW&#x2011;7) | The `nonReentrant` modifier should occur before all other modifiers | 1 |
| [LOW&#x2011;8](#LOW&#x2011;8) | Contracts are not using their OZ Upgradeable counterparts | 62 |
| [LOW&#x2011;9](#LOW&#x2011;9) | Pragma Experimental ABIEncoderV2 is Deprecated | 4 |
| [LOW&#x2011;10](#LOW&#x2011;10) | Remove unused code | 1 |
| [LOW&#x2011;11](#LOW&#x2011;11) | `require()` should be used instead of `assert()` | 18 |
| [LOW&#x2011;12](#LOW&#x2011;12) | Missing Non Reentrancy Modifiers | 5 |

Total: 112 contexts over 12 issues

### Non-critical Issues
| |Issue|Contexts|
|-|:-|:-:|
| [NC&#x2011;1](#NC&#x2011;1) | Add a timelock to critical functions | 30 |
| [NC&#x2011;2](#NC&#x2011;2) | Compliance with Solidity Style rules in Constant expressions | 2 |
| [NC&#x2011;3](#NC&#x2011;3) | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 8 |
| [NC&#x2011;4](#NC&#x2011;4) | Critical Changes Should Use Two-step Procedure | 30 |
| [NC&#x2011;5](#NC&#x2011;5) | `block.timestamp` is already used when emitting events, no need to input timestamp | 1 |
| [NC&#x2011;6](#NC&#x2011;6) | Event emit should emit a parameter | 1 |
| [NC&#x2011;7](#NC&#x2011;7) | Event Is Missing Indexed Fields | 6 |
| [NC&#x2011;8](#NC&#x2011;8) | Function writing that does not comply with the Solidity Style Guide  | 75 |
| [NC&#x2011;9](#NC&#x2011;9) | Use `delete` to Clear Variables | 9 |
| [NC&#x2011;10](#NC&#x2011;10) | NatSpec return parameters should be included in contracts | 1 |
| [NC&#x2011;11](#NC&#x2011;11) | No need to initialize uints to zero | 2 |
| [NC&#x2011;12](#NC&#x2011;12) | Missing event for critical parameter change | 9 |
| [NC&#x2011;13](#NC&#x2011;13) | Implementation contract may not be initialized | 13 |
| [NC&#x2011;14](#NC&#x2011;14) | NatSpec comments should be increased in contracts | 1 |
| [NC&#x2011;15](#NC&#x2011;15) | Non-assembly Method Available | 1 |
| [NC&#x2011;16](#NC&#x2011;16) | Non-usage of specific imports | 196 |
| [NC&#x2011;17](#NC&#x2011;17) | Use a more recent version of Solidity | 75 |
| [NC&#x2011;18](#NC&#x2011;18) | Public Functions Not Called By The Contract Should Be Declared External Instead | 1 |
| [NC&#x2011;19](#NC&#x2011;19) | `require()` / `revert()` Statements Should Have Descriptive Reason Strings | 6 |
| [NC&#x2011;20](#NC&#x2011;20) | Large multiples of ten should use scientific notation | 2 |
| [NC&#x2011;21](#NC&#x2011;21) | Use `bytes.concat()` | 5 |
| [NC&#x2011;22](#NC&#x2011;22) | Use of Block.Timestamp | 18 |
| [NC&#x2011;23](#NC&#x2011;23) | Use Underscores for Number Literals  | 2 |

Total: 494 contexts over 23 issues

## Low Risk Issues

### <a href="#Summary">[LOW&#x2011;1]</a><a name="LOW&#x2011;1"> Draft Import Dependencies
Contracts are importing draft dependencies. These imported contracts are still a draft and are not considered ready for mainnet use and have not received adequate security auditing or are liable to change with future development.

#### <ins>Proof Of Concept</ins>


```solidity
6: import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L6

```solidity
6: import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L6

```solidity
7: import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L7

```solidity
7: import "@openzeppelin/contracts-upgradeable/token/ERC20/extensions/draft-IERC20PermitUpgradeable.sol";
9: import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L7

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L9





#### <ins>Recommended Mitigation Steps</ins>

Stop using draft imported contracts and use their release version if it exists.



### <a href="#Summary">[LOW&#x2011;2]</a><a name="LOW&#x2011;2"> Use of `ecrecover` is susceptible to signature malleability

The built-in EVM precompile `ecrecover` is susceptible to signature malleability, which could lead to replay attacks.
References:  https://swcregistry.io/docs/SWC-117,  https://swcregistry.io/docs/SWC-121, and  https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57.
While this is not immediately exploitable, this may become a vulnerability if used elsewhere.

#### <ins>Proof Of Concept</ins>


```solidity
155: function permit(
        address owner,
        address spender,
        uint256 value,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external override {
        require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
        
        require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
        uint256 currentValidNonce = _nonces[owner];
        bytes32 digest = keccak256(
            abi.encodePacked(
                "/x19/x01",
                getDomainSeparator(),
                keccak256(
                    abi.encode(PERMIT_TYPEHASH, owner, spender, value, currentValidNonce, deadline)
                )
            )
        );
        require(owner == ecrecover(digest, v, r, s), StaticATokenErrors.INVALID_SIGNATURE);
        _nonces[owner] = currentValidNonce.add(1);
        _approve(owner, spender, value);
    }
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L155

```solidity
193: function metaDeposit(
        address depositor,
        address recipient,
        uint256 value,
        uint16 referralCode,
        bool fromUnderlying,
        uint256 deadline,
        SignatureParams calldata sigParams
    ) external override nonReentrant returns (uint256) {
        require(depositor != address(0), StaticATokenErrors.INVALID_DEPOSITOR);
        
        require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
        uint256 currentValidNonce = _nonces[depositor];
        bytes32 digest = keccak256(
            abi.encodePacked(
                "/x19/x01",
                getDomainSeparator(),
                keccak256(
                    abi.encode(
                        METADEPOSIT_TYPEHASH,
                        depositor,
                        recipient,
                        value,
                        referralCode,
                        fromUnderlying,
                        currentValidNonce,
                        deadline
                    )
                )
            )
        );
        require(
            depositor == ecrecover(digest, sigParams.v, sigParams.r, sigParams.s),
            StaticATokenErrors.INVALID_SIGNATURE
        );
        _nonces[depositor] = currentValidNonce.add(1);
        return _deposit(depositor, recipient, value, referralCode, fromUnderlying);
    }
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L193

```solidity
234: function metaWithdraw(
        address owner,
        address recipient,
        uint256 staticAmount,
        uint256 dynamicAmount,
        bool toUnderlying,
        uint256 deadline,
        SignatureParams calldata sigParams
    ) external override nonReentrant returns (uint256, uint256) {
        require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
        
        require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
        uint256 currentValidNonce = _nonces[owner];
        bytes32 digest = keccak256(
            abi.encodePacked(
                "/x19/x01",
                getDomainSeparator(),
                keccak256(
                    abi.encode(
                        METAWITHDRAWAL_TYPEHASH,
                        owner,
                        recipient,
                        staticAmount,
                        dynamicAmount,
                        toUnderlying,
                        currentValidNonce,
                        deadline
                    )
                )
            )
        );

        require(
            owner == ecrecover(digest, sigParams.v, sigParams.r, sigParams.s),
            StaticATokenErrors.INVALID_SIGNATURE
        );
        _nonces[owner] = currentValidNonce.add(1);
        return _withdraw(owner, recipient, staticAmount, dynamicAmount, toUnderlying);
    }
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L234



#### Recommended Mitigation Steps
Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol#L138-L149







### <a href="#Summary">[LOW&#x2011;3]</a><a name="LOW&#x2011;3"> Init functions are susceptible to front-running

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: code-423n4/2021-09-sushimiso-findings#64)

#### <ins>Proof Of Concept</ins>

```solidity
function init(IMain main_, RevenueShare memory dist) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L37


```solidity
function init(IMain main_, IAsset[] calldata assets_) external initializer {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L34

```solidity
function init(IMain main_) external initializer {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L151

```solidity
function init(IMain main_, RevenueShare calldata dist) external initializer {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L41



#### <ins>Recommended Mitigation Steps</ins>

Ensure atomic calls to init functions along with deployment via robust deployment scripts or factory contracts. Emit explicit events for initializations of contracts. Enforce prevention of re-initializations via explicit setting/checking of boolean initialized variables in the main init function instead of downstream function checks.



### <a href="#Summary">[LOW&#x2011;4]</a><a name="LOW&#x2011;4"> Loss of precision due to rounding

Add scalars so roundings are negligible

#### <ins>Proof Of Concept</ins>


```solidity
386: uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;;
401: uint256 draftRSRToTake = (draftRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;
417: seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L386

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L401

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L417




### <a href="#Summary">[LOW&#x2011;5]</a><a name="LOW&#x2011;5"> Use `_safeMint` instead of `_mint`

According to openzepplin's ERC721, the use of `_mint` is discouraged, use _safeMint whenever possible.
https://docs.openzeppelin.com/contracts/3.x/api/token/erc721#ERC721-_mint-address-uint256-

#### <ins>Proof Of Concept</ins>


```solidity
268: _mint(recipient, amtRToken);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L268

```solidity
559: _mint(recipient, amtRToken);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L559

```solidity
791: _mint(account, amtRToken);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L791

```solidity
230: _mint(account, stakeAmount);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L230

```solidity
305: _mint(recipient, amountToMint);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L305



#### <ins>Recommended Mitigation Steps</ins>

Use `_safeMint` whenever possible instead of `_mint`



### <a href="#Summary">[LOW&#x2011;6]</a><a name="LOW&#x2011;6"> Missing parameter validation in `constructor`

Some parameters of constructors are not checked for invalid values.

#### <ins>Proof Of Concept</ins>

```solidity
80: address aToken

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L80



#### <ins>Recommended Mitigation Steps</ins>

Validate the parameters.



### <a href="#Summary">[LOW&#x2011;7]</a><a name="LOW&#x2011;7"> The `nonReentrant` modifier should occur before all other modifiers

Currently the `nonReentrant` modifier is not the first to occur, it should occur before all other modifiers.

#### <ins>Proof Of Concept</ins>


```solidity
66: function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L66



#### <ins>Recommended Mitigation Steps</ins>

Re-sort modifiers so that the `nonReentrant` modifier occurs first.



### <a href="#Summary">[LOW&#x2011;8]</a><a name="LOW&#x2011;8"> Contracts are not using their OZ Upgradeable counterparts

The non-upgradeable standard version of OpenZeppelin’s library are inherited / used by the contracts.
It would be safer to use the upgradeable versions of the library contracts to avoid unexpected behaviour.

#### <ins>Proof of Concept</ins>

```solidity
5: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L5

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAssetRegistry.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol#L4

```solidity
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L5

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRewardable.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrade.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L4

```solidity
6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L6

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
5: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L4-L5

```solidity
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L5

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L4-L6

```solidity
5: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
6: import "@openzeppelin/contracts/proxy/Clones.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L5-L6

```solidity
4: import "@openzeppelin/contracts/proxy/Clones.sol";
5: import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
6: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L4-L6

```solidity
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6: import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L5-L6

```solidity
7: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L7

```solidity
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L5

```solidity
9: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L9

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/utils/SafeERC20.sol";
5: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
6: import "@openzeppelin/contracts/utils/Address.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L4-L6

```solidity
4: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L4

```solidity
6: import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
7: import "@openzeppelin/contracts/utils/Multicall.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L6-L7

```solidity
5: import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/Context.sol";
6: import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";
7: import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/SafeMath.sol";
8: import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/Address.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L5-L8

```solidity
4: import "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol#L4

```solidity
5: import { IERC20 } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L5

```solidity
6: import { IERC20 } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20.sol";
8: import { IERC20Detailed } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/IERC20Detailed.sol";
18: import { SafeERC20 } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/SafeERC20.sol";
21: import { SafeMath } from "@aave/protocol-v2/contracts/dependencies/openzeppelin/contracts/SafeMath.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L6-L21

```solidity
5: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L5

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ICToken.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L4

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L4

```solidity
4: import "@openzeppelin/contracts/governance/Governor.sol";
5: import "@openzeppelin/contracts/governance/extensions/GovernorCountingSimple.sol";
6: import "@openzeppelin/contracts/governance/extensions/GovernorSettings.sol";
7: import "@openzeppelin/contracts/governance/extensions/GovernorTimelockControl.sol";
8: import "@openzeppelin/contracts/governance/extensions/GovernorVotes.sol";
9: import "@openzeppelin/contracts/governance/extensions/GovernorVotesQuorumFraction.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L4-L9

```solidity
4: import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
5: import "@openzeppelin/contracts/utils/math/Math.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L4-L5



#### <ins>Recommended Mitigation Steps</ins>

Where applicable, use the contracts from `@openzeppelin/contracts-upgradeable` instead of `@openzeppelin/contracts`.



### <a href="#Summary">[LOW&#x2011;9]</a><a name="LOW&#x2011;9"> Pragma Experimental ABIEncoderV2 is Deprecated

#### <ins>Proof Of Concept</ins>


```solidity
pragma experimental ABIEncoderV2
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L3

```solidity
pragma experimental ABIEncoderV2
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L3

```solidity
pragma experimental ABIEncoderV2
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol#L3

```solidity
pragma experimental ABIEncoderV2
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L3



#### <ins>Recommended Mitigation Steps</ins>

Use pragma abicoder v2 instead.





### <a href="#Summary">[LOW&#x2011;10]</a><a name="LOW&#x2011;10"> Remove unused code
This code is not used in the main project contract files, remove it or add event-emit
Code that is not in use, suggests that they should not be present and could potentially contain insecure functionalities.

#### <ins>Proof Of Concept</ins>


```solidity
function __ERC20Permit_init_unchained
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L67






### <a href="#Summary">[LOW&#x2011;11]</a><a name="LOW&#x2011;11"> `require()` Should Be Used Instead Of `assert()`

Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction's available gas rather than returning it, as `require()`/`revert()` do. `assert()` should be avoided even past solidity version 0.8.0 as its <a href="https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require">documentation</a> states that "The assert function creates an error of type Panic(uint256). ... Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix".

#### <ins>Proof Of Concept</ins>


```solidity
249: assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249

```solidity
556: assert(targetIndex < targetsLength);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L556

```solidity
696: assert(totalStakes + amount < type(uint224).max);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L696

```solidity
110: assert(doTrade);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L110

```solidity
78: assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L78

```solidity
102: assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L102

```solidity
44: assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44

```solidity
168: assert(errorCode == 0x11 || errorCode == 0x12);
170: assert(keccak256(reason) == UIntOutofBoundsHash);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L168

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L170



```solidity
114: assert(address(trades[sell]) == address(0));

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L114

```solidity
345: assert(amt == amountToWithdraw);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L345

```solidity
98: assert(low == 0);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L98

```solidity
112: assert(low <= high);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L112

```solidity
147: assert(lotLow <= lotHigh);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L147

```solidity
137: assert(low == 0);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L137

```solidity
74: assert(low <= high);

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L74

```solidity
98: assert(origin_ != address(0));

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L98

```solidity
182: assert(isAuctionCleared());

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L182


### <a href="#Summary">[LOW&#x2011;12]</a><a name="LOW&#x2011;12"> Missing Non Reentrancy Modifiers


The following functions are missing reentrancy modifier although some other public/external functions does use reentrancy modifer. Even though I did not find a way to exploit it, it seems like those functions should have the nonReentrant modifier as the other functions have it as well.

#### <ins>Proof Of Concept</ins>

```solidity
function claimRewards() external notPausedOrFrozen {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1\mixins\Trading.sol#L82

```solidity
function claimRewardsSingle(IERC20 erc20) external notPausedOrFrozen {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1\mixins\Trading.sol#L90

```solidity
function setMaxTradeSlippage(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1\mixins\Trading.sol#L128

```solidity
function setMinTradeVolume(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1\mixins\Trading.sol#L135

```solidity
function permit(
        address owner,
        address spender,
        uint256 value,
        uint256 deadline,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external override {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins\aave\StaticATokenLM.sol#L133



#### <ins>Recommended Mitigation Steps</ins>
Add reentrancy modifiers




## Non Critical Issues

### <a href="#Summary">[NC&#x2011;1]</a><a name="NC&#x2011;1"> Add a timelock to critical functions

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user). Consider adding a timelock to the following functions:

#### <ins>Proof Of Concept</ins>


```solidity
40: function setDistribution(address dest, RevenueShare memory share) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L40

```solidity
27: function setPeriod(uint48) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L27

```solidity
38: function setRatio(uint192) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L38

```solidity
162: function setBasketsNeeded(uint192 basketsNeeded) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L162

```solidity
49: function settleTrade(IERC20 sell) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L49

```solidity
180: function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L180

```solidity
187: function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L187

```solidity
256: function setTradingDelay(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256

```solidity
263: function setBackingBuffer(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L263

```solidity
75: function setFrom(Basket storage self, Basket storage other) internal {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L75

```solidity
209: function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L209

```solidity
252: function setBackupConfig(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L252

```solidity
133: function setAuctionLength(uint48 newAuctionLength) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L133

```solidity
143: function setDisabled(bool disabled_) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L143

```solidity
61: function setDistribution(address dest, RevenueShare memory share) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L61

```solidity
88: function setPeriod(uint48 period_) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L88

```solidity
96: function setRatio(uint192 ratio_) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L96

```solidity
579: function setBasketsNeeded(uint192 basketsNeeded_) external {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L579

```solidity
589: function setIssuanceRate(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L589

```solidity
602: function setScalingRedemptionRate(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L602

```solidity
615: function setRedemptionRateFloor(uint256 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L615

```solidity
803: function setName(string calldata name_) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L803

```solidity
807: function setSymbol(string calldata symbol_) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L807

```solidity
812: function setUnstakingDelay(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812

```solidity
820: function setRewardPeriod(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L820

```solidity
828: function setRewardRatio(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L828

```solidity
66: function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L66

```solidity
128: function setMaxTradeSlippage(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L128

```solidity
135: function setMinTradeVolume(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L135

```solidity
169: function settle()
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L169









### <a href="#Summary">[NC&#x2011;2]</a><a name="NC&#x2011;2"> Compliance with Solidity Style rules in Constant expressions

#### <ins>Proof Of Concept</ins>


```solidity
33: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L33


```solidity
45: uint8 public constant decimals = 18;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L45


#### <ins>Recommended Mitigation Steps</ins>

Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.




### <a href="#Summary">[NC&#x2011;3]</a><a name="NC&#x2011;3"> Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

#### <ins>Proof Of Concept</ins>

```solidity
33: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L33

```solidity
126: bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L126

```solidity
27: bytes32 private constant _DELEGATE_TYPEHASH =
        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L27

```solidity
41: bytes32 internal constant EIP712_DOMAIN =
        keccak256(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L41

```solidity
45: bytes32 public constant PERMIT_TYPEHASH =
        keccak256(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L45

```solidity
49: bytes32 public constant METADEPOSIT_TYPEHASH =
        keccak256(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L49

```solidity
53: bytes32 public constant METAWITHDRAWAL_TYPEHASH =
        keccak256(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L53

```solidity
39: bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L39





### <a href="#Summary">[NC&#x2011;4]</a><a name="NC&#x2011;4"> Critical Changes Should Use Two-step Procedure

The critical procedures should be two step process.

See similar findings in previous Code4rena contests for reference:
https://code4rena.com/reports/2022-06-illuminate/#2-critical-changes-should-use-two-step-procedure

#### <ins>Proof Of Concept</ins>

```solidity
40: function setDistribution(address dest, RevenueShare memory share) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L40

```solidity
27: function setPeriod(uint48) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L27

```solidity
38: function setRatio(uint192) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L38

```solidity
162: function setBasketsNeeded(uint192 basketsNeeded) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L162

```solidity
49: function settleTrade(IERC20 sell) external;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L49

```solidity
180: function setShortFreeze(uint48 shortFreeze_) public onlyRole(OWNER) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L180

```solidity
187: function setLongFreeze(uint48 longFreeze_) public onlyRole(OWNER) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L187

```solidity
256: function setTradingDelay(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L256

```solidity
263: function setBackingBuffer(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L263

```solidity
75: function setFrom(Basket storage self, Basket storage other) internal {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L75

```solidity
209: function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L209

```solidity
252: function setBackupConfig(
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L252

```solidity
133: function setAuctionLength(uint48 newAuctionLength) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L133

```solidity
143: function setDisabled(bool disabled_) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L143

```solidity
61: function setDistribution(address dest, RevenueShare memory share) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L61

```solidity
88: function setPeriod(uint48 period_) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L88

```solidity
96: function setRatio(uint192 ratio_) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L96

```solidity
579: function setBasketsNeeded(uint192 basketsNeeded_) external {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L579

```solidity
589: function setIssuanceRate(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L589

```solidity
602: function setScalingRedemptionRate(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L602

```solidity
615: function setRedemptionRateFloor(uint256 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L615

```solidity
803: function setName(string calldata name_) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L803

```solidity
807: function setSymbol(string calldata symbol_) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L807

```solidity
812: function setUnstakingDelay(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L812

```solidity
820: function setRewardPeriod(uint48 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L820

```solidity
828: function setRewardRatio(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L828

```solidity
66: function settleTrade(IERC20 sell) external notPausedOrFrozen nonReentrant {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L66

```solidity
128: function setMaxTradeSlippage(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L128

```solidity
135: function setMinTradeVolume(uint192 val) public governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L135

```solidity
169: function settle()
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L169



#### <ins>Recommended Mitigation Steps</ins>

Lack of two-step procedure for critical operations leaves them error-prone. Consider adding two step procedure on the critical functions.



### <a href="#Summary">[NC&#x2011;5]</a><a name="NC&#x2011;5"> block.timestamp is already used when emitting events, no need to input timestamp

#### <ins>Proof Of Concept</ins>

```solidity
158: emit UnfreezeAtSet(unfreezeAt, uint48(block.timestamp));

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L158






### <a href="#Summary">[NC&#x2011;6]</a><a name="NC&#x2011;6"> Event emit should emit a parameter

Some emitted events do not have any emitted parameters. It is recommended to add some parameter such as state changes or value changes when events are emitted

#### <ins>Proof Of Concept</ins>


```solidity
38: emit MainInitialized()

```




### <a href="#Summary">[NC&#x2011;7]</a><a name="NC&#x2011;7"> Event Is Missing Indexed Fields

Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). 

Each event should use three indexed fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

#### <ins>Proof Of Concept</ins>


```solidity
event PrimeBasketSet(IERC20[] erc20s, uint192[] targetAmts, bytes32[] targetNames);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L21

```solidity
event DeploymentUnregistered(string version, IDeployer deployer);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L7

```solidity
event DeploymentRegistered(string version, IDeployer deployer);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L8

```solidity
event LatestChanged(string version, IDeployer deployer);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L9

```solidity
event DistributionSet(address dest, uint16 rTokenDist, uint16 rsrDist);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L28

```solidity
event BasketsNeededChanged(uint192 oldBasketsNeeded, uint192 newBasketsNeeded);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L83






### <a href="#Summary">[NC&#x2011;8]</a><a name="NC&#x2011;8"> Function writing that does not comply with the Solidity Style Guide

Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

#### <ins>Proof Of Concept</ins>

All in-scope contracts




### <a href="#Summary">[NC&#x2011;9]</a><a name="NC&#x2011;9"> Use `delete` to Clear Variables

`delete a` assigns the initial value for the type to `a`. i.e. for integers it is equivalent to `a = 0`, but it can also be used on arrays, where it assigns a dynamic array of length zero or a static array of the same length with all elements reset. For structs, it assigns a struct with all members reset. Similarly, it can also be used to set an address to zero address. It has no effect on whole mappings though (as the keys of mappings may be arbitrary and are generally unknown). However, individual keys and what they map to can be deleted: If `a` is a mapping, then `delete a[x]` will delete the value stored at `x`.

The `delete` key better conveys the intention and is also more idiomatic. Consider replacing assignments of zero with `delete` statements.

#### <ins>Proof Of Concept</ins>

```solidity
693: queue.left = 0;
694: queue.right = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L693-L694

```solidity
784: queue.left = 0;
785: queue.right = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L784-L785

```solidity
575: stakeRSR = 0;
576: totalStakes = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L575-L576

```solidity
587: draftRSR = 0;
588: totalDrafts = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L587-L588

```solidity
462: _unclaimedRewards[onBehalfOf] = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L462





### <a href="#Summary">[NC&#x2011;10]</a><a name="NC&#x2011;10"> NatSpec return parameters should be included in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability.

https://docs.soliditylang.org/en/v0.8.15/natspec-format.html
#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

Include return parameters in NatSpec comments

Recommendation Code Style: (from Uniswap3)

```solidity
    /// @notice Adds liquidity for the given recipient/tickLower/tickUpper position
    /// @dev The caller of this method receives a callback in the form of IUniswapV3MintCallback#uniswapV3MintCallback
    /// in which they must pay any token0 or token1 owed for the liquidity. The amount of token0/token1 due depends
    /// on tickLower, tickUpper, the amount of liquidity, and the current price.
    /// @param recipient The address for which the liquidity will be created
    /// @param tickLower The lower tick of the position in which to add liquidity
    /// @param tickUpper The upper tick of the position in which to add liquidity
    /// @param amount The amount of liquidity to mint
    /// @param data Any data that should be passed through to the callback
    /// @return amount0 The amount of token0 that was paid to mint the given amount of liquidity. Matches the value in the callback
    /// @return amount1 The amount of token1 that was paid to mint the given amount of liquidity. Matches the value in the callback
    function mint(
        address recipient,
        int24 tickLower,
        int24 tickUpper,
        uint128 amount,
        bytes calldata data
    ) external returns (uint256 amount0, uint256 amount1);
```



### <a href="#Summary">[NC&#x2011;11]</a><a name="NC&#x2011;11"> No need to initialize uints to zero

There is no need to initialize `uint` variables to zero as their default value is `0`

#### <ins>Proof Of Concept</ins>

```solidity
606: uint256 assigned = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L606

```solidity
102: uint256 low = 0;

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L102






### <a href="#Summary">[NC&#x2011;12]</a><a name="NC&#x2011;12"> Missing event for critical parameter change

When changing state variables events are not emitted. Emitting events allows monitoring activities with off-chain monitoring tools.

#### <ins>Proof Of Concept</ins>


```solidity
75: function setFrom(Basket storage self, Basket storage other) internal {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L75

```solidity
803: function setName(string calldata name_) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L803

```solidity
807: function setSymbol(string calldata symbol_) external governance {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L807

```solidity
169: function settle()
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L169





### <a href="#Summary">[NC&#x2011;13]</a><a name="NC&#x2011;13"> Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

#### <ins>Proof Of Concept</ins>


```solidity
42: constructor(
        IERC20Metadata rsr_,
        IGnosis gnosis_,
        IAsset rsrAsset_,
        Implementations memory implementations_
    )
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L42


```solidity
40: constructor(CollateralConfig memory config) FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L40

```solidity
25: constructor(CollateralConfig memory config, IComptroller comptroller_) FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L25

```solidity
23: constructor(
        CollateralConfig memory config,
        AggregatorV3Interface targetUnitChainlinkFeed_,
        IComptroller comptroller_
    ) CTokenFiatCollateral(config, comptroller_)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L23

```solidity
24: constructor(
        CollateralConfig memory config,
        uint8 referenceERC20Decimals_,
        IComptroller comptroller_
    ) SelfReferentialCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L24

```solidity
21: constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
        FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L21

```solidity
63: constructor(CollateralConfig memory config)
        Asset(
            config.priceTimeout,
            config.chainlinkFeed,
            config.oracleError,
            config.erc20,
            config.maxTradeVolume,
            config.oracleTimeout
        )
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L63

```solidity
21: constructor(CollateralConfig memory config, AggregatorV3Interface uoaPerTargetFeed_)
        FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L21

```solidity
27: constructor(IRToken erc20_, uint192 maxTradeVolume_)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L27

```solidity
18: constructor(CollateralConfig memory config) FiatCollateral(config)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L18

```solidity
31: constructor(
        IStRSRVotes token_,
        TimelockController timelock_,
        uint256 votingDelay_, // in blocks
        uint256 votingPeriod_, // in blocks
        uint256 proposalThresholdAsMicroPercent_, // e.g. 1e4 for 0.01%
        uint256 quorumPercent // e.g 4 for 4%
    )
        Governor("Governor Alexios")
        GovernorSettings(votingDelay_, votingPeriod_, proposalThresholdAsMicroPercent_)
        GovernorVotes(IVotes(address(token_)))
        GovernorVotesQuorumFraction(quorumPercent)
        GovernorTimelockControl(timelock_)
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L31





### <a href="#Summary">[NC&#x2011;14]</a><a name="NC&#x2011;14"> NatSpec comments should be increased in contracts

It is recommended that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI). It is clearly stated in the Solidity official documentation. In complex projects such as Defi, the interpretation of all functions and their arguments and returns is important for code readability and auditability. https://docs.soliditylang.org/en/v0.8.15/natspec-format.html

#### <ins>Proof Of Concept</ins>


All in-scope contracts


#### <ins>Recommended Mitigation Steps</ins>

NatSpec comments should be increased in contracts



### <a href="#Summary">[NC&#x2011;15]</a><a name="NC&#x2011;15"> Non-assembly Method Available

#### <ins>Proof Of Concept</ins>


```solidity
265: chainId := chainid()
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L265






### <a href="#Summary">[NC&#x2011;16]</a><a name="NC&#x2011;16"> Non-usage of specific imports

The current form of relative path import is not recommended for use because it can unpredictably pollute the namespace.
Instead, the Solidity docs recommend specifying imported symbols explicitly.
https://docs.soliditylang.org/en/v0.8.15/layout-of-source-files.html#importing-other-source-files

A good example:
```solidity
import {OwnableUpgradeable} from "openzeppelin-contracts-upgradeable/contracts/access/OwnableUpgradeable.sol";
import {SafeTransferLib} from "solmate/utils/SafeTransferLib.sol";
import {SafeCastLib} from "solmate/utils/SafeCastLib.sol";
import {ERC20} from "solmate/tokens/ERC20.sol";
import {IProducer} from "src/interfaces/IProducer.sol";
import {GlobalState, UserState} from "src/Common.sol";
```

#### <ins>Proof Of Concept</ins>


```solidity
6: import "../libraries/Fixed.sol";
7: import "./IMain.sol";
8: import "./IRewardable.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L6-L8

```solidity
5: import "./IAsset.sol";
6: import "./IComponent.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAssetRegistry.sol#L5-L6

```solidity
5: import "./IComponent.sol";
6: import "./ITrading.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L5-L6

```solidity
5: import "../libraries/Fixed.sol";
6: import "./IAsset.sol";
7: import "./IComponent.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L5-L7

```solidity
4: import "./IAsset.sol";
5: import "./IComponent.sol";
6: import "./IGnosis.sol";
7: import "./ITrade.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBroker.sol#L4-L7

```solidity
4: import "./IMain.sol";
5: import "./IVersioned.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IComponent.sol#L4-L5

```solidity
5: import "./IAsset.sol";
6: import "./IDistributor.sol";
7: import "./IGnosis.sol";
8: import "./IMain.sol";
9: import "./IRToken.sol";
10: import "./IStRSR.sol";
11: import "./ITrade.sol";
12: import "./IVersioned.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L5-L12

```solidity
4: import "./IDeployer.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L4

```solidity
5: import "./IComponent.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L5

```solidity
4: import "../p1/RToken.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeAct.sol#L4

```solidity
4: import "../p1/RToken.sol";
5: import "./IRToken.sol";
6: import "./IStRSR.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeRead.sol#L4-L6

```solidity
4: import "./IRToken.sol";
5: import "./IStRSR.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeTest.sol#L4-L5

```solidity
4: import "./IDeployer.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeWrite.sol#L4

```solidity
4: import "../libraries/Fixed.sol";
5: import "./IComponent.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L4-L5

```solidity
6: import "./IAssetRegistry.sol";
7: import "./IBasketHandler.sol";
8: import "./IBackingManager.sol";
9: import "./IBroker.sol";
10: import "./IGnosis.sol";
11: import "./IFurnace.sol";
12: import "./IDistributor.sol";
13: import "./IRToken.sol";
14: import "./IRevenueTrader.sol";
15: import "./IStRSR.sol";
16: import "./ITrading.sol";
17: import "./IVersioned.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L6-L17

```solidity
4: import "./IComponent.sol";
5: import "./ITrading.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRevenueTrader.sol#L4-L5

```solidity
5: import "./IComponent.sol";
6: import "./IMain.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRewardable.sol#L5-L6

```solidity
7: import "../libraries/Fixed.sol";
8: import "./IAsset.sol";
9: import "./IComponent.sol";
10: import "./IMain.sol";
11: import "./IRewardable.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L7-L11

```solidity
7: import "../libraries/Fixed.sol";
8: import "./IComponent.sol";
9: import "./IMain.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L7-L9

```solidity
5: import "../libraries/Fixed.sol";
6: import "./IAsset.sol";
7: import "./IComponent.sol";
8: import "./ITrade.sol";
9: import "./IRewardable.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L5-L9

```solidity
4: import "../interfaces/IRToken.sol";
5: import "./Fixed.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L4-L5

```solidity
4: import "../Array.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol#L4

```solidity
4: import "../Fixed.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L4

```solidity
4: import "../String.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/StringCallerMock.sol#L4

```solidity
5: import "../interfaces/IMain.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L5

```solidity
7: import "../interfaces/IMain.sol";
8: import "./Auth.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L7-L8

```solidity
4: import "../interfaces/IVersioned.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Versioned.sol#L4

```solidity
6: import "../interfaces/IAssetRegistry.sol";
7: import "../interfaces/IMain.sol";
8: import "./mixins/Component.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L6-L8

```solidity
6: import "../interfaces/IAsset.sol";
7: import "../interfaces/IBackingManager.sol";
8: import "../interfaces/IMain.sol";
9: import "../libraries/Array.sol";
10: import "../libraries/Fixed.sol";
11: import "./mixins/Trading.sol";
12: import "./mixins/RecollateralizationLib.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L6-L12

```solidity
8: import "../interfaces/IAssetRegistry.sol";
9: import "../interfaces/IBasketHandler.sol";
10: import "../interfaces/IMain.sol";
11: import "../libraries/Array.sol";
12: import "../libraries/Fixed.sol";
13: import "./mixins/Component.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L8-L13

```solidity
7: import "../interfaces/IBroker.sol";
8: import "../interfaces/IMain.sol";
9: import "../interfaces/ITrade.sol";
10: import "../libraries/Fixed.sol";
11: import "./mixins/Component.sol";
12: import "../plugins/trading/GnosisTrade.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L7-L12

```solidity
7: import "../interfaces/IAsset.sol";
8: import "../interfaces/IAssetRegistry.sol";
9: import "../interfaces/IBackingManager.sol";
10: import "../interfaces/IBasketHandler.sol";
11: import "../interfaces/IBroker.sol";
12: import "../interfaces/IDeployer.sol";
13: import "../interfaces/IDistributor.sol";
14: import "../interfaces/IFurnace.sol";
15: import "../interfaces/IRevenueTrader.sol";
16: import "../interfaces/IRToken.sol";
17: import "../interfaces/IStRSR.sol";
18: import "../mixins/Versioned.sol";
19: import "../plugins/assets/Asset.sol";
20: import "../plugins/assets/RTokenAsset.sol";
21: import "./Main.sol";
22: import "../libraries/String.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L7-L22

```solidity
7: import "../interfaces/IDistributor.sol";
8: import "../interfaces/IMain.sol";
9: import "../libraries/Fixed.sol";
10: import "./mixins/Component.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L7-L10

```solidity
4: import "../libraries/Fixed.sol";
5: import "../interfaces/IFurnace.sol";
6: import "./mixins/Component.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L4-L6

```solidity
8: import "../interfaces/IMain.sol";
9: import "../mixins/ComponentRegistry.sol";
10: import "../mixins/Auth.sol";
11: import "../mixins/Versioned.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L8-L11

```solidity
6: import "../interfaces/IMain.sol";
7: import "../interfaces/IAssetRegistry.sol";
8: import "./mixins/Trading.sol";
9: import "./mixins/TradeLib.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L6-L9

```solidity
6: import "../interfaces/IMain.sol";
7: import "../interfaces/IRToken.sol";
8: import "../libraries/Fixed.sol";
9: import "../libraries/RedemptionBattery.sol";
10: import "./mixins/Component.sol";
11: import "./mixins/RewardableLib.sol";
12: import "../vendor/ERC20PermitUpgradeable.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L6-L12

```solidity
11: import "../interfaces/IStRSR.sol";
12: import "../interfaces/IMain.sol";
13: import "../libraries/Fixed.sol";
14: import "../libraries/Permit.sol";
15: import "./mixins/Component.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L11-L15

```solidity
6: import "../interfaces/IStRSRVotes.sol";
7: import "./StRSR.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L6-L7

```solidity
7: import "../../interfaces/IComponent.sol";
8: import "../../interfaces/IMain.sol";
9: import "../../mixins/Versioned.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L7-L9

```solidity
5: import "../../interfaces/IAsset.sol";
6: import "../../interfaces/IAssetRegistry.sol";
7: import "../../interfaces/IBackingManager.sol";
8: import "../../libraries/Fixed.sol";
9: import "./TradeLib.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L5-L9

```solidity
7: import "../../interfaces/IAssetRegistry.sol";
8: import "../../interfaces/IBackingManager.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L7-L8

```solidity
5: import "../../interfaces/IAsset.sol";
6: import "../../interfaces/IAssetRegistry.sol";
7: import "../../interfaces/ITrading.sol";
8: import "../../libraries/Fixed.sol";
9: import "./RecollateralizationLib.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L5-L9

```solidity
8: import "../../interfaces/ITrade.sol";
9: import "../../interfaces/ITrading.sol";
10: import "../../libraries/Fixed.sol";
11: import "./Component.sol";
12: import "./RewardableLib.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L8-L12

```solidity
6: import "./IAaveIncentivesController.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol#L6

```solidity
6: import "../../interfaces/IAsset.sol";
7: import "./OracleLib.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L6-L7

```solidity
5: import "../../libraries/Fixed.sol";
6: import "./FiatCollateral.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L5-L6

```solidity
5: import "./FiatCollateral.sol";
6: import "./ICToken.sol";
7: import "../../libraries/Fixed.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L5-L7

```solidity
4: import "../../libraries/Fixed.sol";
5: import "./CTokenFiatCollateral.sol";
6: import "./ICToken.sol";
7: import "./OracleLib.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L4-L7

```solidity
4: import "./ICToken.sol";
5: import "./SelfReferentialCollateral.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L4-L5

```solidity
5: import "../../libraries/Fixed.sol";
6: import "./FiatCollateral.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L5-L6

```solidity
5: import "../../interfaces/IAsset.sol";
6: import "../../libraries/Fixed.sol";
7: import "./FiatCollateral.sol";
8: import "./Asset.sol";
9: import "./OracleLib.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L5-L9

```solidity
5: import "../../libraries/Fixed.sol";
6: import "./FiatCollateral.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L5-L6

```solidity
5: import "../../libraries/Fixed.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L5

```solidity
4: import "../../p1/mixins/RecollateralizationLib.sol";
5: import "../../interfaces/IMain.sol";
6: import "../../interfaces/IRToken.sol";
7: import "./Asset.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L4-L7

```solidity
5: import "../../libraries/Fixed.sol";
6: import "./FiatCollateral.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L5-L6

```solidity
10: import "../../interfaces/IStRSRVotes.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L10

```solidity
7: import "../../libraries/Fixed.sol";
8: import "../../interfaces/IBroker.sol";
9: import "../../interfaces/IGnosis.sol";
10: import "../../interfaces/ITrade.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L7-L10

```solidity
12: import "../libraries/Permit.sol";

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L12




#### <ins>Recommended Mitigation Steps</ins>

Use specific imports syntax per solidity docs recommendation.



### <a href="#Summary">[NC&#x2011;17]</a><a name="NC&#x2011;17"> Use a more recent version of Solidity

<a href="https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/">0.8.4</a>:
bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)

<a href="https://blog.soliditylang.org/2022/02/16/solidity-0.8.12-release-announcement/">0.8.12</a>: 
string.concat() instead of abi.encodePacked(<str>,<str>)

<a href="https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/">0.8.13</a>: 
Ability to use using for with a list of free functions

<a href="https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/">0.8.14</a>:

ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against calldatasize() in all cases.
Override Checker: Allow changing data location for parameters only when overriding external functions.

<a href="https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/">0.8.15</a>:

Code Generation: Avoid writing dirty bytes to storage when copying bytes arrays.
Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

<a href="https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/">0.8.16</a>:

Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

<a href="https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/">0.8.17</a>:

Yul Optimizer: Prevent the incorrect removal of storage writes before calls to Yul functions that conditionally terminate the external EVM call.

#### <ins>Proof Of Concept</ins>


```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAssetRegistry.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBackingManager.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBasketHandler.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IBroker.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IComponent.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployerRegistry.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDistributor.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeAct.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeRead.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeTest.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFacadeWrite.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IFurnace.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IMain.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRevenueTrader.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRewardable.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IRToken.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSR.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IStRSRVotes.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrade.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/ITrading.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IVersioned.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L3

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/ArrayCallerMock.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/FixedCallerMock.sol#L2

```solidity
pragma solidity ^0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/StringCallerMock.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Versioned.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L2

```solidity
pragma solidity ^0.6.0;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L3

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L2

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAToken.sol#L2

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L2

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/RayMathNoRounding.sol#L2

```solidity
pragma solidity >=0.6.0 <0.8.0;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L3

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L2

```solidity
pragma solidity 0.6.12;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ICToken.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L2

```solidity
pragma solidity 0.8.9;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L6



#### <ins>Recommended Mitigation Steps</ins>

Consider updating to a more recent solidity version.



### <a href="#Summary">[NC&#x2011;18]</a><a name="NC&#x2011;18"> Public Functions Not Called By The Contract Should Be Declared External Instead

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

#### <ins>Proof Of Concept</ins>


```solidity
function delegateBySig(
        address delegatee,
        uint256 nonce,
        uint256 expiry,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L118





### <a href="#Summary">[NC&#x2011;19]</a><a name="NC&#x2011;19"> `require()` / `revert()` Statements Should Have Descriptive Reason Strings

#### <ins>Proof Of Concept</ins>


```solidity
317: require(x_ <= FIX_ONE);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L317

```solidity
102: revert();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L102

```solidity
116: revert();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L116

```solidity
133: revert();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L133

```solidity
149: revert();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L149

```solidity
78: revert();
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L78









### <a href="#Summary">[NC&#x2011;20]</a><a name="NC&#x2011;20"> Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

#### <ins>Proof Of Concept</ins>


```solidity
165: require(share.rsrDist <= 10000, "RSR distribution too high");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165

```solidity
166: require(share.rTokenDist <= 10000, "RToken distribution too high");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L166





### <a href="#Summary">[NC&#x2011;21]</a><a name="NC&#x2011;21"> Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

#### <ins>Proof Of Concept</ins>


```solidity
19: IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)
19: abi.encodePacked(r, s, v)
28: abi.encodePacked(r, s, v)

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L19

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L19

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L28



```solidity
206: string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol)
207: string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token")

```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L206-L207






#### <ins>Recommended Mitigation Steps</ins>

Use `bytes.concat()` and upgrade to at least Solidity version 0.8.4 if required. 



### <a href="#Summary">[NC&#x2011;22]</a><a name="NC&#x2011;22"> Use of Block.Timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.
References: SWC ID: 116

#### <ins>Proof Of Concept</ins>


```solidity
114: if (block.timestamp < basketTimestamp + tradingDelay) return;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L114

```solidity
71: if (uint48(block.timestamp) < uint64(lastPayout) + period) return;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L71

```solidity
313: require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L313

```solidity
497: if (block.timestamp < payoutLastPaid + rewardPeriod) return;
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L497

```solidity
775: require(block.timestamp <= deadline, "ERC20Permit: expired deadline");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L775

```solidity
126: require(block.timestamp <= expiry, "ERC20Votes: signature expired");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L126

```solidity
144: require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L144

```solidity
172: require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L172

```solidity
212: require(block.timestamp <= deadline, StaticATokenErrors.INVALID_EXPIRATION);
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L212

```solidity
171: } else if (_whenDefault > block.timestamp) {
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L171

```solidity
183: if (_whenDefault <= block.timestamp) return; // prevent DISABLED -> SOUND/IFFY
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L183

```solidity
81: require(block.timestamp <= deadline, "ERC20Permit: expired deadline");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/vendor/ERC20PermitUpgradeable.sol#L81



#### <ins>Recommended Mitigation Steps</ins>
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.






### <a href="#Summary">[NC&#x2011;23]</a><a name="NC&#x2011;23"> Use Underscores for Number Literals

#### <ins>Proof Of Concept</ins>


```solidity
165: require(share.rsrDist <= 10000, "RSR distribution too high");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165

```solidity
166: require(share.rTokenDist <= 10000, "RToken distribution too high");
```

https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L166






