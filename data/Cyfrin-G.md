
### **Gas Optimisations**
#### **[G-01] Call functions on contracts directly**
##### **Context:**
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L208
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L452
##### **Description:**
The `Main` contract is used to retrive addresses for calling functions on other contracts. This is not necessary as the contract addresses are already known and can be called directly, saving an external call.
##### **Recommendation**:
`components.stRSR.init(...)` & `furnace.melt()`.

#### **[G-02] Remove duplicate collateral status check**
##### **Context:**
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L255
##### **Description:**
There is duplicate validation in `issue` that the collateral status is sound. This second check is not necessary as the first `require` statement on [line 217](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L217) will cause the transaction to revert if the collateral status is not sound.
##### **Recommendation**:
Remove the duplicate check.

#### **[G-03] Perform post-increment inside event**
##### **Context:**
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L316-L326
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L574-L593
##### **Description:**
Incrementing of variables can occur within the event, saving gas.
##### **Recommendation**:
In RToken, performing `queue.right++` within the event removes the need for emitting `queue.right - 1` as the increment will happen after the event is emitted.
In `StRSR`, performing a pre-increment within the events leads to the correct incremented `era` values being emitted.