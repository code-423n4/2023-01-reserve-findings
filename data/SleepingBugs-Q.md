
# Single-step process for critical ownership transfer/renounce is risky
## Impact
Given that are derived from `OwnableUpgradeable`, the ownership management of this contract defaults to `Ownable` ’s `transferOwnership()` and `renounceOwnership()` methods which are not overridden here. 

Such critical address transfer/renouncing in one-step is very risky because it is irrecoverable from any mistakes

Scenario: If an incorrect address, e.g. for which the private key is not known, is used accidentally then it prevents the use of all the `onlyOwner()` functions forever, which includes the changing of various critical addresses and parameters. This use of incorrect address may not even be immediately apparent given that these functions are probably not used immediately. 

When noticed, due to a failing `onlyOwner()` function call, it will force the redeployment of these contracts and require appropriate changes and notifications for switching from the old to new address. This will diminish trust in the protocol and incur a significant reputational damage.

## Code Snippet
- [Main.sol#L6](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Main.sol#L6)
import "@openzeppelin/contracts-upgradeable/access/OwnableUpgradeable.sol";


## Recommended steps
Recommend overriding the inherited methods to null functions and use separate functions for a two-step address change:
1. Approve a new address as a `pendingOwner`
2. A transaction from the `pendingOwner` address claims the pending ownership change.

This mitigates risk because if an incorrect address is used in step (1) then it can be fixed by re-approving the correct address. Only after a correct address is used in step (1) can step (2) happen and complete the address/ownership change.

Also, consider adding a time-delay for such sensitive actions. And at a minimum, use a multisig owner address and not an EOA.


# `block.timestamp` used as time proxy 
## Summary

Risk of using `block.timestamp` for time should be considered. 

## Details
`block.timestamp` is not an ideal proxy for time because of issues with synchronization, miner manipulation and changing block times. 

This kind of issue may affect the code allowing or reverting the code before the expected deadline, modifying the normal functioning or reverting sometimes.

## References

SWC ID: 116

## Code Snippet 
- [BackingManager.sol#L114](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BackingManager.sol#L114)
        if (block.timestamp < basketTimestamp + tradingDelay) return;

- [BasketHandler.sol#L637](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L637)
            timestamp = uint48(block.timestamp);

- [Furnace.sol#L42](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Furnace.sol#L42)
        lastPayout = uint48(block.timestamp);

- [Furnace.sol#L71](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Furnace.sol#L71)
        if (uint48(block.timestamp) < uint64(lastPayout) + period) return;

- [Furnace.sol#L74](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Furnace.sol#L74)
        uint48 numPeriods = uint48((block.timestamp) - lastPayout) / period;

- [StRSR.sol#L180](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L180)
        payoutLastPaid = uint48(block.timestamp);

- [StRSR.sol#L313](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L313)
        require(queue[endId - 1].availableAt <= block.timestamp, "withdrawal unavailable");

- [StRSR.sol#L436](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L436)
        uint256 time = block.timestamp;

- [StRSR.sol#L497](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L497)
        if (block.timestamp < payoutLastPaid + rewardPeriod) return;

- [StRSR.sol#L498](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L498)
        uint48 numPeriods = (uint48(block.timestamp) - payoutLastPaid) / rewardPeriod;

- [StRSR.sol#L561](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L561)
        availableAt = uint64(block.timestamp) + unstakingDelay;

- [StRSR.sol#L775](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L775)
        require(block.timestamp <= deadline, "ERC20Permit: expired deadline");

- [StRSRVotes.sol#L126](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSRVotes.sol#L126)
        require(block.timestamp <= expiry, "ERC20Votes: signature expired");

- [Auth.sol#L102](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L102)
        return block.timestamp < unfreezeAt;

- [Auth.sol#L108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L108)
        return paused || block.timestamp < unfreezeAt;

- [Auth.sol#L123](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L123)
        freezeUntil(uint48(block.timestamp) + shortFreeze);

- [Auth.sol#L140](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L140)
        freezeUntil(uint48(block.timestamp) + longFreeze);

- [Auth.sol#L159](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/mixins/Auth.sol#L159)
        unfreezeAt = uint48(block.timestamp);





## Recommendation
- Consider the risk of using `block.timestamp` as time proxy and evaluate if block numbers can be used as an approximation for the application logic. Both have risks that need to be factored in. 
- Consider using an oracle for precision


# Front run initializer
## Summary

The initialize function that initializes important contract state can be called by anyone.

## Details
The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.

In the best case for the victim, they notice it and have to redeploy their contract costing gas.

## Code Snippet

- [AssetRegistry.sol#L34](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/AssetRegistry.sol#L34)
    function init(IMain main_, IAsset[] calldata assets_) external initializer {

- [BackingManager.sol#L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BackingManager.sol#L44)
    function init(

- [BasketHandler.sol#L151](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L151)
    function init(IMain main_) external initializer {

- [Broker.sol#L51](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Broker.sol#L51)
    function init(

- [Distributor.sol#L41](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Distributor.sol#L41)
    function init(IMain main_, RevenueShare calldata dist) external initializer {

- [Furnace.sol#L33](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Furnace.sol#L33)
    function init(

- [Main.sol#L26](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/Main.sol#L26)
    function init(

- [RToken.sol#L147](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RToken.sol#L147)
    function init(

- [RevenueTrader.sol#L23](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/RevenueTrader.sol#L23)
    function init(

- [StRSR.sol#L160](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L160)
    function init(

## Recommendation
Use the constructor to initialize non-proxied contracts.

For initializing proxy contracts deploy contracts using a factory contract that immediately calls initialize after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.


# Use of `asserts()`
## Impact

From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
You have reachable asserts in the following locations (which should be replaced by `require` / are mistakenly left from development phase):

## Details
The Solidity `assert()` function is meant to assert invariants. Properly functioning code should never reach a failing assert statement. A reachable assertion can mean one of two things:

A bug exists in the contract that allows it to enter an invalid state;
The assert statement is used incorrectly, e.g. to validate inputs.

## References
SWC ID: 110

## Code Snippet


- [BackingManager.sol#L249](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BackingManager.sol#L249)
        assert(tradesOpen == 0 && !basketHandler.fullyCollateralized());

- [BasketHandler.sol#L556](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/BasketHandler.sol#L556)
            assert(targetIndex < targetsLength);

- [StRSR.sol#L696](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/StRSR.sol#L696)
        assert(totalStakes + amount < type(uint224).max);

- [RecollateralizationLib.sol#L110](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/RecollateralizationLib.sol#L110)
        assert(doTrade);

- [RewardableLib.sol#L78](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/RewardableLib.sol#L78)
                assert(erc20s[i].balanceOf(address(this)) >= liabilities[erc20s[i]]);

- [RewardableLib.sol#L102](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/RewardableLib.sol#L102)
            assert(erc20.balanceOf(address(this)) >= liabilities[erc20]);

- [TradeLib.sol#L44](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/TradeLib.sol#L44)
        assert(trade.buyPrice > 0 && trade.buyPrice < FIX_MAX && trade.sellPrice < FIX_MAX);

- [TradeLib.sol#L108](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/TradeLib.sol#L108)
        assert(

- [TradeLib.sol#L168](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/TradeLib.sol#L168)
            assert(errorCode == 0x11 || errorCode == 0x12);

- [TradeLib.sol#L170](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/TradeLib.sol#L170)
            assert(keccak256(reason) == UIntOutofBoundsHash);

- [Trading.sol#L114](https://github.com/reserve-protocol/protocol/blob/df7ecadc2bae74244ace5e8b39e94bc992903158//contracts/p1/mixins/Trading.sol#L114)
        assert(address(trades[sell]) == address(0));

## Recommended Mitigation Steps
Substitute `asserts` with `require`/`revert`.




