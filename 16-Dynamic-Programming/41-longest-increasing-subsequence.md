# Longest Increasing Subsequence (LIS) (Step 16.6 — DP on LIS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest Increasing Subsequence (LIS)](https://takeuforward.org/data-structure/longest-increasing-subsequence-dp-41/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums`, return the length of the longest strictly increasing subsequence.

---

## 1. Problem, Restated

Find the maximum length of a strictly increasing subsequence in `nums` using Tabulation DP in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space (and Patience Sorting Binary Search in $\mathcal{O}(N \log N)$ time).

- **Input**: Problem constraints and parameters.
- **Output**: Length / Array result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The 1D LIS Formulation**: 
Let $\text{dp}[i]$ denote the length of the Longest Increasing Subsequence **ending strictly at index $i$**. 
1) Base Case: Every individual element is an increasing subsequence of length 1 $\implies \text{dp}[i] = 1$ for all $i$. 
2) For each index $i \in [0, n - 1]$ and every previous index $j \in [0, i - 1]$: 
   - If $\text{nums}[j] < \text{nums}[i]$, `nums[i]` can extend the increasing subsequence ending at $j$: 
   $$\text{dp}[i] = \max(\text{dp}[i], 1 + \text{dp}[j])$$ 
3) The global answer is $\max_{0 \le i < n}(\text{dp}[i])$. 
Runs in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space. (Can be optimized to $\mathcal{O}(N \log N)$ using Binary Search / Patience Sorting).

- **Underlying Pattern**: `1D LIS Dynamic Programming / Predecessor Relaxation`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore pick / not-pick with predecessor tracking in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int prevIdx, const vector<int>& nums) {
        if (i == nums.size()) return 0;
        int notTake = solve(i + 1, prevIdx, nums);
        int take = 0;
        if (prevIdx == -1 || nums[i] > nums[prevIdx]) {
            take = 1 + solve(i + 1, i, nums);
        }
        return max(notTake, take);
    }
public:
    int lengthOfLIS(vector<int>& nums) {
        return solve(0, -1, nums);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int prevIdx, int[] nums) {
        if (i == nums.length) return 0;
        int notTake = solve(i + 1, prevIdx, nums);
        int take = 0;
        if (prevIdx == -1 || nums[i] > nums[prevIdx]) {
            take = 1 + solve(i + 1, i, nums);
        }
        return Math.max(notTake, take);
    }

    int lengthOfLIS(int[] nums) {
        return solve(0, -1, nums);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary decision tree evaluates $2^N$ subsequences.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table of size N x (N + 1) in O(N^2) time and O(N^2) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        vector<vector<int>> dp(n + 1, vector<int>(n + 1, 0));
        for (int i = n - 1; i >= 0; i--) {
            for (int prev = i - 1; prev >= -1; prev--) {
                int notTake = dp[i + 1][prev + 1];
                int take = 0;
                if (prev == -1 || nums[i] > nums[prev]) {
                    take = 1 + dp[i + 1][i + 1];
                }
                dp[i][prev + 1] = max(notTake, take);
            }
        }
        return dp[0][0];
    }
};
```

### Java Code
```java
class Solution2D {

    int lengthOfLIS(int[] nums) {
        int n = nums.length;
        int[][] dp = new int[n + 1][n + 1];
        for (int i = n - 1; i >= 0; i--) {
            for (int prev = i - 1; prev >= -1; prev--) {
                int notTake = dp[i + 1][prev + 1];
                int take = 0;
                if (prev == -1 || nums[i] > nums[prev]) {
                    take = 1 + dp[i + 1][i + 1];
                }
                dp[i][prev + 1] = Math.max(notTake, take);
            }
        }
        return dp[0][0];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ space.
- **Why it's still not optimal**: 2D matrix allocation uses redundant space.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
1D Linear DP Predecessor Relaxation in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space (and $\mathcal{O}(N \log N)$ Patience Sort).

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        
        // dp[i] stores the length of LIS ending strictly at index i
        vector<int> dp(n, 1);
        int maxLIS = 1;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    dp[i] = max(dp[i], 1 + dp[j]);
                }
            }
            maxLIS = max(maxLIS, dp[i]);
        }
        
        return maxLIS;
    }
};
```

### Java Code
```java
class Solution {

