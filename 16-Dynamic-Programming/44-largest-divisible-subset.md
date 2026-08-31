# Largest Divisible Subset (Step 16.6 — DP on LIS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Largest Divisible Subset](https://takeuforward.org/data-structure/largest-divisible-subset-dp-44/)
- **Difficulty**: Medium
- **Statement**: Given a set of **distinct** positive integers `nums`, return the largest subset `answer` such that every pair of elements `(answer[i], answer[j])` in this subset satisfies: `answer[i] % answer[j] == 0` or `answer[j] % answer[i] == 0`. If there are multiple solutions, return any of them.

---

## 1. Problem, Restated

Find the largest subset where every element divides every larger element using Sorting + 1D LIS DP + Parent Backtracking in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Length / Array result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Transitive Divisibility Property**: 
If we sort the array in ascending order ($a < b < c$): 
If $b \% a == 0$ and $c \% b == 0$, then by mathematical transitivity, $c \% a == 0$ is **AUTOMATICALLY GUARANTEED**! 
Therefore, in a sorted array, we only need to ensure that each new element is divisible by the **largest element chosen before it**! 
**Algorithm**: 
1) Sort `nums` in ascending order in $\mathcal{O}(N \log N)$. 
2) Apply 1D LIS DP with condition `nums[i] % nums[j] == 0`: 
   $$\text{dp}[i] = \max(\text{dp}[i], 1 + \text{dp}[j])$$ 
3) Track `parent[i] = j` and `lastIndex` for global maximum. 
4) Backtrack from `lastIndex` to reconstruct the subset in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `Sorted LIS Paradigm with Divisibility Transition (`nums[i] % nums[j] == 0`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^N$ subsets and check pairwise divisibility in $\mathcal{O}(N^2 \cdot 2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) subset search
};
```

### Java Code
```java
class SolutionNaive {
    // O(2^N) subset search
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \cdot 2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Combinatorial subset search.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DP below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sorting + 1D LIS Divisibility DP + Parent Reconstruction in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<int> largestDivisibleSubset(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return {};
        
        // Step 1: Sort array to enable transitive divisibility
        sort(nums.begin(), nums.end());
        
        vector<int> dp(n, 1);
        vector<int> parent(n);
        for (int i = 0; i < n; i++) parent[i] = i;
        
        int maxLen = 1;
        int lastIndex = 0;
        
        // Step 2: 1D LIS DP with divisibility check
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] % nums[j] == 0 && 1 + dp[j] > dp[i]) {
                    dp[i] = 1 + dp[j];
                    parent[i] = j;
                }
            }
            if (dp[i] > maxLen) {
                maxLen = dp[i];
                lastIndex = i;
            }
        }
        
        // Step 3: Reconstruct the divisible subset
        vector<int> result;
        result.push_back(nums[lastIndex]);
        
        while (parent[lastIndex] != lastIndex) {
            lastIndex = parent[lastIndex];
            result.push_back(nums[lastIndex]);
        }
        
        reverse(result.begin(), result.end());
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] largestDivisibleSubset(int[] nums) {
        int n = nums.length;
        if (n == 0) return {};
        
        // Step 1: Sort array to enable transitive divisibility
        Arrays.sort(nums);
        
        int[] dp = new int[n];
        int[] parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = i;
        
        int maxLen = 1;
        int lastIndex = 0;
        
        // Step 2: 1D LIS DP with divisibility check
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < i; j++) {
                if (nums[i] % nums[j] == 0 && 1 + dp[j] > dp[i]) {
                    dp[i] = 1 + dp[j];
                    parent[i] = j;
                }
            }
            if (dp[i] > maxLen) {
                maxLen = dp[i];
                lastIndex = i;
            }
        }
        
        // Step 3: Reconstruct the divisible subset
        int[] result;
        result.add(nums[lastIndex]);
        
        while (parent[lastIndex] != lastIndex) {
            lastIndex = parent[lastIndex];
            result.add(nums[lastIndex]);
        }
        
        reverse(result.begin(), result.end());
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (Sort: $\mathcal{O}(N \log N)$, DP: $\mathcal{O}(N^2)$, Backtrack: $\mathcal{O}(N)$).
- **Space Complexity**: $\mathcal{O}(N)$ space (`dp` and `parent` vectors of size $N$).
- **Why this is optimal**: Sorting converts pairwise divisibility into a linear LIS chain.

---

## 6. Dry Run

`nums = [1, 16, 7, 8, 4]`, Sorted: `[1, 4, 7, 8, 16]` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 (1)` | dp[0]=1, parent[0]=0 | maxLen = 1 |
| `i = 1 (4)` | 4 % 1 == 0 $\implies dp[1] = 2, parent[1] = 0$ | maxLen = 2 |
| `i = 2 (7)` | 7 % 1 == 0 $\implies dp[2] = 2, parent[2] = 0$ | maxLen = 2 |
| `i = 3 (8)` | 8 % 4 == 0 $\implies dp[3] = 1 + dp[1] = 3, parent[3] = 1$ | maxLen = 3 |
| `i = 4 (16)` | 16 % 8 == 0 $\implies dp[4] = 1 + dp[3] = 4, parent[4] = 3$ | maxLen = 4, lastIndex = 4 |
| `Backtrack` | Start 4 (16) $\to$ parent[4]=3 (8) $\to$ parent[3]=1 (4) $\to$ parent[1]=0 (1) $\to$ stop | subset = `[16, 8, 4, 1]` |
| `Result` | Reverse `[16, 8, 4, 1]` $\implies `[1, 4, 8, 16]` | Largest Divisible Subset = `[1, 4, 8, 16]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `[nums[0]]`).
- No divisibility beyond 1 (returns `[1, x]` or any 1 element).
- All elements are powers of 2.

### Common Bugs to Avoid
- Forgetting to sort the array initially (without sorting, divisibility is not transitive and LIS fails!).
- Using `nums[j] % nums[i] == 0` instead of `nums[i] % nums[j] == 0`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is SORTING the input array absolutely mandatory for this problem?**  
  **A**: Because divisibility on unsorted numbers is not directional! If $a$ divides $b$, and $b$ divides $c$, then $a$ divides $c$ only if $a \le b \le c$. Sorting enforces $nums[j] < nums[i]$, guaranteeing that if $nums[i] \% nums[j] == 0$, $nums[i]$ is divisible by ALL predecessors in $j$'s chain!

- **Q2: Can Largest Divisible Subset be solved in O(N log N) with Patience Sorting?**  
  **A**: No, because divisibility does not form a total ordering (numbers can branch in a divisibility poset / DAG). Patience sorting requires a strict total order (<), so $\mathcal{O}(N^2)$ DP is optimal!

- **Q3: What if nums contains duplicate elements?**  
  **A**: If duplicates exist, sorting places identical numbers adjacent. Since $x \% x == 0$, duplicates naturally extend the chain by $+1$.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `LIS`, `Math`, `Number Theory`, `LeetCode-368`, `Medium`
- **Related problems to practice next**:
- **Longest Increasing Subsequence**: Base LIS.
- **Printing LIS**: Parent backtracking.
