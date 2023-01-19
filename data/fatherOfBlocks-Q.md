**contracts/p1/StRSRVotes**
- L14 - The contract is called StRSRP1Votes, but the file StRSRVotes, this can cause confusion, so the same name should be used if possible.


**contracts/p1/StRSR**
- L4/6/13 - Several contracts are imported that are never used, therefore they should be deleted.

- L33 - The contract is called StRSRP1, but the StRSR file, this can cause confusion, so the same name should be used if possible.


**contracts/p1/RToken**
- L24 - The contract is called RTokenP1, but the RToken file, this can cause confusion, so the same name should be used if possible.


**contracts/p1/RevenueTrader**
- L14 - The contract is called RevenueTraderP1, but the RevenueTrader file, this can cause confusion, so the same name should be used if possible.


**contracts/p1/Main**
- L6 - OwnableUpgradeable is imported but is never used, so it should be removed.


**contracts/p1/Deployer**
- L28 - The contract is called DeployerP1, but the Deployer file, this can cause confusion, so the same name should be used if possible.


**contracts/p1/Broker**
- L18 - The contract is called BrokerP1, but the Broker file, this can cause confusion, so the same name should be used if possible.


**contracts/p1/BasketHandler**
- L556 - When an assert is made, a message should be sent so that when the user sees it, he understands why it was reverted.

- L110 - The contract is called BasketHandlerP1, but the BasketHandler file, this can cause confusion, so the same name should be used if possible.


**contracts/p1/BackingManager**
- L249 - When an assert is made, a message should be sent so that when the user sees it, he understands why it was reverted.

- L20 - The contract is called BackingManagerP1, but the BackingManager file, this can cause confusion, so the same name should be used if possible.


**contracts/p1/AssetRegistry**
- L12 - The contract is called AssetRegistryP1, but the AssetRegistry file, this can cause confusion, so the same name should be used if possible.


**contracts/p1/mixins/Component**
- L14 - The contract is called ComponentP1, but the Component file, this can cause confusion, so the same name should be used if possible.

- L110 - When an assert is made, a message should be sent so that when the user sees it, he understands why it was reverted.


**contracts/p1/mixins/RewardableLib**
- L15 - The contract is called RewardableLibP1, but the RewardableLib file, this can cause confusion, so the same name should be used if possible.

- L102 - When an assert is made, a message should be sent so that when the user sees it, he understands why it was reverted.


**contracts/p1/mixins/TradeLib**
- L4/6 - Several contracts are imported that are never used, therefore they should be deleted.

- L44/108/168/170 - When an assert is made, a message should be sent so that the user, when he sees it, understands why it was reverted.


**contracts/p1/mixins/Trading**
- L114 - When an assert is made, a message should be sent so that when the user sees it, he understands why it was reverted.


**contracts/libraries/String**
- L5 - The contract is called StringLib, but the String file, this can cause confusion, so the same name should be used if possible.


**contracts/libraries/RedemptionBattery**
- L4 - IRToken is imported but never used, therefore it should be removed.

- L19 - The contract is called RedemptionBatteryLib, but the RedemptionBattery file, this can cause confusion, so the same name should be used if possible.


**contracts/libraries/Permit**
- L9 - The contract is called PermitLib, but the Permit file, this can cause confusion, so the same name should be used if possible.


**contracts/libraries/Array**
- L6 - The contract is called ArrayLib, but the Array file, this can cause confusion, so the same name should be used if possible.


**contracts/interfaces/IAsset**
- L6/7 - Several contracts are imported that are never used, therefore they should be deleted.


**contracts/interfaces/IBasketHandler**
- L5/6 - Several contracts are imported that are never used, therefore they should be deleted.


**contracts/interfaces/IDeployer**
- L6 - IDistributor is imported but never used, so it should be removed.


**contracts/interfaces/IFacadeTest**
- L5 - IStRSR is imported but never used, so it should be removed.


**contracts/interfaces/IFacadeWrite**
- L4 - IDeployer is imported but never used, so it should be removed.


**contracts/interfaces/IFurnace**
- L4 - Fixed is imported but never used, so it should be removed.


**contracts/interfaces/IMAin**
- L10/16 - Two contracts are imported that are never used, therefore they should be deleted.


**contracts/interfaces/IRewardable**
- L5/6 - Two contracts are imported that are never used, therefore they should be deleted.


**contracts/interfaces/IRToken**
- L7/8 - Two contracts are imported that are never used, therefore they should be deleted.


**contracts/interfaces/IStRSR**
- L7 - Fixed is imported but never used, so it should be removed.


**contracts/interfaces/ITrading**
- L5/6 - Two contracts are imported that are never used, therefore they should be deleted.


**contracts/maxins/ComponentRegistry**
- L5/6 - Two contracts are imported that are never used, therefore they should be deleted.


**contracts/plugins**
The project is mixed between interfaces and contracts, this could be separated by folders for greater order.


**contracts/plugins/Trading/GnosisTrade**
- L63/98/182 - When an assert is made, a message should be sent so that when the user sees it, he understands why it was reverted.


**contracts/plugins/assets/asset**
- L98/112/147 - When an assert is made, a message should be sent so that when the user sees it, he understands why it was reverted.
	

**contracts/plugins/assets/CTokenNonFiatCollateral**
- L6 - ICToken is imported but never used, therefore it should be removed.


**contracts/plugins/assets/EURFiatCollateral**
- L4 - IERC20Metadata is imported but never used, therefore it should be removed.


**contracts/plugins/assets/FiatCollateral**
- L137 - When an assert is made, a message should be sent so that when the user sees it, he understands why it was reverted.


**contracts/plugins/assets/NonFiatCollateral**
- L4 - IERC20Metadata is imported but never used, therefore it should be removed.


**contracts/plugins/assets/OracleLib**
- L5 - Fixed is imported but never used, so it should be removed.

