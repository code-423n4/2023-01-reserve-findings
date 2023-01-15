# Reserve Low Risk and Non-Critical Issues
## Summary
| Risk      | Title | File | Instances
| ----------- | ----------- | ----------- | ----------- |
| L-01      | Check that `longFreeze >= shortFreeze` | contracts/mixins/Auth.sol | 1 |
| L-02      | Check that `RToken` contract does not receive `rToken` distribution | contracts/p1/Distributor.sol | 1 |
| L-03      | rToken issuances can be spammed | contracts/p1/RToken.sol | 1 |
| L-04      | Asset architecture makes dangerous use of `delegatecall` | - | - |
| L-05      | Introduce max value for `delayUntilDefault` | contracts/plugins/assets/FiatCollateral.sol | 1 |
| L-06      | Only disable basket if new asset is not the same as old asset | contracts/p1/AssetRegistry.sol | 1 |
| N-01      | No need to access components via `Main` | - | 4 |
| N-02      | Use Solidity units when possible | contracts/p1/Broker.sol | 1 |
| N-03      | Redundant code | contracts/p1/RToken.sol | 1 |
| N-04      | Consider adding functionality to restake drafted withdrawal | contracts/p1/StRSR.sol | 1 |
| N-05      | Consider implementing asymmetric peg range | contracts/plugins/assets/FiatCollateral.sol | 1 |

## [L-01] Check that `longFreeze >= shortFreeze`
The `docs/system-design.md` documentation states that:  

> The LONG_FREEZER role should be assigned to an address that will highly optimize for no false positives. It is much longer than the short freeze. It exists so that in the case of a zero-day exploit, governance can act before the system unfreezes and resumes functioning.

This makes it clear that the `longFreeze` duration must not be shorter than the `shortFreeze` duration. Maybe the `longFreeze` should even be strictly longer than the `shortFreeze`.  

If `longFreeze` is shorter than `shortFreeze` it fails to fulfill its purpose of extending the `shortFreeze`.  

The issue is that the `Auth.setShortFreeze` and `Auth.setLongFreeze` functions do not check that `shortFreeze <= longFreeze`.  

Therefore I recommend adding the following line to `Auth.setShortFreeze` ([https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L180-L184](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L180-L184)):  

```solidity
require(shortFreeze_ <= longFreeze, "long freeze cannot be shorter than short freeze")
```

And add the following line to `Àuth.setLongFreeze` ([https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L187-L191](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L187-L191)):  

```solidity
require(shortFreeze <= longFreeze_, "long freeze cannot be shorter than short freeze")
```

In order for the change to work you need to change the order in which you set the short freeze and long freeze values in the `Auth.__Auth_init` function.  

You need to set the long freeze first, then the short freeze:  

```solidity
setLongFreeze(longFreeze_);
setShortFreeze(shortFreeze_);
```

## [L-02] Check that `RToken` contract does not receive `rToken` distribution
The `Distributor` is used to distribute `rsr` and `rToken` to destination addresses.  

When calling the `Distributor.setDistribution` function ([https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L61-L65](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L61-L65)) it should be checked that the destination is not the `RToken` contract.  

This is because the `RToken` contract will revert if it receives `rToken` ([https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L833](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L833)). So the `RToken` contract can be mistakenly registered thereby causing DOS to the `Distributor`.  

Also the `RToken` contract is not able to handle any `rsr` it receives so you can just forbid registering the `RToken` contract as destination.  

## [L-03] rToken issuances can be spammed
The `RToken.issue` function can be called for any recipient ([https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L186](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L186)).  

So any user can issue `rToken` for any other user.  

This can lead to scenarios where malicious users intentionally issue a very small amount of `rToken` to other users in order to spam them.  

E.g. if there is an application with a GUI, other users' GUIs can be spammed with multiple issuances that they did not initiate.  

This can negatively impact user experience.  

It was assessed with the sponsor that they need to have this design and the benefits outweigh the risks.  

However this should be kept in mind when building applications around the issuance functionality because they need to deal with this potential spamming.  

## [L-04] Asset architecture makes dangerous use of `delegatecall`
Assets are registered in the core protocol via the `AssetRegistry` contract. The core protocol components can then claim rewards for an asset they hold by calling `Asset.claimRewards` via `delegatecall`.  

For example the `claimRewards` function in the `CTokenFiatCollateral` Asset looks like this ([https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L52-L57](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L52-L57)):  

