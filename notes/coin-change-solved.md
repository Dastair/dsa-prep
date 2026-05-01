# Coin Change — Medium
Problem Link: https://leetcode.com/problems/coin-change/
Solved Date: 2026-05-01
Pattern Tag: dp

## SRS Tracking
- Stage: 1
- Review Date: 2026-05-02
- Last Rating: —
- Review Count: 0
- Graduated: No

---

# Real World Analogy
Like making change at a register — you want to hand over the fewest possible coins to hit an exact total.

## Core Insight
The minimum coins for amount `n` depends on the minimum coins for `n - coin` for every coin — so subproblems overlap and can be cached.

## Approach
Top-down memoization: recursively compute the minimum coins needed for each sub-amount, caching results to avoid recomputation. Try every coin at every amount and take the minimum.

## Mental Model
```
┌─────────────────────────────────┬──────────────────────────────────────────────┐
│ Decision                        │ Why                                          │
├─────────────────────────────────┼──────────────────────────────────────────────┤
│ base case: amt == 0 → return 0  │ zero coins needed to make zero               │
├─────────────────────────────────┼──────────────────────────────────────────────┤
│ init best = float('inf')        │ amt is not a valid bound when coins skip 1    │
├─────────────────────────────────┼──────────────────────────────────────────────┤
│ convert inf → -1 at top level   │ impossible case; inf must not propagate out   │
└─────────────────────────────────┴──────────────────────────────────────────────┘
```

## Pseudocode
```
function dp(amt):
    if amt == 0: return 0
    if amt in memo: return memo[amt]

    best = infinity
    for each coin:
        if amt - coin >= 0:
            best = min(best, dp(amt - coin) + 1)

    memo[amt] = best
    return best

result = dp(amount)
return result if result != infinity else -1
```

## Complexity
- Time: O(amount × len(coins)) — each sub-amount is solved once; at each we try every coin
- Space: O(amount) — memo stores one entry per unique sub-amount

## Watch Out For
- Base case must return `0`, not `1` — the `+1` for the coin used is added by the caller
- Initialize `best = float('inf')`, not `amt` — `amt` silently gives wrong answers on impossible inputs
- Convert `inf` to `-1` only at the top level, not inside recursion

## Dry Run
Input: `coins = [1, 5, 11], amount = 15`
```
dp(0)  = 0
dp(5)  = min(dp(4)+1, dp(0)+1) = min(5, 1) = 1
dp(10) = min(dp(9)+1, dp(5)+1) = min(6, 2) = 2
dp(11) = min(dp(10)+1, dp(6)+1, dp(0)+1) = min(3, 6, 1) = 1
dp(15) = min(dp(14)+1, dp(10)+1, dp(4)+1)
       = min(5,        3,        5)        = 3
Result: 3 ✓  (5 + 5 + 5)
```

## Boilerplate Template
```python
def coinChange(coins: list[int], amount: int) -> int:
    memo = {}

    def dp(amt: int) -> int:
        if amt == 0:
            return 0
        if amt in memo:
            return memo[amt]

        best = float('inf')
        for coin in coins:
            if amt - coin >= 0:
                sub = dp(amt - coin)
                best = min(best, sub + 1)

        memo[amt] = best
        return best

    result = dp(amount)
    return result if result != float('inf') else -1
```
