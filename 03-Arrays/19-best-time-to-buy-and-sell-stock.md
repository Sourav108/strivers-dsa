# Best Time to Buy and Sell Stock (Step 3.2)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/stock-buy-and-sell/
- **Difficulty**: Easy
- **Statement**: Maximize profit choosing a single day to buy and a later day to sell.

---

## 1. Problem, Restated

Maximize profit choosing a single day to buy and a later day to sell.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Track minimum buying price seen so far and maximize current price minus min price.

- **Underlying Pattern**: Array Manipulation / Mathematical Invariants / Pointers.
- **The "Aha!" Moment**: Recognizing how to avoid redundant work by storing running state or leveraging sorting invariants.

---

## 3. Approach 1 — Brute Force

### Idea
Check all possibilities exhaustively using nested loops.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <set>
#include <unordered_map>
using namespace std;

int maxProfitBrute(const vector<int>& prices) {
    int maxP = 0, n = prices.size();
    for (int i = 0; i < n; i++)
        for (int j = i + 1; j < n; j++)
            maxP = max(maxP, prices[j] - prices[i]);
    return maxP;
}
```

### Java Code
```java
class Solution {
    int maxProfitBrute(int[] prices) {
        int maxP = 0, n = prices.length;
        for (int i = 0; i < n; i++)
            for (int j = i + 1; j < n; j++)
                maxP = Math.max(maxP, prices[j] - prices[i]);
        return maxP;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Pairwise nested loops checking every $(buy, sell)$ pair take $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Running Minimum Price Tracking: Maintain `minPrice = INT_MAX` and `maxProfit = 0`. For each price $p$: update `minPrice = min(minPrice, p)` and `maxProfit = max(maxProfit, p - minPrice)`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

int maxProfitOptimal(const vector<int>& prices) {
    int minPrice = INT_MAX, maxProfit = 0;
    for (int p : prices) {
        minPrice = min(minPrice, p);
        maxProfit = max(maxProfit, p - minPrice);
    }
    return maxProfit;
}
```

### Java Code
```java
class Solution {
    int maxProfitOptimal(int[] prices) {
        int minPrice = Integer.MAX_VALUE, maxProfit = 0;
        for (int p : prices) {
            minPrice = Math.min(minPrice, p);
            maxProfit = Math.max(maxProfit, p - minPrice);
        }
        return maxProfit;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Captures the maximum price spread in single-pass $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

---

## 6. Dry Run

`prices = [7, 1, 5, 3, 6, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `p=7` | minPrice=7, profit=0 | maxProfit=0 |
| `p=1` | minPrice=1, profit=0 | maxProfit=0 |
| `p=5` | minPrice=1, profit=5-1=4 | maxProfit=4 |
| `p=3` | minPrice=1, profit=3-1=2 | maxProfit=4 |
| `p=6` | minPrice=1, profit=6-1=5 | maxProfit=5 |
| `p=4` | minPrice=1, profit=4-1=3 | Final maxProfit: 5 ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Monotonically decreasing prices (`[7, 6, 4, 3, 1]` -> returns 0, no transactions).
- Single day (`[5]` -> returns 0).

### Common Bugs to Avoid
- Attempting to sell before buying.

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does this change if you can make UNLIMITED transactions (Stock II - LeetCode 122)?**  
  **A**: Greedy approach: capture every upward price movement. Whenever `prices[i] > prices[i-1]`, add `prices[i] - prices[i-1]` to total profit in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

- **Q2: What if you can make at most TWO transactions (Stock III - LeetCode 123)?**  
  **A**: Maintain 4 variables in a single pass: `buy1 = max(buy1, -p)`, `sell1 = max(sell1, buy1 + p)`, `buy2 = max(buy2, sell1 - p)`, `sell2 = max(sell2, buy2 + p)` in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

- **Q3: What if there is a 1-day COOLDOWN after selling (Stock with Cooldown - LeetCode 309)?**  
  **A**: Use State Machine DP with 3 states: `held`, `sold`, `reset`. `held = max(held, reset - p)`, `sold = held + p`, `reset = max(reset, prevSold)` in $\mathcal{O}(n)$ time.

- **Q4: What if there is a TRANSACTION FEE per trade (LeetCode 714)?**  
  **A**: Maintain `held = max(held, cash - price)` and `cash = max(cash, held + price - fee)` in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

- **Q5: How does this problem map directly to Kadane's algorithm?**  
  **A**: Construct difference array $D[i] = \text{prices}[i] - \text{prices}[i-1]$. Finding maximum profit between buy day $i$ and sell day $j$ is mathematically identical to finding the maximum contiguous subarray sum in $D$.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Easy`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