```solidity
function claimRewards() external virtual override {
    IERC20 comp = IERC20(comptroller.getCompAddress());
    uint256 oldBal = comp.balanceOf(address(this));
    comptroller.claimComp(address(this));
    emit RewardsClaimed(comp, comp.balanceOf(address(this)) - oldBal);
}
```

The `claimRewards` function is dependent on the `comptroller` state variable of the `CTokenFiatCollateral` contract.  

The only reason this state variable can be accessed even though the `claimRewards` function is called via `delegateCall` is that `comptroller` is an `immutable` variable.  

So the current plugin implementations are dependent on their `claimRewards` functions only accessing `immutable` variables.  

If they try to access regular mutable variables, they will operate on the storage of the caller which can lead to various flaws.  

There is no documentation about this requirement for `immutable` variables to be used and the sponsor admitted that this behavior about `immutable` variables has not been known before and the plugin architecture worked essentially "by accident".  

It was discussed with the sponsor that either the architecture of plugins need to be changed or at least the documentation should be improved to make this very clear to plugin developers.  

## [L-05] Introduce max value for `delayUntilDefault`
Currently the `delayUntilDefault` variable in the `FiatCollateral` contract is only checked to be greater zero:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L75](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L75)  

This is unsafe because if it is a very large value it can be `> type(uint48).max` after `block.timestamp` is added to it.  

This means `_whenDefault` is always set to `NEVER`:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L189](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L189)  

This in turn means that the collateral cannot default.  

I suggest adding a reasonable upper limit to the `delayUntilDefault` variable. Maybe 1 year or 1 month.  

## [L-06] Only disable basket if new asset is not the same as old asset
In the `AssetRegistry.swapRegistered` function, the basket is disabled if the quantity of the asset is greater 0:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L78)  

It is not necessary to disable the basket if the new asset is the same as the old basket.  
Not disabling the basket in this case improves the availability of nearly all operations in the protocol since most of them are dependent on a valid basket.  

I suggest changing the function to this:  

```solidity
    function swapRegistered(IAsset asset) external governance returns (bool swapped) {
        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");

++++    if (assets[asset.erc20()] != asset) {
            uint192 quantity = basketHandler.quantity(asset.erc20());
            if (quantity > 0) basketHandler.disableBasket();
++++    }


        swapped = _registerIgnoringCollisions(asset);
    }
```

## [N-01] No need to access components via `Main`
All components of the protocol are registered in the `Main` contract. It is not always necessary to access components by calling the `Main` contract first.  

Specifically the `p1` implementation follows the style to only access components via `Main` when the component is not accessible from the local contract.  

Here are the instances that should be changed:  

Use `components.stRSR` instead of `main.stRSR()`:  
[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L208](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L208)  

Use `furnace` instead of `main.furnace()`:  
[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L452](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L452)  

Use `assetRegistry` instead of `main.assetRegistry()`:  
[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L380](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L380)  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L443](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L443)  

## [N-02] Use Solidity units when possible
You should consider using Solidity units ([https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html](https://docs.soliditylang.org/en/v0.8.17/units-and-global-variables.html)) whenever possible to improve readability of the code.  

Use `1 weeks` instead of `604800`:  
[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L24)  

## [N-03] Redundant code
### Redundant collateral status check
In the `RToken.issue` function, the collateral status is checked two times:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L217](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L217)  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L255](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L255)  

The second check is redundant because it will always be true as made sure by the first check.  

## [N-04] Consider adding functionality to restake drafted withdrawal
Unstaking `rsr` from the `StRSR` contract is performed by first calling `unstake` and then after an `unstakingDelay` calling `withdraw`.  

This means that when a user calls `unstake`, he MUST wait the `unstakingDelay` and then he MUST call `withdraw`.  

A user might decide to restake his `rsr` at a time when `unstakingDelay` has not passed yet and thereby continue earning rewards.  

It was discussed with the sponsor that they will consider adding such a `restake` functionality in order to provide more flexibility.  

The incentives involved with the `staking/unstaking` mechanism likely don't change much.  

It might make staking even somewhat more attractive because of the added flexibility.  

## [N-05] Consider implementing asymmetric peg range
Currently the `FiatCollateral` contract implements a symmetric range in which `targetPerRef` values are allowed for the collateral to be `SOUND`. This means the same delta is used to calculate the `pegBottom` and `pegTop` sides:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L85-L87](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L85-L87)  

It can be beneficial for some `FiatCollateral` assets to allow a higher deviation on one side than on the other side.  

E.g. the USD/USDT peg might be considered valid in a range from 0.95 to 1.02 with the asymmetric range instead of 0.95 - 1.05 with the symmetric range.  
