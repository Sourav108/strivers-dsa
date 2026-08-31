# Coin Change II (Number of Ways with Infinite Supply) (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Coin Change II (Number of Ways with Infinite Supply)](https://takeuforward.org/data-structure/coin-change-2-dp-22/)
- **Difficulty**: Medium
- **Statement**: You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money. Return the number of combinations that make up that amount. If that amount of money cannot be made up by any combination of the coins, return 0. You may assume that you have an infinite number of each kind of coin.

---

## 1. Problem, Restated

Compute the number of distinct unordered combinations of coins summing to `amount` using Unbounded Knapsack Counting DP in $\mathcal{O}(N \times \text{amount})$ time and $\mathcal{O}(\text{amount})$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Combinations vs Permutations (The Loop Order Invariant)**: 
- **To Count COMBINATIONS (unordered sets like {1, 2} == {2, 1})**: Iterate **COINS in the outer loop** and `amount` in the inner loop! This ensures coins are considered in fixed non-decreasing denomination order, preventing permutations! 
- **State Transition**: 
$$\text{dp}[t] = \text{dp}[t] + \text{dp}[t - \text{coin}]$$ 
**Base Case**: $\text{dp}[0] = 1$ (1 way to make amount 0: pick empty set of coins). 
**Forward Loop**: Iterating $t$ from $\text{coin}$ to $\text{amount}$ enables multiple uses of the same coin in $\mathcal{O}(N \times \text{amount})$ time and $\mathcal{O}(\text{amount})$ space.

- **Underlying Pattern**: `Unbounded Knapsack / Combinations Counting (Outer Coin Loop + Forward State Rolling)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively generate all combinations of coins in $\mathcal{O}(2^{\text{amount}})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int T, const vector<int>& c) {
        if (T == 0) return 1;
        if (i < 0) return 0;
        int notTake = solve(i - 1, T, c);
        int take = (T >= c[i]) ? solve(i, T - c[i], c) : 0;
        return notTake + take;
    }
public:
    int change(int amount, vector<int>& coins) {
        return solve(coins.size() - 1, amount, coins);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int T, int[] c) {
        if (T == 0) return 1;
        if (i < 0) return 0;
        int notTake = solve(i - 1, T, c);
        int take = (T >= c[i]) ? public solve(i, T - c[i], c) { /* initialized: 0;
        return notTake + take;
    }

    int change(int amount, int[] coins)  */ 
        return solve(coins.length - 1, amount, coins);
     }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^{\text{amount}})$ time.
- **Space Complexity**: $\mathcal{O}(\text{amount})$ stack.
- **Why it's not good enough**: Combinatorial recursion tree.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x amount) time and O(N x amount) space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution2D {
public:
    int change(int amount, vector<int>& coins) {
        int n = coins.size();
        vector<vector<unsigned long long>> dp(n, vector<unsigned long long>(amount + 1, 0));
        for (int t = 0; t <= amount; t++) {
            if (t % coins[0] == 0) dp[0][t] = 1;
        }
        for (int i = 1; i < n; i++) {
            for (int t = 0; t <= amount; t++) {
                unsigned long long notTake = dp[i - 1][t];
                unsigned long long take = (t >= coins[i]) ? dp[i][t - coins[i]] : 0;
                dp[i][t] = notTake + take;
            }
        }
        return dp[n - 1][amount];
    }
};
```

