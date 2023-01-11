
### 1st BUG
Cache Array Length Outside of Loop

#### Impact
Issue Information: 
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

Example
🤦 Bad:

for (uint256 i = 0; i < array.length; i++) {
    // invariant: array's length is not changed
}
🚀 Good:

uint256 len = array.length
for (uint256 i = 0; i < len; i++) {
    // invariant: array's length is not changed
}

#### Findings:
```
protocol/contracts/interfaces/IDeployer.sol::40 => uint48 auctionLength; // {s} the length of an auction
protocol/contracts/libraries/Array.sol::10 => uint256 arrLen = arr.length;
protocol/contracts/libraries/Array.sol::22 => uint256 arrLen = arr.length;
protocol/contracts/libraries/String.sol::13 => bytes memory bLower = new bytes(bStr.length);
protocol/contracts/mixins/Auth.sol::37 => uint48 public shortFreeze; // {s} length of an initial freeze
protocol/contracts/mixins/Auth.sol::38 => uint48 public longFreeze; // {s} length of a freeze extension
protocol/contracts/p1/AssetRegistry.sol::37 => uint256 length = assets_.length;
protocol/contracts/p1/AssetRegistry.sol::38 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/AssetRegistry.sol::48 => uint256 length = _erc20s.length();
protocol/contracts/p1/AssetRegistry.sol::49 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/AssetRegistry.sol::125 => uint256 length = _erc20s.length();
protocol/contracts/p1/AssetRegistry.sol::126 => erc20s_ = new IERC20[](length);
protocol/contracts/p1/AssetRegistry.sol::127 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/AssetRegistry.sol::135 => uint256 length = _erc20s.length();
protocol/contracts/p1/AssetRegistry.sol::136 => reg.erc20s = new IERC20[](length);
protocol/contracts/p1/AssetRegistry.sol::137 => reg.assets = new IAsset[](length);
protocol/contracts/p1/AssetRegistry.sol::138 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BackingManager.sol::217 => uint256 length = erc20s.length;
protocol/contracts/p1/BackingManager.sol::219 => uint256[] memory toRSR = new uint256[](length);
protocol/contracts/p1/BackingManager.sol::220 => uint256[] memory toRToken = new uint256[](length);
protocol/contracts/p1/BackingManager.sol::221 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BackingManager.sol::238 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BasketHandler.sol::69 => uint256 length = self.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::70 => for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
protocol/contracts/p1/BasketHandler.sol::77 => uint256 length = other.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::78 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BasketHandler.sol::169 => uint192[] memory refAmts = new uint192[](basket.erc20s.length);
protocol/contracts/p1/BasketHandler.sol::213 => require(erc20s.length > 0, "cannot empty basket");
protocol/contracts/p1/BasketHandler.sol::214 => require(erc20s.length == targetAmts.length, "must be same length");
protocol/contracts/p1/BasketHandler.sol::218 => for (uint256 i = 0; i < config.erc20s.length; ++i) {
protocol/contracts/p1/BasketHandler.sol::225 => bytes32[] memory names = new bytes32[](erc20s.length);
protocol/contracts/p1/BasketHandler.sol::227 => for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/BasketHandler.sol::262 => for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/BasketHandler.sol::280 => uint256 size = basket.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::336 => uint256 len = basket.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::395 => uint256 len = basket.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::412 => uint256 length = basket.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::413 => erc20s = new address[](length);
protocol/contracts/p1/BasketHandler.sol::414 => quantities = new uint256[](length);
protocol/contracts/p1/BasketHandler.sol::416 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BasketHandler.sol::433 => uint256 length = basket.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::434 => if (length == 0 || disabled) return FIX_ZERO;
protocol/contracts/p1/BasketHandler.sol::437 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/BasketHandler.sol::523 => while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
protocol/contracts/p1/BasketHandler.sol::529 => uint256 basketLength = config.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::533 => uint256 targetsLength = _targetNames.length();
protocol/contracts/p1/BasketHandler.sol::596 => uint256 backupLength = backup.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::630 => uint256 newBasketLength = _newBasket.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::641 => basketLength = basket.erc20s.length;
protocol/contracts/p1/BasketHandler.sol::653 => for (uint256 i = 0; i < erc20s.length; i++) {
protocol/contracts/p1/BasketHandler.sol::703 => erc20s = new IERC20[](basket.erc20s.length);
protocol/contracts/p1/BasketHandler.sol::704 => targetNames = new bytes32[](erc20s.length);
protocol/contracts/p1/BasketHandler.sol::705 => targetAmts = new uint192[](erc20s.length);
protocol/contracts/p1/BasketHandler.sol::707 => for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/BasketHandler.sol::724 => erc20s = new IERC20[](backup.erc20s.length);
protocol/contracts/p1/BasketHandler.sol::725 => for (uint256 i = 0; i < erc20s.length; ++i) {
protocol/contracts/p1/Distributor.sol::105 => Transfer[] memory transfers = new Transfer[](destinations.length());
protocol/contracts/p1/Distributor.sol::108 => for (uint256 i = 0; i < destinations.length(); ++i) {
protocol/contracts/p1/Distributor.sol::142 => uint256 length = destinations.length();
protocol/contracts/p1/Distributor.sol::143 => for (uint256 i = 0; i < length; ++i) {
protocol/contracts/p1/Distributor.sol::172 => require(destinations.length() <= MAX_DESTINATIONS_ALLOWED, "Too many destinations");
protocol/contracts/p1/RToken.sol::156 => require(bytes(name_).length > 0, "name empty");
protocol/contracts/p1/RToken.sol::157 => require(bytes(symbol_).length > 0, "symbol empty");
protocol/contracts/p1/RToken.sol::158 => require(bytes(mandate_).length > 0, "mandate empty");
protocol/contracts/p1/RToken.sol::287 => IssueItem storage curr = (queue.right < queue.items.length)
protocol/contracts/p1/RToken.sol::292 => uint256 basketSize = erc20s.length; // gas optimization
protocol/contracts/p1/RToken.sol::302 => curr.deposits = new uint256[](deposits.length);
protocol/contracts/p1/RToken.sol::471 => uint256 erc20length = erc20s.length;
protocol/contracts/p1/RToken.sol::478 => for (uint256 i = 0; i < erc20length; ++i) {
protocol/contracts/p1/RToken.sol::501 => for (uint256 i = 0; i < erc20length; ++i) {
protocol/contracts/p1/RToken.sol::665 => uint256 tokensLen = queue.tokens.length;
protocol/contracts/p1/RToken.sol::711 => for (uint256 i = 0; i < queue.tokens.length; ++i) {
protocol/contracts/p1/RToken.sol::749 => uint256 tokensLen = queue.tokens.length;
protocol/contracts/p1/StRSR.sol::168 => require(bytes(name_).length > 0, "name empty");
protocol/contracts/p1/StRSR.sol::169 => require(bytes(symbol_).length > 0, "symbol empty");
protocol/contracts/p1/StRSR.sol::312 => require(endId <= queue.length, "index out-of-bounds");
protocol/contracts/p1/StRSR.sol::440 => (uint256 left, uint256 right) = (firstRemainingDraft[draftEra][account], queue.length);
protocol/contracts/p1/StRSR.sol::463 => return draftQueues[era_][account].length;
protocol/contracts/p1/StRSR.sol::557 => index = queue.length;
protocol/contracts/p1/StRSRVotes.sol::64 => return SafeCastUpgradeable.toUint48(_checkpoints[era][account].length);
protocol/contracts/p1/StRSRVotes.sol::72 => uint256 pos = _checkpoints[era][account].length;
protocol/contracts/p1/StRSRVotes.sol::101 => uint256 high = ckpts.length;
protocol/contracts/p1/StRSRVotes.sol::198 => uint256 pos = ckpts.length;
protocol/contracts/p1/mixins/RecollateralizationLib.sol::242 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
protocol/contracts/p1/mixins/RecollateralizationLib.sol::329 => for (uint256 i = 0; i < reg.erc20s.length; ++i) {
protocol/contracts/p1/mixins/RecollateralizationLib.sol::434 => uint256 len = basketERC20s.length;
protocol/contracts/p1/mixins/RewardableLib.sol::27 => for (uint256 i = 0; i < registry.assets.length; ++i) {
protocol/contracts/p1/mixins/RewardableLib.sol::63 => uint256 erc20sLen = erc20s.length;
protocol/contracts/plugins/assets/Asset.sol::102 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
protocol/contracts/plugins/assets/Asset.sol::116 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
protocol/contracts/plugins/assets/Asset.sol::133 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
protocol/contracts/plugins/assets/FiatCollateral.sol::149 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
protocol/contracts/plugins/assets/RTokenAsset.sol::78 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
protocol/contracts/plugins/mocks/BadCollateralPlugin.sol::51 => if (errData.length == 0) revert(); // solhint-disable-line reason-string
protocol/contracts/plugins/mocks/EasyAuction.sol::277 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol::288 => for (uint256 i = 0; i < _minBuyAmounts.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol::316 => for (uint256 i = 0; i < _sellOrders.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol::377 => _minBuyAmount.length == 1 && _sellAmount.length == 1,
protocol/contracts/plugins/mocks/EasyAuction.sol::513 => for (uint256 i = 0; i < orders.length; i++) {
protocol/contracts/plugins/mocks/EasyAuction.sol::524 => for (uint256 i = 0; i < orders.length; i++) {
protocol/contracts/plugins/mocks/GnosisMock.sol::70 => auctionId = auctions.length;
protocol/contracts/plugins/mocks/GnosisMock.sol::132 => return auctions.length;
protocol/contracts/plugins/mocks/GnosisMockReentrant.sol::34 => auctionId = auctions.length;
protocol/contracts/plugins/mocks/InvalidBrokerMock.sol::22 => uint48 public auctionLength; // {s} the length of an auction
protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::202 => if (returndata.length > 0) {
protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::842 => if (returndata.length > 0) {
```
#### Tools used
Manual code review

