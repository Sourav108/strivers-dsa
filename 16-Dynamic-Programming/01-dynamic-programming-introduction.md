# Dynamic Programming Introduction (Memoization vs Tabulation) (Step 16.1 — Introduction to DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Dynamic Programming Introduction (Memoization vs Tabulation)](https://takeuforward.org/data-structure/dynamic-programming-introduction/)
- **Difficulty**: Easy
- **Statement**: Given an integer $n$, calculate the $n$-th Fibonacci number $F(n)$ where $F(0) = 0, F(1) = 1$ and $F(n) = F(n-1) + F(n-2)$ for $n \ge 2$. Demonstrate the 4 tiers of Dynamic Programming: Naive Recursion, Top-Down Memoization, Bottom-Up Tabulation, and Constant Space Optimization.

---

## 1. Problem, Restated

Compute $F(n)$ modulo $10^9+7$ (or standard) demonstrating transition from $\mathcal{O}(2^N)$ recursion to $\mathcal{O}(N)$ memoization, $\mathcal{O}(N)$ tabulation, and $\mathcal{O}(1)$ space optimization.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ or $\mathcal{O}(K)$ auxiliary space.

---

## 2. Intuition & Pattern

Dynamic Programming applies when a problem exhibits two foundational properties: 
1) **Optimal Substructure**: The optimal solution to a problem of size $n$ is constructed from optimal solutions to subproblems of size $n-1, n-2, \dots$. 
2) **Overlapping Subproblems**: The recursive expansion evaluates the EXACT same subproblems multiple times ($F(n-2)$ is recomputed in both $F(n)$ and $F(n-1)$ branches). 
**The 4-Stage Evolution**: 
- **Tier 1 (Plain Recursion)**: Recurrence tree has depth $N$, generating $2^N$ nodes $\implies \mathcal{O}(2^N)$ time and $\mathcal{O}(N)$ stack. 
- **Tier 2 (Top-Down Memoization)**: Cache computed subproblems in `dp[n]`. Evaluates each subproblem once $\implies \mathcal{O}(N)$ time and $\mathcal{O}(N) + \mathcal{O}(N)$ space (table + recursion stack). 
- **Tier 3 (Bottom-Up Tabulation)**: Iterative evaluation from base cases $dp[0]=0, dp[1]=1$ to $n$. Eliminates recursion stack overflow $\implies \mathcal{O}(N)$ time and $\mathcal{O}(N)$ space. 
- **Tier 4 (Space Optimization)**: State $i$ only depends on $i-1$ and $i-2$. Two variables `prev` and `prev2` suffice $\implies \mathcal{O}(N)$ time and $\mathcal{O}(1)$ auxiliary space.

- **Underlying Pattern**: `Core DP Paradigm: Optimal Substructure + Overlapping Subproblems (Linear Transition)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Naive binary recursion branching into $F(n-1) + F(n-2)$ in exponential $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
public:
    int fib(int n) {
        if (n <= 1) return n;
        return fib(n - 1) + fib(n - 2);
    }
};
```

### Java Code
```java
class SolutionNaive {

    int fib(int n) {
        if (n <= 1) return n;
        return fib(n - 1) + fib(n - 2);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time (binary tree of height $N$).
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack.
- **Why it's not good enough**: Exponential duplicate evaluation of subproblems.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Top-Down Memoization (Recursion + Cache) and Bottom-Up Tabulation in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

// 1. Top-Down Memoization
class SolutionMemoization {
    int memo(int n, vector<int>& dp) {
        if (n <= 1) return n;
        if (dp[n] != -1) return dp[n];
        return dp[n] = memo(n - 1, dp) + memo(n - 2, dp);
    }
public:
    int fib(int n) {
        vector<int> dp(n + 1, -1);
        return memo(n, dp);
    }
};

// 2. Bottom-Up Tabulation
class SolutionTabulation {
public:
    int fib(int n) {
        if (n <= 1) return n;
        vector<int> dp(n + 1);
        dp[0] = 0;
        dp[1] = 1;
        for (int i = 2; i <= n; i++) {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        return dp[n];
    }
};
```

