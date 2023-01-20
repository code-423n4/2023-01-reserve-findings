## GAS-1: ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow

### Description

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

### Affected file

* Array.sol (Line: 11, 12, 23)
* AssetRegistry.sol (Line: 38, 49, 127, 138)
* BackingManager.sol (Line: 221, 238)
* BasketHandler.sol (Line: 70, 78, 218, 227, 262, 286, 337, 397, 416, 437, 530, 548, 553, 586, 597, 611, 643, 653, 707, 725)
* Distributor.sol (Line: 108, 133, 143)
* EasyAuction.sol (Line: 277, 288, 316, 344, 513, 524)
* RToken.sol (Line: 270, 303, 329, 334, 478, 501, 674, 683, 711, 757, 767, 793)
* RecollateralizationLib.sol (Line: 242, 329, 437)
* RewardableLib.sol (Line: 27, 67, 73)
* String.sol (Line: 14)


## GAS-2: <X> += <Y> costs more gas than <X> = <X> + <Y> for state variables

### Description

Using the addition operator instead of plus-equals saves gas.

### Affected file

* BasketHandler.sol (Line: 636)
* Furnace.sol (Line: 81)
* RToken.sol (Line: 330, 678, 687, 761, 771)
* WETH.sol (Line: 38, 44, 72, 75, 76)

## GAS-3: <X> <= <Y> costs more gas than <X> < <Y> + 1

### Description

In Solididy, the opcode 'less or equal' doesn't exist. So the EVM will translate this by two distinct operation, first the inferior, and then the equal which cost more gas then a strict less.

### Affected file

* Array.sol (Line: 24)
* Asset.sol (Line: 112, 138, 147)
* BackingManager.sol (Line: 257, 264)
* BasketHandler.sol (Line: 232, 274, 349, 350)
* Distributor.sol (Line: 165, 166, 172)
* EasyAuction.sol (Line: 131, 414, 428, 436, 460, 494, 718, 793)
* FiatCollateral.sol (Line: 183, 189, 200)
* Fixed.sol (Line: 212, 213, 216, 317, 324, 337, 345, 364, 389, 390, 395)
* Furnace.sol (Line: 89, 97)
* GnosisMock.sol (Line: 91, 100)
* GnosisMockReentrant.sol (Line: 61)
* GnosisTrade.sol (Line: 88, 89, 95, 96, 223)
* RToken.sol (Line: 253, 410, 590, 603, 623, 660, 741, 747, 813)
* RTokenAsset.sol (Line: 74)
* RTokenCollateral.sol (Line: 56)
* RayMathNoRounding.sol (Line: 15, 21)
* RecollateralizationLib.sol (Line: 270)
* RedemptionBattery.sol (Line: 44)
* RewardableLib.sol (Line: 78, 102)
* StRSRVotes.sol (Line: 126)
* StaticATokenLM.sol (Line: 144, 172, 212)
* String.sol (Line: 16)
* WETH.sol (Line: 43, 68, 71)

## GAS-4: Add unchecked{} for subtractions where the operands cannot underflow because of a previous require() or if statement

### Description

require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }
 if(a <= b); x = b - a => if(a <= b); unchecked { x = b - a }
 This will stop the check for overflow and underflow so it will save gas.

### Affected file

* EasyAuction.sol (Line: 495)
* RedemptionBattery.sol (Line: 48)

## GAS-5: Bytes32 constants are more efficient than string constants

### Description

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

### Affected file

* Deployer.sol (Line: 31)
* StaticATokenErrors.sol (Line: 5, 6, 7, 8, 9, 10, 11)

## GAS-6: Empty blocks should be removed or emit something

### Description

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### Affected file

* ATokenFiatCollateral.sol (Line: 40)
* Asset.sol (Line: 164)
* BadCollateralPlugin.sol (Line: 13)
* BadERC20.sol (Line: 17)
* ComptrollerMock.sol (Line: 12)
* ERC20.sol (Line: 342)
* ERC20Mock.sol (Line: 8)
* EasyAuction.sol (Line: 121)
* Governance.sol (Line: 31)
* InvalidATokenFiatCollateralMock.sol (Line: 7)
* InvalidBrokerMock.sol (Line: 48, 52, 56)
* InvalidChainlinkMock.sol (Line: 15)
* InvalidFiatCollateral.sol (Line: 12)
* Main.sol (Line: 23, 64)
* MockableCollateral.sol (Line: 11)
* NontrivialPegCollateral.sol (Line: 9, 20, 31, 42)
* RToken.sol (Line: 838)
* RTokenAsset.sol (Line: 119)
* USDCMock.sol (Line: 8)
* UnpricedAssetPlugin.sol (Line: 20)
* WBTCMock.sol (Line: 8)
* ZeroDecimalMock.sol (Line: 8)


