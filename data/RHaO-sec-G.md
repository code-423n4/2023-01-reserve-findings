# [G-1] Bytes constants are more efficient than string constants.

If data can fit into 32 bytes, then you should use bytes32 datatype rather than bytes or strings as it is cheaper in solidity.

[Deployer.sol](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Deployer.sol#L31)
[Deployer.sol#L103-L105](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Deployer.sol#L103-L105)
[Deployer.sol#L206-L207](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Deployer.sol#L206-L207)

[RToken.sol#L149-L151](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L149-L151)
[RToken.sol#L44](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L44)

[StRSR.sol#L42-L43](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L42-L43)
[StRSR.sol#L162-L163](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L162-L163)

# [G-2] Use calldata instead of memory for function parameters  [ref](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#use-calldata-instead-of-memory-for-function-parameters)

It is generally cheaper to load variables directly from calldata, rather than copying them to memory. Only use memory if the variable needs to be modified.


[Broker.sol#L85](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Broker.sol#L85)

[Deployer.sol#L103-L104](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Deployer.sol#L103-L104)
[Deployer.sol#L107](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Deployer.sol#L107)

[Distributor.sol#L61](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L61)
[Distributor.sol#L161](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L161)

[Main.sol#L27](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Main.sol#L27)

# [G-3]  BasketHandler.setPrimeBasket() erc20s.length should be cached

[BasketHandler.sol#L213](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L213-L214)
 
function setPrimeBasket(IERC20[] calldata erc20s, uint192[] calldata targetAmts)
        external
        governance
    {
        require(erc20s.length > 0, "cannot empty basket"); // audit gas: (SLOAD 1)
        require(erc20s.length == targetAmts.length, "must be same length"); // // audit gas: (SLOAD 2)
        requireValidCollArray(erc20s);

........


# [G-4] BasketHandler.requireValidCollArray()  erc20s[i] should be cached

[BasketHandler.sol#L654-L657](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L654-L657)

 function requireValidCollArray(IERC20[] calldata erc20s) internal view {
        IERC20 zero = IERC20(address(0));

        for (uint256 i = 0; i < erc20s.length; i++) {
            require(erc20s[i] != rsr, "RSR is not valid collateral"); // audit gas: (SLOAD 1)
            require(erc20s[i] != IERC20(address(rToken)), "RToken is not valid collateral"); // audit gas: (SLOAD 2)
            require(erc20s[i] != IERC20(address(stRSR)), "stRSR is not valid collateral"); // audit gas: (SLOAD 3)
            require(erc20s[i] != zero, "address zero is not valid collateral"); // audit gas: (SLOAD 4)
        }

........

# [G-5] Distributor._setDistribution()  share should be cached

[Distributor.sol#L165-L166](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/Distributor.sol#L165-L166)

  function _setDistribution(address dest, RevenueShare memory share) internal {
        require(dest != address(0), "dest cannot be zero");
        if (dest == FURNACE) 
          require(share.rsrDist == 0, "Furnace must get 0% of RSR");
        if (dest == ST_RSR) 
        require(share.rTokenDist == 0, "StRSR must get 0% of RToken");  // audit gas: (SLOAD 1)
        require(share.rsrDist <= 10000, "RSR distribution too high");  // audit gas: (SLOAD 2)
        require(share.rTokenDist <= 10000, "RToken distribution too high"); // audit gas: (SLOAD 3)

# [G-6]  StRSR.withdraw() basketHandler should be cached

[StRSR.sol](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol)

  function withdraw(address account, uint256 endId) external notPausedOrFrozen {
        // == Refresh ==
        assetRegistry.refresh();

        // == Checks + Effects ==
        require(basketHandler.fullyCollateralized(), "RToken uncollateralized");  // audit gas: (SLOAD 1)
        require(basketHandler.status() == CollateralStatus.SOUND, "basket defaulted");  // audit gas: (SLOAD 2)

# [G-7]  StRSR.seizeRSR() rsrAmount should be cached

[StRSR.sol#L300](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L300)

function seizeRSR(uint256 rsrAmount) external notPausedOrFrozen {
        require(_msgSender() == address(backingManager), "not backing manager");
        require(rsrAmount > 0, "Amount cannot be zero");   // audit gas: (SLOAD 1)
        uint192 initRate = exchangeRate();

        uint256 rsrBalance = rsr.balanceOf(address(this));
        require(rsrAmount <= rsrBalance, "Cannot seize more RSR than we hold");   // audit gas: (SLOAD 2)

        uint256 seizedRSR;
        uint256 rewards = rsrRewards();

        // Remove RSR from stakeRSR
        uint256 stakeRSRToTake = (stakeRSR * rsrAmount + (rsrBalance - 1)) / rsrBalance;
        stakeRSR -= stakeRSRToTake;
        seizedRSR = stakeRSRToTake;

......................

# [G-8]  x = x + y is more efficient, than x += y

[BasketHandler.sol#L345-L346](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L345-L346)
[BasketHandler.sol#L636](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/BasketHandler.sol#L636)


[RToken.sol#L330](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/RToken.sol#L330)

[StRSR.sol#L698-L699](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L698-L699)
[StRSR.sol#L299](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L299)
[StRSR.sol#L396](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L396)
[StRSR.sol#L402-L403](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L402-L403)
[StRSR.sol#L412](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L412)
[StRSR.sol#L417](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L417)
[StRSR.sol#L513](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L513)
[RToken.sol](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L549)
[RToken.sol](https://github.com/reserve-protocol/protocol/blob/master/contracts/p1/StRSR.sol#L684)


