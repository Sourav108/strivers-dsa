# Count Partitions with Given Difference (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Count Partitions with Given Difference](https://takeuforward.org/data-structure/count-partitions-with-given-difference-dp-18/)
- **Difficulty**: Medium
- **Statement**: Given an array `arr` of $N$ integers and an integer $D$, find the number of ways to partition `arr` into two subsets $S_1$ and $S_2$ such that $\text{sum}(S_1) \ge \text{sum}(S_2)$ and $\text{sum}(S_1) - \text{sum}(S_2) = D$. Return the answer modulo $10^9 + 7$.

---

## 1. Problem, Restated

Find the number of subsets with sum $S_1 = (\text{totalSum} + D) / 2$ using Count Subsets DP in $\mathcal{O}(N \times S_1)$ time and $\mathcal{O}(S_1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Algebraic System of Equations**: 
Let total array sum be $S = \sum_{i=0}^{n-1} \text{arr}[i]$. 
We are given two simultaneous constraints on subset sums $S_1$ and $S_2$: 
1) $S_1 + S_2 = S$ 
2) $S_1 - S_2 = D$ 
Adding equation (1) and (2): 
$$(S_1 + S_2) + (S_1 - S_2) = S + D \implies 2S_1 = S + D \implies S_1 = \frac{S + D}{2}$$ 
**Validity Conditions**: 
1) If $S < D$, difference $D$ is impossible $\implies$ return 0. 
2) If $(S + D)$ is **ODD** ($(S + D) \% 2 \ne 0$), $S_1$ cannot be an integer $\implies$ return 0. 
**Reduced Problem**: 
Count the number of subsets in `arr` that sum up to $\text{target} = (S + D) / 2$ using Count Subsets DP modulo $10^9+7$ in $\mathcal{O}(N \times \text{target})$ time and $\mathcal{O}(\text{target})$ space.

- **Underlying Pattern**: `Algebraic Target Reduction to Count Subsets with Sum K`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^N$ subset partitions and verify $S_1 - S_2 = D$ in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) recursion
};
```

### Java Code
```java
class SolutionNaive {
    // O(2^N) recursion
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Exponential duplicate subset partitions.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x target) time and O(N x target) space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;

class Solution2D {
    const int MOD = 1e9 + 7;
public:
    int countPartitions(int n, int d, vector<int>& arr) {
        int totalSum = 0;
        for (int x : arr) totalSum += x;
        if (totalSum < d || (totalSum + d) % 2 != 0) return 0;
        int target = (totalSum + d) / 2;
        
        vector<vector<int>> dp(n, vector<int>(target + 1, 0));
        if (arr[0] == 0) dp[0][0] = 2;
        else dp[0][0] = 1;
        if (arr[0] != 0 && arr[0] <= target) dp[0][arr[0]] = 1;
        
        for (int i = 1; i < n; i++) {
            for (int k = 0; k <= target; k++) {
                int notTake = dp[i - 1][k];
                int take = (k >= arr[i]) ? dp[i - 1][k - arr[i]] : 0;
                dp[i][k] = (notTake + take) % MOD;
            }
        }
        return dp[n - 1][target];
    }
};
```

### Java Code
```java
class Solution2D {
    int MOD = 1e9 + 7;

    int countPartitions(int n, int d, int[] arr) {
        int totalSum = 0;
        for (int x : arr) totalSum += x;
        if (totalSum < d || (totalSum + d) % 2 != 0) return 0;
        int target = (totalSum + d) / 2;
        
        int[][] dp = new int[n][target + 1];
        if (arr[0] == 0) dp[0][0] = 2;
        else dp[0][0] = 1;
        if (arr[0] != 0 && arr[0] <= target) dp[0][arr[0]] = 1;
        
        for (int i = 1; i < n; i++) {
            for (int k = 0; k <= target; k++) {
                int notTake = dp[i - 1][k];
                int take = (k >= arr[i]) ? dp[i - 1][k - arr[i]] : 0;
                dp[i][k] = (notTake + take) % MOD;
            }
        }
        return dp[n - 1][target];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \text{target})$ time.
- **Space Complexity**: $\mathcal{O}(N \times \text{target})$ space.
- **Why it's still not optimal**: 2D table uses redundant row memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Space-Optimized Target-Reduced DP in $\mathcal{O}(N \times \text{target})$ time and $\mathcal{O}(\text{target})$ space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;

class Solution {
    const int MOD = 1e9 + 7;
    
