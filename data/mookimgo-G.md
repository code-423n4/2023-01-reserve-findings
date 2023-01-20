# ChainlinkFeed AggregatorV3Interface's decimals should be cached as an immutable variable at constructor

This `chainlinkFeed.decimals()` is called each time when query a price, which is unnecessary, as Chainlink won't change decimals.

Refactor this code to cache decaimls as an immutable variable should reduce a function call, saving gas.

https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/plugins/assets/OracleLib.sol#L30

----

# referencePrice check in FiatCollateral.sol can be deleted

https://github.com/reserve-protocol/protocol/blob/df7ecadc/contracts/plugins/assets/FiatCollateral.sol#L154

```
        // Check for hard default
        uint192 referencePrice = refPerTok();
        // uint192(<) is equivalent to Fix.lt
        if (referencePrice < prevReferencePrice) {
            markStatus(CollateralStatus.DISABLED);
        }
        prevReferencePrice = referencePrice;
```

This check is useless as function `refPerTok` always return constant FIX_ONE, so referencePrice will always equal to prevReferencePrice.

Suggestion: delete above lines to save gas.