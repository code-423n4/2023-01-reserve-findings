## Gas Optimizations
### [G-01]: USING A = A + B INSTEAD OF A += B IS MORE GAS EFFICIENT. SIMILARLY A = A – B IS MORE EFFICIENT THAN A -= B
There are 36 instances of this. 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
>File: contracts/p1/BasketHandler.sol
>
>345: low256 += quantityMulPrice(qty, lowP);
>346: high256 += quantityMulPrice(qty, highP);
>636: nonce += 1;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol
>File: contracts/p1/Distributor.sol
>
>145: revTotals.rTokenTotal += share.rTokenDist;
>146: revTotals.rsrTotal += share.rsrDist;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol
>File: contracts/p1/Furnace.sol
>
>81: lastPayout += numPeriods * period;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol
>File: contracts/p1/RToken.sol
>
>330: liabilities[IERC20(erc20s[i])] += deposits[i];
>678: liabilities[IERC20(queue.tokens[i])] -= amt[i];
>687: liabilities[IERC20(queue.tokens[i])] -= amt[i];
>761: liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];
>771: liabilities[IERC20(queue.tokens[i])] -= amtDeposits[i];

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
>File: contracts/p1/StRSR.sol
>
>229: stakeRSR += rsrAmount;
>387: stakeRSR -= stakeRSRToTake;
>396: seizedRSR += stakeRSR;
>402: draftRSR -= draftRSRToTake;
>403: seizedRSR += draftRSRToTake;
>412: seizedRSR += draftRSR;
>417: seizedRSR += (rewards * rsrAmount + (rsrBalance - 1)) / rsrBalance;
>513: stakeRSR += payout;
>516: payoutLastPaid += numPeriods * rewardPeriod;
>549: draftRSR += rsrAmount;
>684: eraStakes[to] += amount;
>698: stakes[era][account] += amount;
>699: totalStakes += amount;
>721: totalStakes -= amount;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol
>File: contracts/p1/mixins/RecollateralizationLib.sol
>
>264: assetsLow += low.mul(bal, FLOOR);
>271: else assetsHigh += val;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol
>File: contracts/mixins/Auth.sol
>
>136: longFreezes[_msgSender()] -= 1; // reverts on underflow

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Fixed.sol
>File: contracts/libraries/Fixed.sol
>
>104: shiftLeft += 18;
>392: decimals -= 18; // shift so that toUint happens at the same time.
>504: if (mm > lo) hi -= 1;
>505: lo -= mm;
>509: lo += hi * ((0 - pow2) / pow2 + 1);
>537: if (mm > 0) result += 1;
>539: if (mm > ((z - 1) / 2)) result += 1; // z should be z-1
>555: if (mm < lo) hi -= 1;

