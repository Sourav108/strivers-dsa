# Subset Sum Equal to Target (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Subset Sum Equal to Target](https://takeuforward.org/data-structure/subset-sum-equal-to-target-dp-14/)
- **Difficulty**: Medium
- **Statement**: Given an array of positive integers `arr` of size $N$ and an integer `target`, determine if there exists a subset of `arr` whose elements sum up to exactly `target`.

---

## 1. Problem, Restated

Find whether any subset of `arr` sums to `target` using 0/1 Knapsack Pick / Not-Pick DP in $\mathcal{O}(N \times \text{target})$ time and $\mathcal{O}(\text{target})$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Boolean result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**State Definition**: Let $\text{dp}[i][k]$ be a boolean indicating whether a subset of elements from index $0 \dots i$ can sum up to exactly $k$. 
**Transition**: For element `arr[i]`: 
1) **Not-Pick**: $\text{dp}[i - 1][k]$ (target $k$ is formed without `arr[i]`). 
2) **Pick** (if $k \ge \text{arr}[i]$): $\text{dp}[i - 1][k - \text{arr}[i]]$ (target $k - \text{arr}[i]$ was formed by earlier elements). 
$$\text{dp}[i][k] = \text{dp}[i - 1][k] \lor \text{dp}[i - 1][k - \text{arr}[i]]$$ 
**Base Cases**: 
- $\text{dp}[i][0] = \text{true}$ for all $i$ (empty subset gives sum 0). 
- $\text{dp}[0][\text{arr}[0]] = \text{true}$ (if $\text{arr}[0] \le \text{target}$). 
**Space Optimization**: Maintain a 1D boolean array `prev(target + 1)` evaluated backward from $\text{target}$ down to $\text{arr}[i]$ in $\mathcal{O}(N \times \text{target})$ time and $\mathcal{O}(\text{target})$ space.

- **Underlying Pattern**: `Subsequence / 0-1 Knapsack Pick vs Not-Pick Boolean DP`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^N$ subsets in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    bool solve(int i, int target, const vector<int>& arr) {
        if (target == 0) return true;
        if (i == 0) return arr[0] == target;
        bool notTake = solve(i - 1, target, arr);
        bool take = false;
        if (target >= arr[i]) take = solve(i - 1, target - arr[i], arr);
        return notTake || take;
    }
public:
    bool isSubsetSum(vector<int>& arr, int target) {
        return solve(arr.size() - 1, target, arr);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary decision tree evaluates redundant `(i, target)` subproblems.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table of size N x (target + 1) in O(N x target) time and O(N x target) space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution2D {
public:
    bool isSubsetSum(vector<int>& arr, int target) {
        int n = arr.size();
        vector<vector<bool>> dp(n, vector<bool>(target + 1, false));
        for (int i = 0; i < n; i++) dp[i][0] = true;
        if (arr[0] <= target) dp[0][arr[0]] = true;
        
        for (int i = 1; i < n; i++) {
            for (int k = 1; k <= target; k++) {
                bool notTake = dp[i - 1][k];
                bool take = (k >= arr[i]) ? dp[i - 1][k - arr[i]] : false;
                dp[i][k] = notTake || take;
            }
        }
        return dp[n - 1][target];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \text{target})$ time.
- **Space Complexity**: $\mathcal{O}(N \times \text{target})$ space.
- **Why it's still not optimal**: 2D table uses redundant rows when 1 row suffices.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Backward-Iterating Space-Optimized DP in $\mathcal{O}(N \times \text{target})$ time and $\mathcal{O}(\text{target})$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool isSubsetSum(vector<int>& arr, int target) {
        int n = arr.size();
        
        // prev[k] is true if a subset sum of k is achievable
        vector<bool> prev(target + 1, false);
        
        // Base case: Sum of 0 is always possible with an empty subset
        prev[0] = true;
        
        if (arr[0] <= target) {
            prev[arr[0]] = true;
        }
        
        for (int i = 1; i < n; i++) {
            vector<bool> cur(target + 1, false);
            cur[0] = true; // Base case for new row
            
            for (int k = 1; k <= target; k++) {
                bool notTake = prev[k];
                bool take = (k >= arr[i]) ? prev[k - arr[i]] : false;
                
                cur[k] = notTake || take;
            }
            
            prev = cur;
        }
        
        return prev[target];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \text{target})$ time (pseudo-polynomial dynamic programming).
- **Space Complexity**: $\mathcal{O}(\text{target})$ space (single boolean vector of size $\text{target} + 1$).
- **Why this is optimal**: 1D boolean array enables ultra-fast bitwise transitions and minimal memory footprint.

---

## 6. Dry Run

`arr = [2, 3, 7, 8]`, $\text{target} = 11$

| Step | Action / State Change | Result |
|---|---|---|
| `Base (arr[0]=2)` | prev: `k=0: T, k=2: T`, all other $k$: F | Ready |
| `i = 1 (val 3)` | Combines with {0, 2} $\implies k \in \{0, 2, 3, 5\}$ are True | prev updated |
| `i = 2 (val 7)` | Combines with {0, 2, 3, 5} $\implies k \in \{0, 2, 3, 5, 7, 9, 10, 12\}$ | prev updated |
| `i = 3 (val 8)` | k=11: $11 - 8 = 3$ (which was True!) $\implies$ `take` is True $\implies prev[11] = \text{True}$ | Target 11 achieved! |
| `Result` | Return True (Subset: `[3, 8]`: $3 + 8 = 11$) | Found = True ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $\text{target} = 0$ (always returns true).
- All elements larger than $\text{target}$ (returns false).
- Sum of all elements $< \text{target}$ (returns false).

### Common Bugs to Avoid
- Iterating forward in single-array in-place optimization (causes the SAME element to be reused multiple times like unbounded knapsack; must iterate backward or use separate `cur` vector).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is this algorithm described as 'Pseudo-Polynomial' time?**  
  **A**: Because its time complexity $\mathcal{O}(N \times \text{target})$ is polynomial in the numerical VALUE of $\text{target}$, but exponential in the NUMBER OF BITS $\log(\text{target})$ used to represent $\text{target}$. Subset Sum is NP-Complete, and this DP is polynomial only when $\text{target}$ is reasonably small!

- **Q2: How to optimize using C++ std::bitset?**  
  **A**: We can represent reachable sums as a bitset `bs`: `bs[0] = 1; for (int x : arr) bs |= (bs << x); return bs[target];`. This runs in $\mathcal{O}(N \times \text{target} / 64)$ time using SIMD 64-bit word operations (up to $64\times$ faster in practice)!

- **Q3: How does Subset Sum relate to Partition Equal Subset Sum (Problem 15)?**  
  **A**: If total sum $S = \sum \text{arr}[i]$ is odd, partition is impossible. If $S$ is even, Partition Equal Subset Sum is IDENTICAL to finding a subset with $\text{target} = S / 2$!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `0-1 Knapsack`, `Pseudo-Polynomial`, `Medium`
- **Related problems to practice next**:
- **Partition Equal Subset Sum**: Equal half partition.
- **Count Subsets with Sum K**: Frequency counter variant.
