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
| L-07      | Use `lotLow` price instead of `low` price to calculate total asset value | contracts/p1/mixins/RecollateralizationLib.sol | 1 |
| L-08      | Round with mode `FLOOR` instead of `CEIL` in calculation of `range.bottom` | contracts/p1/mixins/RecollateralizationLib.sol | 1 |
| L-09      | Sending `sell` or `buy` tokens to `GnosisTrade` contract such that `reportViolation` is not called | contracts/plugins/trading/GnosisTrade.sol | 1 |
| L-10      | `minBuyAmountPerOrder` calculation should be based on asset value not token fraction | contracts/plugins/trading/GnosisTrade.sol | 1 |
| N-01      | No need to access components via `Main` | - | 4 |
| N-02      | Use Solidity units when possible | contracts/p1/Broker.sol | 1 |
| N-03      | Redundant code | contracts/p1/RToken.sol | 1 |
| N-04      | Consider adding functionality to restake drafted withdrawal | contracts/p1/StRSR.sol | 1 |
| N-05      | Consider implementing asymmetric peg range | contracts/plugins/assets/FiatCollateral.sol | 1 |
| N-06      | Consider leaving the basket `DISABLED` when it has `IFFY` collateral and allowing redemption when basket is `DISABLED` | - | - |
| N-07      | Use `IStRSRVotes` instead of `IStRSR` interface | contracts/p1/Deployer.sol | - |

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

## [L-07] Use `lotLow` price instead of `low` price to calculate total asset value
Currently the `RecollateralizationLib.totalAssetValue` function makes use of the `low` price to calculate the total asset value under management by the BackingManager:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L264](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L264)  

You should consider using the `lotLow` price instead. This is since the purpose of the `lotLow` price is to provide a price that decays to zero if there is something wrong with the price feed. The `low` price however will just be `0` when there is something wrong with the price feed. The `lotLow` price therefore more accurately reflects the true value of the asset.  

## [L-08] Round with mode `FLOOR` instead of `CEIL` in calculation of `range.bottom`
In the `RecollateralizationLib.basketRange` function, `range.bottom` is calculated like this:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L201](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L201)  

```solidity
range.bottom = fixMin(assetsLow.div(basketPriceHigh, CEIL), range.top);
```

The calculation is using rounding mode `CEIL`. This is wrong because `range.bottom` represents the low value of the range. Therefore the calculation should round down, i.e. use rounding mode `FLOOR`.  

## [L-09] Sending `sell` or `buy` tokens to `GnosisTrade` contract such that `reportViolation` is not called
The `GnosisTrade` contract calls `Broker.reportViolation` when the `clearingPrice` is less than the `worstCasePrice`:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L206-L208](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L206-L208)  

This disables the `Broker` such that no new trades can be opened until the issue is resolved by governance.  

In a case where the `GnosisTrade` contract would call `Broker.reportViolation` this can be avoided by sending `sell` tokens or `buy` tokens (possible a very small amount is sufficient) to the `GnosisTrade` contract.  

This behavior can prevent governance and the protocol from noticing an issue as it emerges (maybe a small amount of funds can manipulate the price sufficiently to keep the protocol going). The governance then only notices the problem as it gets worse.  

The sold amount and bought amount should be queried from the `EasyAuction` contract directly instead of relying on the `GnosisTrade` contract balance.  

## [L-10] `minBuyAmountPerOrder` calculation should be based on asset value not token fraction
The `minBuyAmountPerOrder` in the `GnosisTrade.init` function is calculated like this:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L123-L126](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L123-L126)  

```solidity
uint256 minBuyAmtPerOrder = Math.max(
    minBuyAmount / MAX_ORDERS,
    DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))
);
```

So it is at least as big as `DEFAULT_MIN_BID.shiftl_toUint(int8(buy.decimals()))`, and `DEFAULT_MIN_BID` is one hundredth of a token ([https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L34](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L34)).  

This amount can cause problems for some tokens like WBTC. Currently one hundredth of one WBTC is worth >$200.  

If the assets sold are worth only $100 then nobody is going to buy for $200.  

I suggest the `minBuyAmountPerOrder` to be calculated based on the value of the asset that is sold in case the asset can be priced. Only in the case that the asset is unpriced must there be a fallback mechanism.  

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

## [N-06] Consider leaving the basket `DISABLED` when it has `IFFY` collateral and allowing redemption when basket is `DISABLED`
It was discussed with the Sponsor whether it is correct how the `BasketHandler` handles `IFFY` collateral.  

I brought up the following concern:  

> In the BasketHandler._switchBasket function, a collateral with the status IFFY is considered a good collateral and is added to the reference basket. 
> Some operations however like StRSR.withdraw require that there is no IFFY collateral in the basket.

In the discussion with the sponsor it was determined that:  

> So it might be that leaving the basket DISABLED and enabling redemption on DISABLED baskets makes more sense

There is no vulnerability with the current behavior but it might be beneficial to change the behavior of the protocol as stated above.  

The sponsor should therefore further assess the proposed change and implement it if it is beneficial.  

## [N-07] Use `IStRSRVotes` instead of `IStRSR` interface
The `Deployer` is used to deploy an instance of a RToken.  

This is achieved by cloning an implementation contract of each contract that is needed.  

One of the contracts is called `stRSR` and it must implement the `IStRSR` interface:  

[https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L121](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L121)  

This is too permissive though because there are two contracts that implement the `IStRSR` interface: `StRSR` and `StRSRVotes`.  

The RToken protocol however requires that the contract is `StRSRVotes` not `StRSR` (voting capabilities are needed for governance).  

The `Deployer` contract therefore should use `IStRSRVotes` instead of `IStRSR` such that the implementation contract must implement `IStRSRVotes`.  
