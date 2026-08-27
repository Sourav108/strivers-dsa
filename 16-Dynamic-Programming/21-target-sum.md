# Target Sum (Assign +/- signs to achieve target) (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Target Sum (Assign +/- signs to achieve target)](https://takeuforward.org/data-structure/target-sum-dp-21/)
- **Difficulty**: Medium
- **Statement**: You are given an integer array `nums` and an integer `target`. You want to build an expression out of `nums` by adding one of the symbols '+' and '-' before each integer in `nums` and then concatenate all the integers. Return the number of different expressions that you can build, which evaluates to `target`.

---

## 1. Problem, Restated

Find the number of ways to assign signs to elements such that positive subset $S_1$ and negative subset $S_2$ satisfy $S_1 - S_2 = \text{target}$ in $\mathcal{O}(N \times S_1)$ time and $\mathcal{O}(S_1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Algebraic Isomorphism**: 
Assigning $+$ or $-$ to each element groups `nums` into two subsets: 
- $S_1$: set of elements assigned a $+$ sign 
- $S_2$: set of elements assigned a $-$ sign 
We require: 
1) $S_1 - S_2 = \text{target}$ 
2) $S_1 + S_2 = \text{totalSum}$ 
Adding equations: 
$$2S_1 = \text{totalSum} + \text{target} \implies S_1 = \frac{\text{totalSum} + \text{target}}{2}$$ 
**Boundary Checks**: 
- If $\text{totalSum} < |\text{target}|$ or $(\text{totalSum} + \text{target}) \% 2 \ne 0$, return 0 immediately! 
- If $\text{totalSum} + \text{target} < 0$, return 0. 
**Reduced Problem**: Count subsets with sum $S_1$ in $\mathcal{O}(N \times S_1)$ time and $\mathcal{O}(S_1)$ space.

- **Underlying Pattern**: `Subset Partition Algebraic Reduction: $S_1 = (\text{totalSum} + \text{target}) / 2$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^N$ sign assignments in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int curSum, const vector<int>& nums, int target) {
        if (i == nums.size()) return curSum == target ? 1 : 0;
        int plus = solve(i + 1, curSum + nums[i], nums, target);
        int minus = solve(i + 1, curSum - nums[i], nums, target);
        return plus + minus;
    }
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        return solve(0, 0, nums, target);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary decision tree evaluates $2^N$ leaves without memoization.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x S1) time and O(N x S1) space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
#include <cmath>
using namespace std;

class Solution2D {
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int totalSum = 0;
        for (int x : nums) totalSum += x;
        if (totalSum < abs(target) || (totalSum + target) % 2 != 0) return 0;
        int s1 = (totalSum + target) / 2;
        int n = nums.size();
        
        vector<vector<int>> dp(n, vector<int>(s1 + 1, 0));
        if (nums[0] == 0) dp[0][0] = 2;
        else dp[0][0] = 1;
        if (nums[0] != 0 && nums[0] <= s1) dp[0][nums[0]] = 1;
        
        for (int i = 1; i < n; i++) {
            for (int k = 0; k <= s1; k++) {
                int notTake = dp[i - 1][k];
                int take = (k >= nums[i]) ? dp[i - 1][k - nums[i]] : 0;
                dp[i][k] = notTake + take;
            }
        }
        return dp[n - 1][s1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times S_1)$ time.
- **Space Complexity**: $\mathcal{O}(N \times S_1)$ space.
- **Why it's still not optimal**: 2D matrix is redundant.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Space-Optimized Target-Reduced DP in $\mathcal{O}(N \times S_1)$ time and $\mathcal{O}(S_1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
#include <cmath>
using namespace std;

class Solution {
private:
    int countSubsets(const vector<int>& nums, int target) {
        int n = nums.size();
        vector<int> prev(target + 1, 0);
        
        // Base case for nums[0] (including zero)
        if (nums[0] == 0) {
            prev[0] = 2; // +0 or -0
        } else {
            prev[0] = 1;
            if (nums[0] <= target) {
                prev[nums[0]] = 1;
            }
        }
        
        for (int i = 1; i < n; i++) {
            vector<int> cur(target + 1, 0);
            for (int k = 0; k <= target; k++) {
                int notTake = prev[k];
                int take = (k >= nums[i]) ? prev[k - nums[i]] : 0;
                cur[k] = notTake + take;
            }
            prev = cur;
        }
        
        return prev[target];
    }
    
public:
    int findTargetSumWays(vector<int>& nums, int target) {
        int totalSum = 0;
        for (int x : nums) {
            totalSum += x;
        }
        
        // If target is unreachable in magnitude or parity is odd, 0 ways exist
        if (totalSum < abs(target) || (totalSum + target) % 2 != 0) {
            return 0;
        }
        
        int s1 = (totalSum + target) / 2;
        return countSubsets(nums, s1);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \frac{\text{totalSum} + \text{target}}{2})$ time.
- **Space Complexity**: $\mathcal{O}(\frac{\text{totalSum} + \text{target}}{2})$ space.
- **Why this is optimal**: Calculates exact expressions using single-row buffer with 100% zero-handling accuracy.

---

## 6. Dry Run

`nums = [1, 1, 1, 1, 1]`, $\text{target} = 3, \text{totalSum} = 5 \implies S_1 = (5 + 3)/2 = 4$

| Step | Action / State Change | Result |
|---|---|---|
| `Target` | $S_1 = (5 + 3) / 2 = 4$ | Need subsets summing to 4 |
| `Combinatorics` | Choosing 4 ones out of 5: $\binom{5}{4} = 5$ | Combinations = 5 |
| `Expressions` | `+1+1+1+1-1`, `+1+1+1-1+1`, `+1+1-1+1+1`, `+1-1+1+1+1`, `-1+1+1+1+1` | 5 distinct expressions |
| `Result` | Return 5 | Target Sum Ways = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $\text{target} > \text{totalSum}$ (returns 0).
- $\text{target} < -\text{totalSum}$ (returns 0).
- Array containing multiple zeroes.

### Common Bugs to Avoid
- Forgetting `abs(target)` check when target is negative (e.g. `totalSum = 5, target = -7`).
- Not starting inner loop from `k = 0` when zeroes exist.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do zeroes in nums create exponential 2^Z multipliers?**  
  **A**: Because assigning $+0$ or $-0$ yields the exact same total sum! Each zero in `nums` doubles the number of valid sign expressions ($2^Z$ factor). Our DP naturally accumulates this via `k = 0` transitions.

- **Q2: Can this be solved using an offset state space [-totalSum, +totalSum]?**  
  **A**: Yes, by indexing DP states with an offset $+S$: $\text{dp}[i][\text{sum} + S]$. However, that requires $2S$ space, whereas our algebraic target reduction $S_1 = (S + T)/2$ requires only $S/2$ space (a $4\times$ memory reduction!).

- **Q3: What if nums could contain negative numbers as input?**  
  **A**: If input numbers are already signed, we can convert all elements to their absolute values $|\text{nums}[i]|$ since choosing $\pm x$ is equivalent to choosing $\pm |x|$.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `Target Sum`, `LeetCode-494`, `Medium`
- **Related problems to practice next**:
- **Count Partitions with Difference**: Mathematical twin.
- **Subset Sum Equal to Target**: Base boolean form.
