# Partition Set Into 2 Subsets With Min Absolute Sum Difference (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Partition Set Into 2 Subsets With Min Absolute Sum Difference](https://takeuforward.org/data-structure/partition-set-into-2-subsets-with-min-absolute-sum-diff-dp-16/)
- **Difficulty**: Hard
- **Statement**: Given an array `arr` of $N$ non-negative integers. Divide it into two sets $S_1$ and $S_2$ such that the absolute difference between their sums $|\text{sum}(S_1) - \text{sum}(S_2)|$ is minimized. Return the minimum absolute difference.

---

## 1. Problem, Restated

Find subset sum $s_1 \le \text{totalSum} / 2$ that minimizes $(\text{totalSum} - 2s_1)$ using the last row of the Subset Sum DP table in $\mathcal{O}(N \times \text{totalSum})$ time and $\mathcal{O}(\text{totalSum})$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Mathematical Formulation**: 
Let total array sum be $S = \sum_{i=0}^{n-1} \text{arr}[i]$. 
If subset $S_1$ has sum $s_1$, then subset $S_2$ must have sum $s_2 = S - s_1$. 
The absolute difference is: 
$$|s_1 - s_2| = |s_1 - (S - s_1)| = |S - 2s_1|$$ 
Without loss of generality, let $s_1 \le S / 2$. Then the difference is simply $S - 2s_1$. 
**Algorithm**: 
1) Run the 0/1 Knapsack Subset Sum DP up to $\text{target} = S$. 
2) The final row `dp[n - 1][k]` (or space-optimized boolean array `prev[k]`) tells us for EVERY integer $k \in [0, S]$ whether a subset with sum $k$ is possible! 
3) Iterate $s_1$ from $0$ to $S / 2$: if `prev[s1] == true`, candidate difference is $S - 2s_1$. 
4) The minimum candidate across $[0, S/2]$ is the answer! 
Runs in $\mathcal{O}(N \times S)$ time and $\mathcal{O}(S)$ space.

- **Underlying Pattern**: `Subset Sum Reachability DP + Half-Sum Range Boundary Scan`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^N$ subsets and evaluate $|S - 2s_1|$ in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) recursive partition search
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack.
- **Why it's not good enough**: Exponential duplicate subset exploration.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table of size N x (S + 1) in O(N x S) time and O(N x S) space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
#include <cmath>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int minSubsetSumDifference(vector<int>& arr, int n) {
        int totalSum = 0;
        for (int x : arr) totalSum += x;
        
        vector<vector<bool>> dp(n, vector<bool>(totalSum + 1, false));
        for (int i = 0; i < n; i++) dp[i][0] = true;
        if (arr[0] <= totalSum) dp[0][arr[0]] = true;
        
        for (int i = 1; i < n; i++) {
            for (int k = 1; k <= totalSum; k++) {
                bool notTake = dp[i - 1][k];
                bool take = (k >= arr[i]) ? dp[i - 1][k - arr[i]] : false;
                dp[i][k] = notTake || take;
            }
        }
        
        int minDiff = 1e9;
        for (int s1 = 0; s1 <= totalSum / 2; s1++) {
            if (dp[n - 1][s1]) {
                minDiff = min(minDiff, totalSum - 2 * s1);
            }
        }
        return minDiff;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times S)$ time.
- **Space Complexity**: $\mathcal{O}(N \times S)$ space.
- **Why it's still not optimal**: Full 2D table is redundant.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Space-Optimized DP (or Meet-in-the-Middle for negative numbers) in $\mathcal{O}(N \times S)$ time and $\mathcal{O}(S)$ space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
#include <cmath>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minSubsetSumDifference(vector<int>& arr, int n) {
        int totalSum = 0;
        for (int x : arr) {
            totalSum += x;
        }
        
        // prev[k] is true if a subset sum of k is achievable
        vector<bool> prev(totalSum + 1, false);
        prev[0] = true; // Base case: sum 0 is always achievable
        
        if (arr[0] <= totalSum) {
            prev[arr[0]] = true;
        }
        
        for (int i = 1; i < n; i++) {
            vector<bool> cur(totalSum + 1, false);
            cur[0] = true;
            
            for (int k = 1; k <= totalSum; k++) {
                bool notTake = prev[k];
                bool take = (k >= arr[i]) ? prev[k - arr[i]] : false;
                
                cur[k] = notTake || take;
            }
            
            prev = cur;
        }
        
        // Scan the reachable sums up to totalSum / 2
        int minDiff = 1e9;
        for (int s1 = 0; s1 <= totalSum / 2; s1++) {
            if (prev[s1]) {
                int s2 = totalSum - s1;
                minDiff = min(minDiff, abs(s2 - s1));
            }
        }
        
        return minDiff;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times S)$ time (where $S = \sum \text{arr}[i]$).
- **Space Complexity**: $\mathcal{O}(S)$ space (single boolean row array).
- **Why this is optimal**: Scans only the reachable sums in the first half $[0, S/2]$ with zero redundant matrix overhead.

---

## 6. Dry Run

`arr = [1, 2, 3, 9]`, $N = 4, S = 15, S/2 = 7$

| Step | Action / State Change | Result |
|---|---|---|
| `Reachable sums` | prev array computed for all $k \in [0, 15]$ | Achievable in $[0, 7]$: `{0, 1, 2, 3, 4, 5, 6}` |
| `s1 = 6` | $s_2 = 15 - 6 = 9 \implies |9 - 6| = 3$ | Diff = 3 |
| `s1 = 7` | Not reachable (prev[7] = false) | Skipped |
| `Result` | Min Diff = 3 (Subset 1: `{1, 2, 3}` sum 6; Subset 2: `{9}` sum 9) | Min Difference = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 2$ (returns $|\text{arr}[0] - \text{arr}[1]|$).
- Total sum is even and divisible (returns 0).
- All array elements are identical.

### Common Bugs to Avoid
- Scanning beyond $S / 2$ (unnecessary duplication since $S - 2s_1$ becomes symmetric).
- Negative array elements (standard DP fails; requires Meet-in-the-Middle with binary search!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to solve this problem if array elements can be NEGATIVE (LeetCode 2035)?**  
  **A**: When elements can be negative, DP table indices can underflow and $S$ can be unbounded. Since $N \le 30$, we use **Meet-in-the-Middle**: split array into two halves of size $N/2$, compute all subset sums grouped by size, sort the second half, and use `std::lower_bound` in $\mathcal{O}(2^{N/2} \cdot N)$ time!

- **Q2: What if both subsets are strictly required to have EQUAL sizes ($N/2$ elements each)?**  
  **A**: We add an extra state dimension $\text{dp}[i][\text{count}][\text{sum}]$, tracking whether a subset of size $\text{count} = N/2$ can form sum $s_1$ in $\mathcal{O}(N^2 \cdot S)$ time.

- **Q3: Can std::bitset be used here?**  
  **A**: YES! `bitset<50001> bs; bs[0] = 1; for (int x : arr) bs |= (bs << x);` Scans bits up to $S/2$ in $\mathcal{O}(1)$ word time!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `0-1 Knapsack`, `Meet-in-the-Middle`, `Hard`
- **Related problems to practice next**:
- **Partition Equal Subset Sum**: Zero-difference special case.
- **Count Partitions with Difference**: Counting counterpart.
