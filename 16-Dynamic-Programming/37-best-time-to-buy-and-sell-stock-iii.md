# Best Time to Buy and Sell Stock III (At most 2 transactions) (Step 16.5 — DP on Stocks)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Best Time to Buy and Sell Stock III (At most 2 transactions)](https://takeuforward.org/data-structure/buy-and-sell-stock-iii-dp-37/)
- **Difficulty**: Hard
- **Statement**: You are given an array `prices` where `prices[i]` is the price of a given stock on the $i$-th day. Find the maximum profit you can achieve. You may complete at most **two transactions**. Note: You may not engage in multiple transactions simultaneously (i.e., you must sell the stock before you buy again).

---

## 1. Problem, Restated

Maximize profit with at most 2 transactions using 3D DP $\text{dp}[i][\text{buy}][\text{cap}]$ space-optimized to $\mathcal{O}(1)$ space in $\mathcal{O}(N)$ time.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Profit result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**State Representation**: 
We add a capacity parameter `cap` $\in \{0, 1, 2\}$ representing the remaining allowed transactions. A transaction completes upon selling the stock! 
**Transitions**: 
1) **If `buy == 1`**: 
   $$\text{dp}[i][1][\text{cap}] = \max(-\text{prices}[i] + \text{dp}[i + 1][0][\text{cap}], \text{dp}[i + 1][1][\text{cap}])$$ 
2) **If `buy == 0`**: 
   $$\text{dp}[i][0][\text{cap}] = \max(+\text{prices}[i] + \text{dp}[i + 1][1][\text{cap} - 1], \text{dp}[i + 1][0][\text{cap}])$$ 
**Base Cases**: If $i == n$ or $\text{cap} == 0$, profit is 0. 
**Space Optimization**: 
- Approach A: Maintain a $2 \times 3$ 2D matrix `ahead[2][3]` representing the next day in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space. 
- Approach B (4-Variable Pipeline): Track `buy1`, `profit1`, `buy2`, `profit2` in a single forward pass.

- **Underlying Pattern**: `3D State Space $\text{dp}[i][\text{buy}][\text{cap}]$ / 4-Variable Transaction Pipeline`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively branch on buy, sell, and transaction count in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) recursion
};
```

### Java Code
```java
class SolutionNaive {
    // O(2^N) recursion
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Branching with capacity parameter.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
3D Tabulation DP table of size N x 2 x 3 in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution3D {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<vector<int>>> dp(n + 1, vector<vector<int>>(2, vector<int>(3, 0)));
        
        for (int i = n - 1; i >= 0; i--) {
            for (int buy = 0; buy <= 1; buy++) {
                for (int cap = 1; cap <= 2; cap++) {
                    if (buy) {
                        dp[i][buy][cap] = max(-prices[i] + dp[i + 1][0][cap], dp[i + 1][1][cap]);
                    } else {
                        dp[i][buy][cap] = max(+prices[i] + dp[i + 1][1][cap - 1], dp[i + 1][0][cap]);
                    }
                }
            }
        }
        return dp[0][1][2];
    }
};
```

### Java Code
```java
class Solution3D {

    int maxProfit(int[] prices) {
        int n = prices.length;
        int[][][] dp = new int[n + 1][2][3];
        
        for (int i = n - 1; i >= 0; i--) {
            for (int buy = 0; buy <= 1; buy++) {
                for (int cap = 1; cap <= 2; cap++) {
                    if (buy) {
                        dp[i][buy][cap] = Math.max(-prices[i] + dp[i + 1][0][cap], dp[i + 1][1][cap]);
                    } else {
                        dp[i][buy][cap] = Math.max(+prices[i] + dp[i + 1][1][cap - 1], dp[i + 1][0][cap]);
                    }
                }
            }
        }
        return dp[0][1][2];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: Full 3D table allocates redundant day slices.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized $2 \times 3$ Buffer / 4-Variable State Machine in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        
        // ahead[buy][cap] stores max profit from day (i + 1)
        vector<vector<int>> ahead(2, vector<int>(3, 0));
        
        for (int i = n - 1; i >= 0; i--) {
            vector<vector<int>> cur(2, vector<int>(3, 0));
            
            for (int buy = 0; buy <= 1; buy++) {
                for (int cap = 1; cap <= 2; cap++) {
                    if (buy == 1) {
                        // Buy or Skip
                        cur[buy][cap] = max(-prices[i] + ahead[0][cap], ahead[1][cap]);
                    } else {
                        // Sell (consumes 1 transaction cap) or Skip
                        cur[buy][cap] = max(+prices[i] + ahead[1][cap - 1], ahead[0][cap]);
                    }
                }
            }
            
            ahead = cur;
        }
        
        // Start at day 0 with buy permission and cap = 2
        return ahead[1][2];
    }
};
```

### Java Code
```java
class Solution {

