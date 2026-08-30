# Frog Jump with K Distances (Step 16.1 — Introduction to DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Frog Jump with K Distances](https://takeuforward.org/data-structure/dynamic-programming-frog-jump-with-k-distances-dp-4/)
- **Difficulty**: Medium
- **Statement**: Given an array `height` of $N$ stairs and an integer $K$. A frog starts at index 0 and wants to reach index $N - 1$. The frog can jump at most $K$ steps forward (from $i$ to $i + j$ for $1 \le j \le K$). The energy lost in a jump from $i$ to $i + j$ is $|\text{height}[i] - \text{height}[i + j]|$. Find the minimum total energy lost to reach the last stair.

---

## 1. Problem, Restated

Find the minimum energy path from 0 to $n - 1$ where the frog can jump up to $K$ stairs forward in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(N)$ or $\mathcal{O}(K)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ or $\mathcal{O}(K)$ auxiliary space.

---

## 2. Intuition & Pattern

Generalization of Frog Jump from $K = 2$ to arbitrary $K$: 
To arrive at stair $i$, the frog could have jumped from any of the previous $K$ stairs ($i - 1, i - 2, \dots, i - K$). 
**Recurrence Relation**: 
$$\text{dp}[i] = \min_{1 \le j \le K \text{ and } i - j \ge 0} \left( \text{dp}[i - j] + |\text{height}[i] - \text{height}[i - j]| \right)$$ 
Base case: $\text{dp}[0] = 0$. 
We compute $\text{dp}[i]$ iteratively for all $i \in [1, n-1]$. Each step examines at most $K$ states, taking $\mathcal{O}(N \times K)$ time. 
Space can be maintained via an array of size $N$, or a circular buffer of size $K$ taking $\mathcal{O}(K)$ auxiliary space.

- **Underlying Pattern**: `1D DP / Multi-Branch Lookback Transition ($K$ previous states)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Branching recursion evaluating up to $K$ choices at every stair in $\mathcal{O}(K^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, const vector<int>& h, int k) {
        if (i == 0) return 0;
        int minSteps = 1e9;
        for (int j = 1; j <= k; j++) {
            if (i - j >= 0) {
                int jump = solve(i - j, h, k) + abs(h[i] - h[i - j]);
                minSteps = min(minSteps, jump);
            }
        }
        return minSteps;
    }
public:
    int minimizeCost(vector<int>& height, int n, int k) {
        return solve(n - 1, height, k);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int[] h, int k) {
        if (i == 0) return 0;
        int minSteps = 1e9;
        for (int j = 1; j <= k; j++) {
            if (i - j >= 0) {
                int jump = solve(i - j, h, k) + Math.abs(h[i] - h[i - j]);
                minSteps = Math.min(minSteps, jump);
            }
        }
        return minSteps;
    }

    int minimizeCost(int[] height, int n, int k) {
        return solve(n - 1, height, k);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: $K$-ary tree with exponential branching depth.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Top-Down Memoization in O(N x K) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <cmath>
#include <algorithm>
using namespace std;

class SolutionMemo {
    int memo(int i, const vector<int>& h, int k, vector<int>& dp) {
        if (i == 0) return 0;
        if (dp[i] != -1) return dp[i];
        int minCost = 1e9;
        for (int j = 1; j <= k; j++) {
            if (i - j >= 0) {
                int jump = memo(i - j, h, k, dp) + abs(h[i] - h[i - j]);
                minCost = min(minCost, jump);
            }
        }
        return dp[i] = minCost;
    }
public:
    int minimizeCost(vector<int>& height, int n, int k) {
        vector<int> dp(n, -1);
        return memo(n - 1, height, k, dp);
    }
};
```

