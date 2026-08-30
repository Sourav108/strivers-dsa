# Check Armstrong Number (Step 1.4 — Know Basic Maths)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Check Armstrong Number](https://takeuforward.org/maths/check-if-a-number-is-armstrong-number-or-not/)
- **Difficulty**: Easy
- **Statement**: An Armstrong number of $K$ digits is an integer such that the sum of its digits raised to the $K$-th power equals the number itself (e.g. $153 = 1^3 + 5^3 + 3^3 = 1 + 125 + 27 = 153$). Check if $N$ is Armstrong.

---

## 1. Problem, Restated

Verify if $\sum_{i=1}^K d_i^K == N$ where $K = \lfloor \log_{10} N \rfloor + 1$.

- **Input**: Parameters specified.
- **Output**: Result meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Count digits $K$. 2) Extract each digit $d$, compute $d^K$, and accumulate into a sum. 3) Compare `sum == originalN`.

- **Underlying Pattern**: `Digit Power Sum Accumulation`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Standard digit power loop.

### C++17 Code
```cpp
#include <cmath>
using namespace std;
bool isArmstrongBrute(int n) {
    int k = (int)log10(n) + 1;
    int temp = n, sum = 0;
    while (temp > 0) {
        int d = temp % 10;
        sum += (int)pow(d, k);
        temp /= 10;
    }
    return sum == n;
}
```

### Java Code
```java
class Solution {
    boolean isArmstrongBrute(int n) {
        int k = (int)log10(n) + 1;
        int temp = n, sum = 0;
        while (temp > 0) {
            int d = temp % 10;
            sum += (int)Math.pow(d, k);
            temp /= 10;
        }
        return sum == n;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Floating-point `pow` can introduce roundoff bugs.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard mathematical / optimal algorithmic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Pure integer power multiplication without floating-point `pow`.

### C++17 Code
```cpp
using namespace std;

long long intPower(int base, int exp) {
    long long res = 1;
    for (int i = 0; i < exp; i++) res *= base;
    return res;
}

bool checkArmstrong(int n) {
    if (n < 0) return false;
    
    // Count digits
    int temp = n, k = 0;
    while (temp > 0) {
        k++;
        temp /= 10;
    }
    
    temp = n;
    long long sum = 0;
    while (temp > 0) {
        int d = temp % 10;
        sum += intPower(d, k);
        temp /= 10;
    }
    
    return sum == n;
}
```

### Java Code
```java
class Solution {
    long intPower(int base, int exp) {
        long res = 1;
        for (int i = 0; i < exp; i++) res *= base;
        return res;
    }
    
    boolean checkArmstrong(int n) {
        if (n < 0) return false;
        
        // Count digits
        int temp = n, k = 0;
        while (temp > 0) {
            k++;
            temp /= 10;
        }
        
        temp = n;
        long sum = 0;
        while (temp > 0) {
            int d = temp % 10;
            sum += intPower(d, k);
            temp /= 10;
        }
        
        return sum == n;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Avoids IEEE 754 floating-point inaccuracies.

---

## 6. Dry Run

Trace for $N = 371$ ($K = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Digit 1` | $1^3 = 1$ | sum = 1 |
| `Digit 7` | $7^3 = 343$ | sum = 344 |
| `Digit 3` | $3^3 = 27$ | sum = 371 |
| `Check` | 371 == 371 -> TRUE | Armstrong Number! ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 0$ to $9$ (all single-digit numbers are Armstrong: $d^1 = d$).
- Large Armstrong: 1634 ($1^4 + 6^4 + 3^4 + 4^4 = 1634$).

### Common Bugs to Avoid
- Hardcoding $K = 3$ (e.g. 1634 has $K = 4$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why avoid std::pow for integers?**  
  **A**: `std::pow` uses floating-point approximations where $5^3$ might evaluate to $124.999999$, casting to integer $124$ and failing precision tests.


---

## 9. Tags & Related Problems

- **Tags**: `Maths`, `Digits`, `Easy`
- **Related problems to practice next**:
- **Count Digits in a Number**: Digit count.
