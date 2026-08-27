# Check for Prime Number O(sqrt(N)) (Step 1.4 — Know Basic Maths)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check for Prime Number O(sqrt(N))](https://takeuforward.org/data-structure/check-if-a-number-is-prime-or-not/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$, determine if $N$ is a prime number in $\mathcal{O}(\sqrt{N})$ time.

---

## 1. Problem, Restated

Check if $N > 1$ has no positive divisors other than $1$ and $N$.

- **Input**: Parameters specified.
- **Output**: Result meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

If $N$ is composite, it must have at least one factor $\le \sqrt{N}$. If no factor is found in $[2, \sqrt{N}]$, $N$ is prime. Furthermore, all primes $> 3$ are of the form $6k \pm 1$. We can test divisibility by $2$ and $3$, then step by $6$ ($i$ and $i+2$), cutting trial divisions by $3\times$.

- **Underlying Pattern**: `Trial Division up to $\sqrt{N}$ with $6k \pm 1$ Optimization`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Check all $i \in [2, N-1]$ in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
bool isPrimeBrute(int n) {
    if (n <= 1) return false;
    for (int i = 2; i < n; i++) {
        if (n % i == 0) return false;
    }
    return true;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Fails for large prime checks.

---

## 4. Approach 2 — Better

### Idea
Standard trial division up to sqrt(N).

### C++17 Code
```cpp
bool isPrimeSqrt(int n) {
    if (n <= 1) return false;
    for (int i = 2; (long long)i * i <= n; i++) {
        if (n % i == 0) return false;
    }
    return true;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\sqrt{N})$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: Standard trial division.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
6k +/- 1 Wheel Factorization Trial Division: checks only potential prime candidates.

### C++17 Code
```cpp
using namespace std;

bool isPrime(int n) {
    if (n <= 1) return false;
    if (n <= 3) return true; // 2 and 3 are prime
    
    // Eliminate multiples of 2 and 3
    if (n % 2 == 0 || n % 3 == 0) return false;
    
    // Check numbers of form 6k +/- 1 up to sqrt(n)
    for (int i = 5; (long long)i * i <= n; i += 6) {
        if (n % i == 0 || n % (i + 2) == 0) {
            return false;
        }
    }
    
    return true;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\frac{\sqrt{N}}{3}) = \mathcal{O}(\sqrt{N})$ time ($3\times$ faster than standard trial division).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Skips all even numbers and multiples of 3.

---

## 6. Dry Run

Check $N = 29$

| Step | Action / State Change | Result |
|---|---|---|
| `Base tests` | $29 > 3$, $29 \% 2 \ne 0$, $29 \% 3 \ne 0$ | Passed |
| `Loop $i = 5$` | $5^2 = 25 \le 29$. $29 \% 5 \ne 0$, $29 \% 7 \ne 0$ | Passed |
| `Next $i = 11$` | $11^2 = 121 > 29$ | Loop terminates |
| `Result` | No factors found | Prime! ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N \le 1$ (neither prime nor composite $\implies$ false)
- $N = 2, 3$ (true)
- $N = 4$ (false).

### Common Bugs to Avoid
- Treating $0$ or $1$ as prime.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why are all primes > 3 of the form 6k +/- 1?**  
  **A**: Any integer can be written as $6k, 6k+1, 6k+2, 6k+3, 6k+4, 6k+5$. Notice: $6k, 6k+2, 6k+4$ are divisible by 2; $6k+3$ is divisible by 3. Thus, only $6k+1$ and $6k+5$ (which is $6(k+1)-1$) can be prime!


---

## 9. Tags & Related Problems

- **Tags**: `Maths`, `Prime`, `Number Theory`, `Easy`
- **Related problems to practice next**:
- **Print All Divisors**: Divisors.
- **Sieve of Eratosthenes**: Bulk prime generation.
