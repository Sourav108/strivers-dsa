# Best Time to Buy and Sell Stock (Single transaction) (Step 16.5 — DP on Stocks)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Best Time to Buy and Sell Stock (Single transaction)](https://takeuforward.org/data-structure/stock-buy-and-sell/)
- **Difficulty**: Easy
- **Statement**: You are given an array `prices` where `prices[i]` is the price of a given stock on the $i$-th day. You want to maximize your profit by choosing a single day to buy one stock and choosing a different day in the future to sell that stock. Return the maximum profit you can achieve from this transaction. If you cannot achieve any profit, return 0.

---

## 1. Problem, Restated

Find $\max_{0 \le i < j < n} (\text{prices}[j] - \text{prices}[i])$ in a single pass by tracking running minimum buy price in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Future Selling Invariant**: 
If we sell on day $i$, to maximize profit, we MUST have bought the stock on the day with the **minimum price in the prefix `prices[0 ... i - 1]`**! 
**Algorithm**: 
1) Maintain `minPrice` initialized to `prices[0]`. 
2) Maintain `maxProfit` initialized to 0. 
3) For each day $i \in [1, n - 1]$: 
   - Current potential profit: `cost = prices[i] - minPrice` 
   - Update global maximum: `maxProfit = max(maxProfit, cost)` 
   - Update lowest buy price: `minPrice = min(minPrice, prices[i])` 
4) Return `maxProfit`. Runs in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Prefix Minimum DP State Compression (Kadane's / Stock Paradigm)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Nested loops testing all $\mathcal{O}(N^2)$ buy-sell day pairs $(i, j)$ with $j > i$ in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
class SolutionNaive {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size(), maxP = 0;
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                maxP = max(maxP, prices[j] - prices[i]);
            }
        }
        return maxP;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Quadratic pair examination.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard single-pass DP below directly achieves optimal $\mathcal{O}(1)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Running Prefix Minimum in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        if (n <= 1) return 0;
        
        int minPrice = prices[0]; // Minimum price seen so far
        int maxProfit = 0;        // Maximum profit achievable
        
        for (int i = 1; i < n; i++) {
            int currentProfit = prices[i] - minPrice;
            maxProfit = max(maxProfit, currentProfit);
            minPrice = min(minPrice, prices[i]);
        }
        
        return maxProfit;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single pass through `prices` array).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (two scalar variables).
- **Why this is optimal**: Prefix minimum encapsulates all historical buy decisions in $\mathcal{O}(1)$ state.

---

## 6. Dry Run

`prices = [7, 1, 5, 3, 6, 4]` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `Day 0 (p=7)` | minPrice = 7, maxProfit = 0 | Init |
| `Day 1 (p=1)` | profit = $1 - 7 = -6 \implies maxP = 0$; minPrice = $\min(7, 1) = 1$ | minPrice updated to 1 |
| `Day 2 (p=5)` | profit = $5 - 1 = 4 \implies maxP = 4$; minPrice = 1 | maxProfit = 4 |
| `Day 3 (p=3)` | profit = $3 - 1 = 2 \implies maxP = 4$; minPrice = 1 | maxProfit = 4 |
| `Day 4 (p=6)` | profit = $6 - 1 = 5 \implies maxP = 5$; minPrice = 1 | maxProfit = 5 |
| `Day 5 (p=4)` | profit = $4 - 1 = 3 \implies maxP = 5$; minPrice = 1 | maxProfit = 5 |
| `Result` | Return maxProfit = 5 (Buy Day 1 at 1, Sell Day 4 at 6) | Max Profit = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Monotonically decreasing prices `[7, 6, 4, 3, 1]` (returns 0: no profit possible).
- $N = 1$ (returns 0).
- All prices equal (returns 0).

### Common Bugs to Avoid
- Buying on day $j$ and selling on day $i$ with $i < j$ (cannot travel back in time!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does Stock I relate to Maximum Subarray Sum (Kadane's Algorithm)?**  
  **A**: Let $D[i] = \text{prices}[i] - \text{prices}[i-1]$ be daily price changes. The total profit between buy day $i$ and sell day $j$ is $\sum_{k=i+1}^j D[k]$. Finding maximum profit is EXACTLY finding the Maximum Subarray Sum on differences array $D$ using Kadane's Algorithm!

- **Q2: How does Stock I extend to Stock II (Unlimited Transactions / Problem 36)?**  
  **A**: In Stock II, we can buy and sell multiple times. We capture EVERY positive price increment: $\sum_{i=1}^{n-1} \max(0, \text{prices}[i] - \text{prices}[i-1])$!

- **Q3: How does Stock I extend to Stock III (At most 2 transactions / Problem 37)?**  
  **A**: In Stock III, we track 4 state variables in a single pass: `buy1`, `profit1`, `buy2`, `profit2` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Prefix Minimum`, `Stock DP`, `Kadane's`, `LeetCode-121`, `Easy`
- **Related problems to practice next**:
- **Stock II**: Unlimited transactions.
- **Stock III**: At most 2 transactions.
