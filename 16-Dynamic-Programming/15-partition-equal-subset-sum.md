# Partition Equal Subset Sum (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Partition Equal Subset Sum](https://takeuforward.org/data-structure/partition-equal-subset-sum-dp-15/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums`, return `true` if you can partition the array into two subsets such that the sum of the elements in both subsets is equal, or `false` otherwise.

---

## 1. Problem, Restated

Determine if `nums` can be split into two subsets of equal sum $S / 2$ (where $S = \sum \text{nums}[i]$) using Subset Sum DP in $\mathcal{O}(N \times S)$ time and $\mathcal{O}(S)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Boolean result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Mathematical Reduction**: 
Let total array sum be $S = \sum_{i=0}^{n-1} \text{nums}[i]$. 
1) If $S$ is **ODD** ($S \% 2 \ne 0$), it is mathematically impossible to divide an odd integer into two equal integer halves $\implies$ return `false` immediately! 
2) If $S$ is **EVEN**, partitioning into two subsets of equal sum is EXACTLY EQUIVALENT to finding ANY subset of `nums` that sums up to $\text{target} = S / 2$! 
If one subset sums to $S / 2$, the remaining elements must also automatically sum to $S - S/2 = S/2$! 
We run the optimal Subset Sum DP with $\text{target} = S / 2$ in $\mathcal{O}(N \times (S/2))$ time and $\mathcal{O}(S/2)$ space.

- **Underlying Pattern**: `Subset Sum Isomorphism with Target Reduction $S/2$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^N$ partitions in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    bool solve(int i, int target, const vector<int>& nums) {
        if (target == 0) return true;
        if (i == 0) return nums[0] == target;
        bool notTake = solve(i - 1, target, nums);
        bool take = (target >= nums[i]) ? solve(i - 1, target - nums[i], nums) : false;
        return notTake || take;
    }
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for (int x : nums) sum += x;
        if (sum % 2 != 0) return false;
        return solve(nums.size() - 1, sum / 2, nums);
    }
};
```

### Java Code
```java
class SolutionNaive {
    boolean solve(int i, int target, int[] nums) {
        if (target == 0) return true;
        if (i == 0) return nums[0] == target;
        boolean notTake = solve(i - 1, target, nums);
        boolean take = (target >= nums[i]) ? public solve(i - 1, target - nums[i], nums) { /* initialized: false;
        return notTake || take;
    }

    boolean canPartition(int[] nums)  */ 
        int sum = 0;
        for (int x : nums) sum += x;
        if (sum % 2 != 0) return false;
        return solve(nums.length - 1, sum / 2, nums);
     }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary decision tree evaluates redundant subproblems exponentially.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x S/2) time and O(N x S/2) space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;

class Solution2D {
public:
    bool canPartition(vector<int>& nums) {
        int n = nums.size();
        int totalSum = 0;
        for (int x : nums) totalSum += x;
        if (totalSum % 2 != 0) return false;
        
        int target = totalSum / 2;
        vector<vector<bool>> dp(n, vector<bool>(target + 1, false));
        for (int i = 0; i < n; i++) dp[i][0] = true;
        if (nums[0] <= target) dp[0][nums[0]] = true;
        
        for (int i = 1; i < n; i++) {
            for (int k = 1; k <= target; k++) {
                bool notTake = dp[i - 1][k];
                bool take = (k >= nums[i]) ? dp[i - 1][k - nums[i]] : false;
                dp[i][k] = notTake || take;
            }
        }
        return dp[n - 1][target];
    }
};
```

### Java Code
```java
class Solution2D {

    boolean canPartition(int[] nums) {
        int n = nums.length;
        int totalSum = 0;
        for (int x : nums) totalSum += x;
        if (totalSum % 2 != 0) return false;
        
        int target = totalSum / 2;
        boolean[][] dp = new boolean[n][target + 1];
        for (int i = 0; i < n; i++) dp[i][0] = true;
        if (nums[0] <= target) dp[0][nums[0]] = true;
        
        for (int i = 1; i < n; i++) {
            for (int k = 1; k <= target; k++) {
                boolean notTake = dp[i - 1][k];
                boolean take = (k >= nums[i]) ? dp[i - 1][k - nums[i]] : false;
                dp[i][k] = notTake || take;
            }
        }
        return dp[n - 1][target];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times S/2)$ time.
- **Space Complexity**: $\mathcal{O}(N \times S/2)$ space.
- **Why it's still not optimal**: 2D matrix allocation uses redundant space.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Space-Optimized Boolean DP in $\mathcal{O}(N \times S/2)$ time and $\mathcal{O}(S/2)$ space (or `std::bitset`).

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;

class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int n = nums.size();
        int totalSum = 0;
        for (int x : nums) {
            totalSum += x;
        }
        
        // If total sum is odd, it cannot be partitioned into two equal integer halves
        if (totalSum % 2 != 0) {
            return false;
        }
        
        int target = totalSum / 2;
        
        // prev[k] indicates whether subset sum k is achievable
        vector<bool> prev(target + 1, false);
        prev[0] = true; // Base case: sum 0 is always possible
        
        if (nums[0] <= target) {
            prev[nums[0]] = true;
        }
        
        for (int i = 1; i < n; i++) {
            vector<bool> cur(target + 1, false);
            cur[0] = true;
            
            for (int k = 1; k <= target; k++) {
                bool notTake = prev[k];
                bool take = (k >= nums[i]) ? prev[k - nums[i]] : false;
                
                cur[k] = notTake || take;
            }
            
            prev = cur;
        }
        
        return prev[target];
    }
};
```

