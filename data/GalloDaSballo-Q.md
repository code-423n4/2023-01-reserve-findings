* [L - BasketHandler - Some tokens have 2 contracts](#L-BasketHandler-Sometokenshave2contracts)
* [L - Inconsistent usage of main.address, can cause issues if changed](#L-Inconsistentusageofmain.addresscancauseissuesifchanged)
* [L - BLOCKS_PER_HOUR doesn't work on L2](#L-BLOCKS_PER_HOURdoesntworkonL2)
* [L - Not all tokens have max approve to max uint](#L-Notalltokenshavemaxapprovetomaxuint)
* [L - Notice on upgrading EIP712 to a new version](#L-NoticeonupgradingEIP712toanewversion)
* [L - Delegations are all reset after an era](#L-Delegationsareallresetafteranera)
* [Gnosis Trade should be refactored to use immutable variables](#GnosisTradeshouldberefactoredtouseimmutablevariables)
* [R - Pointer to storage doesn't need to be re-declared](#R-Pointertostoragedoesntneedtobere-declared)
* [NC - Require <= will never happen](#NC-Requirewillneverhappen)
* [R - Minor Gotcha - Discrepancy between String and Bytes32, gotcha for Governor](#R-MinorGotcha-DiscrepancybetweenStringandBytes32gotchaforGovernor)
* [R - _ for naming, sometimes not used for internal and private vars](#R-_fornamingsometimesnotusedforinternalandprivatevars)
* [NC - Peculiar Gap Usage](#NC-PeculiarGapUsage)
* [R - EIP712 is no longer a draft](#R-EIP712isnolongeradraft)
* [R - Inconsistent usage of `__EIP712_init` vs `__EIP712_init_unchained`](#R-Inconsistentusageof__EIP712_initvs__EIP712_init_unchained)
* [Informational - Compiler Warnings](#Informational-CompilerWarnings)
* [Usual Findings](#UsualFindings)

# <a name='Legend:'></a>Legend:
- L -> Low Severity -> Could cause issues however impact is limited
- R -> Refactoring -> Suggested Code Change to improve readability and maintainability or to offer better User Experience
- NC -> Non-Critical / Informational -> No risk of loss, pertains to events or has no impact


## <a name='L-BasketHandler-Sometokenshave2contracts'></a>L - BasketHandler - Some tokens have 2 contracts

        requireValidCollArray(erc20s);

    Some tokens have [2 addresses](https://medium.com/chainsecurity/trueusd-compound-vulnerability-bc5b696d29e2) through which they can be interacted with. 

    Notice that if the second address was added, that may create accounting issues.

    However, because the governance needs to add asset, this should be fine, just a minor gotcha for this codebase

## <a name='L-Inconsistentusageofmain.addresscancauseissuesifchanged'></a>L - Inconsistent usage of main.address, can cause issues if changed

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L60-L75

```solidity
    IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
    rToken = main_.rToken();
```

The in-scope codebase seems to jump between using the `main.address()` pattern to then using the local variables.

For the goal of using a hub and spoke pattern, it would be best to only use value from main.

Alternatively, you could only use main to `init` those values and then read them from the local value

If `main` was changed to allow it's values to be changed, then the current setup could cause inconsistencies which would break mutability.


### <a name='Recommendation'></a>Recommendation

Either only use `main.address()` or only read from the local variable

### <a name='AdditionalInstance'></a>Additional Instance

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L529

## <a name='L-BLOCKS_PER_HOURdoesntworkonL2'></a>L - BLOCKS_PER_HOUR doesn't work on L2

BLOCKS_PER_HOUR will only work on mainnet

This is valid with the current implementation but worth flagging as QA

Due to how block number and timestamps are calculated, it may be best to use timestamps, especially on L2s

This is in spite of the 2019 article by OpenZeppelin, which just as any other technical resource is too old to be trusted without taking it with a grain of salt

## <a name='L-Notalltokenshavemaxapprovetomaxuint'></a>L - Not all tokens have max approve to max uint
    function grantRTokenAllowance(IERC20 erc20) external notPausedOrFrozen {
        require(assetRegistry.isRegistered(erc20), "erc20 unregistered");
        // == Interaction ==
        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), 0);
        IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), type(uint256).max);
    }

Some tokens may support up to u96, u112, or u128 for approvals, see [Weird Erc-20 List](https://github.com/d-xo/weird-erc20#revert-on-large-approvals--transfers), for this reason, it may be best to approve the exact amount each time is needed

## <a name='L-NoticeonupgradingEIP712toanewversion'></a>L - Notice on upgrading EIP712 to a new version

Because of how EIP712 is coded, when upgrading to a new version you may want to change the version and name
    function __EIP712_init_unchained(string memory name, string memory version) internal onlyInitializing {

Because of this you'll have to change the child contract to replicate this functionality
https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/25aabd286e002a1526c345c8db259d57bdf0ad28/contracts/utils/cryptography/EIP712Upgradeable.sol#L53-L58

And perform it right after the upgrade (and only once)

## <a name='L-Delegationsareallresetafteranera'></a>L - Delegations are all reset after an era

Because balances are zeroed out, and are indexed by epoch, all delegations must be re-done when an epoch changes

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L156-L164

This may require additional monitoring from end-users, alters on your side, and ultimately is something that users need to be aware of to ensure that if they chose to perform a delegation, that remains over time

### <a name='Recommendation-1'></a>Recommendation

You could create a delegate registry, that re-delegates on era resets by allowing the delegated to claim the delegation (and then set a boolean to verify they already did), this will require further thinking around the topic.

## <a name='GnosisTradeshouldberefactoredtouseimmutablevariables'></a>Gnosis Trade should be refactored to use immutable variables

Because of it's disposability, I recommend refactoring Gnosis Trade to not be using storage slots for it's variables.

You'll save way more gas if you just make everything immutable.

If you need to keep the flexibility you could look into [Clones With Immutable Args](https://github.com/wighawag/clones-with-immutable-args)

## <a name='R-Pointertostoragedoesntneedtobere-declared'></a>R - Pointer to storage doesn't need to be re-declared

You already have the pointer, and storage is always updated to latest (it's pointers so values are not cached)
```solidity
            queue = issueQueues[recipient];
```

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L212

## <a name='NC-Requirewillneverhappen'></a>NC - Require <= will never happen

        if (queue.left == endId) return;

        require(queue.left <= endId && endId <= queue.right, "out of range");

        queue.left <= endId 
        Cannot happen because of the return above, remove the `=`

## <a name='R-MinorGotcha-DiscrepancybetweenStringandBytes32gotchaforGovernor'></a>R - Minor Gotcha - Discrepancy between String and Bytes32, gotcha for Governor

A usability notice I have is that proposals are sent with the full string, but are canceled / operated via the hash

This is fine but can make it more complicated to interact with the code via Etherscan, especially in lack of public hashing function

Consider adding a `hashDescrition` function which performs:
    bytes32 data = keccak256(bytes(description));



## <a name='R-_fornamingsometimesnotusedforinternalandprivatevars'></a>R - _ for naming, sometimes not used for internal and private vars

Throughout the codebase, some functions have an `_` prefix

Example:
[`BackingManager._manageTokens`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L105)

vs

[`RToken.requireValidBUExchangeRate`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L802)

I recommend consistently prefixing with an `_`

## <a name='NC-PeculiarGapUsage'></a>NC - Peculiar Gap Usage
    /**
     * @dev This empty reserved space is put in place to allow future versions to add new
     * variables without shifting down storage in the inheritance chain.
     * See https://docs.openzeppelin.com/contracts/4.x/upgradeable#storage_gaps
     */
    uint256[47] private __gap;

While there's no harm, you don't necessarily need to cap your gap at 50 slots
Nor you have to change the count for non-deployed contracts

Am flagging because this is the first time I've seen this done

No strong opinion in favour or against

If anything, I personally recommend committing to a system that only increases slots in the Child Contracts (gapless)
As that is going to be less mentally taxing and will avoid very risky gotchas in the future

However, historically, C4 has flagged lack of gaps, so ultimately there is no broader consensus

## <a name='R-EIP712isnolongeradraft'></a>R - EIP712 is no longer a draft
File is deprecated
import "@openzeppelin/contracts-upgradeable/utils/cryptography/draft-EIP712Upgradeable.sol";

## <a name='R-Inconsistentusageof__EIP712_initvs__EIP712_init_unchained'></a>R - Inconsistent usage of `__EIP712_init` vs `__EIP712_init_unchained`
Very minor detail, but each of these is used once, which is inconsistent
No specific preference over which one you should use


# QA NC - Immutable doesn't allocate a slot - Potential issue with storage
    bytes32 private _PERMIT_TYPEHASH_DEPRECATED_SLOT;


QA (OZ has it too)
https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/token/ERC20/extensions/draft-ERC20PermitUpgradeable.sol

## <a name='Informational-CompilerWarnings'></a>Informational - Compiler Warnings

The Compiler is warning for Contract Size, although this may be avoided by using the optimizer, I thought it was worth flagging

```bash
Warning: Contract code size exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
  --> contracts/p0/RToken.sol:34:1:
   |
34 | contract RTokenP0 is ComponentP0, RewardableP0, ERC20PermitUpgradeable, IRToken {
   | ^ (Relevant source part starts here and spans across multiple lines).


Warning: Contract code size exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
  --> contracts/p0/Deployer.sol:27:1:
   |
27 | contract DeployerP0 is IDeployer, Versioned {
   | ^ (Relevant source part starts here and spans across multiple lines).


Warning: Contract code size exceeds 24576 bytes (a limit introduced in Spurious Dragon). This contract may not be deployable on mainnet. Consider enabling the optimizer (with a low "runs" value!), turning off revert strings, or using libraries.
 --> contracts/plugins/mocks/upgrades/RTokenV2.sol:7:1:
  |
7 | contract RTokenP1V2 is RTokenP1 {
  | ^ (Relevant source part starts here and spans across multiple lines).
```

## <a name='UsualFindings'></a>Usual Findings

**Risk for Rebasing and FeeOnTransfer Tokens**

Adding this here just in case it get's awarded, per the docs I'm aware you want to wrap any non-standard token around an asset.

This is a great idea and should avoid most issues

However, notice that even that integration may incur losses if it doesn't check for the balance before and after a transfer, as well as if the token rebases.

Adding this just in case, but because of the suggested flow the finding should either be out of scope or QA at best.