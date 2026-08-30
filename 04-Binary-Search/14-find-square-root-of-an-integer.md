# Find Square Root of an Integer using Binary Search (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [LeetCode #69 - Sqrt(x)](https://leetcode.com/problems/sqrtx/) | [TakeUForward](https://takeuforward.org/binary-search/finding-sqrt-of-a-number-using-binary-search/)
- **Difficulty**: Easy
- **Statement**: Given a non-negative integer $n$, compute and return the integer square root of $n$ (i.e. $\lfloor \sqrt{n} \rfloor$). The returned integer should be non-negative, truncated to an integer (not rounded up). You must not use any built-in exponent or square root function (such as `pow(x, 0.5)` or `sqrt(x)`).

---

## 1. Problem, Restated

Find the largest integer $m$ such that $m \times m \le n$. For example, if $n = 28$, $\sqrt{28} \approx 5.2915$, so the truncated integer answer is $5$ because $5^2 = 25 \le 28$ and $6^2 = 36 > 28$.

- **Input**: A non-negative integer $n$ ($0 \le n \le 2^{31} - 1$).
- **Output**: The integer square root $\lfloor \sqrt{n} \rfloor$.
- **Key Constraints**: $n$ fits in a 32-bit signed integer; operations like $m \times m$ can exceed $2^{31}-1$, requiring overflow prevention.

---

## 2. Intuition & Pattern

### The Monotonic Answer Space Pattern
Unlike previous problems where we searched an explicit input array, here **there is no input array**. Instead, the search space is the conceptual range of all possible integer answers: $[1, 2, 3, \dots, n]$.

Because multiplication is monotonically increasing for positive numbers ($1^2 < 2^2 < 3^2 < \dots$), the predicate $f(m) = (m^2 \le n)$ divides the number line into two contiguous zones:
- `[true, true, ..., true, false, false, ...]`

We want to find the **LAST `true`** (the largest integer $m$ where $m^2 \le n$).

```
Search Space for n = 28:
m:        1    2    3    4    5  |   6    7   ...
m^2:      1    4    9   16   25  |  36   49   ...
f(m)<=28: T    T    T    T    T  |   F    F   ...
                              ^
                       Last 'true' = 5
```

At any midpoint `mid`:
- If `mid * mid <= n`: `mid` is a valid candidate. We record `ans = mid` and search the **right half** (`low = mid + 1`) to see if a larger valid integer exists.
- If `mid * mid > n`: `mid` and all numbers greater than `mid` are too large. We eliminate them and search the **left half** (`high = mid - 1`).

---

## 3. Approach 1 — Brute Force (Linear Search)

### Idea
Iterate $i$ from $1$ upwards. Compute $i \times i$. As long as $i \times i \le n$, update `ans = i`. The moment $i \times i > n$, stop and return `ans`.

### C++17 Code
```cpp
using namespace std;

int mySqrtLinear(int n) {
    if (n == 0) return 0;
    
    int ans = 1;
    for (long long i = 1; i * i <= (long long)n; i++) {
        ans = (int)i;
    }
    return ans;
}
```

### Java Code
```java
class Solution {
    int mySqrtLinear(int n) {
        if (n == 0) return 0;
        
        int ans = 1;
        for (long i = 1; i * i <= (long)n; i++) {
            ans = (int)i;
        }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\sqrt{n})$ — the loop runs $\lfloor \sqrt{n} \rfloor$ times. For $n = 2^{31}-1 \approx 2.14 \times 10^9$, $\sqrt{n} \approx 46,340$ operations (acceptable for 32-bit $n$, but $\mathcal{O}(\sqrt{n})$ for 64-bit integers $n = 10^{18}$ takes $10^9$ operations and causes TLE).
- **Space Complexity**: $\mathcal{O}(1)$ — uses only scalar integer variables.
- **Why it's not good enough**: Linear search steps by $1$ through the answer space, failing to exploit the monotonic order that permits logarithmic halving.

---

## 4. Approach 2 — Better (Newton-Raphson Method)

### Idea
Apply the Newton-Raphson numerical method for finding roots of $f(x) = x^2 - n = 0$. The iterative update rule is:
$$x_{k+1} = \frac{1}{2} \left( x_k + \frac{n}{x_k} \right)$$
Starting with an initial guess $x_0 = n$, this sequence converges quadratically to $\sqrt{n}$. For integers, we iterate while $x_{k+1} < x_k$.

### C++17 Code
```cpp
using namespace std;

