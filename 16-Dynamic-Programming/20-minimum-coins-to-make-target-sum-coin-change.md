# Minimum Coins to Make a Target Sum (Coin Change I) (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Coins to Make a Target Sum (Coin Change I)](https://takeuforward.org/data-structure/minimum-coins-dp-20/)
- **Difficulty**: Medium
- **Statement**: You are given an integer array `coins` representing coins of different denominations and an integer `amount` representing a total amount of money. Return the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return `-1`. You may assume that you have an infinite number of each kind of coin.

---

## 1. Problem, Restated

Find the minimum number of coins to form `amount` using Unbounded Knapsack DP (forward iteration) in $\mathcal{O}(N \times \text{amount})$ time and $\mathcal{O}(\text{amount})$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Unbounded Knapsack Recurrence**: 
Since we have an **infinite supply** of each coin, after choosing coin $i$, we stay at index $i$ to potentially pick it again! 
1) **Not-Pick Coin $i$**: Move to previous coin: $\text{dp}[i - 1][T]$. 
2) **Pick Coin $i$** (if $T \ge \text{coins}[i]$): Cost is $1 + \text{dp}[i][T - \text{coins}[i]]$ (same row $i$!). 
$$\text{dp}[i][T] = \min(\text{dp}[i - 1][T], 1 + \text{dp}[i][T - \text{coins}[i]])$$ 
**Single-Array 1D Space Optimization**: 
Because picking coin $i$ references the CURRENT row's updated state $\text{dp}[T - \text{coins}[i]]$, iterating $T$ **FORWARD from $\text{coins}[i]$ to $\text{amount}$** automatically achieves unbounded reuse! 
$$\text{dp}[T] = \min(\text{dp}[T], 1 + \text{dp}[T - c])$$ 
Base case: $\text{dp}[0] = 0$, all other $\text{dp}[T] = \infty$. Runs in $\mathcal{O}(N \times \text{amount})$ time and $\mathcal{O}(\text{amount})$ space.

- **Underlying Pattern**: `Unbounded Knapsack / Forward 1D Space Relaxation (Min-Cost Coin Transitions)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursive search exploring all coin combinations in $\mathcal{O}(N^{\text{amount}})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int T, const vector<int>& c) {
        if (T == 0) return 0;
        if (i < 0 || T < 0) return 1e9;
        int notTake = solve(i - 1, T, c);
        int take = 1 + solve(i, T - c[i], c);
        return min(notTake, take);
    }
public:
    int coinChange(vector<int>& coins, int amount) {
        int res = solve(coins.size() - 1, amount, coins);
        return (res >= 1e9) ? -1 : res;
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int T, int[] c) {
        if (T == 0) return 0;
        if (i < 0 || T < 0) return 1e9;
        int notTake = solve(i - 1, T, c);
        int take = 1 + solve(i, T - c[i], c);
        return Math.min(notTake, take);
    }

    int coinChange(int[] coins, int amount) {
        int res = solve(coins.length - 1, amount, coins);
        return (res >= 1e9) ? -1 : res;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^{\text{amount}})$ time.
- **Space Complexity**: $\mathcal{O}(\text{amount})$ recursion stack.
- **Why it's not good enough**: Unbounded branching tree with combinatorial duplication.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x amount) time and O(N x amount) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int coinChange(vector<int>& coins, int amount) {
        int n = coins.size();
        vector<vector<int>> dp(n, vector<int>(amount + 1, 1e9));
        for (int t = 0; t <= amount; t++) {
            if (t % coins[0] == 0) dp[0][t] = t / coins[0];
        }
        for (int i = 1; i < n; i++) {
            for (int t = 0; t <= amount; t++) {
                int notTake = dp[i - 1][t];
                int take = (t >= coins[i]) ? 1 + dp[i][t - coins[i]] : 1e9;
                dp[i][t] = min(notTake, take);
            }
        }
        return (dp[n - 1][amount] >= 1e9) ? -1 : dp[n - 1][amount];
    }
};
```

