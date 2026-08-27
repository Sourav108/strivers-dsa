# Climbing Stairs (Step 16.1 — Introduction to DP)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Climbing Stairs](https://takeuforward.org/data-structure/dynamic-programming-climbing-stairs/)
- **Difficulty**: Easy
- **Statement**: You are climbing a staircase. It takes $n$ steps to reach the top. Each time you can either climb 1 or 2 steps. In how many distinct ways can you climb to the top?

---

## 1. Problem, Restated

Compute the number of distinct ways to reach step $n$ by taking hops of $+1$ or $+2$ in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ or $\mathcal{O}(K)$ auxiliary space.

---

## 2. Intuition & Pattern

To reach step $n$, you must have jumped from either step $n - 1$ (with a 1-step hop) or step $n - 2$ (with a 2-step hop). Since these two arrival paths are mutually exclusive and exhaustive: 
$$\text{ways}(n) = \text{ways}(n - 1) + \text{ways}(n - 2)$$ 
Base cases: $\text{ways}(0) = 1$ (1 way to stay at ground), $\text{ways}(1) = 1$. This is isomorphic to the Fibonacci sequence! We optimize space using two sliding variables `prev` and `prev2` in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Linear 1D DP / Distinct Ways Counting / Fibonacci Isomorphism`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursive tree exploring all 1-step and 2-step choices in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
public:
    int climbStairs(int n) {
        if (n <= 1) return 1;
        return climbStairs(n - 1) + climbStairs(n - 2);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary recursive tree with redundant branch evaluation.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Tabulation DP in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class SolutionTabulation {
public:
    int climbStairs(int n) {
        if (n <= 1) return 1;
        vector<int> dp(n + 1);
        dp[0] = 1;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector.
- **Why it's still not optimal**: Array allocation is unnecessary when only the previous 2 states are accessed.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized Fibonacci Rolling Variables in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
class Solution {
public:
    int climbStairs(int n) {
        if (n <= 1) return 1;
        
        int prev2 = 1; // ways(0)
        int prev = 1;  // ways(1)
        
        for (int i = 2; i <= n; i++) {
            int cur = prev + prev2;
            prev2 = prev;
            prev = cur;
        }
        
        return prev;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single pass $2 \dots n$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct state rolling eliminates all dynamic allocations and recursion overhead.

---

## 6. Dry Run

$n = 4$ stairs

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | prev2 = 1 (step 0), prev = 1 (step 1) | Ready |
| `i = 2` | cur = 1 + 1 = 2; prev2 = 1, prev = 2 | ways(2) = 2: `[1+1, 2]` |
| `i = 3` | cur = 2 + 1 = 3; prev2 = 2, prev = 3 | ways(3) = 3: `[1+1+1, 1+2, 2+1]` |
| `i = 4` | cur = 3 + 2 = 5; prev2 = 3, prev = 5 | ways(4) = 5 |
| `Result` | Return 5 | Distinct Ways = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n = 1$ (returns 1).
- $n = 2$ (returns 2).
- $n = 45$ (standard max 32-bit limit).

### Common Bugs to Avoid
- Setting `ways(0) = 0` (there is 1 valid way to be at step 0: taking no steps).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What if we could take up to K steps at a time instead of 2?**  
  **A**: The recurrence becomes $\text{ways}(n) = \sum_{j=1}^K \text{ways}(n - j)$. Tabulation takes $\mathcal{O}(N \times K)$ time and $\mathcal{O}(K)$ space. We can optimize it to $\mathcal{O}(N)$ time using a **Sliding Window Sum** of size $K$!

- **Q2: What if certain steps are broken / forbidden?**  
  **A**: If step $i$ is broken, set $\text{dp}[i] = 0$. The transitions continue as normal, automatically dropping paths stepping on broken tiles.

- **Q3: What if each step has a cost (Min Cost Climbing Stairs / LeetCode 746)?**  
  **A**: The state equation transitions from sum to minimum cost: $\text{dp}[i] = \text{cost}[i] + \min(\text{dp}[i - 1], \text{dp}[i - 2])$ with base cases $\text{dp}[0] = \text{cost}[0], \text{dp}[1] = \text{cost}[1]$.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Math`, `Memoization`, `LeetCode-70`, `Easy`
- **Related problems to practice next**:
- **Frog Jump**: Stairs with energy costs.
- **Min Cost Climbing Stairs**: Cost-minimization variant.
