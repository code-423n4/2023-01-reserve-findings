# 1. Unchecked return value in `swapRegistered` function
Link : https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol#L73

Summary: 

The function `swapRegistered(IAsset asset)` calls the function `basketHandler.disableBasket()` if the quantity of the asset's erc20 token is greater than 0, but does not check the return value of this function call. This could cause unexpected behavior if the function call fails.

Impact: 

If the `basketHandler.disableBasket()` call fails, the state of the contract and the assets may become inconsistent. This could lead to incorrect calculations or even loss of funds.

Recommendation:

Check the return value of the `basketHandler.disableBasket()` call and revert the transaction if it returns false.
Add error handling mechanism in case of function call failure to prevent contract failure.
Add relevant comments in the code to make it clear that the return value should be checked.

# 2. BackingManagerP1 contract allows infinite token allowance over unregistered tokens

Summary: 

The `grantRTokenAllowance` function in the `BackingManagerP1` contract allows an infinite allowance to be given to the `rToken` address over any ERC20 token passed as an argument, even if the token is not registered in the `assetRegistry`. This can lead to loss of funds for the contract owner.

Impact: 

If an attacker is able to call the `grantRTokenAllowance` function with an unregistered ERC20 token address, they can transfer an infinite amount of that token from the contract to any address they control. This can result in a loss of funds for the contract owner.

Recommendation: 

The `grantRTokenAllowance` function should be modified to check if the passed ERC20 token is registered in the `assetRegistry` before granting an allowance. Additionally, a maximum allowance limit should be implemented to prevent infinite allowance being granted.
f.e : 
```
function grantRTokenAllowance(IERC20 erc20) external notPausedOrFrozen {
    require(assetRegistry.isRegistered(erc20), "erc20 unregistered");
    require(msg.sender == owner, "Only owner can grant allowance");
    require(allowance[msg.sender][erc20] <= MAX_ALLOWANCE, "Allowance limit reached");
    // == Interaction ==
    IERC20Upgradeable(address(erc20)).safeApprove(address(main.rToken()), allowance[msg.sender][erc20] + ALLOWANCE_INCREMENT);
    allowance[msg.sender][erc20] += ALLOWANCE_INCREMENT;
}
```
Where `MAX_ALLOWANCE` is a constant set to a reasonable maximum allowance limit and `ALLOWANCE_INCREMENT` is a constant set to a reasonable allowance increment limit.

# 3. Use of deprecated `safeApprove()` function

Link : https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol#L72

From Openzeppelin docs: "Deprecated. This function has issues similar to the ones found in IERC20.approve, and its usage is discouraged."

Recommendation: 

The `safeApprove` function should be replaced with `safeIncreaseAllowance`. This will prevent the infinite allowance loop and allow for better control over the allowance granted to the `RToken` contract.

For example:
```
function grantRTokenAllowance(IERC20 erc20) external notPausedOrFrozen {
    require(assetRegistry.isRegistered(erc20), "erc20 unregistered");
    IERC20Upgradeable(address(erc20)).safeIncreaseAllowance(address(main.rToken()), type(uint256).max);
}
```
# 4. Integer overflow vulnerability in `BrokerP1.openTrade()` function

Link: https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L105

Summary:

The `BrokerP1` contract's `openTrade()` function is vulnerable to an integer overflow when calculating the `req.sellAmount` value. The vulnerability occurs when the `req.sellAmount` value is greater than the maximum value of a `uint256 (2^256 - 1)` and the value is used in an arithmetic operation without proper checks. This can lead to unexpected behavior and potential loss of funds.

Impact:

An attacker can exploit this vulnerability by passing a large value for `req.sellAmount`, causing an integer overflow and potentially leading to loss of funds.

Recommendation:

To fix this issue, add a check to validate that the `req.sellAmount` value does not exceed the maximum value of a `uint256` before performing any arithmetic operations. 

Example:
```
require(req.sellAmount <= uint256(2).pow(256) - 1, "req.sellAmount value too large");
```
# 5. Auction Length Governance Parameter Vulnerability in BrokerP1 Contract

Link : https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L133

Summary:

The BrokerP1 contract has a vulnerability where an attacker is able to set the auction length to a value greater than the maximum allowed value of 604800 seconds (1 week). This vulnerability is present in the `setAuctionLength()` function, which can be called by anyone, including non-owner actors.

Impact:

An attacker can set the auction length to a much larger value, allowing for trades to be open for an extended period of time. This can potentially lead to a loss of funds for traders, as well as a loss of credibility for the platform.

Recommendation:

To fix this vulnerability, the `setAuctionLength()` function should be restricted to only be called by the contract owner or an authorized governance contract. Additionally, the maximum value for the auction length should be enforced within the function to prevent values greater than 604800 seconds.

Example:
```
function setAuctionLength(uint48 _auctionLength) external onlyOwner {
    require(_auctionLength <= MAX_AUCTION_LENGTH, "auction length exceeds maximum allowed value");
    auctionLength = _auctionLength;
}
```
This will restrict the `setAuctionLength()` function to only be called by the owner of the contract and will also check if the value is less than or equal to the maximum allowed value. 

# 6. Integer Overflow in FurnaceP1 Contract

