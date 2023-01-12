Why in the StaticATokenLM contract and line 403, we don’t need to update LastRewardBlock?

Why in the StaticATokenLM contract and line 468, we don’t check the receiver to don’t be a zero address?

In the Asset contract and line 69, should we check the updated at for price? 

In StRSR contract and line 417, we should not use uint48(block.timestamp)?

In StRSR contract and line 429, payoutRatio should not be uint256, similar to rsrRewardsAtLastPayout ?

In StRSR contract and line 147, if the balance account is zero, you will add the user address to the accounts array, now what if the user already exists and just unstaked before the new stake? There is no problem ?