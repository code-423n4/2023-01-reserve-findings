## Description: 
Using memory arrays instead of storage arrays for `self.erc20s.push(tok)` and `self.refAmts[tok]` in the `add` function for [BasketHandler.sol#L87-L104](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L87-L104), will reduce the gas cost as well.

## The Base function:
```sol
    function add(
        Basket storage self,
        IERC20 tok,
        uint192 weight
    ) internal {
        // untestable:
        //      Both calls to .add() use a weight that has been CEIL rounded in the
        //      Fixed library div function, so weight will never be 0 here.
        //      Additionally, setPrimeBasket() enforces prime-basket tokens must have a weight > 0.
        if (weight == FIX_ZERO) return;
        if (self.refAmts[tok].eq(FIX_ZERO)) {
            self.erc20s.push(tok);
            self.refAmts[tok] = weight;
        } else {
            self.refAmts[tok] = self.refAmts[tok].plus(weight);
        }
    }
}
``` 
## The code after using memory arrays:
After using memory arrays instead of storage arrays for `self.erc20s.push(tok)` and `self.refAmts[tok]` in the `add` function.

```sol
function add(
    Basket storage self,
    IERC20 tok,
    uint192 weight
) internal {
    // untestable:
    //      Both calls to .add() use a weight that has been CEIL rounded in the
    //      Fixed library div function, so weight will never be 0 here.
    //      Additionally, setPrimeBasket() enforces prime-basket tokens must have a weight > 0.
    if (weight == FIX_ZERO) return;

    // Declare memory arrays
    IERC20[] memory erc20s = new IERC20[](self.erc20s.length + 1);
    uint192[] memory refAmts = new uint192[](self.refAmts.length + 1);

    // Copy storage arrays to memory arrays
    for (uint i = 0; i < self.erc20s.length; ++i) {
        erc20s[i] = self.erc20s[i];
        refAmts[i] = self.refAmts[i];
    }

    if (self.refAmts[tok].eq(FIX_ZERO)) {
        // Add token to memory array
        erc20s[self.erc20s.length] = tok;
        refAmts[self.refAmts.length] = weight;
    } else {
        // Find the index of the token in the storage array
        uint index;
        for (uint i = 0; i < self.erc20s.length; ++i) {
            if (self.erc20s[i] == tok) {
                index = i;
                break;
            }
        }
        // Add the weight to the corresponding element in the memory array
        refAmts[index] = refAmts[index].plus(weight);
    }

    // Assign memory arrays to storage arrays
    self.erc20s = erc20s;
    self.refAmts = refAmts;
}

```

### The Affected code: 
[BasketHandler.sol#L87-L104](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L87-L104)
