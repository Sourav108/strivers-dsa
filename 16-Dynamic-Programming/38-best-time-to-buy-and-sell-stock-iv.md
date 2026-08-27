# Best Time to Buy and Sell Stock IV (At most K transactions) (Step 16.5 — DP on Stocks)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Best Time to Buy and Sell Stock IV (At most K transactions)](https://takeuforward.org/data-structure/buy-and-sell-stocks-iv-dp-38/)
- **Difficulty**: Hard
- **Statement**: You are given an integer array `prices` and an integer `k`. You may complete at most `k` transactions. Notice that you cannot engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again). Return the maximum profit you can achieve.

---

## 1. Problem, Restated

Find the maximum profit with at most $K$ transactions using Space-Optimized Stock DP in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(K)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Profit result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Generalization of Stock III**: 
When maximum transactions is a parameter $k$: 
1) **Edge Case Optimization ($k \ge n / 2$)**: 
   If $k \ge n / 2$, you can execute a transaction on every single price rise. This reduces to **Stock II (Unlimited Transactions)** solved in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space! 
2) **General $K$ DP Formulation**: 
   State: $\text{dp}[\text{buy}][\text{cap}]$ for $\text{cap} \in [0, k]$. 
   - `cur[1][cap] = max(-prices[i] + ahead[0][cap], ahead[1][cap])` 
   - `cur[0][cap] = max(+prices[i] + ahead[1][cap - 1], ahead[0][cap])` 
**Space Optimization**: A $2 \times (K + 1)$ 2D matrix `ahead` representing the previous day in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(K)$ space.

- **Underlying Pattern**: `Multi-Transaction Stock DP with Dynamic Capacity $K$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursive tree evaluating all $K$ transactions in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) recursion
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Combinatorial recursion tree.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
3D Tabulation DP table of size N x 2 x (K + 1) in O(N x K) time and O(N x K) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution3D {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        if (n <= 1 || k == 0) return 0;
        vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(2, vector<int>(k + 1, 0)));
        for (int i = n - 1; i >= 0; i--) {
            for (int buy = 0; buy <= 1; buy++) {
                for (int cap = 1; cap <= k; cap++) {
                    if (buy) dp[i][buy][cap] = max(-prices[i] + dp[i + 1][0][cap], dp[i + 1][1][cap]);
                    else dp[i][buy][cap] = max(+prices[i] + dp[i + 1][1][cap - 1], dp[i + 1][0][cap]);
                }
            }
        }
        return dp[0][1][k];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times K)$ time.
- **Space Complexity**: $\mathcal{O}(N \times K)$ space.
- **Why it's still not optimal**: Full 3D table uses redundant day slices.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized $2 \times (K + 1)$ Array DP with $K \ge N/2$ Greedy Bypass in $\mathcal{O}(N \times K)$ time and $\mathcal{O}(K)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxProfit(int k, vector<int>& prices) {
        int n = prices.size();
        if (n <= 1 || k == 0) return 0;
        
        // Optimization: If k >= n / 2, equivalent to unlimited transactions
        if (k >= n / 2) {
            int maxP = 0;
            for (int i = 1; i < n; i++) {
                if (prices[i] > prices[i - 1]) {
                    maxP += prices[i] - prices[i - 1];
                }
            }
            return maxP;
        }
        
        // ahead[buy][cap] stores max profit from day (i + 1)
        vector<vector<int>> ahead(2, vector<int>(k + 1, 0));
        
        for (int i = n - 1; i >= 0; i--) {
            vector<vector<int>> cur(2, vector<int>(k + 1, 0));
            
            for (int buy = 0; buy <= 1; buy++) {
                for (int cap = 1; cap <= k; cap++) {
                    if (buy == 1) {
                        cur[buy][cap] = max(-prices[i] + ahead[0][cap], ahead[1][cap]);
                    } else {
                        cur[buy][cap] = max(+prices[i] + ahead[1][cap - 1], ahead[0][cap]);
                    }
                }
            }
            
            ahead = cur;
        }
        
        return ahead[1][k];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times K)$ time (or $\mathcal{O}(N)$ when $K \ge N/2$).
- **Space Complexity**: $\mathcal{O}(K)$ space (two 2D vectors of size $2 \times (K + 1)$).
- **Why this is optimal**: Dynamic capacity tracking ensures optimal memory bound $\mathcal{O}(K)$.

---

## 6. Dry Run

`k = 2`, `prices = [2, 4, 1]` ($N = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 2 (p=1)` | aheadBuy = 0, aheadNotBuy = 1 | Init |
| `i = 1 (p=4)` | curBuy = max(-4+1, 0) = 0; curNotBuy = max(4+0, 1) = 4 | aheadNotBuy = 4 |
| `i = 0 (p=2)` | curBuy = max(-2+4, 0) = 2 | aheadBuy = 2 |
| `Result` | Return 2 (Buy at 2, Sell at 4 $\implies$ Profit = 2) | Max Profit = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 0$ (returns 0).
- $k \ge n / 2$ (unlimited transaction bypass triggered).
- $N = 1$ (returns 0).

### Common Bugs to Avoid
- Not checking $k \ge n / 2$ (can cause Memory Limit Exceeded if $k = 10^9$).
- Using 1-based indexing incorrectly for capacity.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is k >= n / 2 equivalent to unlimited transactions?**  
  **A**: Because in an array of size $N$, the maximum number of non-overlapping transactions you can possibly execute is $\lfloor N / 2 \rfloor$ (each transaction requires at least 1 buy day and 1 sell day). If $k \ge N / 2$, the constraint $k$ is NEVER binding!

- **Q2: What is the Transaction States Alternative Formulation (2K states)?**  
  **A**: Instead of `buy` and `cap`, track a single state variable `tranNo` $\in [0, 2k - 1]$. If `tranNo % 2 == 0`, it's a Buy; if `tranNo % 2 != 0`, it's a Sell!

- **Q3: How to reconstruct the exact buy and sell days for all K transactions?**  
  **A**: Backtrack the full 3D DP table: whenever `dp[i][buy][cap] == -prices[i] + dp[i+1][0][cap]`, day $i$ was a Buy day; whenever `+prices[i] + dp[i+1][1][cap-1]`, day $i$ was a Sell day!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Stocks`, `FSM`, `LeetCode-188`, `Hard`
- **Related problems to practice next**:
- **Stock III**: K=2 special case.
- **Stock II**: K=infinity counterpart.
