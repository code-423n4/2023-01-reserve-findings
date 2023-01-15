## [G-01] Cache array length outside of loop

### Description
If the array's length is not changed during a loop, caching it outside the loop saves reading it on each iteration

### Findings
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
::523 => while (_targetNames.length() > 0) _targetNames.remove(_targetNames.at(0));
```

### References

- [G002 - Cache Array Length Outside of Loop](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g002---cache-array-length-outside-of-loop)
- [Avoid unnecessary read of array length in for loops can save gas](https://github.com/code-423n4/2021-11-badgerzaps-findings/issues/36)



## [G-02] Missing implementation Shift Right/Left for division and multiplication

### Description

The `SHR` opcode only utilizes 3 gas, compared to the 5 gas used by the `DIV` opcode. Additionally, shifting is used to get around Solidity's division operation's division-by-0 prohibition.

### Findings

```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
::372 => uint256 shiftDelta = rawDelta + (FIX_ONE / 2);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
::427 => return (FIX_SCALE_SQ + (stakeRate / 2)) / stakeRate; // ROUND method
::453 => test = (left + right) / 2; // left < test < right because left < right - 1
::816 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
::824 => require(rewardPeriod * 2 <= unstakingDelay, "unstakingDelay/rewardPeriod incompatible");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol
::430 => return (a / 2) + (b / 2) + (((a % 2) + (b % 2)) / 2);
```

### References
- [G008 - Use Shift Right/Left instead of Division/Multiplication if possible](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
- [EVM Opcodes](https://www.evm.codes/)



## [G-03] Make function external instead of public

### Description

Version 0.6.9 removed the restriction on public functions accepting calldata arguments. Public functions for Solidity versions 0.6.9 have to transfer the parameters to memory.

Note: valid only for solidity versions `<0.6.9`

### Findings
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol
::66 =>     function name() public view returns (string memory) { || pragma solidity ^0.6.0;
::74 =>     function symbol() public view returns (string memory) { || pragma solidity ^0.6.0;
::91 =>     function decimals() public view returns (uint8) { || pragma solidity ^0.6.0;
::98 =>     function totalSupply() public view override returns (uint256) { || pragma solidity ^0.6.0;
::105 =>     function balanceOf(address account) public view override returns (uint256) { || pragma solidity ^0.6.0;
::117 =>     function transfer(address recipient, uint256 amount) public virtual override returns (bool) { || pragma solidity ^0.6.0;
::142 =>     function approve(address spender, uint256 amount) public virtual override returns (bool) { || pragma solidity ^0.6.0;
::188 =>     function increaseAllowance(address spender, uint256 addedValue) public virtual returns (bool) { || pragma solidity ^0.6.0;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol
::37 =>     function deposit() public payable { || pragma solidity >=0.4.22 <0.6;
::42 =>     function withdraw(uint256 wad) public { || pragma solidity >=0.4.22 <0.6;
::49 =>     function totalSupply() public view returns (uint256) { || pragma solidity >=0.4.22 <0.6;
::53 =>     function approve(address guy, uint256 wad) public returns (bool) { || pragma solidity >=0.4.22 <0.6;
::59 =>     function transfer(address dst, uint256 wad) public returns (bool) { || pragma solidity >=0.4.22 <0.6;
```

### Resources

