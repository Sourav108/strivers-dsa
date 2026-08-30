# Printing Longest Increasing Subsequence (Step 16.6 — DP on LIS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Printing Longest Increasing Subsequence](https://takeuforward.org/data-structure/printing-longest-increasing-subsequence-dp-42/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums` of size $N$, find and return the actual elements of the Longest Increasing Subsequence. If there are multiple valid LIS of maximum length, return any one of them.

---

## 1. Problem, Restated

Reconstruct and return the exact elements forming the Longest Increasing Subsequence using 1D DP and a `hash` parent backtrack array in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Length / Array result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Reconstruction via Predecessor Pointers**: 
To reconstruct the actual elements: 
1) Maintain `dp[i]` storing the LIS length ending at $i$. 
2) Maintain an auxiliary array `parent[i]` (or `hash[i]`) initialized to `parent[i] = i`. 
3) Whenever `nums[j] < nums[i]` and `1 + dp[j] > dp[i]`: 
   - Update length: `dp[i] = 1 + dp[j]` 
   - Record predecessor: `parent[i] = j` 
4) Track `lastIndex` corresponding to the global maximum `maxLIS`. 
5) **Backtrack**: Starting at `lastIndex`, repeatedly push `nums[lastIndex]` and jump `lastIndex = parent[lastIndex]` until `parent[lastIndex] == lastIndex`. Reverse the collected array! Runs in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `1D LIS DP + Parent Tracking Hash Array (`hash[i] = j`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively generate all $2^N$ increasing subsequences and select the longest in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) subsequence search
};
```

### Java Code
```java
class SolutionNaive {
    // O(2^N) subsequence search
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack.
- **Why it's not good enough**: Combinatorial subsequence generation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DP below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
1D DP with Parent Predecessor Array in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<int> printingLongestIncreasingSubsequence(vector<int>& nums, int n) {
        vector<int> dp(n, 1);
        vector<int> parent(n);
        
        // Initialize each node as its own parent
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
        
        int maxLIS = 1;
        int lastIndex = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i] && 1 + dp[j] > dp[i]) {
                    dp[i] = 1 + dp[j];
                    parent[i] = j; // Record predecessor index
                }
            }
            if (dp[i] > maxLIS) {
                maxLIS = dp[i];
                lastIndex = i;
            }
        }
        
        // Backtrack to reconstruct the LIS array
        vector<int> lis;
        lis.push_back(nums[lastIndex]);
        
        while (parent[lastIndex] != lastIndex) {
            lastIndex = parent[lastIndex];
            lis.push_back(nums[lastIndex]);
        }
        
        // Reverse because elements were collected backwards
        reverse(lis.begin(), lis.end());
        return lis;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] printingLongestIncreasingSubsequence(int[] nums, int n) {
        int[] dp = new int[n];
        int[] parent = new int[n];
        
        // Initialize each node as its own parent
        for (int i = 0; i < n; i++) {
            parent[i] = i;
        }
        
        int maxLIS = 1;
        int lastIndex = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i] && 1 + dp[j] > dp[i]) {
                    dp[i] = 1 + dp[j];
                    parent[i] = j; // Record predecessor index
                }
            }
            if (dp[i] > maxLIS) {
                maxLIS = dp[i];
                lastIndex = i;
            }
        }
        
        // Backtrack to reconstruct the LIS array
        List<Integer> lis = new ArrayList<>();
        lis.add(nums[lastIndex]);
        
        while (parent[lastIndex] != lastIndex) {
            lastIndex = parent[lastIndex];
            lis.add(nums[lastIndex]);
        }
        
        // Reverse because elements were collected backwards
        reverse(lis.begin(), lis.end());
        return lis;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (DP fill $\mathcal{O}(N^2)$, backtracking path $\mathcal{O}(N)$).
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary space (`dp` and `parent` arrays of size $N$).
- **Why this is optimal**: Predecessor pointers allow direct linear extraction of the optimal subsequence.

---

## 6. Dry Run

`nums = [5, 4, 11, 1, 16, 8]` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (5)` | dp[0]=1, parent[0]=0 | maxLIS = 1 |
| `i = 1 (4)` | dp[1]=1, parent[1]=1 | maxLIS = 1 |
| `i = 2 (11)` | j=0 (5<11): dp[2]=2, parent[2]=0; j=1 (4<11): dp[2]=2, parent[2]=1 | maxLIS = 2 |
| `i = 4 (16)` | j=2 (11<16): dp[4]=1+dp[2]=3, parent[4]=2 | maxLIS = 3, lastIndex = 4 |
| `Backtrack` | Start 4 (val 16) $\to$ parent[4]=2 (val 11) $\to$ parent[2]=1 (val 4) $\to$ parent[1]=1 (stop!) | lis = `[16, 11, 4]` |
| `Result` | Reverse `[16, 11, 4]` $\implies `[4, 11, 16]` | LIS = `[4, 11, 16]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `[nums[0]]`).
- Strictly descending array `[9, 8, 7]` (returns `[9]` or any single element).
- Multiple valid LIS paths.

### Common Bugs to Avoid
- Forgetting string/array reversal at the end of backtracking.
- Updating `parent[i]` even when `1 + dp[j] == dp[i]` without strict increase.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can we reconstruct the LIS in O(N log N) time?**  
  **A**: YES! By storing the predecessor index alongside each element in the `tails` array during Patience Sorting, we can reconstruct the LIS in $\mathcal{O}(N \log N)$ time!

- **Q2: Why does parent[i] = i serve as an ideal termination condition?**  
  **A**: Because the starting element of the LIS has no predecessor and points to itself (`parent[start] == start`), providing a clean `while(parent[curr] != curr)` loop termination without sentinel `-1` handling!

- **Q3: How does this pattern extend to Largest Divisible Subset (Problem 44)?**  
  **A**: Exact same parent backtracking algorithm! Sort the array, use divisibility condition `nums[i] % nums[j] == 0`, and backtrack from `lastIndex`.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `LIS`, `Backtracking`, `Subsequences`, `Medium`
- **Related problems to practice next**:
- **Longest Increasing Subsequence**: Length calculation.
- **Largest Divisible Subset**: Divisibility variant.
