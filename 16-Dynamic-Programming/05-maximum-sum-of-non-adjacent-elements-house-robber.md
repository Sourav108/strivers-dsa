# Maximum Sum of Non-Adjacent Elements (House Robber I) (Step 16.1 — Introduction to DP)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Maximum Sum of Non-Adjacent Elements (House Robber I)](https://takeuforward.org/data-structure/maximum-sum-of-non-adjacent-elements-dp-5/)
- **Difficulty**: Medium
- **Statement**: Given an array `nums` of $N$ non-negative integers representing the amount of money in each house. A thief wants to rob houses along a street. The thief cannot rob two adjacent houses as security alarms will trigger. Find the maximum amount of money the thief can rob without alerting the police.

---

## 1. Problem, Restated

Find the maximum sum subsequence of `nums` such that no two chosen elements are adjacent in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ or $\mathcal{O}(K)$ auxiliary space.

---

## 2. Intuition & Pattern

At every house $i$, the thief has exactly two mutually exclusive choices: 
1) **Pick House $i$**: Rob house $i$, earning `nums[i]`. The thief CANNOT rob house $i - 1$, so they add the optimal loot from house $i - 2$: 
   $$\text{pick} = \text{nums}[i] + \text{dp}[i - 2]$$ 
2) **Do Not Pick House $i$**: Skip house $i$, carrying over the optimal loot from house $i - 1$: 
   $$\text{notPick} = \text{dp}[i - 1]$$ 
**Master Recurrence**: 
$$\text{dp}[i] = \max(\text{nums}[i] + \text{dp}[i - 2], \text{dp}[i - 1])$$ 
Base cases: $\text{dp}[0] = \text{nums}[0]$, $\text{dp}[1] = \max(\text{nums}[0], \text{nums}[1])$. 
Since $\text{dp}[i]$ depends only on $\text{dp}[i-1]$ and $\text{dp}[i-2]$, maintain two sliding variables `prev` and `prev2` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Pick / Not-Pick Recurrence Paradigm (House Robber I)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Generate all $2^N$ subsequences, filter out adjacent pairs, and find maximum in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, const vector<int>& nums) {
        if (i == 0) return nums[0];
        if (i < 0) return 0;
        int pick = nums[i] + solve(i - 2, nums);
        int notPick = solve(i - 1, nums);
        return max(pick, notPick);
    }
public:
    int rob(vector<int>& nums) {
        return solve(nums.size() - 1, nums);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Exponential overlapping pick/not-pick tree.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Bottom-Up Tabulation in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionTabulation {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 1) return nums[0];
        vector<int> dp(n, 0);
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);
        for (int i = 2; i < n; i++) {
            int pick = nums[i] + dp[i - 2];
            int notPick = dp[i - 1];
            dp[i] = max(pick, notPick);
        }
        return dp[n - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector.
- **Why it's still not optimal**: Vector memory is redundant when 2 previous scalar values suffice.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized Pick / Not-Pick DP in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        if (n == 1) return nums[0];
        
        int prev2 = 0;       // dp[i - 2]
        int prev = nums[0];  // dp[i - 1]
        
        for (int i = 1; i < n; i++) {
            int pick = nums[i] + prev2;
            int notPick = prev;
            
            int cur = max(pick, notPick);
            prev2 = prev;
            prev = cur;
        }
        
        return prev;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single pass through `nums`).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (two integer variables).
- **Why this is optimal**: Constant memory usage with zero heap allocations.

---

## 6. Dry Run

`nums = [2, 7, 9, 3, 1]` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | prev2 = 0, prev = 2 | i = 1 |
| `i = 1 (val 7)` | pick = 7 + 0 = 7; notPick = 2 $\implies cur = \max(7, 2) = 7$. prev2=2, prev=7 | dp[1] = 7 |
| `i = 2 (val 9)` | pick = 9 + 2 = 11; notPick = 7 $\implies cur = \max(11, 7) = 11$. prev2=7, prev=11 | dp[2] = 11 |
| `i = 3 (val 3)` | pick = 3 + 7 = 10; notPick = 11 $\implies cur = \max(10, 11) = 11$. prev2=11, prev=11 | dp[3] = 11 |
| `i = 4 (val 1)` | pick = 1 + 11 = 12; notPick = 11 $\implies cur = \max(12, 11) = 12$. prev2=11, prev=12 | dp[4] = 12 |
| `Result` | Return prev = 12 (Rob houses 0, 2, 4: $2 + 9 + 1 = 12$) | Max Loot = 12 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `nums[0]`).
- All elements 0 (returns 0).
- Strictly descending array (e.g. `[10, 1, 1]` $\implies$ returns 11).

### Common Bugs to Avoid
- Forgetting base case when $N = 1$ (can cause out of bounds lookahead).
- Adding `nums[i]` to `notPick` branch.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does this problem extend to Circular Houses (House Robber II / LeetCode 213)?**  
  **A**: If houses form a circle, the first and last houses are adjacent (cannot rob both). We split the problem into TWO linear House Robber I runs: 
1) $\text{Case 1}$: Rob range $[0, n-2]$ (excluding last house). 
2) $\text{Case 2}$: Rob range $[1, n-1]$ (excluding first house). 
$\text{Answer} = \max(\text{rob}(0, n-2), \text{rob}(1, n-1))$!

- **Q2: How to reconstruct the exact houses that were robbed?**  
  **A**: Backtrack from index $n-1$: if $\text{dp}[i] == \text{nums}[i] + \text{dp}[i-2]$, house $i$ was robbed (add to list, move to $i-2$); else house $i$ was skipped (move to $i-1$).

- **Q3: How does House Robber relate to Weighted Independent Set on Interval Graphs?**  
  **A**: On a linear path graph, House Robber is precisely the **Maximum Weight Independent Set (MWIS)** problem, which is NP-hard on general graphs but solvable in $\mathcal{O}(N)$ time on trees and interval graphs via DP.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `1D DP`, `Pick Not-Pick`, `LeetCode-198`, `Medium`
- **Related problems to practice next**:
- **House Robber II**: Circular street extension.
- **Ninja's Training**: 2D activity selection DP.
