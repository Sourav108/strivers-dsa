# Best Time to Buy and Sell Stock II (Unlimited transactions) (Step 16.5 — DP on Stocks)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Best Time to Buy and Sell Stock II (Unlimited transactions)](https://takeuforward.org/data-structure/buy-and-sell-stock-ii-dp-36/)
- **Difficulty**: Medium
- **Statement**: You are given an integer array `prices` where `prices[i]` is the price of a given stock on the $i$-th day. On each day, you may decide to buy and/or sell the stock. You can only hold at most one share of the stock at any time. However, you can buy it then immediately sell it on the same day. Find and return the maximum profit you can achieve.

---

## 1. Problem, Restated

Maximize total profit with unlimited transactions (holding at most 1 stock at a time) using 2-State Finite State Machine DP in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Profit result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**State Representation**: 
At day $i$, we can be in one of two states: 
- `buy = 1`: We can BUY a stock on day $i$ (or skip). 
- `buy = 0`: We hold a stock and can SELL on day $i$ (or skip). 
**Recurrence**: 
1) When `buy == 1`: $\text{profit} = \max(-\text{prices}[i] + \text{dp}[i + 1][0], 0 + \text{dp}[i + 1][1])$ 
2) When `buy == 0`: $\text{profit} = \max(+\text{prices}[i] + \text{dp}[i + 1][1], 0 + \text{dp}[i + 1][0])$ 
**Space Optimization**: Only day $i + 1$ is needed to compute day $i$. We track two scalar variables `aheadBuy` and `aheadNotBuy` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space. 
**Greedy Equivalence**: Total profit is simply the sum of all positive daily differences: $\sum_{i=1}^{n-1} \max(0, \text{prices}[i] - \text{prices}[i-1])$.

- **Underlying Pattern**: `2-State Stock DP (Buy / Sell Transitions) + Valley-Peak Greedy Equivalence`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all buy/sell combinations at every day in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int buy, const vector<int>& p) {
        if (i == p.size()) return 0;
        if (buy) return max(-p[i] + solve(i + 1, 0, p), solve(i + 1, 1, p));
        return max(+p[i] + solve(i + 1, 1, p), solve(i + 1, 0, p));
    }
public:
    int maxProfit(vector<int>& prices) {
        return solve(0, 1, prices);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int buy, int[] p) {
        if (i == p.length) return 0;
        if (buy) return Math.max(-p[i] + solve(i + 1, 0, p), solve(i + 1, 1, p));
        return Math.max(+p[i] + solve(i + 1, 1, p), solve(i + 1, 0, p));
    }

    int maxProfit(int[] prices) {
        return solve(0, 1, prices);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary decision tree evaluates redundant daily states.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table of size N x 2 in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>> dp(n + 1, vector<int>(2, 0));
        
        for (int i = n - 1; i >= 0; i--) {
            // buy == 1
            dp[i][1] = max(-prices[i] + dp[i + 1][0], dp[i + 1][1]);
            // buy == 0
            dp[i][0] = max(+prices[i] + dp[i + 1][1], dp[i + 1][0]);
        }
        return dp[0][1];
    }
};
```

### Java Code
```java
class Solution2D {

    int maxProfit(int[] prices) {
        int n = prices.length;
        int[][] dp = new int[n + 1][2];
        
        for (int i = n - 1; i >= 0; i--) {
            // buy == 1
            dp[i][1] = Math.max(-prices[i] + dp[i + 1][0], dp[i + 1][1]);
            // buy == 0
            dp[i][0] = Math.max(+prices[i] + dp[i + 1][1], dp[i + 1][0]);
        }
        return dp[0][1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: 2D matrix is redundant when only 2 scalar states are referenced.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized 2-Variable FSM / Greedy Peak-Valley in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        
        int aheadBuy = 0;    // dp[i + 1][1]
        int aheadNotBuy = 0; // dp[i + 1][0]
        
        for (int i = n - 1; i >= 0; i--) {
            int curBuy = max(-prices[i] + aheadNotBuy, aheadBuy);
            int curNotBuy = max(+prices[i] + aheadBuy, aheadNotBuy);
            
            aheadBuy = curBuy;
            aheadNotBuy = curNotBuy;
        }
        
        return aheadBuy;
    }
};
```

### Java Code
```java
class Solution {

