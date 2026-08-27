# Count Subsets with Sum K (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Count Subsets with Sum K](https://takeuforward.org/data-structure/count-subsets-with-sum-k-dp-17/)
- **Difficulty**: Medium
- **Statement**: Given an array `arr` of $N$ integers (which may include 0) and an integer $K$, count the total number of non-empty/empty subsets whose elements sum up to exactly $K$. Return the answer modulo $10^9 + 7$.

---

## 1. Problem, Restated

Compute the number of subsets with sum $K$ in an array containing non-negative integers (including zeroes) modulo $10^9 + 7$ in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(K)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**State Definition**: $\text{dp}[i][k] =$ number of subsets from elements $0 \dots i$ that sum to $k$. 
**Additive Transition**: 
$$\text{dp}[i][k] = (\text{dp}[i - 1][k] + \text{dp}[i - 1][k - \text{arr}[i]]) \pmod{10^9 + 7}$$ 
**CRITICAL GOTCHA (Handling Zeroes)**: 
If `arr` contains zeroes, $\text{arr}[0] = 0$ provides **TWO ways** to achieve sum 0 at index 0 (pick 0 or skip 0)! 
- If $\text{arr}[0] == 0$: $\text{dp}[0][0] = 2$ 
- If $\text{arr}[0] \ne 0$: $\text{dp}[0][0] = 1$, and $\text{dp}[0][\text{arr}[0]] = 1$ (if $\text{arr}[0] \le K$). 
Alternatively, start with standard base case $\text{dp}[0] = 1$ and process elements from index 0 uniformly! 
**Space Optimization**: A 1D row array `prev(K + 1)` in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(K)$ space.

- **Underlying Pattern**: `Subsequence Counting DP / Additive Branch Accumulation with Zero Handling`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^N$ subsets and count matching subset sums in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int countSubsets(int i, int sum, const vector<int>& arr) {
        if (i == 0) {
            if (sum == 0 && arr[0] == 0) return 2;
            if (sum == 0 || sum == arr[0]) return 1;
            return 0;
        }
        int notTake = countSubsets(i - 1, sum, arr);
        int take = (sum >= arr[i]) ? countSubsets(i - 1, sum - arr[i], arr) : 0;
        return notTake + take;
    }
public:
    int perfectSum(vector<int>& arr, int n, int sum) {
        return countSubsets(n - 1, sum, arr);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary recursive tree with repeated subproblem evaluation.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x K) time and O(N x K) space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution2D {
    const int MOD = 1e9 + 7;
public:
    int perfectSum(vector<int>& arr, int n, int sum) {
        vector<vector<int>> dp(n, vector<int>(sum + 1, 0));
        if (arr[0] == 0) dp[0][0] = 2;
        else dp[0][0] = 1;
        if (arr[0] != 0 && arr[0] <= sum) dp[0][arr[0]] = 1;
        
        for (int i = 1; i < n; i++) {
            for (int k = 0; k <= sum; k++) {
                int notTake = dp[i - 1][k];
                int take = (k >= arr[i]) ? dp[i - 1][k - arr[i]] : 0;
                dp[i][k] = (notTake + take) % MOD;
            }
        }
        return dp[n - 1][sum];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times K)$ time.
- **Space Complexity**: $\mathcal{O}(N \times K)$ table space.
- **Why it's still not optimal**: Full 2D table uses redundant memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Space-Optimized Counting DP in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(K)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
    const int MOD = 1e9 + 7;
public:
    int perfectSum(vector<int>& arr, int n, int sum) {
        // prev[k] stores number of subsets summing to k
        vector<int> prev(sum + 1, 0);
        
        // Base case for index 0
        if (arr[0] == 0) {
            prev[0] = 2; // Pick 0 or Don't Pick 0
        } else {
            prev[0] = 1; // Don't pick
            if (arr[0] <= sum) {
                prev[arr[0]] = 1; // Pick
            }
        }
        
        for (int i = 1; i < n; i++) {
            vector<int> cur(sum + 1, 0);
            
            // Loop from k = 0 to sum (k = 0 must be computed for subsequent zeroes!)
            for (int k = 0; k <= sum; k++) {
                int notTake = prev[k];
                int take = (k >= arr[i]) ? prev[k - arr[i]] : 0;
                
                cur[k] = (notTake + take) % MOD;
            }
            
            prev = cur;
        }
        
        return prev[sum];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times K)$ time (single 2D state loop).
- **Space Complexity**: $\mathcal{O}(K)$ space (two 1D vectors of size $K + 1$).
- **Why this is optimal**: Calculates exact subset counts with modulo arithmetic in minimal 1D space.

---

## 6. Dry Run

`arr = [0, 0, 1]`, $N = 3, K = 1$

| Step | Action / State Change | Result |
|---|---|---|
| `arr[0] = 0` | prev[0] = 2 (subsets: `[]`, `[0]`) | Ready |
| `i = 1 (val 0)` | cur[0] = prev[0] + prev[0] = 2 + 2 = 4 (subsets: `[]`, `[0_1]`, `[0_2]`, `[0_1, 0_2]`) | prev[0] = 4 |
| `i = 2 (val 1)` | k=1: notTake(0) + take(prev[0]=4) = 4 | cur[1] = 4 |
| `Result` | Total Subsets = 4 (`[1]`, `[0, 1]`, `[0, 1]`, `[0, 0, 1]`) | Count = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $K = 0$ with multiple zeroes in array ($2^{\text{zeroes}}$ subsets).
- $K < \min(\text{arr})$ (returns 0).
- All elements identical.

### Common Bugs to Avoid
- Looping `k` from 1 to `sum` instead of 0 to `sum` (misses updating `prev[0]` when multiple zeroes are present!).
- Hardcoding `prev[0] = 1` unconditionally without checking `if (arr[0] == 0)`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST the inner loop start from k = 0 instead of k = 1?**  
  **A**: Because if subsequent array elements are 0, they DOUBLE the number of ways to form sum 0 (and all other sums)! If `k = 0` is not updated, zeroes will be ignored in the combinatorics count!

- **Q2: How to handle large arrays with zeroes via pre-processing?**  
  **A**: We can filter out all zeroes into count $Z$, solve the subset sum DP strictly on non-zero positive integers, and finally multiply the result by $2^Z \pmod{MOD}$!

- **Q3: How does Count Subsets relate to Target Sum (LeetCode 494)?**  
  **A**: In Target Sum, assigning $+$ and $-$ signs to achieve $T$ is mathematically reduced to counting subsets with sum $S_1 = (\text{totalSum} + T) / 2$!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `Counting DP`, `Modulo Arithmetic`, `Medium`
- **Related problems to practice next**:
- **Count Partitions with Difference**: Difference equation reduction.
- **Target Sum**: Sign assignment isomorphism.
