## 1. USE NAMED IMPORTS INSTEAD OF PLAIN `IMPORT ‘FILE.SOL’

Can import the required specific contracts, functions or variables by using the named imports explicitly. Plain imports will import the entire context of the imported contract which could lead into variable name conflicts etc ...

Currently the **IDeployerRegistry** is imported as follows:

    import "../interfaces/IDeployerRegistry.sol";

But it can be imported explicitly by the name as follows:

	import {IDeployerRegistry} from "../interfaces/IDeployerRegistry.sol";

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/DeployerRegistry.sol#L5

There are 15 more instances of this issue:

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/FacadeAct.sol#L4-L16
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/FacadeRead.sol#L4-L16
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/FacadeTest.sol#L4-L10
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/FacadeWrite.sol#L4-L5
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/AssetRegistry.sol#L4-L7
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/BackingManager.sol#L4-L13
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Deployer.sol#L4-L21
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Distributor.sol#L4-L8
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Furnace.sol#L4-L6
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Main.sol#L4-L10
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L5-L17
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RevenueTrader.sol#L4-L9
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/StRSR.sol#L4-L18
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/Broker.sol#L4-L12
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L5-L12
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L4-L15

## 2. EXPRESSIONS SHOULD USE IMMUTABLE INSTEAD OF CONSTANTS

There is a difference in the way constants and immutable are used. Each should be used in their appropriate contexts. Constants should be used where literal values are written into the code and immutable to used for expressions or values calculated in the constructor.

    uint192 constant FIX_TWO = FIX_ONE * 2;

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/facade/FacadeTest.sol#L12

The above should be declared either as a `immutable` variable and initialized inside the constructor or the final calculated value should be assigned to the constant as literal.

There are 4 more instances of this issue:

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L47
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L15
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L65
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/StRSR.sol#L87

## 3.`assert()` FUNCTION IS USED WITHOUT DESCRIPTION MESSAGE FOR THE REVERT.

    assert(address(reg.erc20s[i]) != address(0)); 

Add a description message to the assert function for the revert. It is very important to add a revert message, then `msg.sender` has enough information to learn the reason of failure.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/AssetRegistry.sol#L105-L108

There are 4 more instances of this issue:

https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderFactory.sol#L106
https://github.com/code-423n4/2023-01-ondo/blob/main/contracts/cash/factory/CashKYCSenderReceiverFactory.sol#L106
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Broker.sol#L56
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L394

## 4. EXPLICITLY DECLARE THE VISIBILITY TYPES OF `constant` FOR BETTER CODE READABILITY

    uint256 constant GNOSIS_MAX_TOKENS = 7e28;

Above code line can be written as follows:

    uint256 public constant GNOSIS_MAX_TOKENS = 7e28;

This will improve the readability and understanding of the code.
   
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Broker.sol#L15

## 5. PASSED IN FUNCTION ARGUMENT, WHICH IS SUBSEQUENTLY CHECKED FOR `!= address(0)` NOT USED AND HENCE CAN BE REMOVED.

        require(
            address(tradeImplementation_) != address(0),
            "invalid Trade Implementation address"
        );
		
`tradeImplementation_` variable is passed into the `function init()` and checked for the zero address as shown above.
But the variable is not used inside the function implementation. Hence can be removed.
The function has `initializer` modifier and can be called only once.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Broker.sol#L35-L49