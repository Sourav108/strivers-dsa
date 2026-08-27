# Print Prime Factors of a Given Number (Trial Division) (Step 8.3 — Advanced Maths)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Print Prime Factors of a Given Number (Trial Division)](https://takeuforward.org/data-structure/prime-factorisation/)
- **Difficulty**: Medium
- **Statement**: Given an integer $N$, find and return all of its unique prime factors in sorted ascending order in $\mathcal{O}(\sqrt{N})$ time.

---

## 1. Problem, Restated

Compute the prime factorization of $N$ using $\mathcal{O}(\sqrt{N})$ trial division.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Extract factor 2: if $N \% 2 == 0$, add 2, divide $N$ by 2 repeatedly until odd. 2) Iterate odd numbers $i = 3, 5, 7, \dots$ while $i \times i \le N$: if $N \% i == 0$, add $i$, divide $N$ by $i$ repeatedly. 3) If $N > 1$ after loop, the remaining $N$ is itself a prime factor! Total time: $\mathcal{O}(\sqrt{N})$.

- **Underlying Pattern**: `Prime Factor Extraction & Sieve Reduction`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Loop $2$ to $N$, check `isPrime(i)` and `N % i == 0` in $\mathcal{O}(N \sqrt{N})$ time.

### C++17 Code
```cpp
// O(N sqrt(N)) naive check
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \sqrt{N})$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Redundant primality tests.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Trial Division with Factor Reduction in $\mathcal{O}(\sqrt{N})$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> AllPrimeFactors(int N) {
        vector<int> primeFactors;
        
        // Check for factor 2
        if (N % 2 == 0) {
            primeFactors.push_back(2);
            while (N % 2 == 0) N /= 2;
        }
        
        // Check odd factors up to sqrt(N)
        for (int i = 3; (long long)i * i <= N; i += 2) {
            if (N % i == 0) {
                primeFactors.push_back(i);
                while (N % i == 0) N /= i;
            }
        }
        
        // If remaining N > 1, it must be prime
        if (N > 1) {
            primeFactors.push_back(N);
        }
        
        return primeFactors;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\sqrt{N})$ time ($N = 10^9 \implies \approx 31622$ iterations).
- **Space Complexity**: $\mathcal{O}(\log N)$ space to store prime factors.
- **Why this is optimal**: Dividing $N$ by prime factors dynamically reduces the remaining search bound.

---

## 6. Dry Run

$N = 60$

| Step | Action / State Change | Result |
|---|---|---|
| `Factor 2` | 60 % 2 == 0 -> push 2, 60/2/2 = 15 | N = 15 |
| `Factor 3` | 15 % 3 == 0 -> push 3, 15/3 = 5 | N = 5 |
| `Factor 5` | $5^2 = 25 > 5$ -> loop ends | N = 5 > 1 -> push 5 |
| `Result` | Unique prime factors | `[2, 3, 5]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Prime numbers ($N = 29 \implies [29]$).
- $N = 2$ ($[2]$).
- Powers of 2 ($N = 16 \implies [2]$).

### Common Bugs to Avoid
- Not checking `if (N > 1)` after the loop (misses primes larger than $\sqrt{N}$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is remaining N > 1 guaranteed to be prime?**  
  **A**: Because we divided out all prime factors $\le \sqrt{N}$. Any composite number must have at least one prime factor $\le \sqrt{N}$. If none exist, $N$ must be prime.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Maths`, `Prime`, `Medium`
- **Related problems to practice next**:
- **Sieve of Eratosthenes**: Bulk primes.
