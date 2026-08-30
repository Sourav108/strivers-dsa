# Longest Bitonic Subsequence (Step 16.6 — DP on LIS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest Bitonic Subsequence](https://takeuforward.org/data-structure/longest-bitonic-subsequence-dp-46/)
- **Difficulty**: Medium
- **Statement**: A bitonic subsequence is a subsequence of an array in which the elements are first strictly increasing, and after reaching a peak, they are strictly decreasing. Given an integer array `nums` of length $N$, find the length of the longest bitonic subsequence.

---

## 1. Problem, Restated

Find the maximum $(dp_1[i] + dp_2[i] - 1)$ across all peak pivot elements $i$ where $dp_1$ is LIS from left-to-right and $dp_2$ is LDS from right-to-left in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Length / Count / Cost result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Peak Pivot Invariant**: 
In a bitonic sequence, there is a peak element at index $i$ such that: 
1) Elements to the left of $i$ form an **Increasing Subsequence** ending at $i$. Length $= \text{dp}_1[i]$. 
2) Elements to the right of $i$ form a **Decreasing Subsequence** starting at $i$ (which is an Increasing Subsequence ending at $i$ when traversed from right to left!). Length $= \text{dp}_2[i]$. 
**Master Formula**: 
At peak index $i$, element `nums[i]` is counted in both $\text{dp}_1[i]$ and $\text{dp}_2[i]$. To avoid double-counting the peak: 
$$\text{Bitonic Length}(i) = \text{dp}_1[i] + \text{dp}_2[i] - 1$$ 
**Algorithm**: 
1) Compute $\text{dp}_1[i]$ (standard LIS from $0 \dots N-1$). 
2) Compute $\text{dp}_2[i]$ (standard LIS from $N-1 \dots 0$). 
3) Find $\max_{0 \le i < N}(\text{dp}_1[i] + \text{dp}_2[i] - 1)$ (strictly bitonic requires $\text{dp}_1[i] > 1$ and $\text{dp}_2[i] > 1$ if pure monotonic sequences are disqualified). Runs in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `Dual LIS State Combination / Forward LIS + Backward LDS Prefix-Suffix Merging`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
For every pivot $i$, recursively compute LIS on prefix and LDS on suffix in $\mathcal{O}(N \cdot 2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(N * 2^N) brute force
};
```

### Java Code
```java
class SolutionNaive {
    // O(N * 2^N) brute force
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack.
- **Why it's not good enough**: Exponential duplicate branch exploration.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DP below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Dual-Pass Prefix LIS + Suffix LDS in $\mathcal{O}(N^2)$ time (or $\mathcal{O}(N \log N)$ with Binary Search) and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int LongestBitonicSequence(int n, vector<int>& nums) {
        if (n <= 1) return n;
        
        // dp1[i] stores length of LIS ending at index i (left to right)
        vector<int> dp1(n, 1);
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i] && 1 + dp1[j] > dp1[i]) {
                    dp1[i] = 1 + dp1[j];
                }
            }
        }
        
        // dp2[i] stores length of LDS starting at index i (right to left)
        vector<int> dp2(n, 1);
        for (int i = n - 1; i >= 0; i--) {
            for (int j = n - 1; j > i; j--) {
                if (nums[j] < nums[i] && 1 + dp2[j] > dp2[i]) {
                    dp2[i] = 1 + dp2[j];
                }
            }
        }
        
        int maxBitonic = 0;
        for (int i = 0; i < n; i++) {
            // Optional: If strictly bitonic requires both increasing and decreasing parts:
            // if (dp1[i] > 1 && dp2[i] > 1)
            maxBitonic = max(maxBitonic, dp1[i] + dp2[i] - 1);
        }
        
        return maxBitonic;
    }
};
```

### Java Code
```java
class Solution {

    int LongestBitonicSequence(int n, int[] nums) {
        if (n <= 1) return n;
        
        // dp1[i] stores length of LIS ending at index i (left to right)
        int[] dp1 = new int[n];
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i] && 1 + dp1[j] > dp1[i]) {
                    dp1[i] = 1 + dp1[j];
                }
            }
        }
        
        // dp2[i] stores length of LDS starting at index i (right to left)
        int[] dp2 = new int[n];
        for (int i = n - 1; i >= 0; i--) {
            for (int j = n - 1; j > i; j--) {
                if (nums[j] < nums[i] && 1 + dp2[j] > dp2[i]) {
                    dp2[i] = 1 + dp2[j];
                }
            }
        }
        
        int maxBitonic = 0;
        for (int i = 0; i < n; i++) {
            // Optional: If strictly bitonic requires both increasing and decreasing parts:
            // if (dp1[i] > 1 && dp2[i] > 1)
            maxBitonic = Math.max(maxBitonic, dp1[i] + dp2[i] - 1);
        }
        
        return maxBitonic;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (two passes of 1D LIS DP).
- **Space Complexity**: $\mathcal{O}(N)$ space (two 1D vectors `dp1` and `dp2`).
- **Why this is optimal**: Merging forward and backward DP arrays evaluates every possible peak pivot in $\mathcal{O}(1)$ time per element.

---

## 6. Dry Run

`nums = [1, 11, 2, 10, 4, 5, 2, 1]` ($N = 8$)

| Step | Action / State Change | Result |
|---|---|---|
| `Forward LIS (dp1)` | dp1 = `[1, 2, 2, 3, 3, 4, 2, 1]` | LIS computed |
| `Backward LDS (dp2)` | dp2 = `[1, 5, 2, 4, 3, 3, 2, 1]` | LDS computed |
| `Peak i=1 (val 11)` | $dp_1[1] + dp_2[1] - 1 = 2 + 5 - 1 = 6$ (`[1, 11, 10, 5, 2, 1]`) | Length 6 |
| `Peak i=3 (val 10)` | $dp_1[3] + dp_2[3] - 1 = 3 + 4 - 1 = 6$ (`[1, 2, 10, 4, 2, 1]`) | Length 6 |
| `Peak i=5 (val 5)` | $dp_1[5] + dp_2[5] - 1 = 4 + 3 - 1 = 6$ (`[1, 2, 4, 5, 2, 1]`) | Length 6 |
| `Result` | Return maxBitonic = 6 | Longest Bitonic = 6 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Monotonically increasing array (returns $N$).
- Monotonically decreasing array (returns $N$).
- $N = 1$ (returns 1).

### Common Bugs to Avoid
- Double-counting peak element (forgetting $- 1$ in formula).
- Loop bounds in backward pass (`j > i` vs `j < i`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What if the problem requires STRICTLY bitonic (must have at least 1 increase AND 1 decrease)?**  
  **A**: Add condition: `if (dp1[i] > 1 && dp2[i] > 1) maxBitonic = max(maxBitonic, dp1[i] + dp2[i] - 1);`. If no such peak exists, return 0 (e.g. LeetCode 1671 Minimum Number of Removals to Make Mountain Array)!

- **Q2: Can Longest Bitonic Subsequence be solved in O(N log N) time?**  
  **A**: YES! Compute `dp1` using Patience Sorting binary search left-to-right, and `dp2` using binary search right-to-left in $\mathcal{O}(N \log N)$ time!

- **Q3: How to print the actual bitonic subsequence?**  
  **A**: Maintain parent arrays `parent1[i]` and `parent2[i]`. Backtrack left from peak $i$, backtrack right from peak $i$, and concatenate the two halves!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `LIS`, `Bitonic`, `Prefix-Suffix`, `Medium`
- **Related problems to practice next**:
- **Longest Increasing Subsequence**: Subroutine.
- **Mountain Array Removals**: Strict mountain variant.