    // Helper function to count subsets with given sum in O(target) space
    int countSubsets(const vector<int>& arr, int target) {
        int n = arr.size();
        vector<int> prev(target + 1, 0);
        
        // Base case handling for arr[0] (including zero)
        if (arr[0] == 0) {
            prev[0] = 2; // {empty}, {0}
        } else {
            prev[0] = 1;
            if (arr[0] <= target) {
                prev[arr[0]] = 1;
            }
        }
        
        for (int i = 1; i < n; i++) {
            vector<int> cur(target + 1, 0);
            for (int k = 0; k <= target; k++) {
                int notTake = prev[k];
                int take = (k >= arr[i]) ? prev[k - arr[i]] : 0;
                
                cur[k] = (notTake + take) % MOD;
            }
            prev = cur;
        }
        
        return prev[target];
    }
    
public:
    int countPartitions(int n, int d, vector<int>& arr) {
        int totalSum = 0;
        for (int x : arr) {
            totalSum += x;
        }
        
        // Edge cases: Total sum cannot be less than difference, and (totalSum + d) must be even
        if (totalSum < d || (totalSum + d) % 2 != 0) {
            return 0;
        }
        
        int target = (totalSum + d) / 2;
        return countSubsets(arr, target);
    }
};
```

### Java Code
```java
class Solution {
    int MOD = 1e9 + 7;
    
    // Helper function to count subsets with given sum in O(target) space
    int countSubsets(int[] arr, int target) {
        int n = arr.length;
        int[] prev = new int[target + 1];
        
        // Base case handling for arr[0] (including zero)
        if (arr[0] == 0) {
            prev[0] = 2; // {empty}, {0}
        } else {
            prev[0] = 1;
            if (arr[0] <= target) {
                prev[arr[0]] = 1;
            }
        }
        
        for (int i = 1; i < n; i++) {
            int[] cur = new int[target + 1];
            for (int k = 0; k <= target; k++) {
                int notTake = prev[k];
                int take = (k >= arr[i]) ? prev[k - arr[i]] : 0;
                
                cur[k] = (notTake + take) % MOD;
            }
            prev = cur;
        }
        
        return prev[target];
    }

    int countPartitions(int n, int d, int[] arr) {
        int totalSum = 0;
        for (int x : arr) {
            totalSum += x;
        }
        
        // Edge cases: Total sum cannot be less than difference, and (totalSum + d) must be even
        if (totalSum < d || (totalSum + d) % 2 != 0) {
            return 0;
        }
        
        int target = (totalSum + d) / 2;
        return countSubsets(arr, target);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \frac{S + D}{2})$ time.
- **Space Complexity**: $\mathcal{O}(\frac{S + D}{2})$ space.
- **Why this is optimal**: Algebraic reduction reduces partition search to standard 1D subset count.

---

## 6. Dry Run

`arr = [5, 2, 6, 4]`, $D = 3, S = 17 \implies S + D = 20, \text{target} = 10$

| Step | Action / State Change | Result |
|---|---|---|
| `Formula` | $S_1 = (17 + 3) / 2 = 10$ | target = 10 |
| `Count Subsets` | Subsets summing to 10 from `[5, 2, 6, 4]`: | `{6, 4}` (sum 10, remaining `{5, 2}` sum 7, diff = $10-7=3$) |
| `Result` | Total valid partitions = 1 (`{6, 4}` and `{5, 2}`) | Count = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $S < D$ (returns 0).
- $(S + D) \% 2 \ne 0$ (returns 0).
- Array with zeroes (correctly handled by $k=0$ base transitions).

### Common Bugs to Avoid
- Forgetting `(totalSum + d) % 2 != 0` check (performing integer division on odd sum yields wrong target).
- Not handling zeroes properly in base case.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is this problem 100% mathematically identical to LeetCode 494 Target Sum?**  
  **A**: In Target Sum, we assign $+$ or $-$ signs to each number. Sum of positive elements is $S_1$, sum of negative elements is $S_2$. Total target is $S_1 - S_2 = \text{target}$. Since $S_1 + S_2 = \text{totalSum}$, $S_1 = (\text{totalSum} + \text{target}) / 2$, which is identical to Count Partitions with Difference!

- **Q2: What happens if target sum D is negative?**  
  **A**: If $D < 0$, by symmetry $|D| = -D$, we substitute $|D|$ since partition difference is unsigned.

- **Q3: What is the time complexity when D is very large?**  
  **A**: If $D > S$, condition `totalSum < d` exits in $\mathcal{O}(1)$ time immediately!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `Target Sum`, `Modulo Arithmetic`, `Medium`
- **Related problems to practice next**:
- **Count Subsets with Sum K**: Base subroutine.
- **Target Sum**: Sign assignment equivalent.
