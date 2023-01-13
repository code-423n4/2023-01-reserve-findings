# GAS REPORTS

##

### [GAS-1]  ++I/I++ OR --I/I-- SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} OR  UNCHECKED{--I}/UNCHECKED{I--}WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

       38 :   for (uint256 i = 0; i < length; ++i) {

       49:   for (uint256 i = 0; i < length; ++i) {

      127 :  for (uint256 i = 0; i < length; ++i) {

      138 :  for (uint256 i = 0; i < length; ++i) {

[FILE : BackingManager.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BackingManager.sol)

      221 :  for (uint256 i = 0; i < length; ++i) {

      238 :   for (uint256 i = 0; i < length; ++i) {

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

      70 :   for (uint256 i = 0; i < length; ++i) 

      78 :   for (uint256 i = 0; i < length; ++i) {

      218:   for (uint256 i = 0; i < config.erc20s.length; ++i) {

      227:   for (uint256 i = 0; i < erc20s.length; ++i) {

     262:   for (uint256 i = 0; i < erc20s.length; ++i) {

     286:   for (uint256 i = 0; i < size; ++i) {

     337 :   for (uint256 i = 0; i < len; ++i) {

    397 :   for (uint256 i = 0; i < len; ++i) {

    416 :    for (uint256 i = 0; i < length; ++i) {

     437 :   for (uint256 i = 0; i < length; ++i) {

    530 :   for (uint256 i = 0; i < basketLength; ++i) {

    548 :   for (uint256 i = 0; i < basketLength; ++i) {

    553 :   for (targetIndex = 0; targetIndex < targetsLength; ++targetIndex) {

    586 :   for (uint256 i = 0; i < targetsLength; ++i) {

    597 :   for (uint256 j = 0; j < backupLength && size < backup.max; ++j) {

    611 :    for (uint256 j = 0; j < backupLength && assigned < size; ++j) {

    653 :    for (uint256 i = 0; i < erc20s.length; i++) {

    707 :    for (uint256 i = 0; i < erc20s.length; ++i) {

    725:    for (uint256 i = 0; i < erc20s.length; ++i) {

##

### [GAS-2]  NOT USING THE NAMED RETURN VARIABLES WHEN A FUNCTION RETURNS, WASTES DEPLOYMENT GAS

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

       162 :  function _registerIgnoringCollisions(IAsset asset) private returns (bool swapped) {

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

       325 :   function lotPrice() external view returns (uint192 lotLow, uint192 lotHigh) {

##

### [GAS-3] ADD UNCHECKED {} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS REQUIRE() OR IF-STATEMENT . This saves 30-40 gas

SOLUTION:

  require(a <= b); x = b - a => require(a <= b); unchecked { x = b - a }

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

         141 :  if (req.sellAmount > bal) stRSR.seizeRSR(req.sellAmount - bal);

##

### [GAS-4]  CAN MAKE THE VARIABLE OUTSIDE THE LOOP TO SAVE GAS.ITS POSSIBLE TO SAVE 10 GAS

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

        for (uint256 i = 0; i < length; ++i) {
            IAsset asset = assetRegistry.toAsset(erc20s[i]);  //@audit variable outside the loop

            uint192 req = needed.mul(basketHandler.quantity(erc20s[i]), CEIL);  //@audit variable outside the loop
            if (asset.bal(address(this)).gt(req)) {
                // delta: {qTok}, the excess quantity of this asset that we hold
                uint256 delta = asset.bal(address(this)).minus(req).shiftl_toUint(     //@audit variable outside the loop
                    int8(IERC20Metadata(address(erc20s[i])).decimals())                     //@audit variable outside the loop
                );
                // no div-by-0: Distributor guarantees (totals.rTokenTotal + totals.rsrTotal) > 0
                // initial division is intentional here! We'd rather save the dust than be unfair
                toRSR[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rsrTotal;
                toRToken[i] = (delta / (totals.rTokenTotal + totals.rsrTotal)) * totals.rTokenTotal;
            }
        }


        for (uint256 i = 0; i < length; ++i) {
            IERC20Upgradeable erc20 = IERC20Upgradeable(address(erc20s[i]));  //@audit variable outside the loop
            if (toRToken[i] > 0) erc20.safeTransfer(address(rTokenTrader), toRToken[i]);
            if (toRSR[i] > 0) erc20.safeTransfer(address(rsrTrader), toRSR[i]);
        }


[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

        for (uint256 i = 0; i < size; ++i) {
            CollateralStatus s = assetRegistry.toColl(basket.erc20s[i]).status();  //@audit variable outside the loop
            if (s.worseThan(status_)) status_ = s;
        }


      for (uint256 i = 0; i < length; ++i) {
            ICollateral coll = assetRegistry.toColl(basket.erc20s[i]);   //@audit variable outside the loop
            if (coll.status() == CollateralStatus.DISABLED) return FIX_ZERO;

            uint192 refPerTok = coll.refPerTok();       //@audit variable outside the loop
            // If refPerTok is 0, then we have zero of coll's reference unit.
            // We know that basket.refAmts[basket.erc20s[i]] > 0, so we have no baskets.
            if (refPerTok == 0) return FIX_ZERO;

            // {tok}
            uint192 bal = coll.bal(account);        //@audit variable outside the loop

            // {tok/BU} = {ref/BU} / {ref/tok}.  0-division averted by condition above.
            uint192 q = basket.refAmts[basket.erc20s[i]].div(refPerTok, CEIL);        //@audit variable outside the loop

            // {BU} = {tok} / {tok/BU}.  q > 0 because q = (n).div(_, CEIL) and n > 0
            baskets = fixMin(baskets, bal.div(q));
        }
    }

       for (uint256 i = 0; i < basketLength; ++i) {
            IERC20 erc20 = config.erc20s[i];                  //@audit variable outside the loop
      

##

### [GAS-5]  USE REQUIRE INSTEAD OF ASSERT

The assert() and require() functions are a part of the error handling aspect in Solidity. Solidity makes use of state-reverting error handling exceptions. This means all changes made to the contract on that call or any sub-calls are undone if an error is thrown. It also flags an error.

They are quite similar as both check for conditions and if they are not met, would throw an error.

The big difference between the two is that the assert() function when false, uses up all the remaining gas and reverts all the changes made.

Meanwhile, a require() function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay. This is the most common Solidity function used by developers for debugging and error handling.

[FILE: AssetRegistry.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/AssetRegistry.sol)

         249 :  assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

         556 :  assert(targetIndex < targetsLength);

##

### [GAS-6]  USING STORAGE INSTEAD OF MEMORY FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a memory variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional MLOAD rather than a cheap stack read. Instead of declearing the variable with the memory keyword, declaring the variable with the storage keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a memory variable, is if the full struct/array is being returned by the function, is being passed to a function that requires memory, or if the array/struct is being read from another memory array/struct

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

        169 :  uint192[] memory refAmts = new uint192[](basket.erc20s.length);

        541 :   uint192[] memory goodWeights = new uint192[](targetsLength);

        545 :   uint192[] memory totalWeights = new uint192[](targetsLength);

##

### [ GAS-7]  <X> += <Y> COSTS MORE GAS THAN <X> = <X> + <Y> FOR STATE VARIABLES . FOR EVERY CALL CAN SAVE 13 GAS 

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

        636:  nonce += 1;

##

### [GAS-8]  State variable nonce  should be cached with stack variable to save gas 

[FILE : BasketHandler.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol)

       636 :    nonce += 1;

       646:    emit BasketSet(nonce, basket.erc20s, refAmts, disabled);


      
       




GAS-1	Use assembly to check for address(0)	9
GAS-2	array[index] += amount is cheaper than array[index] = array[index] + amount (or related variants)	8
GAS-5	State variables should be cached in stack variables rather than re-reading them from storage	20
GAS-6	Use calldata instead of memory for function arguments that do not get mutated	19
GAS-14	internal functions not called by the contract should be removed	20



GAS-1	Using bools for storage incurs overhead	17
GAS-2	Cache array length outside of loop	18
GAS-3	Use Custom Errors	202
GAS-4	Don't initialize variables with default value	66
GAS-5	Long revert strings	36
GAS-6	Functions guaranteed to revert when called by normal users can be marked payable	16
GAS-7	++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)	21
GAS-8	Using private rather than public for constants, saves gas	38
GAS-9	Use shift Right/Left instead of division/multiplication if possible	5
GAS-10	Use != 0 instead of > 0 for unsigned integer comparison	96