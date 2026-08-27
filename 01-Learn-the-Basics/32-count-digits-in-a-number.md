# Count Digits in a Number (Step 1.4 — Know Basic Maths)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Count Digits in a Number](https://takeuforward.org/data-structure/count-digits-in-a-number/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$, return the total number of digits in $N$.

---

## 1. Problem, Restated

Find the length of the base-10 representation of $N$.

- **Input**: Parameters specified.
- **Output**: Result meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Dividing $N$ by $10$ strips off the least significant digit. The loop runs $\lfloor \log_{10} N \rfloor + 1$ times. Alternatively, the closed-form formula $\lfloor \log_{10} N \rfloor + 1$ computes the answer directly in $\mathcal{O}(1)$ using floating-point math.

- **Underlying Pattern**: `Logarithmic Base-10 Arithmetic / Division`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
String Conversion: Convert $N$ to `std::to_string(n)` and return `.length()` in $\mathcal{O}(\log_{10} N)$ time with extra string memory.

### C++17 Code
```cpp
#include <string>
using namespace std;
int countDigitsString(int n) {
    if (n == 0) return 1;
    return to_string(abs(n)).length();
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} N)$ time.
- **Space Complexity**: $\mathcal{O}(\log_{10} N)$ string allocation.
- **Why it's not good enough**: Allocates unnecessary heap memory.

---

## 4. Approach 2 — Better

### Idea
Iterative division by 10 in O(1) space.

### C++17 Code
```cpp
int countDigitsIterative(int n) {
    if (n == 0) return 1;
    int count = 0;
    n = abs(n);
    while (n > 0) {
        count++;
        n /= 10;
    }
    return count;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: Iterative loop.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Logarithmic Formula: `(int)(log10(abs(n))) + 1` in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
#include <cmath>
using namespace std;

int countDigits(int n) {
    if (n == 0) return 1;
    return (int)log10(abs(n)) + 1;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct mathematical evaluation.

---

## 6. Dry Run

$N = 7789$

| Step | Action / State Change | Result |
|---|---|---|
| `log10(7789)` | 3.89148 | floor(3.89148) = 3 |
| `Result` | 3 + 1 = 4 | Total digits = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- N = 0 (returns 1)
- Negative numbers $N < 0$ (use `abs(N)`)
- INT_MIN (handle `-2147483648` with `unsigned` cast).

### Common Bugs to Avoid
- Calling `log10(0)` which is undefined ($-\infty$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is digit count equal to floor(log10(N)) + 1?**  
  **A**: Any integer $N$ with $d$ digits satisfies $10^{d-1} \le N < 10^d$. Taking $\log_{10}$ yields $d - 1 \le \log_{10} N < d$, so $\lfloor \log_{10} N \rfloor = d - 1$, meaning $d = \lfloor \log_{10} N \rfloor + 1$.


---

## 9. Tags & Related Problems

- **Tags**: `Maths`, `Digits`, `Easy`
- **Related problems to practice next**:
- **Reverse a Number**: Digit extraction.
