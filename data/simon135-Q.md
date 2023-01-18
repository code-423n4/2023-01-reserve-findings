## `setFeeParamters()` dosnt emit  the  new `feeNumerator` var
```solidity
       feeNumerator = newFeeNumerator;
    }
```
https://github.com/reserve-protocol/protocol/blob/8223cdab24bc50f54a994706633e1a3e30f1cc4a/contracts/plugins/mocks/EasyAuction.sol#L134
## no 2 step owner,make 2 step owner ship to make sure that owner can accept
```solidity
 function transferOwnership(address newOwner) public virtual onlyOwner {
        require(newOwner != address(0), "Ownable: new owner is the zero address");
        emit OwnershipTransferred(_owner, newOwner);
        _owner = newOwner;
    }
```
https://github.com/reserve-protocol/protocol/blob/6b1689d1680e1e4d99496c94da5c274d3012de6e/contracts/plugins/mocks/vendor/EasyAuction.sol#L935
