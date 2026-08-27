# Count Good Numbers (Modulo Exponentiation) (Step 7.1 — Get a Strong Hold)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Count Good Numbers (Modulo Exponentiation)](https://takeuforward.org/recursion/count-good-numbers/)
- **Difficulty**: Medium
- **Statement**: A digit string is good if digits at even indices (0-indexed) are **even** (0, 2, 4, 6, 8 -> 5 choices) and digits at odd indices are **prime** (2, 3, 5, 7 -> 4 choices). Given an integer $n$, return the total number of good digit strings of length $n$ modulo $10^9 + 7$.

---

## 1. Problem, Restated

Compute $(5^{\lceil n/2 \rceil} \times 4^{\lfloor n/2 \rfloor}) \pmod{10^9 + 7}$ in $\mathcal{O}(\log N)$ time using modular exponentiation.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Length $n$ has $\lceil n/2 \rceil = (n + 1)/2$ even indices and $\lfloor n/2 \rfloor = n/2$ odd indices. Each even index has 5 choices, each odd index has 4 choices. Total good numbers = $5^{(n+1)/2} \times 4^{n/2} \pmod{10^9 + 7}$. Use binary modular exponentiation to compute large powers in $\mathcal{O}(\log N)$ time.

- **Underlying Pattern**: `Combinatorics & Modular Binary Exponentiation`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Linear modular multiplication in $\mathcal{O}(N)$ time ($N = 10^{15} \implies$ TLE).

### C++17 Code
```cpp
// Linear loop TLE
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Fails for $N = 10^{15}$.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Modular Binary Exponentiation in $\mathcal{O}(\log N)$ time.

### C++17 Code
```cpp
class Solution {
private:
    const int MOD = 1e9 + 7;
    
    long long modPow(long long base, long long exp) {
        long long res = 1;
        base %= MOD;
        while (exp > 0) {
            if (exp % 2 == 1) res = (res * base) % MOD;
            base = (base * base) % MOD;
            exp /= 2;
        }
        return res;
    }

public:
    int countGoodNumbers(long long n) {
        long long evenPositions = (n + 1) / 2; // ceiling of n/2
        long long oddPositions  = n / 2;       // floor of n/2
        
        long long countEvens = modPow(5, evenPositions);
        long long countOdds  = modPow(4, oddPositions);
        
        return (countEvens * countOdds) % MOD;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Modular exponentiation runs in $< 60$ operations for $N = 10^{15}$.

---

## 6. Dry Run

$n = 4$

| Step | Action / State Change | Result |
|---|---|---|
| `Indices` | even = 2 (indices 0, 2), odd = 2 (indices 1, 3) | evenPositions = 2, oddPositions = 2 |
| `Powers` | $5^2 = 25, 4^2 = 16$ | Calculated in O(log N) |
| `Result` | $25 \times 16 = 400 \pmod{10^9+7}$ | Return 400 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n = 1$ ($5^1 \times 4^0 = 5$).
- $n = 50$ ($N = 10^{15}$ handled safely with 64-bit `long long`).

### Common Bugs to Avoid
- Integer overflow during `(countEvens * countOdds)` without `long long` cast.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why are there 5 even choices and 4 prime choices?**  
  **A**: Even digits are `{0, 2, 4, 6, 8}` (5 choices). Single-digit primes are `{2, 3, 5, 7}` (4 choices). Note: 0 and 1 are not prime.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Maths`, `Modular Arithmetic`, `LeetCode-1922`, `Medium`
- **Related problems to practice next**:
- **Pow(x, n)**: Binary exponentiation.
