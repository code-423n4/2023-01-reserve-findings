# Summary

\[L-01\] Constants not declared
\[L-02\] Inefficient Boolean Mappings
\[L-03\] Redundant Initializations
\[L-04\] Checking for collateral assets twice in the same function
\[NC-01\] Storage gaps not explicitly documented
\[NC-02\] Splitting require() statements that use multiple condition
\[NC-03\] To improve the readability, consider changing the event name

# \[L-01\] Constants not declared

In the Distributor.sol contract, the literal values **10000** which are used as maximum amount 
of shares distributed ([L165](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165) and [L166](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L166))

To improve the code’s readability and facilitate refactoring, consider defining a constant for every “magic value” and ensuring that

all constants have clear names.

# \[L-02\] Inefficient Boolean Mappings

In Broker.sol contract [L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L44)
In InvalidBrokerMock.sol contract [L20](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/InvalidBrokerMock.sol#L20)
In BadERC20.sol contract [L15](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/BadERC20.sol#L15)

Mappings that contain boolean variables are inefficient in general, because the solidity compiler 
masks the values to ensure no dirty bits are propagated.

## Recommandation

Using `mapping(address => uint256)` instead of `mapping(address => bool)` eliminates these checks 
and the number of SLOADs needed to retrieve an entry from mapping.

# \[L-03\] Redundant Initializations

Some storage variables are initialized to their default values, which is redundant and wastes gas.

In GnosisMock.sol contract [L50](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol#L50)

In EasyAuction.sol contract [L123](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol#L123)
# \[L-04\] Checking for collateral assets twice in the same function

In the function setPrimeBasket, the collateral is checked two times, in [L230](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L230) throught the require statement and in [L236](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L236) throught the call of the toColl function for an asset. Is it relevant to check two times ? I don't 
think so, especially since the collateral value is not settable and does not change.

## Recommandation

Use another logic, for exemple retrieve the targetName of the asset by creating another function 
that doesn't check for collateral assuming that the check already done,
# \[NC-01\] Storage gaps not documented

When using the proxy pattern for upgrades, it is common practice to include storage gaps on parent contracts to reserve 
space for potential future variables. The size is typically chosen so that all contracts have the same number of variables (usually 50).

Differents contracts include the storages gaps but they use inconsistent sizes. Sometimes [**30**](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L839), somtimes [**37**](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L845), [**42**](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L738) and so on.If there is any reason to choose a precise gaps size then it's not explicitly documented.

## Recommandation

Use a size gaps of **50** for all your contracts. By the way, it's important to remind that gaps 
storage does not cost additional gas when deploying a contract.

# \[NC-02\] Splitting require() statements that use multiple condition

It's a best practice to split the different require statement instead a long require.

Exemple in Deployer.sol [L48](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48)

If the requirement fail, we cannot explicitly determine which address was set to zero without 
checking each address one by one.


## \[NC-03\] To improve the readability, consider changing the event name

## In BasketHandler.sol [L170](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L170) [L646](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L646)

When the disabled state variable is modified, an event is emited but the event is called

```solidity 
emit BasketSet(nonce, basket.erc20s, refAmts, disabled);
```

To be logic the event name must be **disabledBasketSet** or keep **basketSet** but the emit should
be like this :

```solidity 
emit BasketSet(nonce, basket.erc20s, refAmts, !disabled);
```