# Unbounded Knapsack Problem (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Unbounded Knapsack Problem](https://takeuforward.org/data-structure/unbounded-knapsack-dp-23/)
- **Difficulty**: Medium
- **Statement**: Given a knapsack weight $W$ and two arrays `val` and `wt` of size $N$ representing the values and weights of $N$ items. Find the maximum value that can be put in a knapsack of capacity $W$. Each item can be used any number of times (infinite supply).

---

## 1. Problem, Restated

Maximize $\sum \text{val}[i]$ subject to $\sum \text{wt}[i] \le W$ with infinite item repetitions using 1D Forward DP in $\mathcal{O}(N \times W)$ time and $\mathcal{O}(W)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Unbounded Knapsack State Equation**: 
At item $i$ and capacity $w$: 
1) **Not-Pick**: $\text{dp}[i - 1][w]$ (use previous items only). 
2) **Pick** (if $w \ge \text{wt}[i]$): $\text{val}[i] + \text{dp}[i][w - \text{wt}[i]]$ (same row $i$, allowing item $i$ to be picked again!). 
$$\text{dp}[i][w] = \max(\text{dp}[i - 1][w], \text{val}[i] + \text{dp}[i][w - \text{wt}[i]])$$ 
**Single-Array 1D Space Optimization**: 
In 0/1 Knapsack, we looped BACKWARD ($W \to \text{wt}[i]$) to access row $i-1$. 
In **Unbounded Knapsack**, because picking item $i$ depends on the UPDATED state of row $i$, we loop **FORWARD from $\text{wt}[i]$ to $W$**: 
$$\text{dp}[w] = \max(\text{dp}[w], \text{val}[i] + \text{dp}[w - \text{wt}[i]])$$ 
Runs in $\mathcal{O}(N \times W)$ time and $\mathcal{O}(W)$ space.

- **Underlying Pattern**: `Unbounded Knapsack / 1D Forward Capacity Relaxation`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all combinations with repetition in $\mathcal{O}(2^W)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int w, const vector<int>& wt, const vector<int>& val) {
        if (i == 0) return (w / wt[0]) * val[0];
        int notTake = solve(i - 1, w, wt, val);
        int take = 0;
        if (wt[i] <= w) take = val[i] + solve(i, w - wt[i], wt, val);
        return max(notTake, take);
    }
public:
    int knapSack(int N, int W, vector<int>& val, vector<int>& wt) {
        return solve(N - 1, W, wt, val);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^W)$ time.
- **Space Complexity**: $\mathcal{O}(W)$ recursion stack.
- **Why it's not good enough**: Combinatorial branching tree with infinite repetitions.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x W) time and O(N x W) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int knapSack(int N, int W, vector<int>& val, vector<int>& wt) {
        vector<vector<int>> dp(N, vector<int>(W + 1, 0));
        for (int w = 0; w <= W; w++) dp[0][w] = (w / wt[0]) * val[0];
        
        for (int i = 1; i < N; i++) {
            for (int w = 0; w <= W; w++) {
                int notTake = dp[i - 1][w];
                int take = (w >= wt[i]) ? val[i] + dp[i][w - wt[i]] : 0;
                dp[i][w] = max(notTake, take);
            }
        }
        return dp[N - 1][W];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times W)$ time.
- **Space Complexity**: $\mathcal{O}(N \times W)$ space.
- **Why it's still not optimal**: 2D matrix is redundant.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Single-Vector Forward DP in $\mathcal{O}(N \times W)$ time and $\mathcal{O}(W)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int knapSack(int N, int W, vector<int>& val, vector<int>& wt) {
        // dp[w] stores max value achievable with knapsack capacity w
        vector<int> dp(W + 1, 0);
        
        // Base case: Item 0 can be taken multiple times
        for (int w = wt[0]; w <= W; w++) {
            dp[w] = (w / wt[0]) * val[0];
        }
        
        // Iterate through remaining items
        for (int i = 1; i < N; i++) {
            // FORWARD iteration from wt[i] to W allows unbounded reuse!
            for (int w = wt[i]; w <= W; w++) {
                dp[w] = max(dp[w], val[i] + dp[w - wt[i]]);
            }
        }
        
        return dp[W];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times W)$ time (each item relaxes all capacities in linear time).
- **Space Complexity**: $\mathcal{O}(W)$ auxiliary space (a single 1D vector of size $W + 1$).
- **Why this is optimal**: Forward iteration directly accesses the same item's updated state with zero extra array overhead.

---

## 6. Dry Run

`wt = [2, 4, 6]`, `val = [5, 11, 13]`, $W = 10$

| Step | Action / State Change | Result |
|---|---|---|
| `Item 0 (w=2, v=5)` | dp[w] = $(w/2) \times 5 \implies dp[10] = 25$ | dp = [0, 0, 5, 5, 10, 10, 15, 15, 20, 20, 25] |
| `Item 1 (w=4, v=11)` | w=4: max(10, 11+0)=11; w=8: max(20, 11+dp[4]=22)=22; w=10: max(25, 11+dp[6]=26)=26 | dp[10] = 26 (two wt 4 + one wt 2 = 11+11+5 = 27? w=10: 11+dp[6]=11+16=27) |
| `Item 2 (w=6, v=13)` | No improvements over item 1 combinations | dp[10] = 27 |
| `Result` | Return dp[10] = 27 (Items: $4 + 4 + 2$, value: $11 + 11 + 5 = 27$) | Max Value = 27 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $W = 0$ (returns 0).
- All weights $> W$ (returns 0).
- Single item can fill entire capacity.

### Common Bugs to Avoid
- Iterating backward (which turns it into 0/1 Knapsack!).
- Using integer division `w / wt[i]` inside the inner loop (double counting).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the fundamental difference in 1D array loops between 0/1 Knapsack and Unbounded Knapsack?**  
  **A**: 1) **0/1 Knapsack**: Inner loop runs **BACKWARD ($W \to \text{wt}[i]$)** so `dp[w - wt[i]]` reads the PREVIOUS item's value without duplication. 2) **Unbounded Knapsack**: Inner loop runs **FORWARD ($\text{wt}[i] \to W$)** so `dp[w - wt[i]]` reads the CURRENT item's updated value, allowing infinite item repetitions!

- **Q2: How does Rod Cutting (Problem 24) relate to Unbounded Knapsack?**  
  **A**: Rod Cutting is an exact Unbounded Knapsack where item lengths $1 \dots N$ correspond to weights `wt[i] = i`, and rod length $N$ corresponds to capacity $W = N$!

- **Q3: Can Greedy work for Unbounded Knapsack?**  
  **A**: No. Consider `wt = [5, 6]`, `val = [10, 13]`, $W = 11$. Densities are $10/5 = 2.0$ and $13/6 = 2.16$. Greedy picks wt 6, leaving remaining capacity 5 with value $13 + 10 = 23$. But picking two wt 5 gives $10 + 10 = 20$? No, what if $W = 10$? Greedy picks wt 6 (leaving 4, unusable $\implies 13$), but DP picks $5 + 5 = 20$!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `Unbounded Knapsack`, `Optimization`, `Medium`
- **Related problems to practice next**:
- **0/1 Knapsack**: Single-use counterpart.
- **Rod Cutting Problem**: Direct application.
