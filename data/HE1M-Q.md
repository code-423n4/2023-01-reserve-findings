### No. 1
If userA delegates 10 voting power to userB, and userB delegates 20 voting power to userC. Finally userC will have voting power of 20 not 30.
In other words only one level the voting power can be distributed. 
https://github.com/reserve-protocol/protocol/blob/fa81006e97c84f41b43b0eefa395cc253b6e731b/contracts/p1/StRSRVotes.sol#L166

It is better to make it possible to transfer the delegated voting power.