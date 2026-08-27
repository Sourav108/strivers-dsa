# Find the Nth Root of an Integer M (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [GeeksforGeeks - Nth Root of a Number](https://practice.geeksforgeeks.org/problems/find-nth-root-of-m5843/1) | [TakeUForward](https://takeuforward.org/data-structure/nth-root-of-a-number-using-binary-search/)
- **Difficulty**: Easy
- **Statement**: You are given two positive integers $N$ and $M$. Find the integer $N^{\text{th}}$ root of $M$ (i.e. find an integer $x$ such that $x^N = M$). If no such integer exists (i.e. $\sqrt[N]{M}$ is not a whole number), return `-1`.

---

## 1. Problem, Restated

Find an integer $x$ whose $N$-th power is exactly equal to $M$ ($x^N = M$). If no exact integer exists, return `-1`.

- **Input**: Integer $N$ (the root degree, $1 \le N \le 30$), and integer $M$ ($1 \le M \le 10^9$).
- **Output**: The integer $x$ if $x^N == M$, otherwise `-1`.
- **Key Constraints**: $x^N$ can grow explosively (e.g. $10^{15} > \text{ULLONG\_MAX}$), so checking $x^N$ directly can easily overflow 64-bit integers unless protected by early cutoff.

---

## 2. Intuition & Pattern

### Monotonic Answer Space + Overflow-Safe Evaluation
The answer $x$ must lie in the discrete range $[1, M]$. Because the power function $f(x) = x^N$ is strictly monotonically increasing for $x \ge 1$, we can binary search the answer space $[1, M]$.

At each midpoint `mid`, we need to compare `mid^N` against `M`. To prevent 64-bit arithmetic overflow when `mid` and `N` are large, we use an **overflow-safe power comparator**:
```cpp
// Returns: 1 if mid^N == M, 2 if mid^N > M, 0 if mid^N < M
int checkPower(int mid, int n, int m) {
    long long ans = 1;
    for (int i = 1; i <= n; i++) {
        ans = ans * mid;
        if (ans > m) return 2; // Early exit: already exceeded M!
    }
    if (ans == m) return 1;
    return 0;
}
```

```
Search Space: [ 1 . . . . . . . . mid . . . . . . . . M ]
                                    |
                 +------------------+------------------+
                 |                  |                  |
           checkPower == 1    checkPower == 0    checkPower == 2
             (Found x!)         (mid^N < M)        (mid^N > M)
             return mid         low = mid + 1      high = mid - 1
```

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Iterate $i$ from $1$ upwards. Compute $i^N$. If $i^N == M$, return $i$. If $i^N > M$, stop and return `-1`.

### C++17 Code
```cpp
int nthRootLinear(int n, int m) {
    for (int i = 1; i <= m; i++) {
        long long val = 1;
        bool exceeded = false;
        for (int j = 1; j <= n; j++) {
            val *= i;
            if (val > m) {
                exceeded = true;
                break;
            }
        }
        if (val == m) return i;
        if (exceeded) break;
    }
    return -1;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M^{1/N} \cdot N)$ — in the worst case (when $N = 1$), this requires $\mathcal{O}(M)$ steps, which takes $10^9$ operations and triggers Time Limit Exceeded.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary memory.
- **Why it's not good enough**: Linear search fails to take advantage of binary search halving over the monotonic power curve.

---

## 4. Approach 2 — Better (Floating-Point Built-in `pow`)

### Idea
Compute $x = \text{round}(M^{1/N}) = \text{round}(\text{pow}(M, 1.0 / N))$. Then verify if $x^N == M$ using the power helper.

### C++17 Code
```cpp
#include <cmath>
using namespace std;

