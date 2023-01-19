# summary

\[G-01\] Use unchecked for value that cannot overflow
\[G-02\] Use cached value for length of state variable
\[G-03\] Splitting require() statements that use multiple condition saves gas
  

# \[G-01\] Use unchecked for value that cannot overflow

For values that cannot overflow, we must specify to the compiler to not check for arithmetic overflow, by doing this we save a non negligeble amount of gas, especially for **long** and **multiple** loop iteration throught the code

## Exemple in p1/BasketHandeler.sol  [L70](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L70)
also in L78, L218, L227, L262, L286, L337, L397, L416, L437, L530, L548, L586, L643, L653, L707, L725

And in others location in different contracts 

## Recommandation

```diff
diff --git a/contracts/p1/BasketHandler.sol b/contracts/p1/BasketHandler.sol
index f74155b1..2b190727 100644
--- a/contracts/p1/BasketHandler.sol
+++ b/contracts/p1/BasketHandler.sol
@@ -67,7 +67,7 @@ library BasketLibP1 {
     // self'.refAmts = {} (empty map)
     function empty(Basket storage self) internal {
         uint256 length = self.erc20s.length;
-        for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
+        for (uint256 i = 0; i < length; i=unsafe_inc(i)) self.refAmts[self.erc20s[i]] = FIX_ZERO;
         delete self.erc20s;
     }
 
@@ -101,6 +101,10 @@ library BasketLibP1 {
             self.refAmts[tok] = self.refAmts[tok].plus(weight);
         }
     }
+
+    function unsafe_inc(uint256 x) private returns (uint256) {
+        unchecked { return ++x; }
+    }
 }

```

# \[G-02\] Use cached value for length of state variable 

## Exemple in BasketHandler [L537](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L537)

Instead of reading the length of state variable in each iteration, cache the value in a memory variable and use **for loop** over **while loop**
## Recommandation 

```diff
diff --git a/contracts/p1/BasketHandler.sol b/contracts/p1/BasketHandler.sol
index f74155b1..d88d245f 100644
--- a/contracts/p1/BasketHandler.sol
+++ b/contracts/p1/BasketHandler.sol
@@ -520,7 +520,9 @@ contract BasketHandlerP1 is ComponentP1, IBasketHandler {
         disabled = false;
 
         // _targetNames := {}
-        while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
+        uint256 _targetNamesLength = _targetNames.length() // or use the same variable declared below (uint256 targetsLength)
+        for (uint256 i = 0; i < _targetNamesLength; ++i) _targetNames.remove(_targetNames.at(0));
+
         // _newBasket := {}
         _newBasket.empty();
```
Here I've not included unsafe_inc function in order to show only one optimisation at the time. Of course we can add this line code for **[G-01]**, if you decide to include this optmisation.

## Example in BasketHandler [L519](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L519)

Through the code,  **_switchBasket** has to read and write several times in the disabled variable. 

Because reading and writing from memory is much less consuming, Consider creating a memory variable named for example _disabled and use this variable for assignement and different checking (also for emitting an event). And in the end of the code set  ```disabled = _disabled```


## Exemple in Distributor.sol [L105](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L105)

In Distributor.sol ([L105](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L105) and L[108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L108)) consider storing the size of **destinations** array in a memory variable to reduce gas consumption.


# \[G-03\] Splitting require() statements that use multiple condition saves gas

Adding operator like **&&** in require statement consume more gas than splitting into two different require line

Exemple in Deployer.sol [L48](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol#L48) 