    int maxProfit(int[] prices) {
        int n = prices.length;
        
        // ahead[buy][cap] stores max profit from day (i + 1)
        int[][] ahead = new int[2][3];
        
        for (int i = n - 1; i >= 0; i--) {
            int[][] cur = new int[2][3];
            
            for (int buy = 0; buy <= 1; buy++) {
                for (int cap = 1; cap <= 2; cap++) {
                    if (buy == 1) {
                        // Buy or Skip
                        cur[buy][cap] = Math.max(-prices[i] + ahead[0][cap], ahead[1][cap]);
                    } else {
                        // Sell (consumes 1 transaction cap) or Skip
                        cur[buy][cap] = Math.max(+prices[i] + ahead[1][cap - 1], ahead[0][cap]);
                    }
                }
            }
            
            ahead = cur;
        }
        
        // Start at day 0 with buy permission and cap = 2
        return ahead[1][2];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times 2 \times 3) = \mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space ($2 \times 3 = 6$ ints).
- **Why this is optimal**: Tracks at most 6 state configurations per day in constant hardware registers.

---

## 6. Dry Run

`prices = [3, 3, 5, 0, 0, 3, 1, 4]` ($N = 8$)

| Step | Action / State Change | Result |
|---|---|---|
| `Transaction 1` | Buy at day 3 (p=0), Sell at day 5 (p=3) $\implies profit_1 = 3 - 0 = 3$ | Profit 1 = 3 |
| `Transaction 2` | Buy at day 6 (p=1), Sell at day 7 (p=4) $\implies profit_2 = 4 - 1 = 3$ | Profit 2 = 3 |
| `Total Profit` | $3 + 3 = 6$ | Profit = 6 |
| `Alternative` | Buy at day 0 (p=3), Sell at day 2 (p=5) $\implies 2$; Buy at 3, Sell at 7 $\implies 4$; Total = 6 | Profit = 6 |
| `Result` | Return ahead[1][2] = 6 | Max Profit = 6 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Monotonically decreasing prices (returns 0).
- $N \le 1$ (returns 0).
- Only 1 profitable transaction possible.

### Common Bugs to Avoid
- Decrementing `cap` on buy instead of sell (or vice-versa inconsistently).
- Allowing `cap < 0`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does the 4-Variable State Pipeline work for Stock III?**  
  **A**: We maintain 4 variables in a single forward pass: 
`buy1 = min(buy1, price); profit1 = max(profit1, price - buy1);` 
`buy2 = min(buy2, price - profit1); profit2 = max(profit2, price - buy2);` 
`buy2` uses reinvested profit from transaction 1, achieving pure $\mathcal{O}(1)$ space in 4 lines!

- **Q2: How to generalize to Stock IV (At most K transactions / Problem 38)?**  
  **A**: Replace the fixed capacity of 2 with general parameter $K$. Space becomes $\mathcal{O}(K)$ and time $\mathcal{O}(N \times K)$!

- **Q3: What if K >= N / 2?**  
  **A**: If $K \ge N/2$, you can make as many transactions as there are price peaks. The problem collapses to **Stock II (Unlimited Transactions)** solved in $\mathcal{O}(N)$ time!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Stocks`, `FSM`, `LeetCode-123`, `Hard`
- **Related problems to practice next**:
- **Stock IV**: At most K transactions.
- **Stock II**: Unlimited transactions.