### 2nd BUG
Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
Issue Information: 
When dealing with unsigned integer types, comparisons with != 0 are cheaper than with > 0.

Example
🤦 Bad:

// `a` being of type unsigned integer
require(a > 0, "!a > 0");
🚀 Good:

// `a` being of type unsigned integer
require(a != 0, "!a > 0");

#### Findings:
```
protocol/contracts/libraries/Fixed.sol::537 => if (mm > 0) result += 1;
```
#### Tools used
Manual code review

### 3rd BUG
Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### Impact
Issue Information: 
⚡️ Only valid for solidity versions <0.6.12 ⚡️

Access roles marked as constant results in computing the keccak256 operation each time the variable is used because assigned operations for constant variables are re-evaluated every time.

Changing the variables to immutable results in computing the hash only once on deployment, leading to gas savings.

Example
🤦 Bad:

bytes32 public constant GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");
🚀 Good:

bytes32 public immutable GOVERNOR_ROLE = keccak256("GOVERNOR_ROLE");

#### Findings:
```
protocol/contracts/libraries/Fixed.sol::33 => bytes32 constant UIntOutofBoundsHash = keccak256(abi.encodeWithSignature("UIntOutOfBounds()"));
protocol/contracts/p1/StRSR.sol::127 => keccak256(
protocol/contracts/p1/StRSR.sol::777 => bytes32 structHash = keccak256(
protocol/contracts/p1/StRSRVotes.sol::28 => keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
protocol/contracts/p1/StRSRVotes.sol::128 => _hashTypedDataV4(keccak256(abi.encode(_DELEGATE_TYPEHASH, delegatee, nonce, expiry))),
protocol/contracts/p1/mixins/TradeLib.sol::170 => assert(keccak256(reason) == UIntOutofBoundsHash);
protocol/contracts/plugins/aave/StaticATokenLM.sol::42 => keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::46 => keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::50 => keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::54 => keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::146 => bytes32 digest = keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::150 => keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::174 => bytes32 digest = keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::178 => keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::214 => bytes32 digest = keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::218 => keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::268 => keccak256(
protocol/contracts/plugins/aave/StaticATokenLM.sol::271 => keccak256(bytes(name())),
protocol/contracts/plugins/aave/StaticATokenLM.sol::272 => keccak256(EIP712_REVISION),
protocol/contracts/plugins/mocks/BadERC20.sol::32 => bytes memory data = abi.encodePacked((bytes4(keccak256("absentDecimalsFn()"))));
```
#### Tools used
Manual code review