### Java Code
```java
class Solution {

    boolean canPartition(int[] nums) {
        int n = nums.length;
        int totalSum = 0;
        for (int x : nums) {
            totalSum += x;
        }
        
        // If total sum is odd, it cannot be partitioned into two equal integer halves
        if (totalSum % 2 != 0) {
            return false;
        }
        
        int target = totalSum / 2;
        
        // prev[k] indicates whether subset sum k is achievable
        boolean[] prev = new boolean[target + 1];
        prev[0] = true; // Base case: sum 0 is always possible
        
        if (nums[0] <= target) {
            prev[nums[0]] = true;
        }
        
        for (int i = 1; i < n; i++) {
            boolean[] cur = new boolean[target + 1];
            cur[0] = true;
            
            for (int k = 1; k <= target; k++) {
                boolean notTake = prev[k];
                boolean take = (k >= nums[i]) ? prev[k - nums[i]] : false;
                
                cur[k] = notTake || take;
            }
            
            prev = cur;
        }
        
        return prev[target];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times (S / 2))$ time (where $S = \sum \text{nums}[i]$).
- **Space Complexity**: $\mathcal{O}(S / 2)$ auxiliary space.
- **Why this is optimal**: Reduces partition problem to 1D subset sum verification of half total sum.

---

## 6. Dry Run

`nums = [1, 5, 11, 5]` ($N = 4$)

| Step | Action / State Change | Result |
|---|---|---|
| `Total Sum` | Sum = $1 + 5 + 11 + 5 = 22$ (Even!) $\implies \text{target} = 22 / 2 = 11$ | target = 11 |
| `Base (nums[0]=1)` | prev: `k=0: T, k=1: T`, others F | Ready |
| `i = 1 (val 5)` | Achievable sums: `{0, 1, 5, 6}` | prev updated |
| `i = 2 (val 11)` | nums[2]=11: $11 - 11 = 0$ (True!) $\implies prev[11] = \text{True}$ | Target 11 reached! |
| `Result` | Return True (Subset 1: `[1, 5, 5]` = 11; Subset 2: `[11]` = 11) | Equal Partition = True ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns false: cannot form two non-empty subsets).
- Total sum is odd (returns false immediately).
- Max element $> S / 2$ (returns false).

### Common Bugs to Avoid
- Forgetting `totalSum % 2 != 0` check.
- Integer division without odd parity check ($15 / 2 = 7$, might search for 7 on sum 15!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this be solved using std::bitset for ultra-fast bitwise execution?**  
  **A**: YES! `bitset<10001> bs; bs[0] = 1; for (int x : nums) bs |= (bs << x); return bs[totalSum / 2];`. For $S \le 20000$, this executes in $\sim 1$ ms!

- **Q2: How to extend this to Partitioning into K Subsets with Equal Sum (LeetCode 698)?**  
  **A**: For $K > 2$, subset sum reduction no longer suffices because we must track all $K$ subset states. We use **Bitmask DP with Backtracking** $\text{dp}[\text{mask}]$ in $\mathcal{O}(K \cdot 2^N)$ time!

- **Q3: What is the difference between Partition Equal Subset Sum and 0/1 Knapsack?**  
  **A**: In 0/1 Knapsack, each item has a weight and a separate value; in Partition Equal Subset Sum, the weight and value of each item are identical to its numerical value $\text{nums}[i]$, with capacity $W = S / 2$.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `0-1 Knapsack`, `LeetCode-416`, `Medium`
- **Related problems to practice next**:
- **Subset Sum Equal to Target**: Base subset sum problem.
- **Partition Set Min Absolute Diff**: Optimization variant.
