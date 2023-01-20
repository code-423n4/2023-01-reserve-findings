### QA Issues List
| Number |Issues Details
|:--:|:-------|:--:|
|[QA-01]| Use latest Solidity version
|[QA-02]| Use stable pragma statement
|[QA-03]| Different pragma directives are used
|[QA-04]|Update external dependency to latest version
|[QA-05]|Use named inports instead of plain import `FILE.SOL`
|[QA-06]| Missing checks for `address(0X0)`
|[QA-07]|Too many digits
|[QA-08]| Constant values such as a call to `keccak256()`, should used to immutable rather than constant
***

## [QA-01] Use latest Solidity version

Solidity pragma versioning should be upgraded to latest available version.
***

## [QA-02] Use stable pragma statement

Using a floating pragma statement is discouraged as code can compile to different bytecodes with different compiler versions. Use a stable pragma statement to get a deterministic bytecode.
```
Fixed.sol:
3: pragma solidity ^0.8.9;

ERC20.sol:
pragma solidity ^0.6.0;
```
***

## [QA-03] Different pragma directives are used

Use one Solidity version on each contract.
***

## [QA-04] Update external dependency to latest version

The leatest version is 4.8.1.

```
package.json:
52:     "@openzeppelin/contracts": "^4.7.3",
63:     "@openzeppelin/contracts-upgradeable": "^4.7.3",
```
***

## [QA-05] USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

```
AssetRegistry.sol:
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
import "../interfaces/IAssetRegistry.sol";
import "../interfaces/IMain.sol";
import "./mixins/Component.sol";

BackingManager.sol:
import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "../interfaces/IAsset.sol";
import "../interfaces/IBackingManager.sol";
import "../interfaces/IMain.sol";
import "../libraries/Array.sol";
import "../libraries/Fixed.sol";
import "./mixins/Trading.sol";
import "./mixins/RecollateralizationLib.sol";

BasketHandler.sol:
import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
import "../interfaces/IAssetRegistry.sol";
import "../interfaces/IBasketHandler.sol";
import "../interfaces/IMain.sol";
import "../libraries/Array.sol";
import "../libraries/Fixed.sol";
import "./mixins/Component.sol";

Broker.sol:
import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
import "@openzeppelin/contracts/proxy/Clones.sol";
import "../interfaces/IBroker.sol";
import "../interfaces/IMain.sol";
import "../interfaces/ITrade.sol";
import "../libraries/Fixed.sol";
import "./mixins/Component.sol";
import "../plugins/trading/GnosisTrade.sol";

Deployer.sol:
import "@openzeppelin/contracts/proxy/Clones.sol";
import "@openzeppelin/contracts/proxy/ERC1967/ERC1967Proxy.sol";
import "@openzeppelin/contracts/token/ERC20/extensions/IERC20Metadata.sol";
import "../interfaces/IAsset.sol";
import "../interfaces/IAssetRegistry.sol";
import "../interfaces/IBackingManager.sol";
import "../interfaces/IBasketHandler.sol";
import "../interfaces/IBroker.sol";
import "../interfaces/IDeployer.sol";
import "../interfaces/IDistributor.sol";
import "../interfaces/IFurnace.sol";
import "../interfaces/IRevenueTrader.sol";
import "../interfaces/IRToken.sol";
import "../interfaces/IStRSR.sol";
import "../mixins/Versioned.sol";
import "../plugins/assets/Asset.sol";
import "../plugins/assets/RTokenAsset.sol";
import "./Main.sol";
import "../libraries/String.sol";

Distributor.sol:
import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "@openzeppelin/contracts/utils/structs/EnumerableSet.sol";
import "../interfaces/IDistributor.sol";
import "../interfaces/IMain.sol";
import "../libraries/Fixed.sol";
import "./mixins/Component.sol";

Distributor.sol:
import "../libraries/Fixed.sol";
import "../interfaces/IFurnace.sol";
import "./mixins/Component.sol";

Main.sol:
import "@openzeppelin/contracts-upgradeable/proxy/utils/Initializable.sol";
import "@openzeppelin/contracts-upgradeable/proxy/utils/UUPSUpgradeable.sol";
import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "../interfaces/IMain.sol";
import "../mixins/ComponentRegistry.sol";
import "../mixins/Auth.sol";
import "../mixins/Versioned.sol";

RevenueTrader.sol:
import "@openzeppelin/contracts-upgradeable/token/ERC20/utils/SafeERC20Upgradeable.sol";
import "@openzeppelin/contracts/token/ERC20/IERC20.sol";
import "../interfaces/IMain.sol";
import "../interfaces/IAssetRegistry.sol";
import "./mixins/Trading.sol";
import "./mixins/TradeLib.sol";

Also in the following contracts:
RToken.sol
StRSR.sol
StRSRVotes.sol
Component.sol
RecollateralizationLib.sol
RewardableLib.sol
TradeLib.sol
Trading.sol
Array.sol
Permit.sol
RedemptionBattery.sol
```
***

## [QA-06] MISSING CHECKS FOR ADDRESS(0X0)
Many places in the code are missing CHECKS FOR ADDRESS(0X0).
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

```
BasketHandler.sol:
432: function basketsHeldBy(address account) public view returns (uint192 baskets) {

RToken.sol:
634: function queueBounds(address account)
655: function refundSpan(
        address account
737: function vestUpTo(address account, uint256 endId) private {
828: function _beforeTokenTransfer(
        address,
        address to,
        uint256

StRSR.sol:
795: function _useNonce(address owner)

StRSRVotes.sol:
137: function _mint(address account, uint256 amount) internal override {
142: function _burn(address account, uint256 amount) internal override {
```

#### Recommended Mitigation Steps
Consider adding explicit zero-address validation on input parameters of address type.
***

## [QA-07] Too many digits
Literals with many digits are difficult to read and review.
Is better to separate the digits with `_`.

```
BackingManager.sol:
33: uint48 public constant MAX_TRADING_DELAY = 31536000;

Broker.sol:
24: uint48 public constant MAX_AUCTION_LENGTH = 604800;

StRSR.sol:
37: uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
38: uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
```
***

## [QA-08] CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USED TO IMMUTABLE RATHER THAN CONSTANT
There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand.

Constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

```
StRSR.sol:
126: bytes32 private constant _PERMIT_TYPEHASH =
        keccak256(
            "Permit(address owner,address spender,uint256 value,uint256 nonce,uint256 deadline)"
        );

StRSRVotes.sol:
27: bytes32 private constant _DELEGATE_TYPEHASH =
28:        keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");

Fixed.sol:
33: bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
```