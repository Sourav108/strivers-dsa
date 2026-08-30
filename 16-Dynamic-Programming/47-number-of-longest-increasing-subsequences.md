# Number of Longest Increasing Subsequences (Step 16.6 — DP on LIS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Number of Longest Increasing Subsequences](https://takeuforward.org/data-structure/number-of-longest-increasing-subsequences-dp-47/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums`, return the number of longest increasing subsequences. Notice that the sequence has to be strictly increasing.

---

## 1. Problem, Restated

Find the count of all subsequences of maximum possible length using dual-array LIS Counting DP (`dp` and `cnt`) in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Length / Count / Cost result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Dual-State Formulation**: 
1) `dp[i]`: Length of the LIS ending at index $i$. 
2) `cnt[i]`: Total number of distinct LIS of length `dp[i]` ending at index $i$. 
**Transitions for each $j < i$ where `nums[j] < nums[i]`**: 
- **Case 1 (`1 + dp[j] > dp[i]`)**: A strictly LONGER subsequence is discovered! 
  - Update length: `dp[i] = 1 + dp[j]` 
  - Inherit count: `cnt[i] = cnt[j]` (reset count to predecessors of $j$) 
- **Case 2 (`1 + dp[j] == dp[i]`)**: An ALTERNATIVE path of the SAME maximum length is found! 
  - Accumulate count: `cnt[i] += cnt[j]` 
**Global Aggregation**: 
Find the global maximum length `maxLen = max(dp)`. Sum all `cnt[i]` where `dp[i] == maxLen` in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `1D LIS DP + Frequency Counter Tracking (`dp[i]` and `cnt[i]`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Generate all subsequences, find max length, and count occurrences in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) subsequence counting
};
```

### Java Code
```java
class SolutionNaive {
    // O(2^N) subsequence counting
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack.
- **Why it's not good enough**: Combinatorial enumeration.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DP below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
1D LIS Length and Frequency Counting DP in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int findNumberOfLIS(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        
        // dp[i] = length of LIS ending at index i
        // cnt[i] = number of LIS of length dp[i] ending at index i
        vector<int> dp(n, 1);
        vector<int> cnt(n, 1);
        
        int maxLen = 1;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    if (1 + dp[j] > dp[i]) {
                        // Found a strictly longer LIS: adopt length and reset count
                        dp[i] = 1 + dp[j];
                        cnt[i] = cnt[j];
                    } else if (1 + dp[j] == dp[i]) {
                        // Found an additional LIS of the same length: accumulate count
                        cnt[i] += cnt[j];
                    }
                }
            }
            maxLen = max(maxLen, dp[i]);
        }
        
        // Sum the counts of all subsequences achieving the maximum length
        int totalNumberOfLIS = 0;
        for (int i = 0; i < n; i++) {
            if (dp[i] == maxLen) {
                totalNumberOfLIS += cnt[i];
            }
        }
        
        return totalNumberOfLIS;
    }
};
```

### Java Code
```java
class Solution {

    int findNumberOfLIS(int[] nums) {
        int n = nums.length;
        if (n == 0) return 0;
        
        // dp[i] = length of LIS ending at index i
        // cnt[i] = number of LIS of length dp[i] ending at index i
        int[] dp = new int[n];
        int[] cnt = new int[n];
        
        int maxLen = 1;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    if (1 + dp[j] > dp[i]) {
                        // Found a strictly longer LIS: adopt length and reset count
                        dp[i] = 1 + dp[j];
                        cnt[i] = cnt[j];
                    } else if (1 + dp[j] == dp[i]) {
                        // Found an additional LIS of the same length: accumulate count
                        cnt[i] += cnt[j];
                    }
                }
            }
            maxLen = Math.max(maxLen, dp[i]);
        }
        
        // Sum the counts of all subsequences achieving the maximum length
        int totalNumberOfLIS = 0;
        for (int i = 0; i < n; i++) {
            if (dp[i] == maxLen) {
                totalNumberOfLIS += cnt[i];
            }
        }
        
        return totalNumberOfLIS;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (nested loops relaxing lengths and accumulating frequencies).
- **Space Complexity**: $\mathcal{O}(N)$ space (`dp` and `cnt` arrays of size $N$).
- **Why this is optimal**: Tracks both length and combinatorial count simultaneously in linear space.

---

## 6. Dry Run

`nums = [1, 3, 5, 4, 7]` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (1)` | dp[0]=1, cnt[0]=1 | maxLen = 1 |
| `i = 1 (3)` | dp[1]=2, cnt[1]=1 (`[1, 3]`) | maxLen = 2 |
| `i = 2 (5)` | dp[2]=3, cnt[2]=1 (`[1, 3, 5]`) | maxLen = 3 |
| `i = 3 (4)` | dp[3]=3, cnt[3]=1 (`[1, 3, 4]`) | maxLen = 3 |
| `i = 4 (7)` | From 5 (len 3): dp[4]=4, cnt[4]=1; From 4 (len 3): 1+3==4 $\implies cnt[4] += cnt[3] = 1+1=2$ | dp[4] = 4, cnt[4] = 2 |
| `Result` | maxLen = 4, total count = cnt[4] = 2 (`[1,3,5,7]` and `[1,3,4,7]`) | Number of LIS = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements identical `[2, 2, 2, 2]` (all LIS of length 1 $\implies$ returns $N = 4$).
- Strictly increasing array `[1, 2, 3]` (returns 1).
- $N = 1$ (returns 1).

### Common Bugs to Avoid
- Setting `cnt[i] += 1` instead of `cnt[i] += cnt[j]` (must propagate all branching paths from predecessor $j$!).
- Resetting `cnt[i] = 1` instead of `cnt[i] = cnt[j]` on longer path discovery.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST we inherit cnt[j] instead of adding 1 when a longer LIS is found?**  
  **A**: Because if index $j$ itself had 3 different ways to form its maximum LIS, extending with `nums[i]` immediately creates 3 distinct ways for index $i$! Adding 1 would lose all previous branching paths!

- **Q2: Can Number of LIS be solved in O(N log N) time?**  
  **A**: YES! Using a **Fenwick Tree (Binary Indexed Tree)** or **Segment Tree** where each tree node maintains a pair `{maxLen, count}`, we can query and update in $\mathcal{O}(N \log N)$ time!

- **Q3: What if nums contains 10^5 elements?**  
  **A**: For $N = 10^5$, $\mathcal{O}(N^2)$ TLEs. The Segment Tree / Fenwick Tree approach with coordinate compression is strictly required.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `LIS`, `Counting DP`, `LeetCode-673`, `Medium`
- **Related problems to practice next**:
- **Longest Increasing Subsequence**: Length subroutine.
- **Distinct Subsequences**: Counting pattern.
