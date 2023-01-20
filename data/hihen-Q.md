
## 01. Constants should be defined rather than using magic numbers

[Permit.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L20): `0x1626ba7e`
  ```
  require(
      IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==
          0x1626ba7e,
      "ERC1271: Unauthorized"
  );
  ```

[Distributor.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L165-L166): `10000`
  ```
  require(share.rsrDist <= 10000, "RSR distribution too high");
  require(share.rTokenDist <= 10000, "RToken distribution too high");
  ```

## 02. GnosisTrade.settle() misses a check for endTime
[`GnosisTrade.settle()`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/plugins/trading/GnosisTrade.sol#L169-L174) need check `now >= endTime` as described in the comment:
  ```
    // checks:
    //   state is OPEN
    //   caller is `origin`
    //   now >= endTime
  ```
Should add the following to `GnosisTrade.settle()`:
```
require(endTime <= block.timestamp, "can not settle now");
```

## 03. ReentrancyGuardUpgradeable is not initialized
[TradingP1](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L16) inherits ReentrancyGuardUpgradeable, but forgets to call `__ReentrancyGuard_init()` in [`__Trading_init()`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/mixins/Trading.sol#L44-L52):
```
abstract contract TradingP1 is Multicall, ComponentP1, ReentrancyGuardUpgradeable, ITrading {
    ...
    function __Trading_init(
        IMain main_,
        uint192 maxTradeSlippage_,
        uint192 minTradeVolume_
    ) internal onlyInitializing {
        broker = main_.broker();
        setMaxTradeSlippage(maxTradeSlippage_);
        setMinTradeVolume(minTradeVolume_);
    }
    ...
}
```
## 04. StRSR and Furnace should be prevented from being added to the destinations
The StRSR and Furnance are treated specially in [DistributorP1.sol](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L31-L32) and are replaced by two special addresses:
```
contract DistributorP1 is ComponentP1, IDistributor {
    ...
    address public constant FURNACE = address(1);
    address public constant ST_RSR = address(2);
    ...
    function _setDistribution(address dest, RevenueShare memory share) internal {
        require(dest != address(0), "dest cannot be zero");
        if (dest == FURNACE) require(share.rsrDist == 0, "Furnace must get 0% of RSR");
        if (dest == ST_RSR) require(share.rTokenDist == 0, "StRSR must get 0% of RToken");
        ...
    }
    ...
}
```

That is, we need to pass `dest = 0x1` to set Furnace destination and `dest = 0x2` to set StRSR destination, both of which have special share limitations.
However, these share limitations can be overcome by passing in the real Furnace or StRSR address.

It is recommended to revert if dest address is the real Furnace or StRSR address in [_setDistribution()](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Distributor.sol#L161):
  ```
  require(dest != furnace, "dest cannot be furnace");
  require(dest != stRSR, "dest cannot be stRSR");
  ```

## 05. Redundant code in PermitLib.requireSignature
The if branch in [`PermitLib.requireSignature()`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/libraries/Permit.sol#L17-L23) is redundant because `SignatureCheckerUpgradeable.isValidSignatureNow()` also verifies ERC1271 signature.
```
library PermitLib {
    function requireSignature(
        address owner,
        bytes32 hash,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external view {
        if (AddressUpgradeable.isContract(owner)) {
            require(
                IERC1271Upgradeable(owner).isValidSignature(hash, abi.encodePacked(r, s, v)) ==
                    0x1626ba7e,
                "ERC1271: Unauthorized"
            );
        } else {
            require(
                SignatureCheckerUpgradeable.isValidSignatureNow(
                    owner,
                    hash,
                    abi.encodePacked(r, s, v)
                ),
                "ERC20Permit: invalid signature"
            );
        }
    }
}
```
## 06. Missing targetName check for collaterals in BasketHandler.setBackupConfig()
The function [`BasketHandler.setBackupConfig()`](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/BasketHandler.sol#L252) only checks that each of the `erc20s` is a collateral, but doesn't check that whether its targetName is correct.
This results in potentially adding collaterals with mismatched targetName to `config.backups[targetName]`. 

It is recommended to check the targetName when adding backup collaterals:
  ```
  function setBackupConfig(
      bytes32 targetName,
      uint256 max,
      IERC20[] calldata erc20s
  ) external governance {
      ...
      for (uint256 i = 0; i < erc20s.length; ++i) {
          require(assetRegistry.toAsset(erc20s[i]).isCollateral(), "token is not collateral");
          // FIX BEGIN
          require(targetName == ICollateral(address(assetRegistry.toAsset(erc20s[i]))).targetName(), "targetName not match");
          // FIX END
          conf.erc20s.push(erc20s[i]);
      }
      emit BackupConfigSet(targetName, max, erc20s);
  }
  ```