- [G009 - Make Function external instead of public](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g009---make-function-external-instead-of-public)
- [Public vs External Functions in Solidity | Gustavo (Gus) Guimaraes post](https://gus-tavo-guim.medium.com/public-vs-external-functions-in-solidity-b46bcf0ba3ac)
- [StackOverflow answer](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices?answertab=active#tab-top)



## [G-04] Using bools for storage incurs overhead

### Description

Use uint256 for true/false to avoid a Gwarmaccess (100 gas), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past.

### Findings
```
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/interfaces/IGnosis.sol
::17 =>     bool minFundingThresholdNotReached;
::18 =>     bool isAtomicClosureAllowed;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol
::93 =>     bool isRSR = erc20 == rsr; // if false: isRToken
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol
::500 =>    bool allZero = true;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol
::107 =>    bool minFundingThresholdNotReached;
::108 =>    bool isAtomicClosureAllowed;
::319 =>    bool success = sellOrders[auctionId].removeKeepHistory(_sellOrders[i]);
::523 =>    bool minFundingThresholdNotReached = auctionData[auctionId].minFundingThresholdNotReached;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol
::321 =>    bool result = removeKeepHistory(self, elementToRemove);
```

### References

- [[GAS-1] Using bools for storage incurs overhead](https://gist.github.com/Picodes/ab2df52379e4b4993709be1b91aab651#gas-1-using-bools-for-storage-incurs-overhead)
- [OpenZeppelin Contracts](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)


## [G-05] Use "require" instead of "assert" when possible

If `assert()` returns a false assertion, it compiles to the invalid opcode `0xfe`, which eats up all the gas left and completely undoes the changes. `require()` compiles to `0xfd`, which is the opcode for a `REVERT`, indicating that it will return the remaining gas if it returns a false assertion.

### Findings

```Solidity
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol
::249 =>         assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
::556 =>             assert(targetIndex < targetsLength);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
::696 =>         assert(totalStakes + amount < type(uint224).max);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol
::110 =>         assert(doTrade);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol
::78 =>                 assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);
::102 =>             assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol
::44 =>         assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);
::108 =>         assert(
::168 =>             assert(errorCode == 0x11 || errorCode == 0x12);
::170 =>             assert(keccak256(reason) == UIntOutofBoundsHash);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol
::114 =>         assert(address(trades[sell]) == address(0));
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol
::345 =>             assert(amt == amountToWithdraw);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol
::98 =>                 assert(low == 0);
::112 =>             assert(low <= high);
::147 =>         assert(lotLow <= lotHigh);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol
::137 =>                 assert(low == 0);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol
::74 =>             assert(low <= high);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol
::63 =>         assert(status == TradeStatus.PENDING);
::98 =>         assert(origin_ != address(0));
::182 =>             assert(isAuctionCleared());
```

### Reference

- [Assert() vs Require() in Solidity – Key Difference & What to Use](https://codedamn.com/news/solidity/assert-vs-require-in-solidity)



## [G-06] Use assembly to check for `address(0)`

### Description

By checking for `address(0)` using assembly language, you can avoid the use of more gas-expensive operations such as calling a smart contract or reading from storage. This can save 6 gas per instance.

### Findings

```Solidity
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol
::94 =>         require(account != address(0), "cannot grant role to address 0");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol
::37 =>         require(address(val) != address(0), "invalid RToken address");
::45 =>         require(address(val) != address(0), "invalid StRSR address");
::53 =>         require(address(val) != address(0), "invalid AssetRegistry address");
::61 =>         require(address(val) != address(0), "invalid BasketHandler address");
::69 =>         require(address(val) != address(0), "invalid BackingManager address");
::77 =>         require(address(val) != address(0), "invalid Distributor address");
::85 =>         require(address(val) != address(0), "invalid RSRTrader address");
::93 =>         require(address(val) != address(0), "invalid RTokenTrader address");
::101 =>         require(address(val) != address(0), "invalid Furnace address");
::109 =>         require(address(val) != address(0), "invalid Broker address");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
::670 =>         if (erc20 == IERC20(address(0))) return false;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol
::57 =>         require(address(gnosis_) != address(0), "invalid Gnosis address");
::59 =>             address(tradeImplementation_) != address(0),
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol
::49 =>             address(rsr_) != address(0) &&
::50 =>                 address(gnosis_) != address(0) &&
::51 =>                 address(rsrAsset_) != address(0) &&
::52 =>                 address(implementations_.main) != address(0) &&
::53 =>                 address(implementations_.trade) != address(0) &&
::54 =>                 address(implementations_.components.assetRegistry) != address(0) &&
::55 =>                 address(implementations_.components.backingManager) != address(0) &&
::56 =>                 address(implementations_.components.basketHandler) != address(0) &&
::57 =>                 address(implementations_.components.broker) != address(0) &&
::58 =>                 address(implementations_.components.distributor) != address(0) &&
::59 =>                 address(implementations_.components.furnace) != address(0) &&
::60 =>                 address(implementations_.components.rsrTrader) != address(0) &&
::61 =>                 address(implementations_.components.rTokenTrader) != address(0) &&
::62 =>                 address(implementations_.components.rToken) != address(0) &&
::63 =>                 address(implementations_.components.stRSR) != address(0),
::109 =>         require(owner != address(0) && owner != address(this), "invalid owner");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol
::162 =>         require(dest != address(0), "dest cannot be zero");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol
::32 =>         require(address(rsr_) != address(0), "invalid RSR address");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol
::29 =>         require(address(tokenToBuy_) != address(0), "invalid token address");
::54 =>         if (address(trades[erc20]) != address(0)) return;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
::675 =>         require(from != address(0), "ERC20: transfer from the zero address");
::676 =>         require(to != address(0), "ERC20: transfer to the zero address");
::695 =>         require(account != address(0), "ERC20: mint to the zero address");
::711 =>         require(account != address(0), "ERC20: burn from the zero address");
::732 =>         require(owner != address(0), "ERC20: approve from the zero address");
::733 =>         require(spender != address(0), "ERC20: approve to the zero address");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSRVotes.sol
::172 =>             if (src != address(0)) {
::181 =>             if (dst != address(0)) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol
::34 =>         require(address(main_) != address(0), "main is zero address");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol
::94 =>         if (address(trade.sell) == address(0) || address(trade.buy) == address(0)) {
::401 =>         if (address(trade.sell) == address(0) && address(trade.buy) != address(0)) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol
::68 =>         if (address(trade) == address(0)) return;
::114 =>         assert(address(trades[sell]) == address(0));
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol
::242 =>         require(sender != address(0), "ERC20: transfer from the zero address");
::243 =>         require(recipient != address(0), "ERC20: transfer to the zero address");
::262 =>         require(account != address(0), "ERC20: mint to the zero address");
::283 =>         require(account != address(0), "ERC20: burn from the zero address");
::310 =>         require(owner != address(0), "ERC20: approve from the zero address");
::311 =>         require(spender != address(0), "ERC20: approve to the zero address");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol
::94 =>             if (address(incentivesController) != address(0)) {
::142 =>         require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
::170 =>         require(depositor != address(0), StaticATokenErrors.INVALID_DEPOSITOR);
::210 =>         require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
::294 =>         require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);
::317 =>         require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);
::363 =>         if (address(INCENTIVES_CONTROLLER) == address(0)) {
::366 =>         if (from != address(0)) {
::369 =>         if (to != address(0)) {
::378 =>         if (address(INCENTIVES_CONTROLLER) == address(0)) {
::404 =>         if (address(INCENTIVES_CONTROLLER) == address(0)) {
::473 =>         if (address(INCENTIVES_CONTROLLER) == address(0)) {
::485 =>         if (address(INCENTIVES_CONTROLLER) == address(0)) {
::492 =>         if (address(INCENTIVES_CONTROLLER) == address(0)) {
::531 =>         if (address(INCENTIVES_CONTROLLER) == address(0)) {
::578 =>         if (address(INCENTIVES_CONTROLLER) == address(0)) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol
::49 =>         require(address(chainlinkFeed_) != address(0), "missing chainlink feed");
::51 =>         require(address(erc20_) != address(0), "missing erc20");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol
::26 =>         require(address(comptroller_) != address(0), "comptroller missing");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol
::29 =>             address(targetUnitChainlinkFeed_) != address(0),
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol
::30 =>         require(address(comptroller_) != address(0), "comptroller missing");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol
::24 =>         require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol
::24 =>         require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol
::28 =>         require(address(erc20_) != address(0), "missing erc20");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ATokenMock.sol
::81 =>         if (address(aaveToken) != address(0) && aaveBalances[msg.sender] > 0) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/ComptrollerMock.sol
::24 =>         if (address(compToken) != address(0)) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol
::260 =>             if (allowListManger != address(0)) {
::500 =>         auctionAccessManager[auctionId] = address(0);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMock.sol
::108 =>         if (bid.bidder == address(0)) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/GnosisMockReentrant.sol
::74 =>         if (bid.bidder == address(0)) {
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol
::615 =>         return self.idToAddress[id + 1] != address(0);
::637 =>         require(addr != address(0), "Cannot insert zero address");
::640 =>         if (self.addressToId[addr] != 0 || self.idToAddress[id + 1] != address(0)) {
::936 =>         require(newOwner != address(0), "Ownable: new owner is the zero address");
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol
::98 =>         assert(origin_ != address(0));
```



## [G-07] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

### Description

Gas consumption can be greater if you use items that are less than 32 bytes in size. This is such that the EVM can only handle 32 bytes at once. In order to increase the element's size from 32 bytes to the necessary amount, the EVM must do extra operations if it is lower than that. When necessary, it is advised to utilize a bigger size and then downcast.

### Findings

```Solidity
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol
::37 => uint8 constant FIX_DECIMALS = 18;
::41 => uint64 constant FIX_SCALE = 1e18;
::310 =>     uint64 constant FIX_HALF = uint64(FIX_SCALE) / 2;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/RedemptionBattery.sol
::11 => uint48 constant BLOCKS_PER_HOUR = 300; // {blocks/hour}
::64 =>         uint48 blocks = uint48(block.number) - battery.lastBlock;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol
::8 => uint48 constant MAX_UNFREEZE_AT = type(uint48).max;
::9 => uint48 constant MAX_SHORT_FREEZE = 2592000; // 1 month
::10 => uint48 constant MAX_LONG_FREEZE = 31536000; // 1 year
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol
::33 =>     uint48 public constant MAX_TRADING_DELAY = 31536000; // {s} 1 year
::113 =>         uint48 basketTimestamp = basketHandler.timestamp();
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol
::24 =>     uint48 public constant MAX_AUCTION_LENGTH = 604800; // {s} max valid duration - 1 week
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol
::34 =>     uint8 public constant MAX_DESTINATIONS_ALLOWED = 100;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol
::16 =>     uint48 public constant MAX_PERIOD = 31536000; // {s} 1 year
::74 =>         uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol
::198 =>         uint48 basketNonce = basketHandler.nonce();
::387 =>         uint48 basketNonce = basketHandler.nonce();
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
::37 =>     uint48 public constant MAX_UNSTAKING_DELAY = 31536000; // {s} 1 year
::38 =>     uint48 public constant MAX_REWARD_PERIOD = 31536000; // {s} 1 year
::45 =>     uint8 public constant decimals = 18;
::275 =>         (uint256 index, uint64 availableAt) = pushDraft(account, rsrAmount);
::498 =>         uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;
::560 =>         uint64 lastAvailableAt = index > 0 ? queue[index - 1].availableAt : 0;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol
::137 =>             uint48 delta = uint48(block.timestamp) - lastSave; // {s}
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol
::46 =>         int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol
::49 =>         int8 shiftLeft = 8 - int8(referenceERC20Decimals) - 18;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/FiatCollateral.sol
::47 =>     uint48 private constant NEVER = type(uint48).max;
::48 =>     uint48 private _whenDefault = NEVER;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/OracleLib.sol
::19 =>         (uint80 roundId, int256 p, , uint256 updateTime, uint80 answeredInRound) = chainlinkFeed
::26 =>         uint48 secondsSince = uint48(block.timestamp - updateTime);
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/CTokenMock.sol
::48 =>         int8 leftShift = 18 - int8(decimals()) + int8(IERC20Metadata(_underlyingToken).decimals());
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/EasyAuction.sol
::125 =>     uint64 public feeReceiverUserId = 1;
::177 =>         uint64 userId = getUserId(msg.sender);
::314 =>         uint64 userId = getUserId(msg.sender);
::338 =>         (, , uint96 auctioneerSellAmount) = auctionData[auctionId]
::346 =>             (, , uint96 sellAmountOfIter) = iterOrder.decodeOrder();
::355 =>         (, uint96 buyAmountOfIter, uint96 sellAmountOfIter) = iterOrder.decodeOrder();
::380 =>         uint64 userId = getUserId(msg.sender);
::414 =>         uint96 fillVolumeOfAuctioneerOrder = fullAuctionedAmount;
::519 =>         (, uint96 priceNumerator, uint96 priceDenominator) = auction
::522 =>         (uint64 userId, , ) = orders[0].decodeOrder();
::525 =>             (uint64 userIdOrder, uint96 buyAmount, uint96 sellAmount) = orders[i].decodeOrder();
::570 =>             (, uint96 priceNumerator, uint96 priceDenominator) = auctionData[auctionId]
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/WETH.sol
::23 =>     uint8 public decimals = 18;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/mocks/vendor/EasyAuction.sol
::257 =>         (, , uint96 denominator) = decodeOrder(elementToInsert);
::342 =>         (uint64 userIdLeft, uint96 priceNumeratorLeft, uint96 priceDenominatorLeft) = decodeOrder(
::610 =>         mapping(uint64 => address) idToAddress;
::611 =>         mapping(address => uint64) addressToId;
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol
::31 =>     uint96 public constant MAX_ORDERS = 1e5;
::112 =>         uint96 sellAmount = uint96(
::121 =>         uint96 minBuyAmount = uint96(Math.max(1, req.minBuyAmount)); // Safe downcast; require'd
::202 =>             uint192 clearingPrice = shiftl_toFix(boughtAmt, -int8(buy.decimals())).div(
```

### References

- [Layout of State Variables in Storage | Solidity docs](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html#layout-of-state-variables-in-storage)
- [GAS OPTIMIZATIONS ISSUES by Gokberk Gulgun](https://hackmd.io/@W1m6lTsFT5WAy9C_lRTX_g/rkr5Laoys)