### Java Code
```java
// 1. Top-Down Memoization
class SolutionMemoization {
    int memo(int n, int[] dp) {
        if (n <= 1) return n;
        if (dp[n] != -1) return dp[n];
        return dp[n] = memo(n - 1, dp) + memo(n - 2, dp);
    }

    int fib(int n) {
        int[] dp = new int[n + 1];
        return memo(n, dp);
    }
};

// 2. Bottom-Up Tabulation
class SolutionTabulation {

    int fib(int n) {
        if (n <= 1) return n;
        int[] dp = new int[n + 1];
        dp[0] = 0;
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
- **Space Complexity**: $\mathcal{O}(N)$ table space.
- **Why it's still not optimal**: Tabulation removes stack frames but still uses $\mathcal{O}(N)$ array allocation when only 2 previous states are needed.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized Iterative State Rolling in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    int fib(int n) {
        if (n <= 1) return n;
        
        int prev2 = 0; // F(i - 2)
        int prev = 1;  // F(i - 1)
        
        for (int i = 2; i <= n; i++) {
            int cur = prev + prev2;
            prev2 = prev;
            prev = cur;
        }
        
        return prev;
    }
};
```

### Java Code
```java
class Solution {

    int fib(int n) {
        if (n <= 1) return n;
        
        int prev2 = 0; // F(i - 2)
        int prev = 1;  // F(i - 1)
        
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
- **Time Complexity**: $\mathcal{O}(N)$ time (single loop from 2 to $N$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary memory (two scalar variables).
- **Why this is optimal**: State dependency length is strictly 2, making sliding variables optimal without memory allocations.

---

## 6. Dry Run

$n = 5$, tracing space-optimized state transitions

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | prev2 = 0, prev = 1 | Ready |
| `i = 2` | cur = 1 + 0 = 1; prev2 = 1, prev = 1 | F(2) = 1 |
| `i = 3` | cur = 1 + 1 = 2; prev2 = 1, prev = 2 | F(3) = 2 |
| `i = 4` | cur = 2 + 1 = 3; prev2 = 2, prev = 3 | F(4) = 3 |
| `i = 5` | cur = 3 + 2 = 5; prev2 = 3, prev = 5 | F(5) = 5 |
| `Result` | Return prev = 5 | F(5) = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n = 0$ (returns 0).
- $n = 1$ (returns 1).
- Large $N$ with modulo arithmetic.

### Common Bugs to Avoid
- Swapping `prev` and `prev2` in incorrect temporal order.
- Allocating `dp` array of size `n` instead of `n + 1` (out of bounds for `dp[n]`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the theoretical difference between Memoization and Tabulation?**  
  **A**: 1) **Memoization (Top-Down)**: Starts from target state $n$ and recursively drills down to base cases, caching results on return. Computes only reachable states, but suffers from function call stack overhead and risk of recursion stack overflow. 2) **Tabulation (Bottom-Up)**: Starts from base cases ($0, 1$) and iteratively builds up to $n$ in an array. Computes all states sequentially with zero recursion overhead and cache-friendly contiguous memory.

- **Q2: Can we compute the N-th Fibonacci number in sub-linear time?**  
  **A**: YES! Using **Matrix Exponentiation**: $\begin{pmatrix} F(n+1) & F(n) \\ F(n) & F(n-1) \end{pmatrix} = \begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^n$. Binary exponentiation computes the $n$-th power in $\mathcal{O}(\log N)$ time and $\mathcal{O}(1)$ space! Alternatively, **Binet's Formula** gives $\mathcal{O}(1)$ closed form with floating point precision limits.

- **Q3: How to identify if a problem should be solved using Dynamic Programming?**  
  **A**: Look for 2 signals: 1) **Count total ways / Maximize / Minimize** a quantity; 2) **Overlapping choices**: At each step, choosing an option impacts subsequent subproblems that can be parameterized cleanly with state variables (indices, capacities, thresholds).


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Memoization`, `Tabulation`, `Space Optimization`, `Math`, `Easy`
- **Related problems to practice next**:
- **Climbing Stairs**: Fibonacci state isomorphism.
- **Frog Jump**: Weighted jump DP.
