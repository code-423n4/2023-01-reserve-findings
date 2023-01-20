## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| [GAS-1](#GAS-1) | ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW | 60 |
| [GAS-2](#GAS-2) | X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES | 36 |
| [GAS-3](#GAS-3) | SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS | 32 |
| [GAS-4](#GAS-4) | NO NEED TO EXPLICITLY INITIALIZE VARIABLES WITH DEFAULT VALUES | 2 |
| [GAS-5](#GAS-5) | USE CALLDATA INSTEAD OF MEMORY FOR FUNCTION ARGUMENTS THAT DO NOT GET MUTATED | 7 |
| [GAS-6](#GAS-6) | USE ASSEMBLY TO CHECK FOR ADDRESS(0) | 91 |
| [GAS-7](#GAS-7) | NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS | 8 |
| [GAS-8](#GAS-8) | USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD | 21 |
| [GAS-9](#GAS-9) | STATE VARIABLES CAN BE PACKED INTO FEWER STORAGE SLOTS | 1 |

### [GAS-1] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.

*Instances (60):*
* [`Array.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L11)
* [`Array.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L12)
* [`Array.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L23)
* [`Fixed.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L165)
* [`Fixed.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L169)
* [`String.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14)
* [`AssetRegistry.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L38)
* [`AssetRegistry.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L49)
* [`AssetRegistry.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L127)
* [`AssetRegistry.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L138)
* [`BackingManager.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L221)
* [`BackingManager.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L238)
* [`BasketHandler.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L70)
* [`BasketHandler.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L78)
* [`BasketHandler.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L218)
* [`BasketHandler.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L227)
* [`BasketHandler.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L262)
* [`BasketHandler.sol` Instance 6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L286)
* [`BasketHandler.sol` Instance 7](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L337)
* [`BasketHandler.sol` Instance 8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L397)
* [`BasketHandler.sol` Instance 9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L416)
* [`BasketHandler.sol` Instance 10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L437)
* [`BasketHandler.sol` Instance 11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L530)
* [`BasketHandler.sol` Instance 12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L548)
* [`BasketHandler.sol` Instance 13](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L553)
* [`BasketHandler.sol` Instance 14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L586)
* [`BasketHandler.sol` Instance 15](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L597)
* [`BasketHandler.sol` Instance 16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L598)
* [`BasketHandler.sol` Instance 17](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L611)
* [`BasketHandler.sol` Instance 18](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L621)
* [`BasketHandler.sol` Instance 19](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L643)
* [`BasketHandler.sol` Instance 20](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L653)
* [`BasketHandler.sol` Instance 21](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L707)
* [`BasketHandler.sol` Instance 22](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L725)
* [`Distributor.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L108)
* [`Distributor.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L128)
* [`Distributor.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L133)
* [`Distributor.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L143)
* [`RToken.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L270)
* [`RToken.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L303)
* [`RToken.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L316)
* [`RToken.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L329)
* [`RToken.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L334)
* [`RToken.sol` Instance 6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L478)
* [`RToken.sol` Instance 7](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L501)
* [`RToken.sol` Instance 8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L674)
* [`RToken.sol` Instance 9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L683)
* [`RToken.sol` Instance 10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L711)
* [`RToken.sol` Instance 11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L757)
* [`RToken.sol` Instance 12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L767)
* [`RToken.sol` Instance 13](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L793)
* [`StRSR.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L578)
* [`StRSR.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L590)
* [`RecollateralizationLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L242)
* [`RecollateralizationLib.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L329)
* [`RecollateralizationLib.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L437)
* [`RewardableLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L27)
* [`RewardableLib.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L67)
* [`RewardableLib.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L73)
* [`Trading.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L121)

###  [GAS-2] X += Y COSTS MORE GAS THAN X = X + Y FOR STATE VARIABLES

*Instances (36)*:
* [`Fixed.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L104)
* [`Fixed.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L509)
* [`Fixed.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L537)
* [`Fixed.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L539)
* [`Fixed.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L392)
* [`Fixed.sol` Instance 6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L504)
* [`Fixed.sol` Instance 7](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L505)
* [`Fixed.sol` Instance 8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L555)
* [`Auth.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L136)
* [`BasketHandler.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L345)
* [`BasketHandler.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L346)
* [`BasketHandler.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L636)
* [`Distributor.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L145)
* [`Distributor.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L146)
* [`RToken.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L330)
* [`RToken.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L678)
* [`RToken.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L687)
* [`RToken.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L761)
* [`RToken.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L771)
* [`StRSR.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L229)
* [`StRSR.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L396)
* [`StRSR.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L403)
* [`StRSR.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L412)
* [`StRSR.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L417)
* [`StRSR.sol` Instance 6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L513)
* [`StRSR.sol` Instance 7](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L516)
* [`StRSR.sol` Instance 8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L549)
* [`StRSR.sol` Instance 9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L698)
* [`StRSR.sol` Instance 10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L699)
* [`StRSR.sol` Instance 11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L387)
* [`StRSR.sol` Instance 12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L402)
* [`StRSR.sol` Instance 13](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L721)
* [`RecollateralizationLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L264)
* [`RecollateralizationLib.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L271)

###  [GAS-3] SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

Saves 3 gas per instance.

*Instances (32)*:
* [`Auth.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181)
* [`Auth.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188)
* [`BackingManager.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L249)
* [`BasketHandler.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L188)
* [`Broker.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L135)
* [`Deployer.sol` Instance 1-14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48-L65)
* [`Deployer.sol` Instance 15](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109)
* [`Furnace.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89)
* [`RevenueTrader.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72)
* [`RToken.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410)
* [`RToken.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590)
* [`RToken.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L623)
* [`RToken.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L747)
* [`RToken.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813)
* [`StRSR.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813)
* [`StRSR.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L821)
* [`TradeLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L44)
* [`TradeLib.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L108-L113)
* [`Asset.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L50)

### [GAS-4] NO NEED TO EXPLICITLY INITIALIZE VARIABLES WITH DEFAULT VALUES

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value wastes gas.

*Instances (2):*
* [`Fixed.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L50)
* [`Fixed.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L53)

### [GAS-5] USE CALLDATA INSTEAD OF MEMORY FOR FUNCTION ARGUMENTS THAT DO NOT GET MUTATED

Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

*Instances (7):*
* [`Array.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L9)
* [`Array.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol#L21)
* [`Deployer.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L103)
* [`Deployer.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L104)
* [`Deployer.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L107)
* [`Distributor.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L61)
* [`Distributor.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L161)


### [GAS-6] USE ASSEMBLY TO CHECK FOR ADDRESS(0)

Saves 6 gas per instance

*Instances (91):*
* [`Auth.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L94)
* [`ComponentRegistry.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L37)
* [`ComponentRegistry.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L45)
* [`ComponentRegistry.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L53)
* [`ComponentRegistry.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L61)
* [`ComponentRegistry.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L69)
* [`ComponentRegistry.sol` Instance 6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L77)
* [`ComponentRegistry.sol` Instance 7](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L85)
* [`ComponentRegistry.sol` Instance 8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L93)
* [`ComponentRegistry.sol` Instance 9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L101)
* [`ComponentRegistry.sol` Instance 10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L109)
* [`AssetRegistry.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L93)
* [`BasketHandler.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L651)
* [`BasketHandler.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L670)
* [`Broker.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L57)
* [`Broker.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L59)
* [`Deployer.sol` Instance 1-14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L49-L63)
* [`Deployer.sol` Instance 15](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109)
* [`Distributor.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L162)
* [`Main.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L32)
* [`RevenueTrader.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L29)
* [`RevenueTrader.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L54)
* [`StRSR.sol` Instance 1-2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L675-L676)
* [`StRSR.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L695)
* [`StRSR.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L695)
* [`StRSR.sol` Instance 4-5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L701-L702)
* [`StRSR.sol` Instance 4-5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L701-L702)
* [`StRSR.sol` Instance 6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L711)
* [`StRSR.sol` Instance 7-8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L723-L724)
* [`StRSR.sol` Instance 9-10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L732-L733)
* [`StRSRVotes.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L172)
* [`StRSRVotes.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L181)
* [`Component.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L34)
* [`RecollateralizationLib.sol` Instance 1-2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L94)
* [`RecollateralizationLib.sol` Instance 3-4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L401)
* [`Trading.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L68)
* [`Trading.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L114)
* [`ERC20.sol` Instance 1-2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L242-L243)
* [`ERC20.sol` Instance 3-4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L262-L264)
* [`ERC20.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L268)
* [`ERC20.sol` Instance 6-7](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L283-L285)
* [`ERC20.sol` Instance 8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L289)
* [`ERC20.sol` Instance 9-10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L310-L311)
* [`StaticATokenLM.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L94)
* [`StaticATokenLM.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L142)
* [`StaticATokenLM.sol` Instance 3](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L170)
* [`StaticATokenLM.sol` Instance 4](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L210)
* [`StaticATokenLM.sol` Instance 5](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L294)
* [`StaticATokenLM.sol` Instance 6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L317)
* [`StaticATokenLM.sol` Instance 7](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L363)
* [`StaticATokenLM.sol` Instance 8](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L366)
* [`StaticATokenLM.sol` Instance 9](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L369)
* [`StaticATokenLM.sol` Instance 10](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L378)
* [`StaticATokenLM.sol` Instance 11](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L404)
* [`StaticATokenLM.sol` Instance 12](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L473)
* [`StaticATokenLM.sol` Instance 13](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L485)
* [`StaticATokenLM.sol` Instance 14](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L492)
* [`StaticATokenLM.sol` Instance 15](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L531)
* [`StaticATokenLM.sol` Instance 16](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L578)
* [`Asset.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L49)
* [`Asset.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L51)
* [`CTokenFiatCollateral.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L26)
* [`CTokenNonFiatCollateral.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L29)
* [`CTokenSelfReferentialCollateral.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L30)
* [`EURFiatCollateral.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L24)
* [`NonFiatCollateral.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L24)
* [`RTokenAsset.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L28)
* [`GnosisTrade.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/GnosisTrade.sol#L98)
* [`GnosisTrade.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/GnosisTrade.sol#L149)

### [GAS-7] NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

*Instances (8):*
* [`AssetRegistry.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L162)
* [`RToken.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L634)
* [`RecollateralizationLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L65)
* [`TradeLib.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L41)
* [`TradeLib.sol` Instance 2](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L106)
* [`Asset.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L69-L82)
* [`EURFiatCollateral.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L36-L40)
* [`Governance.sol` Instance 1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L90)

### [GAS-8] USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[Checkout this Solidity Doc](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) 

*Instances (21):*

```solidity
File: IDistributor.sol

7:     struct RevenueShare {
8:          uint16 rTokenDist; // {revShare} A value between [0, 10,000]
9:          uint16 rsrDist; // {revShare} A value between [0, 10,000]
10:     }

12:     /// Assumes no more than 1024 independent distributions.
13:     struct RevenueTotals {
14:         uint24 rTokenTotal; // {revShare}
15:         uint24 rsrTotal; // {revShare}
16:     }

```

```solidity
File: StRSRP1Votes.sol

22:     struct Checkpoint {
23:         uint48 fromBlock;
24:         uint224 val;
25:     }

```

```solidity
File: p1/mixins/RecollateralizationLib.sol

24:      struct TradingRules {
25:         uint192 minTradeVolume; // {UoA}
26:         uint192 maxTradeSlippage; // {1}
27:     }

29:     struct TradeInfo {
30:         IAsset sell;
31:         IAsset buy;
32:         uint192 sellAmount; // {sellTok}
33:         uint192 buyAmount; // {buyTok}
34:         uint192 sellPrice; // {UoA/sellTok} can be 0
35:         uint192 buyPrice; // {UoA/buyTok}
36:     }

116:    struct BasketRange {
117:        uint192 top; // {BU}
118:        uint192 bottom; // {BU}
119:    }

284:    struct MaxSurplusDeficit {
285:        CollateralStatus surplusStatus; // starts SOUND
286:        uint192 surplus; // {UoA}
287:        uint192 deficit; // {UoA}
288:    }

```
[Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L24-L36)
```solidity
File: RToken.sol

67:      uint192 public basketsNeeded; // D18{BU}

72:      uint192 private allVestAt; // D18{fractional block number}

74:      uint192 private lastIssRate;

81:      uint192 when;

83:      uint192 amtBaskets;

```
[Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol)

### [GAS-9] STATE VARIABLES CAN BE PACKED INTO FEWER STORAGE SLOTS

*Instances (1):*
```solidity
File: interfaces/IDeployer.sol L19:L48

    struct DeploymentParams {
        // === Revenue sharing ===
        RevenueShare dist; // revenue sharing splits between RToken and RSR
        //
        // === Trade sizing ===
        uint192 minTradeVolume; // {UoA}
        uint192 rTokenMaxTradeVolume; // {UoA}
        //
        // === Freezing ===
        uint48 shortFreeze; // {s} how long an initial freeze lasts
        uint48 longFreeze; // {s} how long each freeze extension lasts
        //
        // === Rewards (Furnace + StRSR) ===
        uint192 rewardRatio; // the fraction of available revenues that stRSR holders get each PayPeriod
        uint48 rewardPeriod; // {s} the atomic unit of rewards, determines # of exponential rounds
        //
        // === StRSR ===
        uint48 unstakingDelay; // {s} the "thawing time" of staked RSR before withdrawal
        //
        // === BackingManager ===
        uint48 tradingDelay; // {s} how long to wait until starting auctions after switching basket
        uint48 auctionLength; // {s} the length of an auction
        uint192 backingBuffer; // {1} how much extra backing collateral to keep
        uint192 maxTradeSlippage; // {1} max slippage acceptable in a trade
        //
        // === RToken ===
        uint192 issuanceRate; // {1/block} number of RToken to issue per block / (RToken value)
        uint192 scalingRedemptionRate; // {1/hour} max fraction of supply that can be redeemed hourly
        uint256 redemptionRateFloor; // {qRTok/hour} the lowest possible hourly redemption limit
    }

```
[Link to Code](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IDeployer.sol#L19-L48)