int nthRootBuiltin(int n, int m) {
    if (m == 1 || n == 1) return m;
    
    int candidate = (int)round(pow(m, 1.0 / n));
    
    // Verification pass to avoid floating-point precision errors
    long long ans = 1;
    for (int i = 1; i <= n; i++) {
        ans *= candidate;
        if (ans > m) break;
    }
    
    return (ans == m) ? candidate : -1;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ — dominated by verification multiplication.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still worth knowing Binary Search**: Standard floating-point `double` precision has 53 bits of significand ($pprox 15-17$ significant decimal digits). For large integer constraints ($M > 10^{15}$), floating-point roundoff errors produce wrong results.

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
Run binary search over the range `low = 1` to `high = m`. Compute `mid = low + (high - low) / 2`. Evaluate `checkPower(mid, n, m)`:
1. If `checkPower == 1`: exact root found, return `mid`.
2. If `checkPower == 0` ($mid^N < m$): search right `low = mid + 1`.
3. If `checkPower == 2` ($mid^N > m$): search left `high = mid - 1`.
4. If search space is exhausted without a match, return `-1`.

### C++17 Code
```cpp
using namespace std;

// Helper: 1 if mid^n == m, 2 if mid^n > m, 0 if mid^n < m
int checkPower(int mid, int n, int m) {
    long long ans = 1;
    for (int i = 1; i <= n; i++) {
        ans = ans * mid;
        if (ans > m) return 2; // overflow/excess early exit
    }
    if (ans == m) return 1;
    return 0;
}

int NthRoot(int n, int m) {
    int low = 1, high = m;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        int state = checkPower(mid, n, m);
        
        if (state == 1) {
            return mid;       // exact N-th root found!
        } else if (state == 0) {
            low = mid + 1;    // mid^n < m -> search right
        } else {
            high = mid - 1;   // mid^n > m -> search left
        }
    }
    
    return -1; // no integer N-th root exists
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot \log_2 M)$ — binary search takes $\log_2 M \le 30$ iterations, and each iteration does at most $N$ multiplications with early exit. Total operations $\approx 30 \times 30 = 900$, running in $< 1\text{ms}$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Performs pure integer arithmetic without floating-point inaccuracies and avoids memory allocations.

---

## 6. Dry Run

### Dry Run 1: $N = 3, M = 27$ (Exact Match)

| Iteration | `low` | `high` | `mid` | `checkPower(mid, 3, 27)` | Action | Search Window |
|:---:|:---:|:---:|:---:|:---:|---|---|
| **1** | `1` | `27` | `14` | $14^3 = 2744 > 27$ (Returns 2) | `high = mid - 1 = 13` | `[1..13]` |
| **2** | `1` | `13` | `7` | $7^3 = 343 > 27$ (Returns 2) | `high = mid - 1 = 6` | `[1..6]` |
| **3** | `1` | `6` | `3` | $3^3 = 27 == 27$ (Returns 1) | **Found root! Return 3** | ✅ Output: 3 |

### Dry Run 2: $N = 4, M = 69$ (No Exact Match)

| Iteration | `low` | `high` | `mid` | `checkPower(mid, 4, 69)` | Action | Next Window |
|:---:|:---:|:---:|:---:|:---:|---|---|
| **1** | `1` | `69` | `35` | $35^4 = 1500625 > 69$ (Returns 2) | `high = 34` | `[1..34]` |
| **2** | `1` | `34` | `17` | $17^4 > 69$ (Returns 2) | `high = 16` | `[1..16]` |
| **3** | `1` | `16` | `8` | $8^4 = 4096 > 69$ (Returns 2) | `high = 7` | `[1..7]` |
| **4** | `1` | `7` | `4` | $4^4 = 256 > 69$ (Returns 2) | `high = 3` | `[1..3]` |
| **5** | `1` | `3` | `2` | $2^4 = 16 < 69$ (Returns 0) | `low = mid + 1 = 3` | `[3..3]` |
| **6** | `3` | `3` | `3` | $3^4 = 81 > 69$ (Returns 2) | `high = mid - 1 = 2` | `low (3) > high (2)` |
| **Exit** | `3` | `2` | - | Search exhausted | **Return -1** | ✅ Output: -1 |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **$M = 1$**: Any root of 1 is 1 ($1^N = 1$). Returns `1`.
- **$N = 1$**: The 1st root of $M$ is $M$ ($M^1 = M$). Returns `M`.
- **$N = 30, M = 10^9$**: Handled safely because $2^{30} = 1073741824 > 10^9$, so the answer is strictly either $1$ or `-1`.

### Common Bugs to Avoid
- **Integer Overflow in Power Function**: Using standard `pow(mid, n)` or multiplying without checking `if (ans > m) return 2;`, which overflows 64-bit signed/unsigned types.
- **Returning Truncated Root instead of `-1`**: Unlike `mySqrt` which asks for $\lfloor \sqrt{n} floor$, Nth Root specifically requires an *exact integer root* or `-1`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does `checkPower` multiply step-by-step instead of using Fast Modular Exponentiation $\mathcal{O}(\log N)$?**  
  **A**: Because with $N \le 30$, linear multiplication takes at most 30 iterations and allows immediate early exit on the first step where `ans > m`. Fast Exponentiation computes powers like $mid^{16}$ in one step which might overflow 64-bit integers before the comparison can trigger.

- **Q2: How would you compute $\sqrt[N]{M}$ with floating-point precision up to $D$ decimal places?**  
  **A**: Run binary search on `double low = 1.0, high = m` with termination condition `while (high - low > 1e-7)`. At each step, compute `mid = (low + high) / 2.0` and `pow(mid, n)`. If `pow(mid, n) <= m`, `low = mid`; else `high = mid`. Return `low`.

- **Q3: What if $M$ can be negative and $N$ is odd?**  
  **A**: If $M < 0$ and $N$ is odd, $\sqrt[N]{M} = - \sqrt[N]{|M|}$. We compute the N-th root of $|M|$ and negate the result. If $M < 0$ and $N$ is even, no real root exists (imaginary number), return `-1`.

- **Q4: Can we narrow the initial search space upper bound from $M$ to a tighter value?**  
  **A**: Yes, for $N \ge 2$, $\sqrt[N]{M} \le \sqrt{M} \le M/2$ (for $M \ge 4$). Also, since $2^{30} > 10^9$, for $N \ge 30$ the only possible answers are $1$ or $2$.

- **Q5: What is the relationship between this and Newton's generalized N-th root method?**  
  **A**: Newton's method generalizes to $x_{k+1} = rac{1}{N} \left( (N-1)x_k + rac{M}{x_k^{N-1}} ight)$, which converges in $\mathcal{O}(\log \log M)$ steps.

---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Math`, `TakeUForward`, `Easy`
- **Related problems to practice next**:
  - **Find Square Root of Integer**: The $N = 2$ special case.
  - **Koko Eating Bananas (LeetCode 875)**: BS on minimum integer eating speed.
  - **Pow(x, n)**: Fast exponentiation counterpart.
