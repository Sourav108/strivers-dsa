# Sum of First N Numbers (Parameterized & Functional Recursion) (Step 1.5 — Learn Basic Recursion)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Sum of First N Numbers (Parameterized & Functional Recursion)](https://takeuforward.org/data-structure/sum-of-first-n-natural-numbers/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$, find the sum of the first $N$ natural numbers ($1 + 2 + \dots + N$) using parameterized and functional recursion.

---

## 1. Problem, Restated

Compute arithmetic progression sum using recursive accumulation and closed-form math $N(N+1)/2$.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Parameterized**: pass accumulated sum `f(n - 1, sum + n)`. 2) **Functional**: return `n + f(n - 1)`. 3) **Gauss Closed-Form Formula**: $\frac{N(N+1)}{2}$ computes the sum in $\mathcal{O}(1)$ time without recursion.

- **Underlying Pattern**: `Functional vs Parameterized Recursion & Gauss Closed Form`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Iterative loop summing 1 to N in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
long long sumLoop(int n) {
    long long s = 0;
    for (int i = 1; i <= n; i++) s += i;
    return s;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Iterative approach.

---

## 4. Approach 2 — Better

### Idea
Functional recursion: return n + sumFirstN(n - 1).

### C++17 Code
```cpp
long long sumFunctional(long long n) {
    if (n == 0) return 0;
    return n + sumFunctional(n - 1);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack frames.
- **Why it's still not optimal**: Illustrates functional recursion, but Gauss formula is $\mathcal{O}(1)$.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Gauss Arithmetic Closed-Form Formula: `(long long)N * (N + 1) / 2` in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
using namespace std;

class Solution {
public:
    long long sumOfFirstN(long long n) {
        // Closed-form Gauss formula with 64-bit overflow safety
        return (n * (n + 1)) / 2;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct arithmetic computation.

---

## 6. Dry Run

Sum for $N = 5$

| Step | Action / State Change | Result |
|---|---|---|
| `Gauss Formula` | $5 \times (5 + 1) / 2 = 5 \times 6 / 2 = 30 / 2 = 15$ | Sum = 15 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 0$ (returns 0)
- $N = 10^9$ (Gauss formula runs in $\mathcal{O}(1)$ while recursion causes stack overflow).

### Common Bugs to Avoid
- Writing `n * (n + 1) / 2` with 32-bit `int` (overflows for $N > 65536$). Always use `long long`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does recursion fail for N = 10^6?**  
  **A**: Default stack size is $\approx 8\text{MB}$. $10^6$ stack frames exceed stack memory, triggering Stack Overflow segmentation fault.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Maths`, `Easy`
- **Related problems to practice next**:
- **Factorial of N Numbers**: Multiplicative recursion.
