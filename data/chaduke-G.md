G1. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CCashDelegate.sol#L41-L43
This statement can be deleted since the condition is always false.


G2. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L123
Enclosing it inside unchecked can save gas - overflow is impossible here.
```
 uint dstTokensNew = accountTokens[dst] + tokens;
```

G3. https://github.com/code-423n4/2023-01-ondo/blob/f3426e5b6b4561e09460b2e6471eb694efdd6c70/contracts/lending/tokens/cCash/CTokenCash.sol#L182-L190
No need to introduce the ``src`` variable:
```

  function approve(
    address spender,
    uint256 amount
  ) external override returns (bool) {
    transferAllowances[msg.sender][spender] = amount;
    emit Approval(msg.sender, spender, amount);
    return true;
  }


```

