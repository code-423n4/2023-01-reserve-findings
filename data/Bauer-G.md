### Useless import 

I mported itself in the file FiatCollateral.sol 

```
FiatCollateral.sol 

7   import "./FiatCollateral.sol";

```

### Use require instead of assert
Instances (10):
```
BackingManager.sol
249 assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());

BasketHandler.sol

556 assert(targetIndex < targetsLength);

StRSR.sol
696        assert(totalStakes + amount < type(uint224).max);

RecollateralizationLib.sol
110  assert(doTrade);

RewardableLib.sol
79                  assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);

102             assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);

TradeLib.sol
44         assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
108       assert(
            trade.sellPrice > 0 &&
                trade.sellPrice < FIX_MAX &&
                trade.buyPrice > 0 &&
                trade.buyPrice < FIX_MAX
        );
168            assert(errorCode == 0x11 || errorCode == 0x12);

170            assert(keccak256(reason) == UIntOutofBoundsHash);

```

### Use assembly to check for address(0)
```
Broker.sol
57 require(address(gnosis_) != address(0), "invalid Gnosis address");
59      require(
            address(tradeImplementation_) != address(0),
            "invalid Trade Implementation address"
        );
Deployer.sol
48  require(
            address(rsr_) != address(0) &&
                address(gnosis_) != address(0) &&
                address(rsrAsset_) != address(0) &&
                address(implementations_.main) != address(0) &&
                address(implementations_.trade) != address(0) &&
                address(implementations_.components.assetRegistry) != address(0) &&
                address(implementations_.components.backingManager) != address(0) &&
                address(implementations_.components.basketHandler) != address(0) &&
                address(implementations_.components.broker) != address(0) &&
                address(implementations_.components.distributor) != address(0) &&
                address(implementations_.components.furnace) != address(0) &&
                address(implementations_.components.rsrTrader) != address(0) &&
                address(implementations_.components.rTokenTrader) != address(0) &&
                address(implementations_.components.rToken) != address(0) &&
                address(implementations_.components.stRSR) != address(0),
            "invalid address"
        );
109         require(owner != address(0) && owner != address(this), "invalid owner");
Distributor.sol
162        require(dest != address(0), "dest cannot be zero");

Main.sol
32        require(address(rsr_) != address(0), "invalid RSR address");

RevenueTrader.sol
29         require(address(tokenToBuy_) != address(0), "invalid token address");
StRSR.sol
675 require(from != address(0), "ERC20: transfer from the zero address");
676  require(to != address(0), "ERC20: transfer to the zero address");
695        require(account != address(0), "ERC20: mint to the zero address");
711         require(account != address(0), "ERC20: burn from the zero address");
732         require(owner != address(0), "ERC20: approve from the zero address");
733         require(spender != address(0), "ERC20: approve to the zero address");

Component.sol
34         require(address(main_) != address(0), "main is zero address");
RecollateralizationLib.sol
Trading.sol
114        assert(address(trades[sell]) == address(0));

````

### Use calldata instead of memory for function arguments that do not g

```
Deployer.sol
102     function deploy(
        string memory name,
        string memory symbol,
        string calldata mandate,
        address owner,
        DeploymentParams memory params
    ) 

```
### abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

```
Deployer.sol
  206          string memory stRSRSymbol = string(abi.encodePacked(StringLib.toLower(symbol), "RSR"));
  207        string memory stRSRName = string(abi.encodePacked(stRSRSymbol, " Token"));

```
### Unnecessary checked arithmetic in for loop

```
BasketHandler.sol
653  for (uint256 i = 0; i < erc20s.length; i++) {
Distributor.sol

108 for (uint256 i = 0; i < destinations.length(); ++i)
133 for (uint256 i = 0; i < numTransfers; i++) 
143  for (uint256 i = 0; i < length; ++i) {

use like this:
for (uint i; i < len; ) {
    // ...

    unchecked { i++; }
}

```