### 4th BUG
Long Revert Strings

#### Impact
Issue Information: 
Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

If the contract(s) in scope allow using Solidity >=0.8.4, consider using Custom Errors as they are more gas efficient while allowing developers to describe the error in detail using NatSpec.

Example
🤦 Bad:

require(condition, "UniswapV3: The reentrancy guard. A transaction cannot re-enter the pool mid-swap");
🚀 Good (with shorter string):

// TODO: Provide link to a reference of error codes
require(condition, "LOK");
🚀 Good (with custom errors):

/// @notice A transaction cannot re-enter the pool mid-swap.
error NoReentrancy();

// ...

if (!condition) {
    revert NoReentrancy();
}

#### Findings:
```
protocol/contracts/p1/StRSRVotes.sol::28 => keccak256("Delegation(address delegatee,uint256 nonce,uint256 expiry)");
protocol/contracts/plugins/aave/ERC20.sol::247 => _balances[sender] = _balances[sender].sub(amount, "ERC20: transfer amount exceeds balance");
protocol/contracts/plugins/aave/ERC20.sol::287 => _balances[account] = _balances[account].sub(amount, "ERC20: burn amount exceeds balance");
```
#### Tools used
Manual code review

### 5th BUG
Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
Issue Information: 
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

Example
🤦 Bad:

uint256 b = a / 2;
uint256 c = a / 4;
uint256 d = a * 8;
🚀 Good:

uint256 b = a >> 1;
uint256 c = a >> 2;
uint256 d = a << 3;

#### Findings:
```
protocol/contracts/p1/BasketHandler.sol::372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
protocol/contracts/plugins/mocks/vendor/EasyAuction.sol::430 => return (a / 2) + (b / 2) + (((a % 2) + (b % 2)) / 2);
```
#### Tools used
Manual code review

### 6th BUG
Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
protocol/contracts/libraries/Fixed.sol::3 => pragma solidity ^0.8.9;
protocol/contracts/plugins/aave/ERC20.sol::3 => pragma solidity ^0.6.0;
protocol/contracts/plugins/mocks/ChainlinkMock.sol::2 => pragma solidity ^0.8.0;
protocol/contracts/plugins/mocks/InvalidChainlinkMock.sol::2 => pragma solidity ^0.8.0;
```
#### Tools used
Manual code review