### **Low Risk Issues**

#### **[L-01] Init GnosisTrade before transfer**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L109-L116
##### **Description:**
The initialization of the GnosisTrade contract happens after the transfer of tokens to it. While SafeERC20's `safeTransferFrom` function will protect against failed transfers, it does not mitigate against unsafe external calls. In the event an evil erc20 is added as an asset, the initialization of the GnosisTrade contract could happen earlier than expected, via re-entrancy, and subsequently cause the intended initialization to revert. This would effectively lead to the denial of service of trade requests which may prevent the RToken system from recollateralizing.
##### **Recommendation**:
The initialization of the GnosisTrade contract should be done before the transfer of the tokens to the GnosisTrade contract.

#### **[L-02] Prevent issuing RToken to address(0)**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L186-L191
##### **Description:**
The `issue` function does not prevent the issuance of RTokens to the zero address which could lead to loss of user funds.
##### **Recommendation**:
Consider requiring that `issue` function should revert if the recipient address is the zero address.

#### **[L-03] Consider removing storage gaps in favour of using DiamondStorage**
##### **Context**:
A number of contracts in the RToken system which are deployed as ERC-1967 proxies.
##### **Description:**
Storage gaps for upgradeable contracts can be problematic, adding significant mental overhead to developers in addition to cluttering the contract code/storage layout. This technique requires a lot of attention to be paid as forgetting a gap or adding variables without adjustment can result in inadvertently modifying storage between upgrades.
##### **Recommendation**:
A better alternative would be the use of EIP-2535 [DiamondStorage](https://medium.com/cartesi/upgrading-smart-contract-code-and-storage-layout-with-eip-2535-diamonds-31e0aff5d255) which provides a more robust framework for adding storage variables to upgradeable contracts and is [planned to be adopted by OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/2964#issuecomment-1116358040) in a future major release.

#### **[L-04] Trust of Asset functions existance can causes issues**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/BasketHandler.sol#L232
https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/Broker.sol#L110
and more
##### **Description:**
Many contracts in the RToken system make calls to various assets, but the assetRegistry only verifies that `erc20` and `refresh` exist at registration time. This can cause many issues. 
##### **Recommendation**:
Handle calling assets as if they were not to be trusted and handle errors gracefully.

#### **[L-05] An asset with a bad erc20 function cant be removed**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L88
##### **Description:**
Calling unregister makes a call to the collateral contract. If that call fails, the collateral can never be removed or swapped. 
##### **Recommendation**:
Allow governance to force overwrite or remove assets from the registry ignoring collisions. 

#### **[L-06] Asset price of 0 panics with a divide by 0 error**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol#L201
##### **Description:**
If asset has a real price of zero, we get a panic divide by 0 error. 
##### **Recommendation**:
Handle divide by 0 errors gracefully.
```javascript
        if (basketPriceHigh == 0) {
            range.bottom = 0;
        } else {
            range.bottom = fixMin(assetsLow.div(basketPriceHigh, CEIL), range.top);
        }
```
This will result in a more accurate validBURange error. 

#### **[L-07] When protocol is uncollateralized & collateral is sound, new issuers have best case break even, worst case lose it all**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L439
##### **Description:**
When the protocol is uncollateralized, if a new issuer comes along to issue new rTokens, they can't immediatly redeem for the same amount of their original collateral. This seems unfair. Best case, they have to wait for recollateralization to finish to redeem and pray it goes well. Worst case they get all their underlying collateral taken away from them. It feels like the protocol should be able to protect new issuers from this risk.
##### **Recommendation**:
Disallow issuance and redemptions during recollateralization (when not fully collateralized).


#### **[L-08] Whales can flash issue/redeem any amount of RToken**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/ae03d523ac980bc44d53e87400a64a6ff02f745f/contracts/p1/BackingManager.sol#L202
##### **Description:**
The backing manager has the ability to flash issue as many RTokens as needed, so long as it has enough collateral. A whale who wants to manipulate the total supply of RTokens can then send the backing manager a large amount of collateral, and then issue as many RTokens as they want by calling the `manageTokens` function. 
##### **Recommendation**:
Make sure this is intended behaviour. If not, consider putting the backing manager on the slow vesting schedule rules normal users have to follow.  


### **Non-Critical Issues**
#### **[NC-01] Remove unused imports**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol#L5
https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/plugins/assets/FiatCollateral.sol
##### **Description:**
`UUPSUpgradeable` is imported but not used. And in various contracts like `FiatCollateral`, inherited contracts are double imported like:
```
import "../../libraries/Fixed.sol";
import "./FiatCollateral.sol";
```
##### **Recommendation**:
Remove unused imports.

#### **[NC-02] Inconsistent naming of storage variables**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L18-L22
##### **Description:**
There is inconsistency in the naming conventions of storage variables, sometimes they are prefixed with `_` and sometimes they are not. Coupled with the fact that function parameters sometimes have the ‘_’ postfix, and sometimes not, this could lead to confusion.
##### **Recommendation**:
Consider using the ‘s_’ prefix for consistent naming of storage variables. This will make it easier to distinguish between function parameters and storage variables in addition to more verbose accessing of storage variables.

#### **[NC-03] Typo in NatSpec**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L89
##### **Description & Recommendation**:
Maintain is misspelled as ‘Mointain’.

#### **[NC-04] Incorrect dimensional analysis**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L419
##### **Description & Recommendation**:
The correct version of this comment should be `{qTok} = {tok/BU} * {BU} * {qTok/tok}`.
#### **[NC-05] Use recommended mitigation for disabling UUPS logic contract initialization**
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol#L21-L23
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol#L23-L25
##### **Description:**
To mitigate against a [previous UUPS vulnerability](https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680), where a logic contract can be initialized by an attacker to give themselves upgrade authority, the `initializer` modifier has been added to the constructors of `Main` and `Component`. This successfully disables the initialization of the UUPS logic contract; however, this is not the recommended mitigation.
##### **Recommendation**:
The [recommended mitigation](https://forum.openzeppelin.com/t/is-disableinitializers-necessary/31070) is to call the internal `_disableInitializers()` function within the constructor body. This will prevent the initialization of the UUPS logic contract and is more verbose about its intention, both visually and in setting `_initialized` to `type(uint8).max`. As such, it is easier to understand and less likely to be accidentally modified.

#### **[NC-06] Naming of functions and varaibles is confusing
Opinionated section.
##### **Context**:
I don't like some of the variables and function names, they are not descriptive to what's actually happening, and can be confusing. 
##### **Recommendation**:
- [basket](https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/BasketHandler.sol#L128) should be `primeBasket`, since it holds what the prime basket currently consists of.
- [setPrimeBasket](https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/BasketHandler.sol#L205) should be `setPrimeBasketConfig`. `setPrimeBasket` feels like it's currently updating our `primeBasket`, which it isn't, it just updates the config, and is only really set when `refreshBasket` is called. 
- [issueItem](https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/RToken.sol#L5980) function is confusing when you also have an `IssueItem` struct. It would be better to have the function be a classic getter like `getIssueItem`. 

#### **[NC-07] Update natSpec for `endIdForWithdraw` function
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/StRSR.sol#L433
##### **Recommendation**:
Clarify in the natSpec of `endIdForWithdraw` that a return value of 0 means that their isn't a valid endId. 

#### **[NC-08] Inconsistent use of `assetRegistry.refresh()` in `RToken.sol`
##### **Context**:
https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/RToken.sol#L181
##### **Recommendation**:
RToken.sol uses both [`assetRegistry.refresh()`](https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/RToken.sol#L181) and [`main.assetRegistry().refresh();`](https://github.com/reserve-protocol/protocol/blob/fdd9f81fe58953d758dbea62beed169a74523de1/contracts/p1/RToken.sol#L362). It would be better to be consistent and use the same method in both places. It may also be more gas efficient to use one over the other, but we haven't tested which. 