## GAS-7: Internal functions not called by the contract should be removed to save deployment gas

### Description

If the functions are required by an interface, the contract should inherit from that interface and use the override keyword.

### Affected file

* Array.sol (Line: 9, 21)
* BasketHandler.sol (Line: 75, 87)
* ERC20.sol (Line: 261, 282, 324)
* EasyAuction.sol (Line: 117, 125, 144, 160, 172, 243, 252, 320, 369, 374, 397, 413, 420, 428, 458, 510, 622, 627, 632, 656, 661, 717)
* Fixed.sol (Line: 222, 229, 236, 243, 269, 316, 332, 336, 340, 344, 348, 352, 359, 401, 408, 419, 426)
* Governance.sol (Line: 145)
* IAsset.sol (Line: 66)
* Main.sol (Line: 64)
* OracleLib.sol (Line: 14)
* RToken.sol (Line: 828)
* RayMathNoRounding.sol (Line: 11, 19, 25)
* RedemptionBattery.sol (Line: 33)
* StaticATokenLM.sol (Line: 358)
* String.sol (Line: 11)
* TradeLib.sol (Line: 103)

## GAS-8: Internal functions only called once can be inlined to save gas

### Description

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

### Affected file

* BasketHandler.sol (Line: 68)
* EasyAuction.sol (Line: 248, 304, 475, 489, 536, 552, 557, 576, 592, 614, 618, 743, 753, 772, 787, 807, 821)
* FiatCollateral.sol (Line: 199)
* Fixed.sol (Line: 197, 205, 251, 258, 276, 283, 295, 302, 375, 382, 438, 450, 463, 475)
* Governance.sol (Line: 115, 135)
* RecollateralizationLib.sol (Line: 152)
* RedemptionBattery.sol (Line: 53)
* StRSRVotes.sol (Line: 49, 137, 142, 147)
* TradeLib.sol (Line: 38, 140, 154)


## GAS-9: Multiple address/ID mappings can be combined into a single mapping of an address/ID to a struct, where appropriate

### Description

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key’s keccak256 hash (Gkeccak256 - 30 gas) and that calculation’s associated stack operations.

### Affected file

* ERC20.sol (Line: 38, 40)
* StaticATokenLM.sol (Line: 66, 74, 76)
* WETH.sol (Line: 30, 31)

## GAS-10: Not using the named return variables when a function returns, wastes deployment gas

### Description

It is not necessary to have both a named return and a return statement.

### Affected file

* Asset.sol (Line: 78)
* AssetRegistry.sol (Line: 162)
* BasketHandler.sol (Line: 316, 325)
* ChainlinkMock.sol (Line: 82, 104)
* ERC1271Mock.sol (Line: 31)
* EURFiatCollateral.sol (Line: 41)
* EasyAuction.sol (Line: 219, 238)
* FixedCallerMock.sol (Line: 148)
* GnosisMock.sol (Line: 98, 98)
* GnosisMockReentrant.sol (Line: 59, 59)
* Governance.sol (Line: 90)
* InvalidChainlinkMock.sol (Line: 30)
* RToken.sol (Line: 634)
* RTokenCollateral.sol (Line: 51)
* RecollateralizationLib.sol (Line: 66, 66)
* TradeLib.sol (Line: 42, 42, 107)
* UnpricedAssetPlugin.sol (Line: 39)

## GAS-11: Public functions not called by the contract should be declared external instead

### Description

Contracts are allowed to override their parents’ functions and change the visibility from external to public and can save gas by doing so. 

### Affected file

