## [Gas - 01] No need to explicitly initialize variables with default values

Declaration of variables with default values cost gas. such as

`uint256 a = 0;`  change to → `uint256 a;`


*Instances of this issue :* 

* [AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol) :  L38, L49, L127, L138
* [BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol) : L221, L238
* [BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol) : L70, L78, L218, L227, L262, L286, L337, L397, L416, L437, L530, L548, L553, L586, L592, L57, L606, L611, L643, L653, L707, L725
* [Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol) : L108, L133, L143
* [RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol) : L270, L303, L329, L334, L478, L501, L674, L683, L711, L757, L767, L793
* [StRSRVotes.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol) : L102
* [RecollateralizationLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol) : L242, L329, L437
* [RewardableLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol) : L27, L67, L73
* [String.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol) : 14


## [Gas - 02] An array's length should be cached to save gas in for-loops

change ```for(int i; i<arr.length; ++i)``` to 

``` 
int len = arr.length;
for(int i; i<len; ++i)
```

*Instances of this issue :* 

* [BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol) : L218, L227, L262, L653, L707, L725
* [RToken.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol) : L270
* [RecollateralizationLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol) : L242, L329
* [RewardableLib.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol) : L27
* [String.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol) : 14


## [Gas - 03] Use Shift Right/Left instead of Division/Multiplication 
    
    `uint256 b = a / 2;`  →  `uint256 b = a >> 1;`    
    
    `uint256 c = a / 4;`  → `uint256 b = a >> 2;`    
    
    `uint256 d = a * 8;`   →  `uint256 b = a << 3;`

*Instances of this issue :* 

* [BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol) : L372

## [Gas - 04] Using private rather than public for constants, saves gas


*Instances of this issue :* 

* [BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol) : L33, L34
* [BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol) : L117
* [Broker.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol) : L24
* [Deployer.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol) : L31
* [Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol) : L31, L32, L34
* [Furnace.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol) : L15, L16
* [StRSR.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol) : L37, L38, L39, L45
* [Trading.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol) : L20, L21
* [Auth.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol) : L7, L8, L9, L10, L27, L28, L29, L30
* [Fixed.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol) : L33, L36, L37, L41, L44, L48, L50, L51, L52, L53
* [RedemptionBattery.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol) : L11


## [Gas - 05] Prefix increment cheaper than postfix increments 

++i costs less gas than i++, especially when it’s used in for-loops (—i/i— too) Saves 5 gas PER LOOP

*Instances of this issue :* 

* [BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol) : L653
* [String.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol) : 14

## [Gas - 06] Use `address(this)` instead `this`

`this` was supported in the 0.5.x version 

*Instances of this issue :* 

* [BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol) : L135


