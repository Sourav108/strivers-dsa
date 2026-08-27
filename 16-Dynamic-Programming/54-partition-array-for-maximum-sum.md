# Partition Array for Maximum Sum (Front Partition DP) (Step 16.7 — Matrix Chain Multiplication / Partition DP)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Partition Array for Maximum Sum (Front Partition DP)](https://takeuforward.org/data-structure/partition-array-for-maximum-sum-front-partition-dp-54/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `arr`, partition the array into (contiguous) subarrays of length at most `k`. After partitioning, each subarray has their values changed to become the maximum value of that subarray. Return the largest sum of the given array after partitioning.

---

## 1. Problem, Restated

Find the maximum transformed array sum by partitioning into contiguous subarrays of length $\le K$ using 1D Front Partition DP in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(N)$ or $\mathcal{O}(K)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Value / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Subarray Transformation Rule**: 
If we partition subarray `arr[i ... j]` of length `len = j - i + 1` (where `len <= k`), every element in this subarray becomes $\text{maxVal} = \max(\text{arr}[i \dots j])$. 
The total sum contributed by this single subarray is: 
$$\text{subarraySum} = \text{len} \times \text{maxVal}$$ 
**1D Front Partition Recurrence**: 
Let $\text{dp}[i]$ be the maximum partitioned sum for suffix `arr[i ... n - 1]`: 
$$\text{dp}[i] = \max_{1 \le \text{len} \le k \text{ and } i + \text{len} \le n} (\text{len} \times \text{maxVal} + \text{dp}[i + \text{len}])$$ 
Base Case: $\text{dp}[n] = 0$. 
Iterating backward from $n - 1$ down to 0 evaluates each of the $N$ states in at most $K$ steps, running in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(N)$ (or $\mathcal{O}(K)$) space.

- **Underlying Pattern**: `1D Front Partition DP with Running Subarray Maximum (`len <= K`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all partition lengths $1 \dots K$ at every index in $\mathcal{O}(K^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, const vector<int>& arr, int k) {
        if (i == arr.size()) return 0;
        int maxVal = 0, maxSum = 0;
        for (int j = i; j < min((int)arr.size(), i + k); j++) {
            maxVal = max(maxVal, arr[j]);
            int sum = (j - i + 1) * maxVal + solve(j + 1, arr, k);
            maxSum = max(maxSum, sum);
        }
        return maxSum;
    }
public:
    int maxSumAfterPartitioning(vector<int>& arr, int k) {
        return solve(0, arr, k);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: $K$-ary branching decision tree.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DP below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
1D Bottom-Up Front Partition DP in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxSumAfterPartitioning(vector<int>& arr, int k) {
        int n = arr.size();
        
        // dp[i] stores maximum partitioned sum for suffix arr[i ... n - 1]
        vector<int> dp(n + 1, 0);
        
        for (int i = n - 1; i >= 0; i--) {
            int maxVal = 0;
            int maxPartitionSum = 0;
            
            // Explore all valid partition lengths from 1 to k
            for (int len = 1; len <= k && i + len <= n; len++) {
                maxVal = max(maxVal, arr[i + len - 1]);
                
                int currentSum = len * maxVal + dp[i + len];
                maxPartitionSum = max(maxPartitionSum, currentSum);
            }
            
            dp[i] = maxPartitionSum;
        }
        
        return dp[0];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times K)$ time (outer loop runs $N$ times, inner loop at most $K$ times).
- **Space Complexity**: $\mathcal{O}(N)$ space (single 1D DP vector of size $N + 1$, can be compressed to $\mathcal{O}(K)$).
- **Why this is optimal**: Tracks running maximum in $\mathcal{O}(1)$ while relaxing states in linear $\mathcal{O}(K)$ per element.

---

## 6. Dry Run

`arr = [1, 15, 7, 9, 2, 5, 10]`, $k = 3, N = 7$

| Step | Action / State Change | Result |
|---|---|---|
| `i = 6 (val 10)` | len=1: $1 \times 10 + 0 = 10 \implies dp[6] = 10$ | dp[6] = 10 |
| `i = 5 (val 5)` | len=1: $5 + 10 = 15$; len=2: $2 \times 10 + 0 = 20 \implies dp[5] = 20$ | dp[5] = 20 |
| `i = 4 (val 2)` | len=1: $2+20=22$; len=2: $2\times 5+10=20$; len=3: $3\times 10+0=30 \implies dp[4] = 30$ | dp[4] = 30 |
| `i = 3 (val 9)` | len=1: $9+30=39$; len=2: $2\times 9+20=38$; len=3: $3\times 9+10=37 \implies dp[3] = 39$ | dp[3] = 39 |
| `i = 0 (val 1)` | Partitions `[1, 15, 7]` (sum $3\times 15 = 45$) + `[9]` ($1\times 9=9$) + `[2, 5, 10]` ($3\times 10=30$) | dp[0] = 84 |
| `Result` | Return dp[0] = 84 | Max Sum = 84 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $K = 1$ (returns sum of all elements without changes).
- $K \ge N$ (all elements become $N \times \max(arr)$).
- $N = 1$.

### Common Bugs to Avoid
- Recomputing `maxVal` in an inner loop from scratch instead of running `maxVal = max(maxVal, arr[i + len - 1])` in $\mathcal{O}(1)$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to optimize auxiliary space from O(N) to O(K)?**  
  **A**: Since $\text{dp}[i]$ only accesses states up to $\text{dp}[i + k]$, we can maintain a circular sliding array `dp[i % (k + 1)]` in $\mathcal{O}(K)$ auxiliary space!

- **Q2: Why is this pattern called 'Front Partition DP'?**  
  **A**: Because at each step, we decide the length of the 'front' contiguous segment starting at $i$, make a cut, and recurse on the remaining tail $i + len$. This template solves Word Break, Palindrome Partitioning, and Non-Overlapping Subarrays!

- **Q3: How to reconstruct the exact partition boundaries?**  
  **A**: Maintain `bestLen[i]` recording the optimal partition length chosen at index $i$. Trace $i = 0$: segment is `[i, i + bestLen[i] - 1]`, then advance $i += \text{bestLen}[i]$ in $\mathcal{O}(N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Partition DP`, `Front Partition`, `LeetCode-1043`, `Medium`
- **Related problems to practice next**:
- **Palindrome Partitioning II**: Front partition cuts.
- **Word Break**: Dictionary front partition.