    int maxProfit(int[] prices) {
        int n = prices.length;
        
        int aheadBuy = 0;    // dp[i + 1][1]
        int aheadNotBuy = 0; // dp[i + 1][0]
        
        for (int i = n - 1; i >= 0; i--) {
            int curBuy = Math.max(-prices[i] + aheadNotBuy, aheadBuy);
            int curNotBuy = Math.max(+prices[i] + aheadBuy, aheadNotBuy);
            
            aheadBuy = curBuy;
            aheadNotBuy = curNotBuy;
        }
        
        return aheadBuy;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single backward pass).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (two integer variables).
- **Why this is optimal**: State machine transitions depend only on the immediate next day's binary buy/sell state.

---

## 6. Dry Run

`prices = [7, 1, 5, 3, 6, 4]` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 5 (p=4)` | curBuy = max(-4, 0) = 0; curNotBuy = max(+4, 0) = 4 | aheadBuy=0, aheadNotBuy=4 |
| `i = 4 (p=6)` | curBuy = max(-6+4, 0) = 0; curNotBuy = max(6+0, 4) = 6 | aheadBuy=0, aheadNotBuy=6 |
| `i = 3 (p=3)` | curBuy = max(-3+6, 0) = 3; curNotBuy = max(3+0, 6) = 6 | aheadBuy=3, aheadNotBuy=6 |
| `i = 2 (p=5)` | curBuy = max(-5+6, 3) = 3; curNotBuy = max(5+3, 6) = 8 | aheadBuy=3, aheadNotBuy=8 |
| `i = 1 (p=1)` | curBuy = max(-1+8, 3) = 7; curNotBuy = max(1+3, 8) = 8 | aheadBuy=7, aheadNotBuy=8 |
| `i = 0 (p=7)` | curBuy = max(-7+8, 7) = 7; curNotBuy = max(7+7, 8) = 14 | aheadBuy=7, aheadNotBuy=14 |
| `Result` | Return aheadBuy = 7 (Buy day 1 at 1, sell day 2 at 5 $\implies +4$; Buy day 3 at 3, sell day 4 at 6 $\implies +3$; Total = 7) | Max Profit = 7 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Monotonically decreasing prices (returns 0).
- Monotonically increasing prices (returns `prices[n-1] - prices[0]`).
- $N = 1$ (returns 0).

### Common Bugs to Avoid
- Buying without selling the previously held stock.
- Incorrectly initializing base cases.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the Greedy Peak-Valley approach (sum of positive diffs) mathematically identical to DP?**  
  **A**: Any multi-day profit from day $i$ to day $j$ is $(P_j - P_i) = (P_{i+1} - P_i) + (P_{i+2} - P_{i+1}) + \dots + (P_j - P_{j-1})$. Dropping negative daily increments and keeping all positive daily increments captures the exact maximum accumulation of all profitable segments!

- **Q2: How does Stock II generalize to Stock with Transaction Fee (Problem 40)?**  
  **A**: Subtract fee $F$ upon every sale: `curNotBuy = max(+prices[i] - fee + aheadBuy, aheadNotBuy)`!

- **Q3: How does Stock II generalize to Stock with Cooldown (Problem 39)?**  
  **A**: Selling forces a 1-day cooldown, transitioning to day $i + 2$: `+prices[i] + dp[i + 2][1]`.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Stocks`, `FSM`, `Greedy`, `LeetCode-122`, `Medium`
- **Related problems to practice next**:
- **Stock III**: At most 2 transactions.
- **Stock with Cooldown**: 1-day waiting cooldown.
