# Pow(x, n) - Implement Binary Exponentiation Recursively (Step 7.1 — Get a Strong Hold)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Pow(x, n) - Implement Binary Exponentiation Recursively](https://takeuforward.org/data-structure/calculate-the-power-of-a-number/)
- **Difficulty**: Medium
- **Statement**: Implement `myPow(x, n)` which calculates $x^n$ (i.e. $x$ raised to the power $n$) in $\mathcal{O}(\log N)$ time handling negative exponents and $n = -2^{31}$.

---

## 1. Problem, Restated

Compute $x^n$ using divide-and-conquer binary exponentiation: $x^n = (x^2)^{n/2}$ if $n$ is even, $x \cdot x^{n-1}$ if $n$ is odd.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Multiplying $x$ linearly takes $\mathcal{O}(N)$ operations ($N = 2 \times 10^9 \implies$ TLE). Notice $x^{2k} = (x^2)^k$. In each step, we can halve the power $n$ by squaring the base $x$. Recursion depth is $\log_2 N$. Cast $n$ to 64-bit `long long` to prevent `-INT_MIN` overflow (`-(-2147483648) > INT_MAX`).

- **Underlying Pattern**: `Divide and Conquer Binary Exponentiation`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Multiply $x$ by itself $N$ times in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
double powLinear(double x, int n) {
    double ans = 1.0;
    for (int i = 0; i < abs(n); i++) ans *= x;
    return (n < 0) ? 1.0 / ans : ans;
}
```

### Java Code
```java
class Solution {
    double powLinear(double x, int n) {
        double ans = 1.0;
        for (int i = 0; i < Math.abs(n); i++) ans *= x;
        return (n < 0) ? 1.0 / ans : ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time — $2 \times 10^9$ operations causes TLE.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Linear multiplication.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Binary Exponentiation in $\mathcal{O}(\log N)$ time.

### C++17 Code
```cpp
class Solution {
private:
    double binaryPow(double x, long long n) {
        if (n == 0) return 1.0;
        
        double half = binaryPow(x, n / 2);
        
        if (n % 2 == 0) {
            return half * half;
        } else {
            return x * half * half;
        }
    }

public:
    double myPow(double x, int n) {
        long long N = n; // safe cast for INT_MIN
        if (N < 0) {
            x = 1.0 / x;
            N = -N;
        }
        return binaryPow(x, N);
    }
};
```

### Java Code
```java
class Solution {

    double binaryPow(double x, long n) {
        if (n == 0) return 1.0;
        
        double half = binaryPow(x, n / 2);
        
        if (n % 2 == 0) {
            return half * half;
        } else {
            return x * half * half;
        }
    }

    double myPow(double x, int n) {
        long N = n; // safe cast for Integer.MIN_VALUE
        if (N < 0) {
            x = 1.0 / x;
            N = -N;
        }
        return binaryPow(x, N);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 N)$ time — halves power on every step.
- **Space Complexity**: $\mathcal{O}(\log_2 N)$ recursion call stack space.
- **Why this is optimal**: Halves problem size in each divide-and-conquer recurrence.

---

## 6. Dry Run

$x = 2.0, n = 10$

| Step | Action / State Change | Result |
|---|---|---|
| `n = 10` | half = pow(2, 5) | $half^2$ |
| `n = 5` | half = pow(2, 2) | $2 \times half^2$ |
| `n = 2` | half = pow(2, 1) | $half^2$ |
| `n = 1` | half = pow(2, 0) = 1 | $2 \times 1^2 = 2$ |
| `Unwind` | $n=2 \implies 4, n=5 \implies 32, n=10 \implies 1024$ | Result = 1024.0 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n = 0$ (returns 1.0).
- $n = -2147483648$ (casting to `long long` avoids negation overflow).
- $x = 0$ (returns 0).

### Common Bugs to Avoid
- Computing `binaryPow(x, n/2) * binaryPow(x, n/2)` without caching `half` (causes $\mathcal{O}(N)$ branching tree).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does -n overflow when n = INT_MIN?**  
  **A**: `INT_MIN = -2147483648`. In 32-bit signed integers, `INT_MAX = 2147483647`. Thus `-INT_MIN = 2147483648` exceeds 32-bit limits, triggering integer overflow.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Binary Exponentiation`, `Divide and Conquer`, `LeetCode-50`, `Medium`
- **Related problems to practice next**:
- **Count Good Numbers**: Modulo exponentiation.