### Java Code
```java
class Solution2D {

    int change(int amount, int[] coins) {
        int n = coins.length;
        long[][] dp = new long[n][amount + 1];
        for (int t = 0; t <= amount; t++) {
            if (t % coins[0] == 0) dp[0][t] = 1;
        }
        for (int i = 1; i < n; i++) {
            for (int t = 0; t <= amount; t++) {
                long notTake = dp[i - 1][t];
                long take = (t >= coins[i]) ? dp[i][t - coins[i]] : 0;
                dp[i][t] = notTake + take;
            }
        }
        return dp[n - 1][amount];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \text{amount})$ time.
- **Space Complexity**: $\mathcal{O}(N \times \text{amount})$ space.
- **Why it's still not optimal**: 2D matrix uses redundant row allocations.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Ultra-Compact 1D Forward Array DP in $\mathcal{O}(N \times \text{amount})$ time and $\mathcal{O}(\text{amount})$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int change(int amount, vector<int>& coins) {
        // dp[t] stores number of combinations to make amount t
        // Using unsigned long long to avoid intermediate integer overflow
        vector<unsigned long long> dp(amount + 1, 0);
        dp[0] = 1; // Base case: 1 way to form amount 0 (empty combination)
        
        // OUTER LOOP over coins enforces combination order (no permutations)
        for (int coin : coins) {
            // INNER FORWARD LOOP enables unbounded reuse of current coin
            for (int t = coin; t <= amount; t++) {
                dp[t] += dp[t - coin];
            }
        }
        
        return dp[amount];
    }
};
```

### Java Code
```java
class Solution {

    int change(int amount, int[] coins) {
        // dp[t] stores number of combinations to make amount t
        // Using long to avoid intermediate integer overflow
        long[] dp = new long[amount + 1];
        dp[0] = 1; // Base case: 1 way to form amount 0 (empty combination)
        
        // OUTER LOOP over coins enforces combination order (no permutations)
        for (int coin : coins) {
            // INNER FORWARD LOOP enables unbounded reuse of current coin
            for (int t = coin; t <= amount; t++) {
                dp[t] += dp[t - coin];
            }
        }
        
        return dp[amount];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \text{amount})$ time (each coin denomination relaxes array in $\mathcal{O}(\text{amount})$).
- **Space Complexity**: $\mathcal{O}(\text{amount})$ space (a single 1D vector of size $\text{amount} + 1$).
- **Why this is optimal**: Outer coin loop guarantees unique unordered combinations with minimum possible memory footprint.

---

## 6. Dry Run

`coins = [1, 2, 5]`, $\text{amount} = 5$

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dp[0] = 1, dp[1..5] = 0 | Ready |
| `Coin 1` | dp[t] += dp[t-1] $\implies dp = [1, 1, 1, 1, 1, 1]$ | 1 way for all amounts (all 1s) |
| `Coin 2` | t=2: 1+1=2; t=3: 1+1=2; t=4: 1+2=3; t=5: 1+2=3 $\implies dp = [1, 1, 2, 2, 3, 3]$ | Coin 2 processed |
| `Coin 5` | t=5: dp[5] += dp[0] $\implies 3 + 1 = 4$ | dp[5] = 4 |
| `Result` | Return 4 (Combinations: `{1*5}`, `{1*3, 2*1}`, `{1*1, 2*2}`, `{5*1}`) | Combinations = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $\text{amount} = 0$ (returns 1).
- Coins cannot make amount (returns 0).
- Amount smaller than smallest coin.

### Common Bugs to Avoid
- Putting amount loop OUTSIDE and coins loop INSIDE (this counts PERMUTATIONS like LeetCode 377 Combination Sum IV instead of COMBINATIONS!).
- 32-bit integer overflow on intermediate additions.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does putting 'coins' in the outer loop count COMBINATIONS, while putting 'amount' in the outer loop counts PERMUTATIONS?**  
  **A**: When `coins` is outer, coin 2 is ONLY added to sums that were already formed by coin 1. You can NEVER create $\{2, 1\}$ because coin 1 is never considered after coin 2! If `amount` is outer, for amount 3 you will consider $\{1+2\}$ and $\{2+1\}$ as two separate transitions, counting permutations!

- **Q2: How does this compare to Unbounded Knapsack (Problem 23)?**  
  **A**: Unbounded Knapsack **maximizes value** with $\max$; Coin Change II **accumulates total combinations** with $\sum$.

- **Q3: How to handle modulo arithmetic if combinations exceed 64-bit limits?**  
  **A**: Accumulate modulo $10^9 + 7$: `dp[t] = (dp[t] + dp[t - coin]) % MOD`.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `Unbounded Knapsack`, `Combinations`, `LeetCode-518`, `Medium`
- **Related problems to practice next**:
- **Coin Change I**: Fewest coins minimization.
- **Unbounded Knapsack**: Value maximization counterpart.
