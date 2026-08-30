# Best Time to Buy and Sell Stock with Cooldown (Step 16.5 — DP on Stocks)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Best Time to Buy and Sell Stock with Cooldown](https://takeuforward.org/data-structure/buy-and-sell-stocks-with-cooldown-dp-39/)
- **Difficulty**: Medium
- **Statement**: You are given an array `prices` where `prices[i]` is the price of a given stock on the $i$-th day. Find the maximum profit you can achieve. You may complete as many transactions as you like with the following restriction: After you sell your stock, you cannot buy stock on the next day (i.e., **cooldown one day**).

---

## 1. Problem, Restated

Maximize profit with unlimited transactions subject to a 1-day cooldown after selling by jumping to state $i + 2$ in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Profit result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Cooldown Constraint Invariant**: 
In standard Stock II (unlimited transactions), after selling on day $i$, you can buy immediately on day $i + 1$: `dp[i + 1][1]`. 
With a **1-day cooldown**, after selling on day $i$, you CANNOT buy on day $i + 1$; you can only buy on **day $i + 2$**! 
**Recurrence**: 
1) **If `buy == 1`**: 
   $$\text{dp}[i][1] = \max(-\text{prices}[i] + \text{dp}[i + 1][0], \text{dp}[i + 1][1])$$ 
2) **If `buy == 0`**: 
   $$\text{dp}[i][0] = \max(+\text{prices}[i] + \text{dp}[i + 2][1], \text{dp}[i + 1][0])$$ 
**Space Optimization**: Since state $i$ references states from day $i + 1$ and day $i + 2$, we maintain three 2-element state vectors: `front2` ($i+2$), `front1` ($i+1$), and `cur` ($i$) in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `3-Lookahead Stock DP / Day $i + 2$ State Transition`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively branch with $i + 2$ jump on sell in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int buy, const vector<int>& p) {
        if (i >= p.size()) return 0;
        if (buy) return max(-p[i] + solve(i + 1, 0, p), solve(i + 1, 1, p));
        return max(+p[i] + solve(i + 2, 1, p), solve(i + 1, 0, p));
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
        if (i >= p.length) return 0;
        if (buy) return Math.max(-p[i] + solve(i + 1, 0, p), solve(i + 1, 1, p));
        return Math.max(+p[i] + solve(i + 2, 1, p), solve(i + 1, 0, p));
    }

    int maxProfit(int[] prices) {
        return solve(0, 1, prices);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Exponential overlapping subproblems.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table of size (N + 2) x 2 in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int maxProfit(vector<int>& prices) {
        int n = prices.size();
        vector<vector<int>> dp(n + 2, vector<int>(2, 0));
        
        for (int i = n - 1; i >= 0; i--) {
            dp[i][1] = max(-prices[i] + dp[i + 1][0], dp[i + 1][1]);
            dp[i][0] = max(+prices[i] + dp[i + 2][1], dp[i + 1][0]);
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
        int[][] dp = new int[n + 2][2];
        
        for (int i = n - 1; i >= 0; i--) {
            dp[i][1] = Math.max(-prices[i] + dp[i + 1][0], dp[i + 1][1]);
            dp[i][0] = Math.max(+prices[i] + dp[i + 2][1], dp[i + 1][0]);
        }
        return dp[0][1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: Full 2D table uses redundant memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
3-Row Rolling Buffer (`front2`, `front1`, `cur`) in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

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
        
        // front2 corresponds to dp[i + 2], front1 corresponds to dp[i + 1]
        vector<int> front2(2, 0);
        vector<int> front1(2, 0);
        vector<int> cur(2, 0);
        
        for (int i = n - 1; i >= 0; i--) {
            // Option 1: Can buy today (or skip)
            cur[1] = max(-prices[i] + front1[0], front1[1]);
            
            // Option 2: Can sell today (jumping to day i + 2 due to cooldown)
            cur[0] = max(+prices[i] + front2[1], front1[0]);
            
            // Roll state variables backwards
            front2 = front1;
            front1 = cur;
        }
        
        return cur[1];
    }
};
```

### Java Code
```java
class Solution {

    int maxProfit(int[] prices) {
        int n = prices.length;
        if (n <= 1) return 0;
        
        // front2 corresponds to dp[i + 2], front1 corresponds to dp[i + 1]
        int[] front2 = new int[2];
        int[] front1 = new int[2];
        int[] cur = new int[2];
        
        for (int i = n - 1; i >= 0; i--) {
            // Option 1: Can buy today (or skip)
            cur[1] = Math.max(-prices[i] + front1[0], front1[1]);
            
            // Option 2: Can sell today (jumping to day i + 2 due to cooldown)
            cur[0] = Math.max(+prices[i] + front2[1], front1[0]);
            
            // Roll state variables backwards
            front2 = front1;
            front1 = cur;
        }
        
        return cur[1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single backward pass).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (three 2-element arrays).
- **Why this is optimal**: Rolling 3 rows satisfies the 1-day cooldown lookahead in minimal constant memory.

---

## 6. Dry Run

`prices = [1, 2, 3, 0, 2]` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 4 (p=2)` | cur[1]=max(-2,0)=0; cur[0]=max(2,0)=2 | front1=[2,0], front2=[0,0] |
| `i = 3 (p=0)` | cur[1]=max(0+2,0)=2; cur[0]=max(0+0,2)=2 | front1=[2,2], front2=[2,0] |
| `i = 2 (p=3)` | cur[1]=max(-3+2,2)=2; cur[0]=max(3+0,2)=3 | front1=[3,2], front2=[2,2] |
| `i = 1 (p=2)` | cur[1]=max(-2+3,2)=2; cur[0]=max(2+2,3)=4 | front1=[4,2], front2=[3,2] |
| `i = 0 (p=1)` | cur[1]=max(-1+4,2)=3; cur[0]=max(1+2,4)=4 | cur[1] = 3 |
| `Result` | Return cur[1] = 3 (Buy day 0 at 1, sell day 1 at 2 $\implies 1$; Cooldown day 2; Buy day 3 at 0, sell day 4 at 2 $\implies 2$; Total = 3) | Max Profit = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 0).
- $N = 2$ (at most 1 transaction possible).
- All prices decreasing (returns 0).

### Common Bugs to Avoid
- Out of bounds when accessing $i + 2$ in static DP array (catered by sizing table to $N + 2$).
- Skipping cooldown on sale.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to model Stock with Cooldown using a 3-State FSM (Held, Sold, Rest)?**  
  **A**: We define 3 states: 
1) `held[i] = max(held[i-1], rest[i-1] - price)` (holding stock) 
2) `sold[i] = held[i-1] + price` (just sold stock today) 
3) `rest[i] = max(rest[i-1], sold[i-1])` (cooldown or resting) 
Answer is $\max(\text{sold}[n-1], \text{rest}[n-1])$ in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space!

- **Q2: What if cooldown duration is K days instead of 1 day?**  
  **A**: The transition becomes $\text{dp}[i + K + 1][1]$. In tabulation, size table to $N + K + 1$ and maintain a sliding buffer of size $K + 2$!

- **Q3: Can Greedy work for Stock with Cooldown?**  
  **A**: No, because selling today for a small profit prevents you from buying tomorrow on an even bigger dip due to cooldown. DP evaluates all global trade-offs.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Stocks`, `Cooldown`, `FSM`, `LeetCode-309`, `Medium`
- **Related problems to practice next**:
- **Stock II**: No cooldown counterpart.
- **Stock with Fee**: Transaction fee variant.