### Java Code
```java
class Solution2D {

    int coinChange(int[] coins, int amount) {
        int n = coins.length;
        int[][] dp = new int[n][amount + 1];
        for (int t = 0; t <= amount; t++) {
            if (t % coins[0] == 0) dp[0][t] = t / coins[0];
        }
        for (int i = 1; i < n; i++) {
            for (int t = 0; t <= amount; t++) {
                int notTake = dp[i - 1][t];
                int take = (t >= coins[i]) ? 1 + dp[i][t - coins[i]] : 1e9;
                dp[i][t] = Math.min(notTake, take);
            }
        }
        return (dp[n - 1][amount] >= 1e9) ? -1 : dp[n - 1][amount];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \text{amount})$ time.
- **Space Complexity**: $\mathcal{O}(N \times \text{amount})$ space.
- **Why it's still not optimal**: 2D table allocates unnecessary matrix storage.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Forward-Iterating Unbounded DP in $\mathcal{O}(N \times \text{amount})$ time and $\mathcal{O}(\text{amount})$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        // dp[t] stores minimum coins needed to make amount t
        // Initialize with amount + 1 (represents infinity)
        vector<int> dp(amount + 1, amount + 1);
        dp[0] = 0; // Base case: 0 coins needed for 0 amount
        
        // Iterate through all coin denominations
        for (int coin : coins) {
            // Forward iteration enables infinite reuse of current coin
            for (int t = coin; t <= amount; t++) {
                dp[t] = min(dp[t], 1 + dp[t - coin]);
            }
        }
        
        return (dp[amount] > amount) ? -1 : dp[amount];
    }
};
```

### Java Code
```java
class Solution {

    int coinChange(int[] coins, int amount) {
        // dp[t] stores minimum coins needed to make amount t
        // Initialize with amount + 1 (represents infinity)
        int[] dp = new int[amount + 1];
        dp[0] = 0; // Base case: 0 coins needed for 0 amount
        
        // Iterate through all coin denominations
        for (int coin : coins) {
            // Forward iteration enables infinite reuse of current coin
            for (int t = coin; t <= amount; t++) {
                dp[t] = Math.min(dp[t], 1 + dp[t - coin]);
            }
        }
        
        return (dp[amount] > amount) ? -1 : dp[amount];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times \text{amount})$ time (each coin relaxes all amounts in linear time).
- **Space Complexity**: $\mathcal{O}(\text{amount})$ auxiliary space (single 1D vector).
- **Why this is optimal**: Forward iteration directly reflects unbounded coin supply using minimum possible memory.

---

## 6. Dry Run

`coins = [1, 2, 5]`, $\text{amount} = 11$

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dp[0] = 0, dp[1..11] = 12 (INF) | Ready |
| `Coin 1` | dp[t] = t for all $t \in [1, 11]$ (11 coins of 1) | dp = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11] |
| `Coin 2` | dp[t] relaxes to $\min(dp[t], 1 + dp[t-2])$ | dp = [0, 1, 1, 2, 2, 3, 3, 4, 4, 5, 5, 6] |
| `Coin 5` | dp[11] = $\min(6, 1 + dp[6]) = 1 + 3 = 4$; dp[10] = $\min(5, 1 + dp[5]) = 1 + 1 = 2$; dp[11] relaxes to $1 + dp[6] = 1 + 2 = 3$ | dp[11] = 3 |
| `Result` | Return dp[11] = 3 (Two 5-coins + One 1-coin: $5 + 5 + 1 = 11$) | Min Coins = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $\text{amount} = 0$ (returns 0).
- Amount cannot be formed (e.g. `coins = [2]`, `amount = 3` $\implies$ returns -1).
- Coin denominations larger than amount.

### Common Bugs to Avoid
- Using `INT_MAX` for infinity causing integer overflow on `1 + dp[t - coin]` (use `amount + 1` or `1e9`).
- Iterating backward instead of forward (which would restrict coins to at most 1 use).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does forward loop iteration implement Unbounded Knapsack, while backward loop implements 0/1 Knapsack?**  
  **A**: In backward iteration, `dp[t - coin]` is the state BEFORE considering the current coin (0/1 choice). In forward iteration, `dp[t - coin]` has ALREADY been updated for the current coin in the same pass, allowing the coin to be picked repeatedly (unbounded supply)!

- **Q2: Why does Greedy (picking largest coin first) fail for arbitrary coin systems?**  
  **A**: Consider `coins = [1, 3, 4]`, `amount = 6`. Greedy picks $4 + 1 + 1$ (3 coins). But optimal DP picks $3 + 3$ (2 coins)! Greedy is only optimal for canonical coin systems (like US coins).

- **Q3: How does Coin Change II (LeetCode 518) differ from Coin Change I?**  
  **A**: Coin Change I **minimizes** the number of coins ($\min$ operation). Coin Change II **counts** the total number of distinct combinations ($\sum$ operation, with coin loop outside to avoid counting permutations).


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `Unbounded Knapsack`, `Coin Change`, `LeetCode-322`, `Medium`
- **Related problems to practice next**:
- **Coin Change II**: Counting combinations.
- **0/1 Knapsack**: Bounded capacity counterpart.
