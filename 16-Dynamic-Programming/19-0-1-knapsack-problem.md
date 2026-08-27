# 0/1 Knapsack Problem (Bounded) (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [0/1 Knapsack Problem (Bounded)](https://takeuforward.org/data-structure/0-1-knapsack-dp-19/)
- **Difficulty**: Medium
- **Statement**: A thief is robbing a store and can carry a maximal weight of $W$ into their knapsack. There are $N$ items and the $i$-th item weighs `wt[i]` and is of value `val[i]`. Considering the constraints of the maximum capacity that the knapsack can hold, you have to find the maximum total value of items that the thief can steal. Each item can be picked at most once (0/1).

---

## 1. Problem, Restated

Maximize $\sum \text{val}[i]$ subject to $\sum \text{wt}[i] \le W$ where each item can be picked at most once using 1D Space-Optimized DP in $\mathcal{O}(N \times W)$ time and $\mathcal{O}(W)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Classical 0/1 Knapsack Recurrence**: 
At item $i$ with capacity $w$: 
1) **Not-Pick**: Do not take item $i$. Value is $\text{dp}[i - 1][w]$. 
2) **Pick** (if $w \ge \text{wt}[i]$): Take item $i$, gaining `val[i]`. Remaining capacity $w - \text{wt}[i]$ is filled from previous items: $\text{val}[i] + \text{dp}[i - 1][w - \text{wt}[i]]$. 
$$\text{dp}[i][w] = \max(\text{dp}[i - 1][w], \text{val}[i] + \text{dp}[i - 1][w - \text{wt}[i]])$$ 
**Space Optimization (Single Array Magic)**: 
Notice that $\text{dp}[i][w]$ only depends on states from the PREVIOUS row with SMALLER capacities ($w - \text{wt}[i] < w$). 
If we iterate $w$ **BACKWARD from $W$ down to $\text{wt}[i]$**, we read unchanged values from row $i - 1$ before overwriting them! This allows using a **SINGLE 1D array of size $W + 1$** without any auxiliary `cur` array in $\mathcal{O}(N \times W)$ time and $\mathcal{O}(W)$ space!

- **Underlying Pattern**: `0/1 Knapsack / Right-to-Left (Backward) 1D State Compression`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^N$ item inclusion subsets in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int w, const vector<int>& wt, const vector<int>& val) {
        if (i == 0) {
            if (wt[0] <= w) return val[0];
            return 0;
        }
        int notTake = solve(i - 1, w, wt, val);
        int take = 0;
        if (wt[i] <= w) take = val[i] + solve(i - 1, w - wt[i], wt, val);
        return max(notTake, take);
    }
public:
    int knapSack(int W, vector<int>& wt, vector<int>& val, int n) {
        return solve(n - 1, W, wt, val);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary decision tree evaluates redundant `(i, w)` capacity states exponentially.

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
    int knapSack(int W, vector<int>& wt, vector<int>& val, int n) {
        vector<vector<int>> dp(n, vector<int>(W + 1, 0));
        for (int w = wt[0]; w <= W; w++) dp[0][w] = val[0];
        
        for (int i = 1; i < n; i++) {
            for (int w = 0; w <= W; w++) {
                int notTake = dp[i - 1][w];
                int take = (w >= wt[i]) ? val[i] + dp[i - 1][w - wt[i]] : 0;
                dp[i][w] = max(notTake, take);
            }
        }
        return dp[n - 1][W];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times W)$ time.
- **Space Complexity**: $\mathcal{O}(N \times W)$ space.
- **Why it's still not optimal**: 2D table uses redundant memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Ultra-Optimal Single 1D Array Backward-Loop DP in $\mathcal{O}(N \times W)$ time and $\mathcal{O}(W)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    // Function to return max value that can be put in knapsack of capacity W.
    int knapSack(int W, vector<int>& wt, vector<int>& val, int n) {
        // Single 1D DP array of size W + 1
        vector<int> dp(W + 1, 0);
        
        // Base case: Item 0
        for (int w = wt[0]; w <= W; w++) {
            dp[w] = val[0];
        }
        
        // Iterate through remaining items
        for (int i = 1; i < n; i++) {
            // CRITICAL: Iterate capacity BACKWARDS from W down to wt[i]
            // This prevents an item from being included multiple times in the same step!
            for (int w = W; w >= wt[i]; w--) {
                dp[w] = max(dp[w], val[i] + dp[w - wt[i]]);
            }
        }
        
        return dp[W];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times W)$ time (each item relaxes capacities $\ge \text{wt}[i]$ in $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(W)$ auxiliary space (a single 1D vector of size $W + 1$).
- **Why this is optimal**: Backward iteration ensures that `dp[w - wt[i]]` represents the previous row's state, achieving true single-buffer compression.

---

## 6. Dry Run

`wt = [3, 2, 5]`, `val = [30, 40, 60]`, $W = 6$

| Step | Action / State Change | Result |
|---|---|---|
| `Item 0 (w=3, v=30)` | dp[3..6] = 30 $\implies dp = [0, 0, 0, 30, 30, 30, 30]$ | Item 0 ready |
| `Item 1 (w=2, v=40)` | w=6: max(30, 40+dp[4]=70)=70; w=5: max(30, 40+dp[3]=70)=70; w=4: max(30, 40+dp[2]=40)=40; w=3: max(30, 40+0)=40; w=2: 40 | dp = [0, 0, 40, 40, 40, 70, 70] |
| `Item 2 (w=5, v=60)` | w=6: max(70, 60+dp[1]=60)=70; w=5: max(70, 60+dp[0]=60)=70 | dp = [0, 0, 40, 40, 40, 70, 70] |
| `Result` | Return dp[6] = 70 (Items 0 and 1: $30 + 40 = 70$, weight $3 + 2 = 5 \le 6$) | Max Value = 70 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $W = 0$ (returns 0).
- Capacity $W < \min(\text{wt})$ (returns 0).
- Single item larger than capacity.

### Common Bugs to Avoid
- Iterating `w` forward ($0 \to W$) in single-array DP (turns 0/1 knapsack into Unbounded Knapsack by reusing the same item!).
- Forgetting to initialize base case for $w \ge \text{wt}[0]$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does iterating backward (W -> wt[i]) enforce the 0/1 constraint?**  
  **A**: When calculating `dp[w] = max(dp[w], val[i] + dp[w - wt[i]])`, the term `dp[w - wt[i]]` must come from the PREVIOUS item $(i-1)$. Because $w - \text{wt}[i] < w$, backward iteration accesses the lower capacity value BEFORE it has been overwritten for item $i$! Forward iteration would read the already-updated value of item $i$, allowing infinite copies (Unbounded Knapsack)!

- **Q2: What is Fractional Knapsack and why is it solved with Greedy instead of DP?**  
  **A**: In Fractional Knapsack, items can be cut into arbitrary fractions. We greedily pick items by highest value-to-weight density $\text{val}[i] / \text{wt}[i]$ in $\mathcal{O}(N \log N)$ time. In 0/1 Knapsack, items cannot be divided, so greedy density fails and DP is required!

- **Q3: How to handle Knapsack when W is huge ($W = 10^9$) but total value is small ($\sum V \le 10^5$)?**  
  **A**: We invert the DP state: $\text{dp}[i][v] = $ minimum weight required to achieve value $v$. Table size becomes $N \times \sum \text{val}[i]$ solved in $\mathcal{O}(N \cdot V)$ time!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `0-1 Knapsack`, `Space Optimization`, `Medium`
- **Related problems to practice next**:
- **Unbounded Knapsack**: Infinite supply variant.
- **Coin Change I**: Cost-minimization knapsack.