### [G-02]: USE ASSEMBLY TO CHECK FOR ADDRESS(0)
Saves 6 gas per instance if using assembly to check for address(0)
There are 60 instances of this.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/Auth.sol
>File: contracts/mixins/Auth.sol
>
>94: require(account != address(0), "cannot grant role to address 0");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/mixins/ComponentRegistry.sol
>File: contracts/mixins/ComponentRegistry.sol
>
>37: require(address(val) != address(0), "invalid RToken address");
>45: require(address(val) != address(0), "invalid StRSR address");
>53: require(address(val) != address(0), "invalid AssetRegistry address");
>61: require(address(val) != address(0), "invalid BasketHandler address");
>69: require(address(val) != address(0), "invalid BackingManager address");
>77: require(address(val) != address(0), "invalid Distributor address");
>85: require(address(val) != address(0), "invalid RSRTrader address");
>93: require(address(val) != address(0), "invalid RTokenTrader address");
>101: require(address(val) != address(0), "invalid Furnace address"); 
>109: require(address(val) != address(0), "invalid Broker address");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol
>File: contracts/p1/Broker.sol
>
>57: require(address(gnosis_) != address(0), "invalid Gnosis address");
>58-61: 
>>require(
>>    address(tradeImplementation_) != address(0),
>>    "invalid Trade Implementation address"
>>    ); 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol
>File: contracts/p1/Deployer.sol
>
>109: require(owner != address(0) && owner != address(this), "invalid owner");
>48-64: 
>>require(
>>            address(rsr_) != address(0) &&
>>                address(gnosis_) != address(0) &&
>>                address(rsrAsset_) != address(0) &&
>>                address(implementations_.main) != address(0) &&
>>                address(implementations_.trade) != address(0) &&
>>                address(implementations_.components.assetRegistry) != address(0) &&
>>                address(implementations_.components.backingManager) != address(0) &&
>>                address(implementations_.components.basketHandler) != address(0) &&
>>                address(implementations_.components.broker) != address(0) &&
>>                address(implementations_.components.distributor) != address(0) &&
>>                address(implementations_.components.furnace) != address(0) &&
>>               address(implementations_.components.rsrTrader) != address(0) &&
>>                address(implementations_.components.rTokenTrader) != address(0) &&
>>                address(implementations_.components.rToken) != address(0) &&
>>                address(implementations_.components.stRSR) != address(0),
>>            "invalid address"
>>       );

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol
>File: contracts/p1/Distributor.sol
>
>162: require(dest != address(0), "dest cannot be zero");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Main.sol
>File: contracts/p1/Main.sol
>
>32: require(address(rsr_) != address(0), "invalid RSR address");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol
>File: contracts/p1/RevenueTrader.sol
>
>29: require(address(tokenToBuy_) != address(0), "invalid token address");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol
>File: contracts/p1/StRSR.sol
>
>675: require(from != address(0), "ERC20: transfer from the zero address");
>676: require(to != address(0), "ERC20: transfer to the zero address");
>695: require(account != address(0), "ERC20: mint to the zero address");
>711: require(account != address(0), "ERC20: burn from the zero address"); 
>732: require(owner != address(0), "ERC20: approve from the zero address");
>733: require(spender != address(0), "ERC20: approve to the zero address");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Component.sol
>File: contracts/p1/mixins/Component.sol
>
>34: require(address(main_) != address(0), "main is zero address");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol
>File: contracts/p1/mixins/Trading.sol
>
>114: assert(address(trades[sell]) == address(0));

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/ERC20.sol
>File: contracts/plugins/aave/ERC20.sol
>
>242: require(sender != address(0), "ERC20: transfer from the zero address");
>243: require(recipient != address(0), "ERC20: transfer to the zero address");
>262: require(account != address(0), "ERC20: mint to the zero address");
>283: require(account != address(0), "ERC20: burn from the zero address");
>310: require(owner != address(0), "ERC20: approve from the zero address");
>311: require(spender != address(0), "ERC20: approve to the zero address");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/aave/StaticATokenLM.sol
>File: contracts/plugins/aave/StaticATokenLM.sol
>
>142: require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
>170: require(depositor != address(0), StaticATokenErrors.INVALID_DEPOSITOR);
>210: require(owner != address(0), StaticATokenErrors.INVALID_OWNER);
>294: require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);
>317: require(recipient != address(0), StaticATokenErrors.INVALID_RECIPIENT);

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/Asset.sol
>File: contracts/plugins/assets/Asset.sol
>
>49: require(address(chainlinkFeed_) != address(0), "missing chainlink feed");
>51: require(address(erc20_) != address(0), "missing erc20");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenFiatCollateral.sol
>File: contracts/plugins/assets/CTokenFiatCollateral.sol
>
>26: require(address(comptroller_) != address(0), "comptroller missing");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenNonFiatCollateral.sol
>File: contracts/plugins/assets/CTokenNonFiatCollateral.sol
>
>28-31: require(address(targetUnitChainlinkFeed_) != address(0),"missing target unit chainlink feed");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/CTokenSelfReferentialCollateral.sol
>File: contracts/plugins/assets/CTokenSelfReferentialCollateral.sol
>
>30: require(address(comptroller_) != address(0), "comptroller missing");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/EURFiatCollateral.sol
>File: contracts/plugins/assets/EURFiatCollateral.sol
>
>24: require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/NonFiatCollateral.sol
>File: contracts/plugins/assets/NonFiatCollateral.sol
>
>24: require(address(uoaPerTargetFeed_) != address(0), "missing uoaPerTarget feed");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/assets/RTokenAsset.sol
>File: contracts/plugins/assets/RTokenAsset.sol
>
>28: require(address(erc20_) != address(0), "missing erc20");

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol
>File: contracts/plugins/assets/RTokenAsset.sol
>
>98: assert(origin_ != address(0));

