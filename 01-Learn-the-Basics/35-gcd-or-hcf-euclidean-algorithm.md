# GCD / HCF using Euclidean Algorithm (Step 1.4 — Know Basic Maths)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [GCD / HCF using Euclidean Algorithm](https://takeuforward.org/data-structure/find-gcd-of-two-numbers/)
- **Difficulty**: Easy
- **Statement**: Given two positive integers $a$ and $b$, find their Greatest Common Divisor (GCD / HCF).

---

## 1. Problem, Restated

Compute the largest integer that divides both $a$ and $b$ using the Euclidean modulo theorem.

- **Input**: Parameters specified.
- **Output**: Result meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

By the Euclidean algorithm, if $d$ divides both $a$ and $b$, then $d$ also divides the remainder $a \% b$. Repeatedly replacing $(a, b)$ with $(b, a \% b)$ reduces the problem size exponentially. When $b == 0$, the GCD is $a$. Runs in $\mathcal{O}(\log(\min(a, b)))$ (Lamé's Theorem).

- **Underlying Pattern**: `Euclidean Modulo Reduction: $\gcd(a, b) = \gcd(b, a \% b)$`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Linear scan from $\min(a, b)$ downwards to 1 checking common divisibility in $\mathcal{O}(\min(a, b))$.

### C++17 Code
```cpp
int gcdLinear(int a, int b) {
    for (int i = min(a, b); i >= 1; i--) {
        if (a % i == 0 && b % i == 0) return i;
    }
    return 1;
}
```

### Java Code
```java
class Solution {
    int gcdLinear(int a, int b) {
        for (int i = Math.min(a, b); i >= 1; i--) {
            if (a % i == 0 && b % i == 0) return i;
        }
        return 1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\min(a, b))$ time — $10^9$ operations causes TLE.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Linear decrement is too slow.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard mathematical / optimal algorithmic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Iterative Euclidean Modulo Algorithm: `while (b != 0) { a %= b; swap(a, b); }`.

### C++17 Code
```cpp
#include <algorithm>
#include <numeric>
using namespace std;

int gcdEuclidean(int a, int b) {
    while (b != 0) {
        int rem = a % b;
        a = b;
        b = rem;
    }
    return a;
}

// Alternatively in C++17: std::gcd(a, b) from <numeric>

```

### Java Code
```java
class Solution {
    int gcdEuclidean(int a, int b) {
        while (b != 0) {
            int rem = a % b;
            a = b;
            b = rem;
        }
        return a;
    }
    
    // Alternatively in C++17: gcd(a, b) from <numeric>
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2(\min(a, b)))$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Modulo operation halves the larger number every 2 iterations at worst.

---

## 6. Dry Run

GCD of $a = 52, b = 10$

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | 52 % 10 = 2 | a = 10, b = 2 |
| `Step 2` | 10 % 2 = 0 | a = 2, b = 0 |
| `Exit` | b == 0 -> return a = 2 | GCD = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $a = 0$ (GCD is $b$)
- $a = b$ (GCD is $a$)
- Coprime numbers (GCD is 1).

### Common Bugs to Avoid
- Modulo by zero if not terminating at $b == 0$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is Lamé's Theorem on Euclidean algorithm complexity?**  
  **A**: Lamé's theorem proves that the number of division steps is at most $5$ times the number of decimal digits of $\min(a, b)$. Worst-case occurs on consecutive Fibonacci numbers ($F_{k+1}, F_k$).

- **Q2: What is the relationship between GCD and LCM?**  
  **A**: $\text{LCM}(a, b) = \frac{a \times b}{\gcd(a, b)}$. To prevent overflow in multiplication: `(a / gcd(a, b)) * b`.


---

## 9. Tags & Related Problems

- **Tags**: `Maths`, `Number Theory`, `Euclidean`, `Easy`
- **Related problems to practice next**:
- **Print All Divisors**: Divisor enumeration.