* ATokenFiatCollateral.sol (Line: 45)
* ATokenMock.sol (Line: 50)
* Asset.sol (Line: 86)
* AssetRegistryV2.sol (Line: 13)
* BackingManagerV2.sol (Line: 14)
* BadCollateralPlugin.sol (Line: 26)
* BadERC20.sol (Line: 31, 48)
* BasketHandlerV2.sol (Line: 13)
* BrokerV2.sol (Line: 13)
* CTokenFiatCollateral.sol (Line: 44)
* CTokenSelfReferentialCollateral.sol (Line: 47)
* ChainlinkMock.sol (Line: 47, 52, 56)
* DistributorV2.sol (Line: 13)
* ERC1271Mock.sol (Line: 31)
* ERC20.sol (Line: 66, 74, 91, 98, 105, 125, 159, 188, 207)
* EasyAuction.sol (Line: 127, 145, 310, 334, 365, 508, 621, 628, 961, 989, 993)
* FiatCollateral.sol (Line: 121)
* FixedCallerMock.sol (Line: 9, 12, 15, 19, 22, 26, 29, 32, 35, 41, 47, 65, 74, 80, 112, 118, 124, 130, 136, 141, 144, 148)
* FurnaceV2.sol (Line: 13)
* Governance.sol (Line: 67)
* InvalidFiatCollateral.sol (Line: 15)
* MainV2.sol (Line: 14)
* MockableCollateral.sol (Line: 17)
* NontrivialPegCollateral.sol (Line: 12, 23, 34, 45)
* RTokenAsset.sol (Line: 63)
* RTokenCollateral.sol (Line: 55, 85, 92)
* RTokenV2.sol (Line: 14)
* RevenueTraderV2.sol (Line: 14)
* StRSRV2.sol (Line: 13)
* StRSRVotes.sol (Line: 59, 63, 71, 76, 82, 88, 118)
* USDCMock.sol (Line: 10)
* WBTCMock.sol (Line: 10)
* WETH.sol (Line: 42, 49, 53)
* ZeroDecimalMock.sol (Line: 10)

## GAS-12: Replace modifier with function

### Description

Modifiers make code more elegant, but cost more than normal functions.

### Affected file

* EasyAuction.sol (Line: 18, 26, 34, 47)
* GnosisTrade.sol (Line: 59)


## GAS-13: Splitting require() statements that use && saves gas

### Description

Saves 16 gas per instance. If you’re using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas.

### Affected file

* Asset.sol (Line: 50)
* BasketHandler.sol (Line: 186)
* Broker.sol (Line: 134)
* Deployer.sol (Line: 48, 109)
* EasyAuction.sol (Line: 37, 259, 376)
* Furnace.sol (Line: 89)
* RToken.sol (Line: 410, 590, 623, 741, 813)
* RevenueTrader.sol (Line: 72)


## GAS-14: Use assembly to check for address(0)

### Description

Saves 6 gas per instance if using assembly to check for address(0).

### Remediation