Link: https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Furnace.sol#L70

Summary:

The `FurnaceP1` contract has a vulnerability where the variable `numPeriods` can be set to a large value, resulting in an integer overflow when used in a mathematical operation.

Impact:

An attacker can exploit this vulnerability by calling the `melt` function with a block timestamp that is significantly greater than the `lastPayout` variable. This would cause the `numPeriods` variable to be set to a large value, resulting in an integer overflow when used in the mathematical operation. This can result in unexpected and unintended behavior, potentially leading to loss of funds.

Recommendation:

The vulnerability can be fixed by implementing a check to ensure that the `numPeriods` variable is not set to a value greater than the maximum value that can be represented by its data type. Additionally, it is recommended to use the `SafeMath` library to ensure that mathematical operations are performed securely.
Example:
```
function melt() external notPausedOrFrozen {
        if (uint48(block.timestamp) < uint64(lastPayout) + period) return;
        // Check for maximum value of numPeriods
        uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;
        require(numPeriods <= MAX_PERIOD, "numPeriods exceeds maximum value");
        // Use SafeMath library for mathematical operations
        uint192 payoutRatio = FixLib.minus(FIX_ONE, FixLib.pow(FixLib.minus(FIX_ONE, ratio), numPeriods));
        uint256 payoutAmount = rToken.balanceOf(address(this)) * payoutRatio / FIX_ONE;
        rToken.melt(payoutAmount);
        lastPayout = uint48(block.timestamp);
        lastPayoutBal = rToken.balanceOf(address(this));
}
```
# 7. Improper Validation of User-Supplied Input in the 'manageToken' Function

Link: https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L53

Summary: 

The `manageToken` function in the `RevenueTraderP1` contract is vulnerable to an attack where an attacker can call the function with an arbitrary address and cause the contract to trade assets for a non-existent or invalid token. This vulnerability is caused by the lack of proper validation on the input passed to the function to check if the provided token is a valid ERC20 token and if it is registered with the `assetRegistry` contract.

Impact: 

An attacker can exploit this vulnerability to cause the contract to trade assets for a non-existent or invalid token, resulting in a loss of assets for the contract and its users.

Recommendation: 

To fix this vulnerability, the `manageToken` function should be updated to check if the provided token is a valid ERC20 token and if it is registered with the `assetRegistry` contract before executing any trade. This check can be implemented by calling the `isValidToken` function of the `assetRegistry` contract and passing the address of the token to be checked.

Example:
```
require(assetRegistry.isValidToken(address(erc20)), "Invalid Token");
```
This should be added before the check of `if (erc20 == tokenToBuy)`

# 8. Incorrect implementation of `maxTradeSlippage` in `RevenueTraderP1`

Link : https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RevenueTrader.sol#L53

Summary: 

The `RevenueTraderP1` contract uses the `maxTradeSlippage` variable to ensure that trades are made at a reasonable price. However, the implementation of the check is incorrect and will always pass, allowing trades to be made at any price.

Impact: 

An attacker could exploit this vulnerability to trade assets at an arbitrary high price, resulting in significant financial loss to the contract's owner.

Recommendation: 

Correct the implementation of the `maxTradeSlippage` check by comparing the `sellPrice` and `buyPrice` variables to the correct formula.

Example:
```
TradeInfo memory trade = TradeInfo({
            sell: sell,
            buy: buy,
            sellAmount: sell.bal(address(this)),
            buyAmount: 0,
            sellPrice: sellPrice,
            buyPrice: buyPrice
        });
        TradingRules memory rules = TradingRules({
            minTradeVolume: minTradeVolume,
            maxTradeSlippage: maxTradeSlippage
        });
        // check for trade slippage
        require(
            (buyPrice * (1 + maxTradeSlippage / UINT_FIX)) >= sellPrice &&
            (buyPrice * (1 - maxTradeSlippage / UINT_FIX)) <= sellPrice,
            "trade slippage too high"
        );
```
# 9. TradeLib `prepareTradeSell()` function does not check for integer overflow in `safeMulDivCeil()`

Link : https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/TradeLib.sol#L59

Summary: 

The TradeLib `prepareTradeSell()` function in the provided code does not check for integer overflow when calculating the equivalent buy amount within the `safeMulDivCeil()` function.

Impact: 

This vulnerability could lead to an attacker being able to exploit the integer overflow and potentially cause unexpected behavior in the smart contract, leading to potential loss of funds.

Recommendation: 

It is recommended to check for integer overflow within the `safeMulDivCeil()` function to prevent any unexpected behavior in the smart contract.

Example:
```
uint192 b = safeMulDivCeil(
    ITrading(address(this)),
    s.mul(FIX_ONE.minus(rules.maxTradeSlippage)),
    trade.sellPrice, // {UoA/sellTok}
    trade.buyPrice // {UoA/buyTok}
);
```
Add a check for integer overflow before the line above.
```
    if(s > maxSell)
        return (false, req);
```
This check will prevent unexpected behavior and protect the smart contract from any loss of funds.

# 10. Old Solidity version please upgrade this latest version.

Almost all contracts are using solidity version 0.8.9. Please upgrade to the latest version 0.8.17