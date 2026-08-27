# Factorial of N Numbers (Step 1.5 — Learn Basic Recursion)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Factorial of N Numbers](https://takeuforward.org/data-structure/factorial-of-a-number-iterative-and-recursive/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$, compute $N! = N \times (N-1) \times \dots \times 1$.

---

## 1. Problem, Restated

Compute factorial using functional and tail recursion.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Base case: $0! = 1$ and $1! = 1$. Recurrence: $f(N) = N \times f(N-1)$. Note that $20! \approx 2.43 \times 10^{18}$ fits inside 64-bit `unsigned long long`, but $N > 20$ overflows 64-bit integer types (requiring BigInt / vector representation).

- **Underlying Pattern**: `Multiplicative Recurrence: $N! = N \times (N-1)!$`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Standard functional recursion.

### C++17 Code
```cpp
long long factSlow(int n) {
    if (n <= 1) return 1;
    return n * factSlow(n - 1);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why it's not good enough**: Stack frame overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Tail Recursive Factorial with 64-bit integer accumulator.

### C++17 Code
```cpp
using namespace std;

class Solution {
private:
    long long factTail(int n, long long acc) {
        if (n <= 1) return acc;
        return factTail(n - 1, acc * n);
    }

public:
    long long factorial(int n) {
        if (n < 0) return -1; // undefined for negative integers
        return factTail(n, 1LL);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ with tail call optimization.
- **Why this is optimal**: Tail recursion executes without building stack depth.

---

## 6. Dry Run

Factorial of $N = 4$

| Step | Action / State Change | Result |
|---|---|---|
| `factTail(4, 1)` | calls factTail(3, 4) | acc = 4 |
| `factTail(3, 4)` | calls factTail(2, 12) | acc = 12 |
| `factTail(2, 12)` | calls factTail(1, 24) | acc = 24 |
| `Base N=1` | returns acc = 24 | $4! = 24$ ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 0$ (returns 1)
- $N = 1$ (returns 1)
- $N = 20$ ($20! = 2432902008176640000$, largest 64-bit factorial).

### Common Bugs to Avoid
- Calculating $N!$ for $N > 20$ using standard primitives without BigInt arrays.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does 21! overflow 64-bit unsigned integer?**  
  **A**: `ULLONG_MAX` $\approx 1.84 \times 10^{19}$. $21! \approx 5.109 \times 10^{19} > \text{ULLONG\_MAX}$.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Maths`, `Easy`
- **Related problems to practice next**:
- **Sum of First N Numbers**: Additive recursion.
