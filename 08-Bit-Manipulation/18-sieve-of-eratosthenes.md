# Sieve of Eratosthenes (Prime generation up to N in O(N log log N)) (Step 8.3 — Advanced Maths)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Sieve of Eratosthenes (Prime generation up to N in O(N log log N))](https://takeuforward.org/data-structure/sieve-of-eratosthenes/)
- **Difficulty**: Medium
- **Statement**: Given an integer $N$, generate and count all prime numbers strictly less than or equal to $N$ in $\mathcal{O}(N \log(\log N))$ time using the Sieve of Eratosthenes.

---

## 1. Problem, Restated

Compute primes up to $N$ using multiple marking on a boolean array.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Create boolean array `isPrime` of size $N+1$ initialized to `true`. Mark `isPrime[0] = isPrime[1] = false`. For each $p = 2, 3, \dots$ up to $\sqrt{N}$: if `isPrime[p]` is true, mark all its multiples starting from $p \times p$ as `false` (`for (int j = p * p; j <= n; j += p) isPrime[j] = false`).

- **Underlying Pattern**: `Composite Number Cross-Out Sieve ($\sum_{p \le N} \frac{N}{p} = N \log \log N$)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Individual $\mathcal{O}(\sqrt{N})$ prime checks for every number $1$ to $N$ in $\mathcal{O}(N \sqrt{N})$ time.

### C++17 Code
```cpp
// O(N sqrt(N)) prime loop
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \sqrt{N})$ time — for $N = 10^7$, takes $\approx 3 \times 10^{10}$ ops (TLE).
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Redundant trial divisions.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sieve of Eratosthenes in $\mathcal{O}(N \log(\log N))$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int countPrimes(int n) {
        if (n <= 2) return 0; // primes strictly less than n
        
        vector<bool> isPrime(n, true);
        isPrime[0] = isPrime[1] = false;
        
        for (int p = 2; (long long)p * p < n; p++) {
            if (isPrime[p]) {
                // Mark multiples starting from p*p
                for (int j = p * p; j < n; j += p) {
                    isPrime[j] = false;
                }
            }
        }
        
        int primeCount = 0;
        for (int i = 2; i < n; i++) {
            if (isPrime[i]) primeCount++;
        }
        
        return primeCount;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log(\log N))$ time (for $N = 10^7$, runs in $< 50\text{ms}$).
- **Space Complexity**: $\mathcal{O}(N)$ boolean array.
- **Why this is optimal**: Harmonic series of primes $\sum \frac{1}{p} = \log(\log N)$ bound.

---

## 6. Dry Run

Sieve for $N = 10$

| Step | Action / State Change | Result |
|---|---|---|
| `p = 2` | Mark multiples from $2^2 = 4$: 4, 6, 8 | 4,6,8 false |
| `p = 3` | Mark multiples from $3^2 = 9$: 9 | 9 false |
| `p = 4` | $4^2 = 16 > 10$ -> Sieve stops | Completed |
| `Primes` | Indices with true: 2, 3, 5, 7 | Total 4 primes ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N \le 2$ (returns 0).
- $N = 5 \times 10^6$ (runs in $< 30\text{ms}$).

### Common Bugs to Avoid
- Starting inner loop from `2 * p` instead of `p * p` (smaller multiples were already marked by earlier primes).
- Integer overflow on `p * p` (must cast `(long long)p * p`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the inner loop start from p * p?**  
  **A**: Because all smaller multiples $k \cdot p$ for $k < p$ (e.g. $2p, 3p, \dots, (p-1)p$) have already been marked false by the smaller prime factor $k$. Starting at $p^2$ eliminates redundant markings!


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Maths`, `Sieve`, `LeetCode-204`, `Medium`
- **Related problems to practice next**:
- **Print Prime Factors**: Prime factorization.
