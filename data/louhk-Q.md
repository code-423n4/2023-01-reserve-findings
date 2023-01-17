It seems that `manageTokens` function does not check the `erc20s` array is empty.
It is recommended to add `require` function before calling `_manageTokens`