### Java Code
```java
class SolutionMemo {
    int memo(int i, int[] h, int k, int[] dp) {
        if (i == 0) return 0;
        if (dp[i] != -1) return dp[i];
        int minCost = 1e9;
        for (int j = 1; j <= k; j++) {
            if (i - j >= 0) {
                int jump = memo(i - j, h, k, dp) + Math.abs(h[i] - h[i - j]);
                minCost = Math.min(minCost, jump);
            }
        }
        return dp[i] = minCost;
    }

    int minimizeCost(int[] height, int n, int k) {
        int[] dp = new int[n];
        return memo(n - 1, height, k, dp);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times K)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ memo array + call stack.
- **Why it's still not optimal**: Recursion stack overhead.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Bottom-Up Tabulation in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <cmath>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minimizeCost(vector<int>& height, int n, int k) {
        vector<int> dp(n, 1e9);
        dp[0] = 0; // Base case: 0 energy needed to stay at start
        
        for (int i = 1; i < n; i++) {
            // Check all valid jumps of size j from 1 to k
            for (int j = 1; j <= k; j++) {
                if (i - j >= 0) {
                    int jumpCost = dp[i - j] + abs(height[i] - height[i - j]);
                    dp[i] = min(dp[i], jumpCost);
                }
            }
        }
        
        return dp[n - 1];
    }
};
```

### Java Code
```java
class Solution {

    int minimizeCost(int[] height, int n, int k) {
        int[] dp = new int[n];
        dp[0] = 0; // Base case: 0 energy needed to stay at start
        
        for (int i = 1; i < n; i++) {
            // Check all valid jumps of size j from 1 to k
            for (int j = 1; j <= k; j++) {
                if (i - j >= 0) {
                    int jumpCost = dp[i - j] + Math.abs(height[i] - height[i - j]);
                    dp[i] = Math.min(dp[i], jumpCost);
                }
            }
        }
        
        return dp[n - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times K)$ time (outer loop runs $N$ times, inner loop runs at most $K$ times).
- **Space Complexity**: $\mathcal{O}(N)$ dp table space (can be optimized to $\mathcal{O}(K)$ using a fixed circular buffer).
- **Why this is optimal**: Tabulation computes each subproblem once iteratively with cache-contiguous array accesses.

---

## 6. Dry Run

`height = [10, 30, 40, 50, 20]`, $n = 5, k = 3$

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dp = `[0, INF, INF, INF, INF]` | Start at 0 |
| `i = 1 (h=30)` | j=1: dp[0]+|30-10| = 20 $\implies dp[1] = 20$ | dp[1] = 20 |
| `i = 2 (h=40)` | j=1: dp[1]+|40-30|=30; j=2: dp[0]+|40-10|=30 $\implies dp[2] = 30$ | dp[2] = 30 |
| `i = 3 (h=50)` | j=1: 30+|50-40|=40; j=2: 20+|50-30|=40; j=3: 0+|50-10|=40 $\implies dp[3] = 40$ | dp[3] = 40 |
| `i = 4 (h=20)` | j=1: 40+|20-50|=70; j=2: 30+|20-40|=50; j=3: 20+|20-30|=30 $\implies dp[4] = 30$ | dp[4] = 30 |
| `Result` | Return dp[4] = 30 (Jump $0 \to 1 \to 4$: $|10-30| + |30-20| = 30$) | Min Cost = 30 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $K \ge N$ (frog can jump directly from 0 to $N-1$ in 1 hop).
- $K = 1$ (linear chain, cost is sum of adjacent differences).
- $N = 1$ (returns 0).

### Common Bugs to Avoid
- Inner loop continuing when `i - j < 0` without boundary break/check.
- Initializing `dp[0]` to non-zero.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to optimize auxiliary space from O(N) to O(K)?**  
  **A**: Since state $\text{dp}[i]$ only needs the previous $K$ values, maintain a circular buffer/queue of size $K$: `dp[i % k]`. Space drops from $\mathcal{O}(N)$ to $\mathcal{O}(K)$! For $K \ll N$ (e.g. $K=5, N=10^6$), this saves megabytes of RAM!

- **Q2: Can this be optimized further if jump costs are independent of height differences (e.g. Sliding Window Maximum / LeetCode 1696)?**  
  **A**: If jump cost is decoupled or monotonic, we can use a **Monotonic Deque** (Decreasing Queue) to query the minimum in the sliding window of size $K$ in amortized $\mathcal{O}(1)$ time per step, reducing total time from $\mathcal{O}(N \times K)$ to $\mathcal{O}(N)$!

- **Q3: How to handle negative or directional costs?**  
  **A**: If edge costs could be negative and backward jumps were permitted, this would cease to be a DAG DP problem and would require **Bellman-Ford / SPFA** shortest path algorithms.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `1D DP`, `Tabulation`, `Memoization`, `Medium`
- **Related problems to practice next**:
- **Frog Jump**: K=2 base case.
- **Jump Game II**: Greedy jump reachability.
