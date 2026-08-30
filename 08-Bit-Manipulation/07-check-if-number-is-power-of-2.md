# Check if a Number is Power of 2 (n & (n-1) == 0) (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Check if a Number is Power of 2 (n & (n-1) == 0)](https://takeuforward.org/bit-manipulation/check-if-a-number-is-power-of-2/)
- **Difficulty**: Easy
- **Statement**: Given an integer $n$, return `true` if it is a power of two ($n == 2^x$ for some integer $x$). Otherwise, return `false`.

---

## 1. Problem, Restated

Verify if $n > 0$ contains exactly one set bit in binary.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Every power of 2 has exactly ONE set bit in binary ($1 = 1_2, 2 = 10_2, 4 = 100_2, 8 = 1000_2$). Removing its only set bit via `n & (n - 1)` leaves 0! Thus, $n$ is a power of 2 if and only if $n > 0$ and `(n & (n - 1)) == 0`.

- **Underlying Pattern**: `Single Set Bit Verification (`n > 0 && (n & (n - 1)) == 0`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Repeated division by 2 in $\mathcal{O}(\log N)$ time.

### C++17 Code
```cpp
bool isPowerOfTwoLoop(int n) {
    if (n <= 0) return false;
    while (n % 2 == 0) n /= 2;
    return n == 1;
}
```

### Java Code
```java
class Solution {
    boolean isPowerOfTwoLoop(int n) {
        if (n <= 0) return false;
        while (n % 2 == 0) n /= 2;
        return n == 1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Iterative division loop.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Branchless Bitwise Identity in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class Solution {
public:
    bool isPowerOfTwo(int n) {
        return n > 0 && (n & (n - 1)) == 0;
    }
};
```

### Java Code
```java
class Solution {

    boolean isPowerOfTwo(int n) {
        return n > 0 && (n & (n - 1)) == 0;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ single cycle.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Instant bitwise check without loops.

---

## 6. Dry Run

$n = 16$ (`10000_2`)

| Step | Action / State Change | Result |
|---|---|---|
| `n > 0` | 16 > 0 | True |
| `16 & 15` | `10000 & 01111 = 00000` (0) | Zero -> Power of 2! ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n \le 0$ ($n = 0$, $n = -2147483648$ -> returns `false`).
- $n = 1$ ($2^0 = 1 \implies$ returns `true`).

### Common Bugs to Avoid
- Forgetting `n > 0` guard: for $n = 0$, `0 & -1 = 0` would incorrectly return true!

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does n = -2147483648 cause a bug without n > 0?**  
  **A**: `-2147483648` in 32-bit signed int has binary `1000...0000`. Subtracting 1 causes undefined signed underflow. The `n > 0` check safely rejects all negative numbers immediately.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Maths`, `LeetCode-231`, `Easy`
- **Related problems to practice next**:
- **Power of 3**: Maths check.
- **Power of 4**: Power of 2 with mask.
