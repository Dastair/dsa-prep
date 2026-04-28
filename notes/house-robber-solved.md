# House Robber — Medium
Problem Link: https://leetcode.com/problems/house-robber/
Solved Date: 2026-04-28
Pattern Tag: dp

## SRS Tracking
- Stage: 2
- Review Date: 2026-05-01
- Last Rating: —
- Review Count: 0
- Graduated: No

---

# Real World Analogy
Like picking non-adjacent fruit from a row of trees — you can't harvest two trees right next to each other, so you plan which ones are worth skipping.

## Core Insight
You never need to look back more than 3 positions: the best previous "must-rob" is always within 2 or 3 steps because dp values can't improve by skipping further back.

## Approach
Define `dp[i]` as the max money if you must rob house `i`. For each house, take the best of the two valid prior robs (`i-2` or `i-3`) and add the current house value. The answer is the max of the last two entries.

## Mental Model
```
┌─────────────────────────────────┬──────────────────────────────────────────────┐
│ Decision                        │ Why                                          │
├─────────────────────────────────┼──────────────────────────────────────────────┤
│ dp[i] = "must rob i"            │ Splits the space: either we rob this house   │
│                                 │ or we don't — final answer covers both       │
├─────────────────────────────────┼──────────────────────────────────────────────┤
│ Look back i-2 and i-3           │ Can't rob i-1 (adjacent); looking further    │
│                                 │ back than i-3 is never better (dp[i-2] ≥     │
│                                 │ dp[i-4] since values are non-negative)       │
├─────────────────────────────────┼──────────────────────────────────────────────┤
│ return max(dp[n-1], dp[n-2])    │ Optimal run must end at last or second-last  │
│                                 │ house — any earlier ending could extend to n │
└─────────────────────────────────┴──────────────────────────────────────────────┘
```

## Pseudocode
```
if only 1 house → return its value

dp[0] = money[0]
dp[1] = money[1]

for i from 2 to n-1:
    prev3 = dp[i-3] if i >= 3 else 0
    dp[i] = max(dp[i-2], prev3) + money[i]

return max(dp[n-1], dp[n-2])
```

## Complexity
- Time: O(n) — single pass through the array, each house computed once
- Space: O(n) — dp array of size n; reducible to O(1) by keeping only last 3 values

## Watch Out For
- `dp[i-3]` when `i < 3` — Python negative indexing silently reads wrong index; guard with `if i >= 3 else 0`
- Edge case `n == 1` — loop never runs, `dp[n-2]` would be `dp[-1]`; handle before entering DP
- Final answer is `max(dp[-1], dp[-2])`, not just `dp[-1]`

## Dry Run
Input: `[2, 7, 9, 3, 1]`
```
dp[0] = 2
dp[1] = 7
dp[2] = max(dp[0], 0)    + 9 = max(2, 0)   + 9 = 11
dp[3] = max(dp[1], dp[0]) + 3 = max(7, 2)   + 3 = 10
dp[4] = max(dp[2], dp[1]) + 1 = max(11, 7)  + 1 = 12

return max(dp[4], dp[3]) = max(12, 10) = 12 ✓
```

## Boilerplate Template
```python
def rob(nums: list[int]) -> int:
    n = len(nums)
    if n == 1:
        return nums[0]

    dp = [0] * n
    dp[0] = nums[0]
    dp[1] = nums[1]

    for i in range(2, n):
        prev3 = dp[i - 3] if i >= 3 else 0
        dp[i] = max(dp[i - 2], prev3) + nums[i]

    return max(dp[n - 1], dp[n - 2])
```
