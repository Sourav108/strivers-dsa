# Fibonacci Number (Recursion Tree & Dynamic Programming) (Step 1.5 — Learn Basic Recursion)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Fibonacci Number (Recursion Tree & Dynamic Programming)](https://takeuforward.org/data-structure/print-fibonacci-series-up-to-nth-term/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$, return the $N^{\text{th}}$ Fibonacci number $F(N)$ where $F(0) = 0, F(1) = 1, F(N) = F(N-1) + F(N-2)$.

---

## 1. Problem, Restated

Compute $N$-th Fibonacci number analyzing multiple recursion tree calls vs $\mathcal{O}(N)$ iterative space-optimized DP vs $\mathcal{O}(\log N)$ Matrix Exponentiation.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Naive recursion `fib(n-1) + fib(n-2)` creates a binary recursion tree of size $2^N$, computing the same subproblems millions of times. By storing previous two values (`prev2`, `prev`), we compute $F(N)$ iteratively in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Multiple Recursive Calls (Tree Recursion) & Overlapping Subproblems`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Naive Multiple Recursion: `return fib(n-1) + fib(n-2)` in $\mathcal{O}(2^N)$ exponential time.

### C++17 Code
```cpp
int fibNaive(int n) {
    if (n <= 1) return n;
    return fibNaive(n - 1) + fibNaive(n - 2); // Exponential O(2^N) recursion tree!
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ exponential time — $N = 45$ takes billions of operations.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Repeated redundant computation of identical subtrees.

---

## 4. Approach 2 — Better

### Idea
Memoization (Top-Down DP) in O(N) time and O(N) memory.

### C++17 Code
```cpp
#include <vector>
using namespace std;
int fibMemo(int n, vector<int>& dp) {
    if (n <= 1) return n;
    if (dp[n] != -1) return dp[n];
    return dp[n] = fibMemo(n - 1, dp) + fibMemo(n - 2, dp);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ memo table + stack.
- **Why it's still not optimal**: Stores computed states.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Space-Optimized Iterative DP with two scalar variables.

### C++17 Code
```cpp
class Solution {
public:
    int fib(int n) {
        if (n <= 1) return n;
        
        int prev2 = 0;
        int prev = 1;
        
        for (int i = 2; i <= n; i++) {
            int curr = prev + prev2;
            prev2 = prev;
            prev = curr;
        }
        
        return prev;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Tracks only the 2 immediate historical states.

---

## 6. Dry Run

Trace for $N = 5$

| Step | Action / State Change | Result |
|---|---|---|
| `$i = 2$` | curr = 1 + 0 = 1 | prev2 = 1, prev = 1 |
| `$i = 3$` | curr = 1 + 1 = 2 | prev2 = 1, prev = 2 |
| `$i = 4$` | curr = 2 + 1 = 3 | prev2 = 2, prev = 3 |
| `$i = 5$` | curr = 3 + 2 = 5 | prev2 = 3, prev = 5 |
| `Result` | Return prev = 5 | $F(5) = 5$ ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 0$ (returns 0)
- $N = 1$ (returns 1).

### Common Bugs to Avoid
- Using naive recursion in production ($N = 50$ hangs indefinitely).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the exact asymptotic complexity of naive recursive Fibonacci?**  
  **A**: The exact time complexity is $\mathcal{O}(\phi^N)$ where $\phi = \frac{1 + \sqrt{5}}{2} \approx 1.618$ (The Golden Ratio), which is $\mathcal{O}(2^N)$.

- **Q2: How to compute F(N) in O(log N) time?**  
  **A**: Using Matrix Exponentiation on $\begin{pmatrix} 1 & 1 \\ 1 & 0 \end{pmatrix}^N$ in $\mathcal{O}(\log N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Dynamic Programming`, `LeetCode-509`, `Easy`
- **Related problems to practice next**:
- **Climbing Stairs**: Fibonacci mapping.
