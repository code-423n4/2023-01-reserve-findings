### Gas Optimizations Report
| No. | Issue |
| --- | --- |
| 1 | [Splitting `require` which contains `&&` saves gas](#G-01-splitting-require-which-contains--saves-gas)|
| 2 | [Using `uint256` instead of `bool` in mappings is more gas efficient [146 gas per instance]](#G-02-using-uint256-instead-of-bool-in-mappings-is-more-gas-efficient-146-gas-per-instance)|
| 3 | [Use Custom Errors rather than revert() / require() strings to save deployment gas [68 gas per instance]](#G-03-use-custom-errors-rather-than-revert--require-strings-to-save-deployment-gas-68-gas-per-instance)|
| 4 | [Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead](#G-04-usage-of-uintint-smaller-than-32-bytes-256-bits-incurs-overhead)|
| 5 | [`bytes` constants are more efficient than `string` constants](#G-05-bytes-constants-are-more-efficient-than-string-constants)|
---
### [G-01] Splitting `require` which contains `&&` saves gas

---
**Description:**

Using multiple require instead of operator && can save more gas. When a require statement has 2 or more expressions with &&, separate them into individual expression results in lesser gas.

**Lines of Code:** 

- [Auth.sol#L181](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181)
- [Auth.sol#L188](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188)
- [BasketHandler.sol#L186-L190](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L186-L190)
- [Broker.sol#L134-L137](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L134-L137)
- [Deployer.sol#L48-L65](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48-L65)
- [Deployer.sol#L109](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109)
- [Furnace.sol#L89](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89)
- [RToken.sol#L410](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410)
- [RToken.sol#L590](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590)
- [RToken.sol#L623](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L623)
- [RToken.sol#L741](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L741)
- [RToken.sol#L813](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813)
- [RevenueTrader.sol#L72](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72)
- [StRSR.sol#L813](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813)
- [StRSR.sol#L821](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L821)
- [Asset.sol#L50](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L50)
- [EasyAuction.sol#L37-L42](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L37-L42)
- [EasyAuction.sol#L376-L379](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L376-L379)
- [EasyAuction.sol#L259-L262](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L259-L262)

---
### [G-02] Using `uint256` instead of `bool` in mappings is more gas efficient [146 gas per instance]

---
**Description:**

OpenZeppelin uint256 and bool comparison: 

Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled. The values being non-zero value makes deployment a bit more expensive.

Use uint256(1) and uint256(2) for true/false to avoid a Gwarmaccess (100 gas) for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

**Recommendation:**

Use uint256(1) and uint256(2) instead of bool.

**Lines of Code:** 

- [Broker.sol#L44](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L44)
- [BadERC20.sol#L15](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol#L15)
- [InvalidBrokerMock.sol#L20](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L20)

---
### [G-03] Use Custom Errors rather than revert() / require() strings to save deployment gas [68 gas per instance]

---
**Description:**

Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they’re hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas.

<https://blog.soliditylang.org/2021/04/21/custom-errors/>

**Recommendation:**

Use the Custom Errors feature.

**Lines of Code:** 

- [Permit.sol#L18-L22](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L18-L22)
- [Permit.sol#L24-L31](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L24-L31)
- [RedemptionBattery.sol#L44](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol#L44)
- [Auth.sol#L94](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L94)
- [Auth.sol#L181](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181)
- [Auth.sol#L188](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188)
- [Auth.sol#L197](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L197)
- [ComponentRegistry.sol#L37](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L37)
- [ComponentRegistry.sol#L45](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L45)
- [ComponentRegistry.sol#L53](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L53)
- [ComponentRegistry.sol#L61](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L61)
- [ComponentRegistry.sol#L69](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L69)
- [ComponentRegistry.sol#L77](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L77)
- [ComponentRegistry.sol#L85](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L85)
- [ComponentRegistry.sol#L93](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L93)
- [ComponentRegistry.sol#L101](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L101)
- [ComponentRegistry.sol#L109](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L109)
- [AssetRegistry.sol#L74](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L74)
- [AssetRegistry.sol#L88](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L88)
- [AssetRegistry.sol#L89](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L89)
- [AssetRegistry.sol#L103](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L103)
- [AssetRegistry.sol#L111](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L111)
- [AssetRegistry.sol#L112](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L112)
- [AssetRegistry.sol#L151-L154](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L151-L154)
- [BackingManager.sol#L73](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L73)
- [BackingManager.sol#L85](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L85)
- [BackingManager.sol#L97](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L97)
- [BackingManager.sol#L111](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L111)
- [BackingManager.sol#L257](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L257)
- [BackingManager.sol#L264](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L264)
- [BasketHandler.sol#L168](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L168)
- [BasketHandler.sol#L186-L190](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L186-L190)
- [BasketHandler.sol#L213](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L213)
- [BasketHandler.sol#L214](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L214)
- [BasketHandler.sol#L230](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L230)
- [BasketHandler.sol#L231](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L231)
- [BasketHandler.sol#L232](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L232)
- [BasketHandler.sol#L265](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L265)
- [BasketHandler.sol#L654](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L654)
- [BasketHandler.sol#L655](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L655)
- [BasketHandler.sol#L656](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L656)
- [BasketHandler.sol#L657](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L657)
- [BasketHandler.sol#L660](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L660)
- [Broker.sol#L57](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L57)
- [Broker.sol#L58-L61](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L58-L61)
- [Broker.sol#L86](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L86)
- [Broker.sol#L89-L94](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L89-L94)
- [Broker.sol#L125](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L125)
- [Broker.sol#L134-L137](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L134-L137)
- [Deployer.sol#L48-L65](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48-L65)
- [Deployer.sol#L109](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109)
- [Distributor.sol#L92](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L92)
- [Distributor.sol#L98](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L98)
- [Distributor.sol#L162](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L162)
- [Distributor.sol#L163](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L163)
- [Distributor.sol#L164](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L164)
- [Distributor.sol#L165](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165)
- [Distributor.sol#L166](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L166)
- [Distributor.sol#L172](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L172)
- [Distributor.sol#L182](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L182)
- [Furnace.sol#L89](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89)
- [Furnace.sol#L97](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L97)
- [Main.sol#L32](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L32)
- [Main.sol#L48](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L48)
- [RToken.sol#L156](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L156)
- [RToken.sol#L157](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L157)
- [RToken.sol#L158](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L158)
- [RToken.sol#L191](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L191)
- [RToken.sol#L217](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L217)
- [RToken.sol#L384](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L384)
- [RToken.sol#L410](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410)
- [RToken.sol#L440](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L440)
- [RToken.sol#L448](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L448)
- [RToken.sol#L558](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L558)
- [RToken.sol#L581](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L581)
- [RToken.sol#L590](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590)
- [RToken.sol#L603](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L603)
- [RToken.sol#L623](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L623)
- [RToken.sol#L741](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L741)
- [RToken.sol#L747](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L747)
- [RToken.sol#L813](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813)
- [RToken.sol#L833](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L833)
- [RevenueTrader.sol#L29](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L29)
- [RevenueTrader.sol#L72](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72)
- [StRSR.sol#L168](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L168)
- [StRSR.sol#L169](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L169)
- [StRSR.sol#L213](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L213)
- [StRSR.sol#L259](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L259)
- [StRSR.sol#L260](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L260)
- [StRSR.sol#L305](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L305)
- [StRSR.sol#L306](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L306)
- [StRSR.sol#L312](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L312)
- [StRSR.sol#L313](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L313)
- [StRSR.sol#L375](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L375)
- [StRSR.sol#L376](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L376)
- [StRSR.sol#L380](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L380)
- [StRSR.sol#L660](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L660)
- [StRSR.sol#L675](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L675)
- [StRSR.sol#L676](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L676)
- [StRSR.sol#L680](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L680)
- [StRSR.sol#L695](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L695)
- [StRSR.sol#L711](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L711)
- [StRSR.sol#L717](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L717)
- [StRSR.sol#L732](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L732)
- [StRSR.sol#L733](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L733)
- [StRSR.sol#L746](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L746)
- [StRSR.sol#L760](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L760)
- [StRSR.sol#L775](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L775)
- [StRSR.sol#L813](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813)
- [StRSR.sol#L816](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L816)
- [StRSR.sol#L821](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L821)
- [StRSR.sol#L824](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L824)
- [StRSR.sol#L829](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L829)
- [StRSRVotes.sol#L77](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L77)
- [StRSRVotes.sol#L83](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L83)
- [StRSRVotes.sol#L89](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L89)
- [StRSRVotes.sol#L126](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L126)
- [StRSRVotes.sol#L133](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L133)
- [Component.sol#L34](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L34)
- [Component.sol#L42](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L42)
- [Component.sol#L47](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L47)
- [Component.sol#L52](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L52)
- [RewardableLib.sol#L96](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L96)
- [Trading.sol#L69](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L69)
- [Trading.sol#L129](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L129)
- [Trading.sol#L136](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L136)
- [ERC20.sol#L242](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L242)
- [ERC20.sol#L243](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L243)
- [ERC20.sol#L262](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L262)
- [ERC20.sol#L283](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L283)
- [ERC20.sol#L310](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L310)
- [ERC20.sol#L311](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L311)
- [ReentrancyGuard.sol#L51](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ReentrancyGuard.sol#L51)
- [Asset.sol#L48](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L48)
- [Asset.sol#L49](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L49)
- [Asset.sol#L50](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L50)
- [Asset.sol#L51](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L51)
- [Asset.sol#L52](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L52)
- [Asset.sol#L53](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L53)
- [CTokenFiatCollateral.sol#L26](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L26)
- [CTokenNonFiatCollateral.sol#L28-L31](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L28-L31)
- [CTokenSelfReferentialCollateral.sol#L29](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L29)
- [CTokenSelfReferentialCollateral.sol#L30](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L30)
- [EURFiatCollateral.sol#L24](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L24)
- [FiatCollateral.sol#L73](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L73)
- [FiatCollateral.sol#L75](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol#L75)
- [NonFiatCollateral.sol#L24](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L24)
- [RTokenAsset.sol#L28](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L28)
- [RTokenAsset.sol#L29](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L29)
- [SelfReferentialCollateral.sol#L19](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/SelfReferentialCollateral.sol#L19)
- [Governance.sol#L102](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L102)
- [Governance.sol#L112](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L112)
- [Governance.sol#L123](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L123)
- [EasyAuction.sol#L19-L22](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L19-L22)
- [EasyAuction.sol#L27-L30](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L27-L30)
- [EasyAuction.sol#L37-L42](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L37-L42)
- [EasyAuction.sol#L48-L51](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L48-L51)
- [EasyAuction.sol#L131](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L131)
- [EasyAuction.sol#L164](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L164)
- [EasyAuction.sol#L165](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L165)
- [EasyAuction.sol#L166-L169](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L166-L169)
- [EasyAuction.sol#L170-L173](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L170-L173)
- [EasyAuction.sol#L174](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L174)
- [EasyAuction.sol#L261-L268](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L261-L268)
- [EasyAuction.sol#L278-L282](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L278-L282)
- [EasyAuction.sol#L289](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L289)
- [EasyAuction.sol#L292](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L292)
- [EasyAuction.sol#L326](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L326)
- [EasyAuction.sol#L350](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L350)
- [EasyAuction.sol#L356-L359](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L356-L359)
- [EasyAuction.sol#L372-L375](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L372-L375)
- [EasyAuction.sol#L376-L379](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L376-L379)
- [EasyAuction.sol#L381-L386](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L381-L386)
- [EasyAuction.sol#L516](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L516)
- [EasyAuction.sol#L526](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L526)
- [EasyAuction.sol#L607](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L607)
- [GnosisMock.sol#L69](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L69)
- [GnosisMock.sol#L91](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L91)
- [GnosisMock.sol#L92](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L92)
- [GnosisMock.sol#L100](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L100)
- [GnosisMock.sol#L101](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L101)
- [GnosisMockReentrant.sol#L33](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol#L33)
- [GnosisMockReentrant.sol#L61](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol#L61)
- [GnosisMockReentrant.sol#L62](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol#L62)
- [InvalidBrokerMock.sol#L39](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L39)
- [RTokenCollateral.sol#L43](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/RTokenCollateral.sol#L43)
- [MainV2.sol#L10](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/upgrades/MainV2.sol#L10)
- [EasyAuction.sol#L153-L156](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L153-L156)
- [EasyAuction.sol#L205](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L205)
- [EasyAuction.sol#L258](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L258)
- [EasyAuction.sol#L259-L262](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L259-L262)
- [EasyAuction.sol#L362](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L362)
- [EasyAuction.sol#L370](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L370)
- [EasyAuction.sol#L375](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L375)
- [EasyAuction.sol#L377](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L377)
- [EasyAuction.sol#L460](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L460)
- [EasyAuction.sol#L519](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L519)
- [EasyAuction.sol#L623](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L623)
- [EasyAuction.sol#L628](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L628)
- [EasyAuction.sol#L637](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L637)
- [EasyAuction.sol#L638](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L638)
- [EasyAuction.sol#L657](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L657)
- [EasyAuction.sol#L662](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L662)
- [EasyAuction.sol#L718](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L718)
- [EasyAuction.sol#L722](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L722)
- [EasyAuction.sol#L793](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L793)
- [EasyAuction.sol#L794](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L794)
- [EasyAuction.sol#L826](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L826)
- [EasyAuction.sol#L915](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L915)
- [EasyAuction.sol#L936](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L936)
- [GnosisTrade.sol#L60](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L60)
- [GnosisTrade.sol#L88](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L88)
- [GnosisTrade.sol#L89](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L89)
- [GnosisTrade.sol#L95](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L95)
- [GnosisTrade.sol#L96](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L96)
- [GnosisTrade.sol#L174](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L174)
- [GnosisTrade.sol#L216](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L216)

---
### [G-04] Usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

---
**Description:**

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size. Each operation involving a uint8 costs an extra 22-28 gas (depending on whether the other operand is also a variable of type uint8) as compared to ones involving uint256, due to the compiler having to clear the higher bits of the memory word before operating on the uint8, as well as the associated stack operations of doing so.

**Recommendation:**

Use a larger size then downcast where needed

**Lines of Code:** 

- [IAsset.sol#L41](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IAsset.sol#L41)
- [Fixed.sol#L36-L37](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol#L36-L37)
- [Permit.sol#L13](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L13)
- [String.sol#L16](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L16)
- [String.sol#L16](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L16)
- [String.sol#L18](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L18)
- [Distributor.sol#L34](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L34)
- [StRSR.sol#L45](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L45)
- [StRSR.sol#L771](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L771)
- [StRSRVotes.sol#L122](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L122)
- [ERC20.sol#L46](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L46)
- [ERC20.sol#L91](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L91)
- [ERC20.sol#L324](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L324)
- [IAaveIncentivesController.sol#L127](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IAaveIncentivesController.sol#L127)
- [IStaticATokenLM.sol#L11](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L11)
- [IStaticATokenLM.sol#L82](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/IStaticATokenLM.sol#L82)
- [StaticATokenLM.sol#L138](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L138)
- [Asset.sol#L17](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L17)
- [CTokenFiatCollateral.sol#L20](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L20)
- [CTokenSelfReferentialCollateral.sol#L18](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L18)
- [CTokenSelfReferentialCollateral.sol#L26](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L26)
- [OracleLib.sol#L19](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L19)
- [OracleLib.sol#L19](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol#L19)
- [RTokenAsset.sol#L22](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L22)
- [ATokenMock.sol#L50](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol#L50)
- [BadERC20.sol#L31](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol#L31)
- [CTokenMock.sol#L23](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/CTokenMock.sol#L23)
- [ChainlinkMock.sol#L19](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L19)
- [ChainlinkMock.sol#L31](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L31)
- [ChainlinkMock.sol#L57](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L57)
- [ChainlinkMock.sol#L71](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L71)
- [ChainlinkMock.sol#L76](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L76)
- [ChainlinkMock.sol#L80](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L80)
- [ChainlinkMock.sol#L98](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L98)
- [ChainlinkMock.sol#L102](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L102)
- [ChainlinkMock.sol#L106](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L106)
- [ChainlinkMock.sol#L110](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ChainlinkMock.sol#L110)
- [InvalidChainlinkMock.sol#L15](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L15)
- [InvalidChainlinkMock.sol#L24](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L24)
- [InvalidChainlinkMock.sol#L28](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L28)
- [InvalidChainlinkMock.sol#L38](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L38)
- [InvalidChainlinkMock.sol#L42](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L42)
- [USDCMock.sol#L10](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/USDCMock.sol#L10)
- [WBTCMock.sol#L10](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WBTCMock.sol#L10)
- [WETH.sol#L23](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L23)
- [ZeroDecimalMock.sol#L10](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ZeroDecimalMock.sol#L10)

---
### [G-05] `bytes` constants are more efficient than `string` constants

---
**Description:**

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

**Lines of Code:** 

- [Deployer.sol#L31](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L31)
- [StaticATokenErrors.sol#L5](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L5)
- [StaticATokenErrors.sol#L6](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L6)
- [StaticATokenErrors.sol#L7](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L7)
- [StaticATokenErrors.sol#L8](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L8)
- [StaticATokenErrors.sol#L9](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L9)
- [StaticATokenErrors.sol#L10](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L10)
- [StaticATokenErrors.sol#L11](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenErrors.sol#L11)