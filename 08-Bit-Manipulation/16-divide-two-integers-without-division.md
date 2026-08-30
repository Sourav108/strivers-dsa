# Divide Two Integers without Multiplication, Division or Mod (Step 8.2 — Interview Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Divide Two Integers without Multiplication, Division or Mod](https://takeuforward.org/bit-manipulation/divide-two-integers-without-using-multiplication-division-and-mod-operator/)
- **Difficulty**: Medium
- **Statement**: Given two integers `dividend` and `divisor`, divide two integers without using multiplication, division, and mod operator. Return quotient clamped to $[-2^{31}, 2^{31}-1]$.

---

## 1. Problem, Restated

Compute quotient using bitwise binary exponentiation subtraction in $\mathcal{O}(\log^2 N)$ time.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Any dividend can be written in binary decomposition: $\text{dividend} = \sum q_i (\text{divisor} \times 2^i)$. For each power $i$ from 31 down to 0: if $(\text{divisor} \ll i) \le \text{dividend}$, subtract $(\text{divisor} \ll i)$ from dividend and add $(1 \ll i)$ to the quotient. Runs in $\mathcal{O}(\log N)$ operations!

- **Underlying Pattern**: `Bitwise Left Shift Binary Subtraction ($d \cdot 2^i$)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Repeated subtraction: `while (dividend >= divisor) { dividend -= divisor; ans++; }`.

### C++17 Code
```cpp
// Repeated subtraction TLE for 2^31 / 1
```

### Java Code
```java
// Java equivalent
// Repeated subtraction TLE for 2^31 / 1
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\text{quotient})$ — $2 \times 10^9$ subtractions causes TLE.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Linear subtraction is too slow.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bitwise Left Shift Division with 64-bit Overflow Clamping.

### C++17 Code
```cpp
#include <climits>
#include <cmath>
using namespace std;

class Solution {
public:
    int divide(int dividend, int divisor) {
        // Overflow edge case: -2147483648 / -1 = 2147483648 > INT_MAX
        if (dividend == INT_MIN && divisor == -1) return INT_MAX;
        if (dividend == INT_MIN && divisor == 1) return INT_MIN;
        if (dividend == divisor) return 1;
        
        bool isNegative = (dividend < 0) ^ (divisor < 0);
        
        long long n = abs((long long)dividend);
        long long d = abs((long long)divisor);
        long long quotient = 0;
        
        while (n >= d) {
            int count = 0;
            // Find largest power of 2 such that (d << count) <= n
            while (n >= (d << (count + 1))) {
                count++;
            }
            quotient += (1LL << count);
            n -= (d << count);
        }
        
        if (isNegative) quotient = -quotient;
        
        if (quotient > INT_MAX) return INT_MAX;
        if (quotient < INT_MIN) return INT_MIN;
        
        return (int)quotient;
    }
};
```

### Java Code
```java
class Solution {

    int divide(int dividend, int divisor) {
        // Overflow edge case: -2147483648 / -1 = 2147483648 > Integer.MAX_VALUE
        if (dividend == Integer.MIN_VALUE && divisor == -1) return Integer.MAX_VALUE;
        if (dividend == Integer.MIN_VALUE && divisor == 1) return Integer.MIN_VALUE;
        if (dividend == divisor) return 1;
        
        boolean isNegative = (dividend < 0) ^ (divisor < 0);
        
        long n = Math.abs((long)dividend);
        long d = Math.abs((long)divisor);
        long quotient = 0;
        
        while (n >= d) {
            int count = 0;
            // Find largest power of 2 such that (d << count) <= n
            while (n >= (d << (count + 1))) {
                count++;
            }
            quotient += (1LL << count);
            n -= (d << count);
        }
        
        if (isNegative) quotient = -quotient;
        
        if (quotient > Integer.MAX_VALUE) return Integer.MAX_VALUE;
        if (quotient < Integer.MIN_VALUE) return Integer.MIN_VALUE;
        
        return (int)quotient;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log^2(\text{dividend}))$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Doubles divisor using left shifts, subtracting exponential chunks.

---

## 6. Dry Run

$dividend = 22, divisor = 3$

| Step | Action / State Change | Result |
|---|---|---|
| `Pass 1` | $3 \ll 2 = 12 \le 22$ (count=2) | quotient += 4, $n = 22 - 12 = 10$ |
| `Pass 2` | $3 \ll 1 = 6 \le 10$ (count=1) | quotient += 2, $n = 10 - 6 = 4$ |
| `Pass 3` | $3 \ll 0 = 3 \le 4$ (count=0) | quotient += 1, $n = 4 - 3 = 1$ |
| `Exit` | $n < 3$ -> Loop ends | Quotient = 4 + 2 + 1 = 7 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $dividend = \text{INT\_MIN}, divisor = -1$ (overflows to `INT_MAX`).
- $divisor = 1$.

### Common Bugs to Avoid
- Shifting past 31 bits without `1LL` cast (causes integer overflow).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does (dividend < 0) ^ (divisor < 0) determine the sign?**  
  **A**: XOR returns `true` if and only if exactly one operand is negative (different signs $\implies$ negative quotient; identical signs $\implies$ positive quotient).


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Binary Search`, `LeetCode-29`, `Medium`
- **Related problems to practice next**:
- **Pow(x, n)**: Binary exponentiation.
