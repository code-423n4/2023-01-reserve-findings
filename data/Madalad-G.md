# Gas Report

## Use `private` rather than `public` for constant state variables
Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table. If needed to be viewed externally, the values can be read from the verified contract source code.
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L37
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L38
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L39
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L45
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L65
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L87
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L20
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L21
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L33
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L34

<br>

## Avoid using long revert strings
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met. Consider shortening the strings, or using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L231
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L232
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L657
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Broker.sol#L60
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L380
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L816
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L824
- https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/RedemptionBattery.sol#L44

<br>

## Use `unchecked` for operations that cannot overflow/underflow
By bypassing Solidity's built in overflow/underflow checks using `unchecked`, we can save gas. This is especially beneficial for the index variable within for loops (saves 120 gas per iteration).
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L38
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L49
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L127
- https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L138
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L221
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L238
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L70
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L218
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L227
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L262
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L286
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L337
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L397
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L416
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L437
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L530
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L548
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L553
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L586
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L597
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L611
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L643
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L653
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L707
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L725
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L108
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L133
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L143
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L270
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L303
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L329
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L334
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L478
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L501
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L674
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L683
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L711
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L757
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L767
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L793
- https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/Array.sol#L11
- https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/Array.sol#L12
- https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/Array.sol#L23
- https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/String.sol#L14
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RecollateralizationLib.sol#L242
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RecollateralizationLib.sol#L329
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RecollateralizationLib.sol#L437
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RewardableLib.sol#L27
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RewardableLib.sol#L67
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RewardableLib.sol#L73

<br>

## Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2), DUP1(3), ISZERO(3), PUSH2(3), JUMPI(10), PUSH1(3), DUP1(3), REVERT(0), JUMPDEST(1), POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost (2400 per instance).
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/mixins/Trading.sol#L120
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/mixins/Trading.sol#L127
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/AssetRegistry.sol#L62
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/AssetRegistry.sol#L74
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/AssetRegistry.sol#L88
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/BackingManager.sol#L254
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/BackingManager.sol#L261
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/BasketHandler.sol#L205
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/BasketHandler.sol#L248
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/Broker.sol#L133
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/Broker.sol#L143
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/Distributor.sol#L61
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/Furnace.sol#L88
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/Furnace.sol#L96
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/RToken.sol#L564
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/RToken.sol#L577
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/RToken.sol#L590
- https://github.com/reserve-protocol/protocol/blob/d224c14c398d2727d39d133aa7511e1e6b161833/contracts/p1/StRSR.sol#L803
- https://github.com/reserve-protocol/protocol/blob/d224c14c398d2727d39d133aa7511e1e6b161833/contracts/p1/StRSR.sol#L807
- https://github.com/reserve-protocol/protocol/blob/d224c14c398d2727d39d133aa7511e1e6b161833/contracts/p1/StRSR.sol#L812
- https://github.com/reserve-protocol/protocol/blob/d224c14c398d2727d39d133aa7511e1e6b161833/contracts/p1/StRSR.sol#L820
- https://github.com/reserve-protocol/protocol/blob/d224c14c398d2727d39d133aa7511e1e6b161833/contracts/p1/StRSR.sol#L828

<br>

## Use assembly to calculate hashes
Saves 5000 deployment gas per instance and 80 runtime gas per instance: instead of `keccak256(abi.encodePacked(a, b))`, use
```
assembly {
    mstore(0x00, a)
    mstore(0x20, b)
    let hashedVal := keccak256(0x00, 0x40)
}
```
Note that assembly cannot be used to hash non-numerical values such as strings.
- https://github.com/reserve-protocol/protocol/blob/ac66c367403dab81f8d7765795d99b0dba106c02/contracts/plugins/aave/StaticATokenLM.sol#L150-L152
- https://github.com/reserve-protocol/protocol/blob/ac66c367403dab81f8d7765795d99b0dba106c02/contracts/plugins/aave/StaticATokenLM.sol#L178-L189
- https://github.com/reserve-protocol/protocol/blob/ac66c367403dab81f8d7765795d99b0dba106c02/contracts/plugins/aave/StaticATokenLM.sol#L218-L229
- https://github.com/reserve-protocol/protocol/blob/ac66c367403dab81f8d7765795d99b0dba106c02/contracts/plugins/aave/StaticATokenLM.sol#L268-L276
- https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/StRSRVotes.sol#L128
- https://github.com/reserve-protocol/protocol/blob/a673cfdd7df7e06d52c3754bb0285175ed54f281/contracts/p1/mixins/TradeLib.sol#L170
- https://github.com/reserve-protocol/protocol/blob/d224c14c398d2727d39d133aa7511e1e6b161833/contracts/p1/StRSR.sol#L777-L779

<br>

## Empty code blocks should be removed or emit something
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L838
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/Component.sol#L25
- https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/assets/Asset.sol#L164
- https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/assets/ATokenFiatCollateral.sol#L40
- https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/assets/RTokenAsset.sol#L119
- https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/governance/Governance.sol#L44

<br>

## Use storage for local structs/arrays to save gas
When fetching data from a storage location, assigning the data to a memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declaring the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct.
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L218
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L219
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L220
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L169
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L225
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L541
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L545
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L642
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Deployer.sol#L230
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L105
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L237
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L465
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L666
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L750
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RecollateralizationLib.sol#L433
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RewardableLib.sol#L62
- https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/mixins/RewardableLib.sol#L64
- https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L389
- https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L411
- https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L545
- https://github.com/reserve-protocol/protocol/blob/master/contracts/plugins/aave/StaticATokenLM.sol#L582