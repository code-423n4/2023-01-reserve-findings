
AssetRegistry.sol : 

Line 76 - quantity variable is used to store the value returned from quantity onyl to be used once in the function 

function swapRegistered(IAsset asset) external governance returns (bool swapped) {
        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");


        uint192 quantity = basketHandler.quantity(asset.erc20());


        if (quantity > 0) basketHandler.disableBasket();


        swapped = _registerIgnoringCollisions(asset);
    }


Instead of this you can just use the called value in the if condition so that the gas taken up by the uint is saved .You can do something like this 

function swapRegistered(IAsset asset) external governance returns (bool swapped) {
        require(_erc20s.contains(address(asset.erc20())), "no ERC20 collision");


        


        if (basketHandler.quantity(asset.erc20()) > 0) basketHandler.disableBasket();


        swapped = _registerIgnoringCollisions(asset);
    }

This direct call will save a slot in the memory. This can save some more of the gas cost . 

pragma solidity 0.8.0;
contract Reserve{
   
    function test()public pure returns(bool){
      uint temp = getInt();
      return temp < 10;
    }
    // gas cost : 21483
    function test2() public pure returns(bool){
        return getInt() < 10;
    }
    // gas cost : 21448


    function getInt() public pure returns(uint){
        return 10;
    }


   
}
