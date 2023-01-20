## Gas Report

## ADD UNCHECKED {} FOR ITERATOR WHERE THE OPERANDS CANNOT OVERFLOW BECAUSE ITS ALLWAYS BELOW THE GIVEN NUMBER.

for loops j++ and i++ can be set to UNCHECKED{++j} and UNCHECKED{++i} including length.  Cash Array.lenght into variable so that it will evaluate array.lenght
overflow. So no need to check array.length overflow every iteration.    

Recommended Mitigation Steps 
     UNCHECKED{
     ++i ;
     array.length ;
     }


Affected Source Code
Total instances : 42

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/AssetRegistry.sol#L38

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/AssetRegistry.sol#L49

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/AssetRegistry.sol#L127

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/AssetRegistry.sol#L138

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L221

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L238

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L70

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L78

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L218

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#l227

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L262

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L286

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L337

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L397

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L416

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L437

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L548

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L553

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L586

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L597

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L611

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L707

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L725

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L108

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L133

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L143

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L270

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L303

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L329

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L334

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L674

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L683

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L711

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L757

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L767

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L793

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L802

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L242

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L67

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L73


 ## SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
 
 Affected Source Code
Total instances : 5
 
 https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Deployer.sol#L49
 
 https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L590
 
 https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L623
 
 https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L741
 
 https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L813
 
