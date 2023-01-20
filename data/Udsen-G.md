## 1. COMPLEX `require` STATEMENTS CAN BE BROKEN INTO SIMPLER `require` STATEMENTS TO SAVE GAS.

        require(
            newAuctionLength > 0 && newAuctionLength <= MAX_AUCTION_LENGTH,
            "invalid auctionLength"
        );
		
Above `require` statement can be broken into two separate `require` statements as below:

        require(newAuctionLength > 0, "invalid auctionLength");
        require(newAuctionLength <= MAX_AUCTION_LENGTH, "invalid auctionLength");
		
This will make sure, if the first condition is false then the transaction will revert. Hence no need to spend gas on checking both condtions as per the earlier `require` statment.

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Broker.sol#L111-L114

There are 6 more instances of this issue:

https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Deployer.sol#L39-L44
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/Deployer.sol#L64
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L109
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/RToken.sol#L521-L527
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p0/StRSR.sol#L524
https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158/contracts/p1/RToken.sol#L590