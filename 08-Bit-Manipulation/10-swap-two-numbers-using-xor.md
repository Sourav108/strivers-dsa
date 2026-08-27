# Swap Two Numbers without Temp Variable (XOR swap) (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Swap Two Numbers without Temp Variable (XOR swap)](https://takeuforward.org/bit-manipulation/swap-two-numbers-using-xor/)
- **Difficulty**: Easy
- **Statement**: Given two integers $a$ and $b$, swap their values without using a third temporary variable.

---

## 1. Problem, Restated

In-place variable exchange using XOR self-cancellation properties.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

XOR property: `x ^ x = 0` and `x ^ 0 = x`. Step 1: `a = a ^ b`. Step 2: `b = a ^ b = (a ^ b) ^ b = a ^ (b ^ b) = a ^ 0 = a` (now $b$ holds original $a$). Step 3: `a = a ^ b = (a ^ b) ^ a = b ^ (a ^ a) = b ^ 0 = b` (now $a$ holds original $b$).

- **Underlying Pattern**: `XOR Self-Cancellation (`a = a ^ b; b = a ^ b; a = a ^ b;`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Standard temporary variable swap `int temp = a; a = b; b = temp;`.

### C++17 Code
```cpp
void swapTemp(int& a, int& b) {
    int temp = a;
    a = b;
    b = temp;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Uses extra temporary variable.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
3-Step XOR Swap Algorithm in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <utility>
using namespace std;

class Solution {
public:
    pair<int, int> get(int a, int b) {
        a = a ^ b;
        b = a ^ b; // b becomes original a
        a = a ^ b; // a becomes original b
        return {a, b};
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Pure register-level bitwise cancellation.

---

## 6. Dry Run

$a = 5, b = 7$

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1: a = a ^ b` | $a = 5 \oplus 7 = 2$ | a = 2, b = 7 |
| `Step 2: b = a ^ b` | $b = 2 \oplus 7 = 5$ | b = 5 (original a!) |
| `Step 3: a = a ^ b` | $a = 2 \oplus 5 = 7$ | a = 7 (original b!) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $a == b$ (works correctly).
- Aliasing bug: if `&a == &b` (swapping variable with itself via pointer zeros the variable!).

### Common Bugs to Avoid
- Applying XOR swap when `a` and `b` reference the same memory address `a = a ^ a` (results in 0).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does XOR swap fail if a and b point to the same memory location?**  
  **A**: If `&a == &b`, the first step `a = a ^ a` sets the memory location to 0, destroying the value for all subsequent steps.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `XOR`, `Easy`
- **Related problems to practice next**:
- **Introduction to Bitwise**: XOR properties.