int mySqrtNewton(int n) {
    if (n == 0) return 0;
    
    long long x = n;
    while (x * x > n) {
        x = (x + n / x) / 2;
    }
    return (int)x;
}
```

### Java Code
```java
class Solution {
    int mySqrtNewton(int n) {
        if (n == 0) return 0;
        
        long x = n;
        while (x * x > n) {
            x = (x + n / x) / 2;
        }
        return (int)x;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log \log n)$ — quadratic convergence doubles the number of accurate digits each iteration (terminates in $\le 6$ iterations for 32-bit integers).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's still worth knowing Binary Search**: While Newton's method is faster for square roots, Binary Search on Answers is a universal meta-technique that generalizes to non-algebraic predicates (e.g. *Koko Eating Bananas*, *Capacity to Ship Packages*, *Aggressive Cows*).

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
The answer for $n \ge 1$ always lies within the discrete interval $[1, n]$. For $n \ge 4$, $\sqrt{n} \le n/2$, so the search space can be further narrowed to $[1, n/2]$.
1. Handle base cases: if $n == 0$, return $0$.
2. Set `low = 1, high = n` (or `n / 2`).
3. While `low <= high`:
   - Compute `mid = low + (high - low) / 2`.
   - If `(long long)mid * mid <= n`: record `ans = mid`, search right `low = mid + 1`.
   - Else: search left `high = mid - 1`.
4. Return `ans` (or equivalently return `high` at loop termination).

### C++17 Code
```cpp
using namespace std;

int mySqrt(int n) {
    if (n == 0) return 0;
    
    int low = 1;
    int high = n;
    int ans = 1;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        
        // Use 64-bit integer to prevent arithmetic overflow in mid * mid
        long long sq = (long long)mid * mid;
        
        if (sq <= n) {
            ans = mid;       // candidate found, look for larger integer on right
            low = mid + 1;
        } else {
            high = mid - 1;  // mid^2 > n, look on left
        }
    }
    
    return ans;
}
```

### Java Code
```java
class Solution {
    int mySqrt(int n) {
        if (n == 0) return 0;
        
        int low = 1;
        int high = n;
        int ans = 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            // Use 64-bit integer to prevent arithmetic overflow in mid * mid
            long sq = (long)mid * mid;
            
            if (sq <= n) {
                ans = mid;       // candidate found, look for larger integer on right
                low = mid + 1;
            } else {
                high = mid - 1;  // mid^2 > n, look on left
            }
        }
        
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — search space $[1..n]$ is halved in every comparison (at most $31$ iterations for $n = 2^{31}-1$).
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary memory.
- **Why this is optimal**: Information theory bounds comparison-based search over an interval of size $n$ to $\Omega(\log n)$ steps.

---

## 6. Dry Run

Dry Run for $n = 28$:

| Iteration | `low` | `high` | `mid` | `mid * mid` | `sq <= 28` | Action / State Update | Remaining Window |
|:---:|:---:|:---:|:---:|:---:|:---:|---|---|
| **1** | `1` | `28` | `14` | `196` | `196 <= 28` (False) | `high = mid - 1 = 13` | `[1..13]` |
| **2** | `1` | `13` | `7` | `49` | `49 <= 28` (False) | `high = mid - 1 = 6` | `[1..6]` |
| **3** | `1` | `6` | `3` | `9` | `9 <= 28` (True) | `ans = 3, low = mid + 1 = 4` | `[4..6]` |
| **4** | `4` | `6` | `5` | `25` | `25 <= 28` (True) | `ans = 5, low = mid + 1 = 6` | `[6..6]` |
| **5** | `6` | `6` | `6` | `36` | `36 <= 28` (False) | `high = mid - 1 = 5` | `low (6) > high (5)` |
| **Exit** | `6` | `5` | - | - | - | **Return `ans = 5`** | ✅ Correct ($\lfloor \sqrt{28} 
floor = 5$) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **$n = 0$**: Returns `0` immediately via base case check.
- **$n = 1$**: `low=1, high=1, mid=1` $\implies 1^2 \le 1 \implies$ returns `1`.
- **Large Input ($n = 2^{31}-1 = 2147483647$)**: `mid` can be up to $\approx 10^9$. Calculating `mid * mid` in 32-bit signed integer produces $10^{18} \pmod{2^{32}}$, causing integer overflow. Handled by casting to `(long long)mid * mid`.
- **Perfect Squares ($n = 16, 25, 100$)**: Correctly terminates with the exact square root.

### Common Bugs to Avoid
- **Integer Overflow**: Writing `if (mid * mid <= n)` without casting `mid` to `long long`. Alternatively, write `if (mid <= n / mid)` to stay entirely within 32-bit integer arithmetic.
- **Wrong Search Direction on Match**: When `sq <= n`, setting `high = mid - 1` instead of `low = mid + 1` (we are looking for the *largest* valid integer, so we must explore the right side).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How can we implement this strictly in 32-bit integers without `long long` or division-by-zero risks?**  
  **A**: Rewrite the condition `mid * mid <= n` as `mid <= n / mid`. Since `low >= 1`, `mid` is always $\ge 1$, guaranteeing `n / mid` never divides by zero. This runs safely on embedded 32-bit MCUs with no 64-bit register support.

- **Q2: How would you compute $\sqrt{n}$ with precision up to $D$ decimal places (floating-point square root)?**  
  **A**: Use binary search on real numbers: set `low = 0.0, high = n` (or `max(1.0, n)` if $n < 1$). While `high - low > 1e-7` (for 6 decimal places), compute `mid = (low + high) / 2.0`. If `mid * mid <= n`, `low = mid`; else `high = mid`. Return `low`.

- **Q3: How does the Fast Inverse Square Root (`0x5f3759df`) algorithm work in Quake III?**  
  **A**: It computes $1/\sqrt{x}$ for 32-bit IEEE 754 floats by: 1) interpreting float bits as integer, 2) performing a bit-shift and subtracting from magic constant `0x5f3759df` to produce an initial guess accurate to $\approx 3\%$, and 3) running one iteration of Newton's method in $\mathcal{O}(1)$ hardware time.

- **Q4: How does this problem serve as the foundation for the entire "Binary Search on Answers" paradigm?**  
  **A**: It introduces the core concept: whenever the feasibility function $\text{isValid}(x)$ is monotonic (all `true` followed by all `false`, or vice versa), the optimal value can be found in $\mathcal{O}(\log(\text{search space}) \times \text{cost of validation})$ time.

- **Q5: Can `high` be initialized to `n / 2` instead of `n`?**  
  **A**: Yes, for $n \ge 4$, $(n/2)^2 = n^2/4 \ge n$. So $\sqrt{n} \le n/2$. For $n < 4$, the answer is either $0$ (for $n=0$) or $1$ (for $n=1, 2, 3$), which can be handled as early edge cases.

---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Math`, `LeetCode-69`, `Easy`
- **Related problems to practice next**:
  - **Nth Root of an Integer**: Generalizes to finding $\lfloor \sqrt[N]{M} \rfloor$.
  - **Koko Eating Bananas**: BS on minimum eating speed.
  - **Capacity to Ship Packages**: BS on minimum ship capacity.