```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

### Affected file

* Asset.sol (Line: 49, 51)
* Broker.sol (Line: 57, 58)
* CTokenFiatCollateral.sol (Line: 26)
* CTokenNonFiatCollateral.sol (Line: 28)
* CTokenSelfReferentialCollateral.sol (Line: 30)
* Deployer.sol (Line: 48, 109)
* Distributor.sol (Line: 162)
* ERC20.sol (Line: 242, 243, 262, 283, 310, 311)
* EURFiatCollateral.sol (Line: 24)
* EasyAuction.sol (Line: 637)
* Main.sol (Line: 32)
* NonFiatCollateral.sol (Line: 24)
* RTokenAsset.sol (Line: 28)
* RevenueTrader.sol (Line: 29)
* StaticATokenLM.sol (Line: 142, 170, 210, 294, 317)


## GAS-15: Use require() instead of assert()

### Description

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

### Affected file

* Asset.sol (Line: 98, 112, 147)
* BackingManager.sol (Line: 249)
* BasketHandler.sol (Line: 556)
* FiatCollateral.sol (Line: 137)
* GnosisTrade.sol (Line: 63, 98, 182)
* RTokenAsset.sol (Line: 74)
* RecollateralizationLib.sol (Line: 110)
* RewardableLib.sol (Line: 78, 102)
* StaticATokenLM.sol (Line: 345)
* TradeLib.sol (Line: 44, 108, 168, 170)

## GAS-16: Use selfbalance()

### Description

Use selfbalance() instead of address(this).balance when getting your contract’s balance of ETH to save gas.

### Affected file

* EasyAuction.sol (Line: 718, 793)
* WETH.sol (Line: 50)


## GAS-17: Using Solidity version 0.8.17 will provide an overall gas optimization

### Description

Using at least 0.8.10 will save gas due to skipped extcodesize check if there is a return value.

### Affected file

* ATokenFiatCollateral.sol (Line: 2)
* ATokenMock.sol (Line: 2)
* AaveLendingPoolMock.sol (Line: 2)
* Array.sol (Line: 2)
* ArrayCallerMock.sol (Line: 2)
* Asset.sol (Line: 2)
* AssetRegistry.sol (Line: 2)
* AssetRegistryV2.sol (Line: 2)
* Auth.sol (Line: 2)
* BackingManager.sol (Line: 2)
* BackingManagerV2.sol (Line: 2)
* BadCollateralPlugin.sol (Line: 2)
* BadERC20.sol (Line: 2)
* BasketHandler.sol (Line: 2)
* BasketHandlerV2.sol (Line: 2)
* Broker.sol (Line: 2)
* BrokerV2.sol (Line: 2)
* CTokenFiatCollateral.sol (Line: 2)
* CTokenMock.sol (Line: 2)
* CTokenNonFiatCollateral.sol (Line: 2)
* CTokenSelfReferentialCollateral.sol (Line: 2)
* ChainlinkMock.sol (Line: 2)
* Component.sol (Line: 2)
* ComponentRegistry.sol (Line: 2)
* ComptrollerMock.sol (Line: 2)
* Deployer.sol (Line: 2)
* Distributor.sol (Line: 2)
* DistributorV2.sol (Line: 2)
* ERC1271Mock.sol (Line: 2)
* ERC20Mock.sol (Line: 2)
* ERC20PermitUpgradeable.sol (Line: 6)
* EURFiatCollateral.sol (Line: 2)
* FiatCollateral.sol (Line: 2)
* Fixed.sol (Line: 3)
* FixedCallerMock.sol (Line: 2)
* Furnace.sol (Line: 2)
* FurnaceV2.sol (Line: 2)
* GnosisMock.sol (Line: 2)
* GnosisMockReentrant.sol (Line: 2)
* GnosisTrade.sol (Line: 2)
* Governance.sol (Line: 2)
* IAsset.sol (Line: 2)
* IAssetRegistry.sol (Line: 2)
* IBackingManager.sol (Line: 2)
* IBasketHandler.sol (Line: 2)
* IBroker.sol (Line: 2)
* ICToken.sol (Line: 2)
* IComponent.sol (Line: 2)
* IDeployer.sol (Line: 2)
* IDeployerRegistry.sol (Line: 2)
* IDistributor.sol (Line: 2)
* IFacadeAct.sol (Line: 2)
* IFacadeRead.sol (Line: 2)
* IFacadeTest.sol (Line: 2)
* IFacadeWrite.sol (Line: 2)
* IFurnace.sol (Line: 2)
* IGnosis.sol (Line: 2)
* IMain.sol (Line: 2)
* IRToken.sol (Line: 2)
* IRevenueTrader.sol (Line: 2)
* IRewardable.sol (Line: 2)
* IStRSR.sol (Line: 2)
* IStRSRVotes.sol (Line: 2)
* ITrade.sol (Line: 2)
* ITrading.sol (Line: 2)
* IVersioned.sol (Line: 2)
* InvalidATokenFiatCollateralMock.sol (Line: 2)
* InvalidBrokerMock.sol (Line: 2)
* InvalidChainlinkMock.sol (Line: 2)
* InvalidFiatCollateral.sol (Line: 2)
* Main.sol (Line: 2)
* MainV2.sol (Line: 2)
* MockableCollateral.sol (Line: 2)
* NonFiatCollateral.sol (Line: 2)
* NontrivialPegCollateral.sol (Line: 2)
* OracleLib.sol (Line: 2)
* Permit.sol (Line: 2)
* RToken.sol (Line: 2)
* RTokenAsset.sol (Line: 2)
* RTokenCollateral.sol (Line: 2)
* RTokenV2.sol (Line: 2)
* RecollateralizationLib.sol (Line: 2)
* RedemptionBattery.sol (Line: 2)
* RevenueTrader.sol (Line: 2)
* RevenueTraderV2.sol (Line: 2)
* RewardableLib.sol (Line: 2)
* SelfReferentialCollateral.sol (Line: 2)
* SelfdestructTransferMock.sol (Line: 2)
* StRSR.sol (Line: 2)
* StRSRV2.sol (Line: 2)
* StRSRVotes.sol (Line: 2)
* String.sol (Line: 2)
* StringCallerMock.sol (Line: 2)
* TradeLib.sol (Line: 2)
* Trading.sol (Line: 2)
* USDCMock.sol (Line: 2)
* UnpricedAssetPlugin.sol (Line: 2)
* Versioned.sol (Line: 2)
* WBTCMock.sol (Line: 2)
* ZeroDecimalMock.sol (Line: 2)


## GAS-18: abi.encode() is less efficient than abi.encodePacked()

### Description

Use abi.encodePacked() where possible to save gas.

### Affected file

* StRSRVotes.sol (Line: 128)
* StaticATokenLM.sol (Line: 151, 179, 219, 269)