### [G-03, G-04]: ++I/I++ CAN BE UNCHECKED IN FOR LOOPS TO SAVE GAS. LOOP VARIABLE CAN BE INITIALIZED OUTSIDE LOOP TO SAVE GAS.
There are 44  instances of this. 

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Array.sol
>File: contracts\libraries\Array.sol
>
>11: for (uint256 i = 1; i < arrLen; ++i) {
>23: for (uint256 i = 1; i < arrLen; ++i) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol
>File: contracts\p1\AssetRegistry.sol
>
>38: for (uint256 i = 0; i < length; ++i) {
>49: for (uint256 i = 0; i < length; ++i) {
>127: for (uint256 i = 0; i < length; ++i) {
>138: for (uint256 i = 0; i < length; ++i) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol
>File: contracts\p1\BackingManager.sol
>
>221: for (uint256 i = 0; i < length; ++i) {
>238: for (uint256 i = 0; i < length; ++i) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
>File: contracts\p1\BasketHandler.sol
>
>70: for (uint256 i = 0; i < length; ++i) self.refAmts[self.erc20s[i]] = FIX_ZERO;
>78: for (uint256 i = 0; i < length; ++i) {
>218: for (uint256 i = 0; i < config.erc20s.length; ++i) {
>227: for (uint256 i = 0; i < erc20s.length; ++i) {
>262: for (uint256 i = 0; i < erc20s.length; ++i) {
>286: for (uint256 i = 0; i < size; ++i) {
>337: for (uint256 i = 0; i < len; ++i) {
>397: for (uint256 i = 0; i < len; ++i) {
>416: for (uint256 i = 0; i < length; ++i) {
>437: for (uint256 i = 0; i < length; ++i) {
>530: for (uint256 i = 0; i < basketLength; ++i) {
>548: for (uint256 i = 0; i < basketLength; ++i) {
>586: for (uint256 i = 0; i < targetsLength; ++i) {
>643: for (uint256 i = 0; i < basketLength; ++i) {
>707: for (uint256 i = 0; i < erc20s.length; ++i) {
>725: for (uint256 i = 0; i < erc20s.length; ++i) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol
>File: contracts\p1\Distributor.sol
>
>108: for (uint256 i = 0; i < destinations.length(); ++i) {
>143: for (uint256 i = 0; i < length; ++i) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol
>File: contracts\p1\RToken.sol
>
>270: for (uint256 i = 0; i < erc20s.length; ++i) {
>303: for (uint256 i = 0; i < basketSize; ++i) {
>329: for (uint256 i = 0; i < basketSize; ++i) {
>334: for (uint256 i = 0; i < basketSize; ++i) {
>478: for (uint256 i = 0; i < erc20length; ++i) {
>501: for (uint256 i = 0; i < erc20length; ++i) {
>674: for (uint256 i = 0; i < tokensLen; ++i) {
>683: for (uint256 i = 0; i < tokensLen; ++i) {
>711: for (uint256 i = 0; i < queue.tokens.length; ++i) {
>757: for (uint256 i = 0; i < tokensLen; ++i) {
>767: for (uint256 i = 0; i < tokensLen; ++i) {
>793: for (uint256 i = 0; i < tokensLen; ++i) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RecollateralizationLib.sol
>File: contracts\p1\mixins\RecollateralizationLib.sol
>
>242: for (uint256 i = 0; i < reg.erc20s.length; ++i) {
>329: for (uint256 i = 0; i < reg.erc20s.length; ++i) {
>437: for (uint256 i = 0; i < len; ++i) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/RewardableLib.sol
>File: contracts\p1\mixins\RewardableLib.sol
>
>27: for (uint256 i = 0; i < registry.assets.length; ++i) {
>67: for (uint256 i = 0; i < erc20sLen; ++i) {
>73: for (uint256 i = 0; i < erc20sLen; ++i) {

### [G-05]: USE CALLDATA INSTEAD OF MEMORY
Use calldata instead of memory in external functions to save gas. 
There are 6 instances of this.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol
>File: contracts\p1\AssetRegistry.sol
>
>124: function erc20s() external view returns (IERC20[] memory erc20s_) {
>134: function getRegistry() external view returns (Registry memory reg) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol
>File: contracts\p1\BasketHandler.sol
>
>394: function basketTokens() external view returns (IERC20[] memory erc20s) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol
>File: contracts\p1\Broker.sol
>
>85: function openTrade(TradeRequest memory req) external notPausedOrFrozen returns (ITrade) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Deployer.sol
>File: contracts\p1\Deployer.sol
>
>102: function deploy(
103:         string memory name,
104:         string memory symbol,
105          string calldata mandate,
106          address owner,
107:         DeploymentParams memory params
108      ) external returns (address) {

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol
>File: contracts\p1\Distributor.sol
>
>61: function setDistribution(address dest, RevenueShare memory share) external governance {
