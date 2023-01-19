**contracts/p1/StRSRVotes**
- L78/79/84/85 - When a variable is only used once, it could save gas by not creating the variable and using the operation directly inside where it is needed.


**contracts/p1/StRSR**
- L222/224/225/322/324 - When a variable is only used once, it could save gas by not creating the variable and using the operation directly inside where it is needed.

- L380/660/675/676/680/711/717/732/733/816/824 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/p1/RToken**
- L270/303/316/329/334/478/501/674/683/711/757/767/793 - Instead of variable-- it is less expensive to do unchecked{--variable;}

- L446/463/809/810/813 - When a variable is only used once, it could save gas by not creating the variable and using the operation directly inside where it is needed.


**contracts/p1/Main**
- L77/79 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.


**contracts/p1/Distributor**
- L115/126 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.

- L108/128/133/143 - Instead of variable-- it is less expensive to do unchecked{--variable;}


**contracts/p1/Broker**
- L60 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/p1/BasketHandler**
- L231/657 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS


**contracts/p1/AssetRegistry**
- L76/78/90/96 - When a variable is only used once, it could save gas by not creating the variable and using the operation directly inside where it is needed.

- L38/49/127/138 - Instead of variable-- it is less expensive to do unchecked{--variable;}


**contracts/p1/mixins/Component**
- L172/175/185/191 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.


**contracts/p1/mixins/TradeLib**
- L122/126/131 - When a variable is only used once, it could save gas by not creating the variable and using the operation directly inside where it is needed.


**contracts/p1/mixins/Trading**
- L72/121 - Instead of variable-- it is less expensive to do unchecked{--variable;}


**contracts/libraries/RedemptionBattery**
- L64/67 - When a variable is only used once, it could save gas by not creating the variable and using the operation directly inside where it is needed.


**contracts/libraries/Array**
- L11/12/23 - Instead of variable-- it is less expensive to do unchecked{--variable;}


**contracts/plugins/Governance/Governance**
- L150/156/171/172/173/174 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.


**contracts/plugins/Trading/GnosisTrade**
- L199/203/229/230 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.


**contracts/plugins/assets/ATokenFiatCollateral**
- L46/47 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.


**contracts/plugins/assets/CTokenFiatCollateral**
- L27/28/45/46/47 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.


**contracts/plugins/assets/CTokenNonFiatCollateral**
- L30 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L51/54 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.


**contracts/plugins/assets/CTokenSelfReferentialCollateral**
- L48/49/50 - When a variable is only used once, gas could be saved by not creating the variable and using the operation directly inside where it is needed.

