No | Type 
--- | --- |
*1* | Typo's 
*2* | Public Function change to external | 
*3* | Use Require over Revert |
*4* | Const naming covention (solidity styling stds) |
*5* | Only init() Once |
*6* | Implement Upgrade correctly |

### Typo's

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.solhttps://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L537
BasketHandler.sol:537:        // As such, they're each interepreted as a map from target name -> target
should be 
BasketHandler.sol:537:        // As such, they're each interpreted as a map from target name -> target weight

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Furnace.sol#L66
Furnace.sol:66:    //   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay leriod)
should be 
Furnace.sol:66:    //   lastPayoutBal' = rToken.balanceOf'(this) (balance now == at end of pay period)

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L478
RToken.sol:478:        // Bound each withdrawal by the prorata share, in case we're currently under-collateralized
should be 
RToken.sol:478:        // Bound each withdrawal by the pro rata share, in case we're currently under-collateralized

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Fixed.sol#L21
Fixed.sol:21://   Every function should revert iff its result is out of bounds.
should be 
Fixed.sol:21://   Every function should revert if its result is out of bounds.

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Fixed.sol#L26
Fixed.sol:26://   - @return is the value expressed  in "value space", where uint192(1e18) "is" 1.0
should be 
Fixed.sol:26://   - @return is the value expressed in "value space", where uint192(1e18) "is" 1.0

https://github.com/reserve-protocol/protocol/tree/master/contracts/libraries/RedemptionBattery.sol#L18
RedemptionBattery.sol:18:///      behavior in that case. (But keeping maxCapacity outside storage saves SLOADs)
should be 
RedemptionBattery.sol:18:///      behaviour in that case. (But keeping maxCapacity outside storage saves SLOADs)

https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IAsset.sol#L86
IAsset.sol:86:    /// VERY IMPORTANT: In any valid implemntation, status() MUST become DISABLED in refresh() if
should be 
IAsset.sol:86:    /// VERY IMPORTANT: In any valid implementation, status() MUST become DISABLED in refresh() if

https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IRToken.sol#L42
IRToken.sol:42:    /// Emitted when an RToken issuance is canceled, such as during a default
should be 
IRToken.sol:42:    /// Emitted when an RToken issuance is cancelled, such as during a default

https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IRToken.sol#L46
IRToken.sol:46:    /// @param amount {qRTok} The amount of RTokens canceled
should be 
IRToken.sol:46:    /// @param amount {qRTok} The amount of RTokens cancelled

https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IRToken.sol#L47
IRToken.sol:47:    /// That is, id was cancelled iff firstId <= id < endId
should be 
IRToken.sol:47:    /// That is, id was cancelled if firstId <= id < endId

https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IRToken.sol#L74
IRToken.sol:74:    /// @param redeemer The address of the account redeeeming RTokens
should be
IRToken.sol:74:    /// @param redeemer The address of the account redeeming RTokens

https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IRToken.sol#L77
IRToken.sol:77:    /// @param amount {qRTok} The amount of RTokens canceled
should be 
IRToken.sol:77:    /// @param amount {qRTok} The amount of RTokens cancelled

https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IRToken.sol#L71
https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IStRSR.sol
IStRSR.sol:71:    /// Emitted whenever RSR are paids out
should be 
IStRSR.sol:71:    /// Emitted whenever RSR's are paid out

https://github.com/reserve-protocol/protocol/blob/master/contracts/interfaces/IRToken.sol#L76
IStRSR.sol:76:    /// Emitted if all the RSR in the unstakin pool is seized, and all ongoing unstaking is voided.
should be 
IStRSR.sol:76:    /// Emitted if all the RSR in the unstaking pool is seized, and all ongoing unstaking is voided.

### Public functions
Public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L256
https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BackingManager.sol#L263

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L279
https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L296
https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L432

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L141

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Furnace.sol#L88
https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Furnace.sol#L96

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L590
https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L603
https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L616

### Use require() over revert()

https://github.com/reserve-protocol/protocol/blob/b30ab2068dddf111744b8feed0dd94925e10d947/contracts/p1/RToken.sol#L513

One potential issue with this function is the use of the revert() in the redeem() function to throw an error if a 0 value is returned on users fund. This can cause issues with contract execution and gas consumption, as all gas used in the transaction will be consumed but not refunded. It is recommended to use the require() function instead of revert() in such cases, as it allows for a more controlled and efficient handling of errors.
To mitigate this issue, the revert() call in redeem() can be replaced with a require() call that checks for a 0 value like so .... 

```
function redeem(uint256 amount) external notFrozen {
        require(amount > 0, "Cannot redeem zero"); // require to check for empty redemption instead of revert

        // == Refresh ==
        main.assetRegistry().refresh();

        // == Checks and Effects ==
        address redeemer = _msgSender();

        // Check if caller has enough balance
        require(balanceOf[redeemer] >= amount, "Insufficient balance");

        // Allow redemption during IFFY + UNPRICED
        require(basketHandler.status() != CollateralStatus.DISABLED, "collateral default");
 cont code........
```
also consider refactoring the following functions to make use of require instead of revert for better error handling and a higher consideration of the usage of users funds e.g gas consumption when calling certain functions

https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/Fixed.sol#L73
https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/Fixed.sol#L102
https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/Fixed.sol#L213
https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/Fixed.sol#L390
https://github.com/reserve-protocol/protocol/blob/master/contracts/libraries/Fixed.sol#L502

### Const variable naming convention
All capital naming convention should be used for immutable and constant values

https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L45

### Only Initialize Once
```
main.sol 

    function init(
        Components memory components,
        IERC20 rsr_,
        uint48 shortFreeze_,
        uint48 longFreeze_
    ) public virtual initializer {
        require(address(rsr_) != address(0), "invalid RSR address");
        __Auth_init(shortFreeze_, longFreeze_);
        __ComponentRegistry_init(components);
        __UUPSUpgradeable_init();

        rsr = rsr_;
        emit MainInitialized();
    }
```
The init() function in the contract Main.sol can be reinitialised more than once, it may be prudent to add a boolean to protect that so a rare edge case of reinitialization cannot happen, literally just an FYI and down to devs personal prefference.

```
    /// Initializer
    function init(
        Components memory components,
        IERC20 rsr_,
        uint48 shortFreeze_,
        uint48 longFreeze_
    ) public virtual initializer {
        require(!initialized, "already initialized"); // <-- added check
        require(address(rsr_) != address(0), "invalid RSR address");
        __Auth_init(shortFreeze_, longFreeze_);
        __ComponentRegistry_init(components);
        __UUPSUpgradeable_init();

        rsr = rsr_;
        initialized = true; // <-- set to true
        emit MainInitialized();
    }
    // ... rest of the code
```

By adding a boolean variable "initialized" and setting it to true in the init() function, the init() function will only proceed if the variable is false, meaning that the function can only be called once. This ensures that the contract is only initialized once and prevents any accidental or malicious attempts to re-initialize it.


### Function _authorizeUpgrade() in Main.sol 
```
    function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {}
```

This code will only check if the caller of the function has the "OWNER" role, but it doesn't have any actual implementation to authorize the upgrade, So it will not authorize any upgrade, it should be something like this :

```
function _authorizeUpgrade(address newImplementation) internal override onlyRole(OWNER) {
    _setImplementation(newImplementation);
    emit Upgraded(newImplementation);
}
```
It should set the newImplementation as the implementation of the contract and emit an event "Upgraded" with the newImplementation.

