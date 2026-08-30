# Best Time to Buy and Sell Stock with Transaction Fee (Step 16.5 — DP on Stocks)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Best Time to Buy and Sell Stock with Transaction Fee](https://takeuforward.org/data-structure/buy-and-sell-stocks-with-transaction-fee-dp-40/)
- **Difficulty**: Medium
- **Statement**: You are given an array `prices` where `prices[i]` is the price of a given stock on the $i$-th day, and an integer `fee` representing a transaction fee. Find the maximum profit you can achieve. You may complete as many transactions as you like, but you need to pay the transaction fee for each transaction. Note: You may not engage in multiple transactions simultaneously (i.e., you must sell before buying again).

---

## 1. Problem, Restated

Maximize profit with unlimited transactions paying fee $F$ per transaction using 2-State FSM DP in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Profit result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Fee Deduction Invariant**: 
In standard Stock II (unlimited transactions), profit on sell is $+\text{prices}[i]$. 
With transaction fee $F$, every completed transaction incurs fee $F$. We can deduct $F$ either on **Buy** ($-\text{prices}[i] - F$) or on **Sell** ($+\text{prices}[i] - F$)! 
**Recurrence**: 
1) When `buy == 1`: $\text{dp}[i][1] = \max(-\text{prices}[i] + \text{dp}[i + 1][0], \text{dp}[i + 1][1])$ 
2) When `buy == 0`: $\text{dp}[i][0] = \max(+\text{prices}[i] - \text{fee} + \text{dp}[i + 1][1], \text{dp}[i + 1][0])$ 
**Space Optimization**: Only day $i + 1$ is needed. Two scalar variables `aheadBuy` and `aheadNotBuy` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `2-State Stock DP with Fee Deduction (`prices[i] - fee`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively branch on buy and sell with fee subtraction in $\mathcal{O}(2^N)$ time.

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
- **Space Complexity**: $\mathcal{O}(N)$ stack.
- **Why it's not good enough**: Exponential duplicate states.

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
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        vector<vector<int>> dp(n + 1, vector<int>(2, 0));
        
        for (int i = n - 1; i >= 0; i--) {
            dp[i][1] = max(-prices[i] + dp[i + 1][0], dp[i + 1][1]);
            dp[i][0] = max(+prices[i] - fee + dp[i + 1][1], dp[i + 1][0]);
        }
        return dp[0][1];
    }
};
```

### Java Code
```java
class Solution2D {

    int maxProfit(int[] prices, int fee) {
        int n = prices.length;
        int[][] dp = new int[n + 1][2];
        
        for (int i = n - 1; i >= 0; i--) {
            dp[i][1] = Math.max(-prices[i] + dp[i + 1][0], dp[i + 1][1]);
            dp[i][0] = Math.max(+prices[i] - fee + dp[i + 1][1], dp[i + 1][0]);
        }
        return dp[0][1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: 2D matrix is redundant.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized 2-Variable FSM in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxProfit(vector<int>& prices, int fee) {
        int n = prices.size();
        if (n <= 1) return 0;
        
        int aheadBuy = 0;    // dp[i + 1][1]
        int aheadNotBuy = 0; // dp[i + 1][0]
        
        for (int i = n - 1; i >= 0; i--) {
            int curBuy = max(-prices[i] + aheadNotBuy, aheadBuy);
            int curNotBuy = max(+prices[i] - fee + aheadBuy, aheadNotBuy);
            
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

    int maxProfit(int[] prices, int fee) {
        int n = prices.length;
        if (n <= 1) return 0;
        
        int aheadBuy = 0;    // dp[i + 1][1]
        int aheadNotBuy = 0; // dp[i + 1][0]
        
        for (int i = n - 1; i >= 0; i--) {
            int curBuy = Math.max(-prices[i] + aheadNotBuy, aheadBuy);
            int curNotBuy = Math.max(+prices[i] - fee + aheadBuy, aheadNotBuy);
            
            aheadBuy = curBuy;
            aheadNotBuy = curNotBuy;
        }
        
        return aheadBuy;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single backward pass).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (two scalar registers).
- **Why this is optimal**: State machine operates entirely in CPU registers with zero dynamic allocations.

---

## 6. Dry Run

`prices = [1, 3, 2, 8, 4, 9]`, `fee = 2` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `Transaction 1` | Buy at day 0 (p=1), Sell at day 3 (p=8) $\implies 8 - 1 - 2 = 5$ | Profit 1 = 5 |
| `Transaction 2` | Buy at day 4 (p=4), Sell at day 5 (p=9) $\implies 9 - 4 - 2 = 3$ | Profit 2 = 3 |
| `Total Profit` | $5 + 3 = 8$ | Profit = 8 |
| `Alternative` | Single transaction: Buy at 1, Sell at 9 $\implies 9 - 1 - 2 = 6$ (Less profitable!) | DP picks 8 |
| `Result` | Return aheadBuy = 8 | Max Profit = 8 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Fee larger than all price swings (returns 0).
- Monotonically decreasing prices (returns 0).
- $N = 1$ (returns 0).

### Common Bugs to Avoid
- Deducting fee on BOTH buy and sell (double-charging fee per transaction).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can fee be deducted during Buy instead of Sell?**  
  **A**: YES! `curBuy = max(-prices[i] - fee + aheadNotBuy, aheadBuy)` and `curNotBuy = max(+prices[i] + aheadBuy, aheadNotBuy)`. Both yield identical total profits!

- **Q2: How does this compare to Forward Greedy (Hold vs Cash)?**  
  **A**: In forward pass: `hold = max(hold, cash - price)` and `cash = max(cash, hold + price - fee)`. Returns `cash` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space!

- **Q3: Why does simple Peak-Valley Greedy fail when a transaction fee is present?**  
  **A**: A small price dip of size 1 might tempt greedy to sell and rebuy, but paying fee 2 causes a net loss of 1! The DP ensures transactions are only executed when price gains strictly outweigh the transaction fee.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Stocks`, `Transaction Fee`, `FSM`, `LeetCode-714`, `Medium`
- **Related problems to practice next**:
- **Stock II**: Zero-fee counterpart.
- **Stock with Cooldown**: Cooldown constraint variant.