    int lengthOfLIS(int[] nums) {
        int n = nums.length;
        if (n == 0) return 0;
        
        // dp[i] stores the length of LIS ending strictly at index i
        int[] dp = new int[n];
        int maxLIS = 1;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    dp[i] = Math.max(dp[i], 1 + dp[j]);
                }
            }
            maxLIS = Math.max(maxLIS, dp[i]);
        }
        
        return maxLIS;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (nested loops comparing all pairs $j < i$).
- **Space Complexity**: $\mathcal{O}(N)$ space (single 1D vector of size $N$).
- **Why this is optimal**: 1D predecessor array enables both length computation and direct parent tracing for reconstruction.

---

## 6. Dry Run

`nums = [10, 9, 2, 5, 3, 7, 101, 18]` ($N = 8$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (10), i = 1 (9), i = 2 (2)` | dp: `[1, 1, 1]` | Singletons |
| `i = 3 (5)` | nums[2]=2 < 5 $\implies dp[3] = 1 + dp[2] = 2$ | dp: `[1, 1, 1, 2]` |
| `i = 4 (3)` | nums[2]=2 < 3 $\implies dp[4] = 1 + dp[2] = 2$ | dp: `[1, 1, 1, 2, 2]` |
| `i = 5 (7)` | nums[3]=5 < 7 $\implies dp[5] = 1 + dp[3] = 3$ | dp: `[1, 1, 1, 2, 2, 3]` |
| `i = 6 (101)` | nums[5]=7 < 101 $\implies dp[6] = 1 + dp[5] = 4$ | dp: `[1, 1, 1, 2, 2, 3, 4]` |
| `i = 7 (18)` | nums[5]=7 < 18 $\implies dp[7] = 1 + dp[5] = 4$ | dp: `[1, 1, 1, 2, 2, 3, 4, 4]` |
| `Result` | maxLIS = 4 (Subsequence `[2, 3, 7, 101]` or `[2, 5, 7, 18]`) | LIS Length = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Strictly decreasing array `[5, 4, 3, 2, 1]` (returns 1).
- Array with identical elements `[7, 7, 7]` (returns 1 for strictly increasing).
- $N = 1$ (returns 1).

### Common Bugs to Avoid
- Using `<=` instead of `<` for strictly increasing subsequence.
- Returning `dp[n-1]` instead of global maximum `maxLIS` (LIS might not end at the last index!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the 1D DP formulation allow easy extension to other problems like Divisible Subset and String Chain?**  
  **A**: Because the 1D formulation $\text{dp}[i] = \max(1 + \text{dp}[j])$ decouples the transition condition (`nums[j] < nums[i]`). By simply replacing the condition with `nums[i] % nums[j] == 0` (Largest Divisible Subset) or `isPredecessor(s[j], s[i])` (String Chain), the entire framework solves diverse problems effortlessly!

- **Q2: How does Patience Sorting achieve O(N log N) time?**  
  **A**: Maintain an array `tails` where `tails[i]` stores the smallest tail of all increasing subsequences of length $i + 1$. For each $x \in \text{nums}$, use `std::lower_bound` in $\mathcal{O}(\log N)$ to find and replace the first element $\ge x$ or push $x$ if $x$ is larger than all tails!

- **Q3: How to count the total number of Longest Increasing Subsequences (Problem 47)?**  
  **A**: Maintain two arrays: `dp[i]` for length, and `count[i]` for frequency. If a longer LIS is found, reset `count[i] = count[j]`; if an equal length LIS is found, accumulate `count[i] += count[j]`.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `LIS`, `Subsequences`, `LeetCode-300`, `Medium`
- **Related problems to practice next**:
- **Printing LIS**: Reconstruction parent array.
- **LIS with Binary Search**: O(N log N) Patience Sorting.
