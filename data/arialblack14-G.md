# GAS OPTIMIZATION REPORT ⛽

## [G-1] Use calldata instead of memory.

### Description
Use calldata instead of memory for function parameters saves gas if the function argument is only read.

### ✅ Recommendation
Use calldata instead of memory.

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/libraries/String.sol#L11|[function toLower(string memory str) internal pure returns (string memory) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L11 )|
|protocol/contracts/libraries/test/StringCallerMock.sol#L9|[function toLower(string memory str) external pure returns (string memory){](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/test/StringCallerMock.sol#L9 )|
---

## [G-2] `++i/i++` should be `unchecked{++i}`/`unchecked{i++}` when  it is not possible for them to overflow, for example when used in `for` and `while` loops

### Description
In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck [cannot be made inline](https://github.com/ethereum/solidity/issues/10695). 
			Example for loop:

```Solidity
for (uint i = 0; i < length; i++) {
// do something that doesn't change the value of i
}
```

In this example, the for loop post condition, i.e., `i++` involves checked arithmetic, which is not required. This is because the value of i is always strictly less than `length <= 2**256 - 1`. Therefore, the theoretical maximum value of i to enter the for-loop body `is 2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. You should manually do this by:

```Solidity
for (uint i = 0; i < length; i = unchecked_inc(i)) {
	// do something that doesn't change the value of i
}

function unchecked_inc(uint i) returns (uint) {
	unchecked {
		return i + 1;
	}
}
```
Or just:

```Solidity
for (uint i = 0; i < length;) {
	// do something that doesn't change the value of i
	unchecked { i++; 	}
}
```
Note that it’s important that the call to `unchecked_inc` is inlined. This is only possible for solidity versions starting from `0.8.2`.

Gas savings: roughly speaking this can save 30-40 gas per loop iteration. For lengthy loops, this can be significant!
(This is only relevant if you are using the default solidity checked arithmetic.)


### ✅ Recommendation
Use the `unchecked` keyword

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/libraries/String.sol#L14|[for (uint256 i = 0; i < bStr.length; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/String.sol#L14 )|
|protocol/contracts/p1/BasketHandler.sol#L653|[for (uint256 i = 0; i < erc20s.length; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L653 )|
|protocol/contracts/p1/Distributor.sol#L133|[for (uint256 i = 0; i < numTransfers; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L133 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L277|[for (uint256 i = 0; i < _minBuyAmounts.length; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L277 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L288|[for (uint256 i = 0; i < _minBuyAmounts.length; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L288 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L316|[for (uint256 i = 0; i < _sellOrders.length; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L316 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L344|[for (uint256 i = 0; i < iterationSteps; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L344 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L513|[for (uint256 i = 0; i < orders.length; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L513 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L524|[for (uint256 i = 0; i < orders.length; i++) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L524 )|
---

## [G-3] Use assembly to check for address(0)

### Description
You can save about 6 gas per instance if using assembly to check for address(0)

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/mixins/Auth.sol#L94|[require(account != address(0), "cannot grant role to address 0");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L94 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L37|[require(address(val) != address(0), "invalid RToken address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L37 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L45|[require(address(val) != address(0), "invalid StRSR address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L45 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L53|[require(address(val) != address(0), "invalid AssetRegistry address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L53 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L61|[require(address(val) != address(0), "invalid BasketHandler address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L61 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L69|[require(address(val) != address(0), "invalid BackingManager address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L69 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L77|[require(address(val) != address(0), "invalid Distributor address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L77 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L85|[require(address(val) != address(0), "invalid RSRTrader address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L85 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L93|[require(address(val) != address(0), "invalid RTokenTrader address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L93 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L101|[require(address(val) != address(0), "invalid Furnace address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L101 )|
|protocol/contracts/mixins/ComponentRegistry.sol#L109|[require(address(val) != address(0), "invalid Broker address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L109 )|
|protocol/contracts/p1/Broker.sol#L57|[require(address(gnosis_) != address(0), "invalid Gnosis address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L57 )|
|protocol/contracts/p1/Broker.sol#L59|[address(tradeImplementation_) != address(0),](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L59 )|
|protocol/contracts/p1/Deployer.sol#L49|[address(rsr_) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L49 )|
|protocol/contracts/p1/Deployer.sol#L50|[address(gnosis_) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L50 )|
|protocol/contracts/p1/Deployer.sol#L51|[address(rsrAsset_) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L51 )|
|protocol/contracts/p1/Deployer.sol#L52|[address(implementations_.main) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L52 )|
|protocol/contracts/p1/Deployer.sol#L53|[address(implementations_.trade) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L53 )|
|protocol/contracts/p1/Deployer.sol#L54|[address(implementations_.components.assetRegistry) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L54 )|
|protocol/contracts/p1/Deployer.sol#L55|[address(implementations_.components.backingManager) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L55 )|
|protocol/contracts/p1/Deployer.sol#L56|[address(implementations_.components.basketHandler) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L56 )|
|protocol/contracts/p1/Deployer.sol#L57|[address(implementations_.components.broker) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L57 )|
|protocol/contracts/p1/Deployer.sol#L58|[address(implementations_.components.distributor) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L58 )|
|protocol/contracts/p1/Deployer.sol#L59|[address(implementations_.components.furnace) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L59 )|
|protocol/contracts/p1/Deployer.sol#L60|[address(implementations_.components.rsrTrader) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L60 )|
|protocol/contracts/p1/Deployer.sol#L61|[address(implementations_.components.rTokenTrader) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L61 )|
|protocol/contracts/p1/Deployer.sol#L62|[address(implementations_.components.rToken) != address(0) &&](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L62 )|
|protocol/contracts/p1/Deployer.sol#L63|[address(implementations_.components.stRSR) != address(0),](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L63 )|
|protocol/contracts/p1/Deployer.sol#L109|[require(owner != address(0) && owner != address(this), "invalid owner");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109 )|
|protocol/contracts/p1/Distributor.sol#L162|[require(dest != address(0), "dest cannot be zero");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L162 )|
|protocol/contracts/p1/Main.sol#L32|[require(address(rsr_) != address(0), "invalid RSR address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L32 )|
|protocol/contracts/p1/RevenueTrader.sol#L29|[require(address(tokenToBuy_) != address(0), "invalid token address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L29 )|
|protocol/contracts/p1/RevenueTrader.sol#L54|[if (address(trades[erc20]) != address(0)) return;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L54 )|
|protocol/contracts/p1/StRSR.sol#L675|[require(from != address(0), "ERC20: transfer from the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L675 )|
|protocol/contracts/p1/StRSR.sol#L676|[require(to != address(0), "ERC20: transfer to the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L676 )|
|protocol/contracts/p1/StRSR.sol#L695|[require(account != address(0), "ERC20: mint to the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L695 )|
|protocol/contracts/p1/StRSR.sol#L711|[require(account != address(0), "ERC20: burn from the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L711 )|
|protocol/contracts/p1/StRSR.sol#L732|[require(owner != address(0), "ERC20: approve from the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L732 )|
|protocol/contracts/p1/StRSR.sol#L733|[require(spender != address(0), "ERC20: approve to the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L733 )|
|protocol/contracts/p1/StRSRVotes.sol#L172|[if (src != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L172 )|
|protocol/contracts/p1/StRSRVotes.sol#L181|[if (dst != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L181 )|
|protocol/contracts/p1/mixins/Component.sol#L34|[require(address(main_) != address(0), "main is zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L34 )|
|protocol/contracts/p1/mixins/RecollateralizationLib.sol#L401|[if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L401 )|
|protocol/contracts/plugins/aave/ERC20.sol#L242|[require(sender != address(0), "ERC20: transfer from the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L242 )|
|protocol/contracts/plugins/aave/ERC20.sol#L243|[require(recipient != address(0), "ERC20: transfer to the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L243 )|
|protocol/contracts/plugins/aave/ERC20.sol#L262|[require(account != address(0), "ERC20: mint to the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L262 )|
|protocol/contracts/plugins/aave/ERC20.sol#L283|[require(account != address(0), "ERC20: burn from the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L283 )|
|protocol/contracts/plugins/aave/ERC20.sol#L310|[require(owner != address(0), "ERC20: approve from the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L310 )|
|protocol/contracts/plugins/aave/ERC20.sol#L311|[require(spender != address(0), "ERC20: approve to the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L311 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L94|[if (address(incentivesController) != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L94 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L142|[require(owner != address(0), StaticATokenErrors.INVALID_OWNER);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L142 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L170|[require(depositor != address(0), StaticATokenErrors.INVALID_DEPOSITOR);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L170 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L210|[require(owner != address(0), StaticATokenErrors.INVALID_OWNER);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L210 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L294|[require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L294 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L317|[require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L317 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L366|[if (from != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L366 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L369|[if (to != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L369 )|
|protocol/contracts/plugins/assets/Asset.sol#L49|[require(address(chainlinkFeed_) != address(0), "missing chainlink feed");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L49 )|
|protocol/contracts/plugins/assets/Asset.sol#L51|[require(address(erc20_) != address(0), "missing erc20");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L51 )|
|protocol/contracts/plugins/assets/CTokenFiatCollateral.sol#L26|[require(address(comptroller_) != address(0), "comptroller missing");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol#L26 )|
|protocol/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L29|[address(targetUnitChainlinkFeed_) != address(0),](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol#L29 )|
|protocol/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L30|[require(address(comptroller_) != address(0), "comptroller missing");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol#L30 )|
|protocol/contracts/plugins/assets/EURFiatCollateral.sol#L24|[require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol#L24 )|
|protocol/contracts/plugins/assets/NonFiatCollateral.sol#L24|[require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol#L24 )|
|protocol/contracts/plugins/assets/RTokenAsset.sol#L28|[require(address(erc20_) != address(0), "missing erc20");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L28 )|
|protocol/contracts/plugins/mocks/ATokenMock.sol#L81|[if (address(aaveToken) != address(0) && aaveBalances[msg.sender] > 0) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol#L81 )|
|protocol/contracts/plugins/mocks/ComptrollerMock.sol#L24|[if (address(compToken) != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ComptrollerMock.sol#L24 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L260|[if (allowListManger != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L260 )|
|protocol/contracts/plugins/mocks/vendor/EasyAuction.sol#L615|[return self.idToAddress[id + 1] != address(0);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L615 )|
|protocol/contracts/plugins/mocks/vendor/EasyAuction.sol#L637|[require(addr != address(0), "Cannot insert zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L637 )|
|protocol/contracts/plugins/mocks/vendor/EasyAuction.sol#L640|[if (self.addressToId[addr] != 0 || self.idToAddress[id + 1] != address(0)) {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L640 )|
|protocol/contracts/plugins/mocks/vendor/EasyAuction.sol#L936|[require(newOwner != address(0), "Ownable: new owner is the zero address");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L936 )|
|protocol/contracts/plugins/trading/GnosisTrade.sol#L98|[assert(origin_ != address(0));](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L98 )|
---

## [G-4] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate.

### Description
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

### ✅ Recommendation
Where appropriate, you can combine multiple address mappings into a single mapping of an address to a struct.

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/p1/StRSR.sol#L68|[mapping(uint256 => mapping(address => mapping(address => uint256))) private _allowances;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L68 )|
|protocol/contracts/plugins/aave/ERC20.sol#L40|[mapping(address => mapping(address => uint256)) private _allowances;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L40 )|
|protocol/contracts/plugins/mocks/WETH.sol#L31|[mapping(address => mapping(address => uint256)) public allowance;](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol#L31 )|
---

## [G-5] Using storage instead of memory for structs/arrays saves gas.

### Description
When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read.
Instead of declaring the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct([src](https://ethereum.stackexchange.com/questions/128380/why-using-storage-keyword-instead-of-memory-cost-less-gas))

### ✅ Recommendation
Use storage for `struct/array`

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/p1/BackingManager.sol#L219|[uint256[] memory toRSR = new uint256[](length);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L219 )|
|protocol/contracts/p1/BackingManager.sol#L220|[uint256[] memory toRToken = new uint256[](length);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L220 )|
|protocol/contracts/p1/BasketHandler.sol#L169|[uint192[] memory refAmts = new uint192[](basket.erc20s.length);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L169 )|
|protocol/contracts/p1/BasketHandler.sol#L225|[bytes32[] memory names = new bytes32[](erc20s.length);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L225 )|
|protocol/contracts/p1/BasketHandler.sol#L541|[uint192[] memory goodWeights = new uint192[](targetsLength);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L541 )|
|protocol/contracts/p1/BasketHandler.sol#L545|[uint192[] memory totalWeights = new uint192[](targetsLength);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L545 )|
|protocol/contracts/p1/BasketHandler.sol#L642|[uint192[] memory refAmts = new uint192[](basketLength);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L642 )|
|protocol/contracts/p1/Deployer.sol#L230|[IAsset[] memory assets = new IAsset[](2);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L230 )|
|protocol/contracts/p1/Distributor.sol#L105|[Transfer[] memory transfers = new Transfer[](destinations.length());](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L105 )|
|protocol/contracts/p1/RToken.sol#L666|[uint256[] memory amt = new uint256[](tokensLen);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L666 )|
|protocol/contracts/p1/RToken.sol#L750|[uint256[] memory amtDeposits = new uint256[](tokensLen);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L750 )|
|protocol/contracts/p1/mixins/RewardableLib.sol#L64|[uint256[] memory deltas = new uint256[](erc20sLen); // {qTok}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol#L64 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L389|[address[] memory assets = new address[](1);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L389 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L411|[address[] memory assets = new address[](1);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L411 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L545|[address[] memory assets = new address[](1);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L545 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L582|[address[] memory assets = new address[](1);](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L582 )|
---

## [G-6] Empty blocks should be removed or emit something.

### Description
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.
If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified
			```solidity
if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}}
			```

### ✅ Recommendation
Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/p1/Main.sol#L23|[constructor() initializer {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L23 )|
|protocol/contracts/p1/Main.sol#L64|[function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L64 )|
|protocol/contracts/p1/RToken.sol#L452|[try main.furnace().melt() {} catch {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L452 )|
|protocol/contracts/p1/RToken.sol#L838|[function requireNotPausedOrFrozen() private notPausedOrFrozen {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L838 )|
|protocol/contracts/p1/StRSR.sol#L157|[//   draft' = {}, bal' = {}, all totals zero, all rates FIX_ONE.](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L157 )|
|protocol/contracts/p1/mixins/Component.sol#L25|[constructor() initializer {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L25 )|
|protocol/contracts/p1/mixins/Component.sol#L57|[function _authorizeUpgrade(address newImplementation) internal view override governance {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L57 )|
|protocol/contracts/plugins/aave/ERC20.sol#L346|[) internal virtual {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L346 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L98|[} catch {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L98 )|
|protocol/contracts/plugins/assets/ATokenFiatCollateral.sol#L40|[constructor(CollateralConfig memory config) FiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/ATokenFiatCollateral.sol#L40 )|
|protocol/contracts/plugins/assets/Asset.sol#L164|[function claimRewards() external virtual {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L164 )|
|protocol/contracts/plugins/assets/RTokenAsset.sol#L119|[function claimRewards() external virtual {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol#L119 )|
|protocol/contracts/plugins/governance/Governance.sol#L44|[{}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/governance/Governance.sol#L44 )|
|protocol/contracts/plugins/mocks/BadCollateralPlugin.sol#L13|[constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadCollateralPlugin.sol#L13 )|
|protocol/contracts/plugins/mocks/BadERC20.sol#L17|[constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol#L17 )|
|protocol/contracts/plugins/mocks/ComptrollerMock.sol#L12|[constructor() {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ComptrollerMock.sol#L12 )|
|protocol/contracts/plugins/mocks/ERC20Mock.sol#L8|[constructor(string memory name, string memory symbol) ERC20(name, symbol) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ERC20Mock.sol#L8 )|
|protocol/contracts/plugins/mocks/EasyAuction.sol#L121|[constructor() public Ownable() {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L121 )|
|protocol/contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol#L7|[constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidATokenFiatCollateralMock.sol#L7 )|
|protocol/contracts/plugins/mocks/InvalidBrokerMock.sol#L48|[function reportViolation() external {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L48 )|
|protocol/contracts/plugins/mocks/InvalidBrokerMock.sol#L52|[function setAuctionLength(uint48 newAuctionLength) external governance {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L52 )|
|protocol/contracts/plugins/mocks/InvalidBrokerMock.sol#L56|[function setDisabled(bool disabled_) external governance {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L56 )|
|protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol#L17|[{}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidChainlinkMock.sol#L17 )|
|protocol/contracts/plugins/mocks/InvalidFiatCollateral.sol#L12|[constructor(CollateralConfig memory config) FiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidFiatCollateral.sol#L12 )|
|protocol/contracts/plugins/mocks/MockableCollateral.sol#L11|[constructor(CollateralConfig memory config) ATokenFiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/MockableCollateral.sol#L11 )|
|protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol#L9|[constructor(CollateralConfig memory config) FiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol#L9 )|
|protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol#L20|[constructor(CollateralConfig memory config) FiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol#L20 )|
|protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol#L31|[constructor(CollateralConfig memory config) FiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol#L31 )|
|protocol/contracts/plugins/mocks/NontrivialPegCollateral.sol#L42|[constructor(CollateralConfig memory config) FiatCollateral(config) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/NontrivialPegCollateral.sol#L42 )|
|protocol/contracts/plugins/mocks/USDCMock.sol#L8|[constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/USDCMock.sol#L8 )|
|protocol/contracts/plugins/mocks/UnpricedAssetPlugin.sol#L27|[) Asset(priceTimeout_, chainlinkFeed_, oracleError_, erc20_, maxTradeVolume_, oracleTimeout_) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/UnpricedAssetPlugin.sol#L27 )|
|protocol/contracts/plugins/mocks/WBTCMock.sol#L8|[constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WBTCMock.sol#L8 )|
|protocol/contracts/plugins/mocks/ZeroDecimalMock.sol#L8|[constructor(string memory name, string memory symbol) ERC20Mock(name, symbol) {}](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ZeroDecimalMock.sol#L8 )|
---

## [G-7] Use two `require` statements instead of operator `&&` to save gas.

### Description
Usage of double require will save you around 10 gas with the optimizer enabled.
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper.
 Example:
```Solidity
contract Requires {
uint256 public gas;
			
				function check1(uint x) public {
					gas = gasleft();
					require(x == 0 && x < 1 ); // gas cost 22156
					gas -= gasleft();
				}
			
				function check2(uint x) public {
					gas = gasleft();
					require(x == 0); // gas cost 22148
					require(x < 1);
					gas -= gasleft();
	}
}
```


### ✅ Recommendation
Consider changing one `require()` statement to two `require()` to save gas

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/mixins/Auth.sol#L181|[require(shortFreeze_ > 0 && shortFreeze_ <= MAX_SHORT_FREEZE, "short freeze out of range");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L181 )|
|protocol/contracts/mixins/Auth.sol#L188|[require(longFreeze_ > 0 && longFreeze_ <= MAX_LONG_FREEZE, "long freeze out of range");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol#L188 )|
|protocol/contracts/p1/Deployer.sol#L109|[require(owner != address(0) && owner != address(this), "invalid owner");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L109 )|
|protocol/contracts/p1/Furnace.sol#L89|[require(period_ > 0 && period_ <= MAX_PERIOD, "invalid period");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L89 )|
|protocol/contracts/p1/RToken.sol#L410|[require(queue.left <= endId && endId <= queue.right, "out of range");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L410 )|
|protocol/contracts/p1/RToken.sol#L590|[require(val > 0 && val <= MAX_ISSUANCE_RATE, "invalid issuanceRate");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590 )|
|protocol/contracts/p1/RToken.sol#L623|[require(index >= item.left && index < item.right, "out of range");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L623 )|
|protocol/contracts/p1/RToken.sol#L741|[require(queue.left <= endId && endId <= queue.right, "out of range");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L741 )|
|protocol/contracts/p1/RToken.sol#L813|[require(uint192(low) >= 1e9 && uint192(high) <= 1e27, "BU rate out of range");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L813 )|
|protocol/contracts/p1/RevenueTrader.sol#L72|[require(buyPrice > 0 && buyPrice < FIX_MAX, "buy asset price unknown");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L72 )|
|protocol/contracts/p1/StRSR.sol#L813|[require(val > 0 && val <= MAX_UNSTAKING_DELAY, "invalid unstakingDelay");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L813 )|
|protocol/contracts/p1/StRSR.sol#L821|[require(val > 0 && val <= MAX_REWARD_PERIOD, "invalid rewardPeriod");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L821 )|
|protocol/contracts/plugins/assets/Asset.sol#L50|[require(oracleError_ > 0 && oracleError_ < FIX_ONE, "oracle error out of range");](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol#L50 )|
---

## [G-8] `abi.encode()` is less efficient than `abi.encodePacked()`

### Description
`abi.encode` will apply ABI encoding rules. Therefore all elementary types are padded to 32 bytes and dynamic arrays include their length. Therefore it is possible to also decode this data again (with `abi.decode`) when the type are known.

`abi.encodePacked` will only use the only use the minimal required memory to encode the data. E.g. an address will only use 20 bytes and for dynamic arrays only the elements will be stored without length. For more info see the Solidity docs for packed mode.

For the input of the `keccak` method it is important that you can ensure that the resulting bytes of the encoding are unique. So if you always encode the same types and arrays always have the same length then there is no problem. But if you switch the parameters that you encode or encode multiple dynamic arrays you might have conflicts.
For example:
`abi.encodePacked(address(0x0000000000000000000000000000000000000001), uint(0))` encodes to the same as `abi.encodePacked(uint(0x0000000000000000000000000000000000000001000000000000000000000000), address(0))` 
and `abi.encodePacked(uint[](1,2), uint[](3))` encodes to the same as `abi.encodePacked(uint[](1), uint[](2,3))`
Therefore these examples will also generate the same hashes even so they are different inputs.
On the other hand you require less memory and therefore in most cases abi.encodePacked uses less gas than abi.encode.

### ✅ Recommendation
Use `abi.encodePacked()` where possible to save gas

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/p1/StRSR.sol#L778|[abi.encode(_PERMIT_TYPEHASH, owner, spender, value, _useNonce(owner), deadline)](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L778 )|
|protocol/contracts/p1/StRSRVotes.sol#L128|[_hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol#L128 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L151|[abi.encode(PERMIT_TYPEHASH, owner, spender, value, currentValidNonce, deadline)](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L151 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L179|[abi.encode(](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L179 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L219|[abi.encode(](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L219 )|
|protocol/contracts/plugins/aave/StaticATokenLM.sol#L269|[abi.encode(](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol#L269 )|
---

## [G-9] Internal functions only called once can be inlined to save gas.

### Description
Not inlining costs 20 to 40 gas because of two extra `JUMP` instructions and additional stack operations needed for function calls.
Check this out: [link](https://betterprogramming.pub/solidity-gas-optimizations-and-tricks-2bcee0f9f1f2)
Note: To sum up, when there is an internal function that is only called once, there is no point for that function to exist. 

### ✅ Recommendation
TODO - CHECK IF THIS IS REALLY THE CASE!!!!

### 🔍 Findings:
| | |
|---|---|
|protocol/contracts/p1/BasketHandler.sol#L68|[function empty(Basket storage self) internal {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L68 )|
|protocol/contracts/plugins/aave/ERC20.sol#L324|[function _setupDecimals(uint8 decimals_) internal {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol#L324 )|
|protocol/contracts/plugins/mocks/vendor/EasyAuction.sol#L243|[function initializeEmptyList(Data storage self) internal {](https://github.com/reserve-protocol/protocol/tree/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol#L243 )|
---
