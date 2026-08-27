# Introduction to Bitwise Operators (AND, OR, XOR, NOT, Shifts) (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Introduction to Bitwise Operators (AND, OR, XOR, NOT, Shifts)](https://takeuforward.org/bit-manipulation/introduction-to-bitwise-operators/)
- **Difficulty**: Easy
- **Statement**: Explain the 6 fundamental bitwise operators in C++: Bitwise AND (`&`), Bitwise OR (`|`), Bitwise XOR (`^`), Bitwise NOT (`~`), Left Shift (`<<`), and Right Shift (`>>`), including truth tables, 2's complement representation, and CPU cycle performance.

---

## 1. Problem, Restated

Fundamental bitwise operators, mathematical bit masking primitives, and CPU hardware-level bit shifts.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Bitwise operators manipulate integer bits directly in registers in a single CPU cycle ($1\text{ clock cycle}$). Left shift `x << k` multiplies $x$ by $2^k$. Arithmetic right shift `x >> k` divides signed $x$ by $2^k$. XOR `x ^ y` has key identities: `x ^ x = 0`, `x ^ 0 = x`, and is both commutative and associative.

- **Underlying Pattern**: `Bitwise Boolean Logic & Hardware-Level Bit Shifts`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Simulating bitwise arithmetic via string binary conversions in $\mathcal{O}(32)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;
string toBinary(int n) {
    string s = "";
    for (int i = 31; i >= 0; i--) s += ((n >> i) & 1) ? '1' : '0';
    return s;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(32)$ string overhead.
- **Space Complexity**: $\mathcal{O}(32)$ memory.
- **Why it's not good enough**: Unnecessary string parsing.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct Native C++ Bitwise Operators in $\mathcal{O}(1)$ Hardware Instructions.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

void demonstrateBitwiseOperators(int a, int b) {
    cout << "a & b (AND):  " << (a & b) << "\n";
    cout << "a | b (OR):   " << (a | b) << "\n";
    cout << "a ^ b (XOR):  " << (a ^ b) << "\n";
    cout << "~a    (NOT):  " << (~a)    << "\n"; // Two's complement: -(a + 1)
    cout << "a << 2 (x4):  " << (a << 2) << "\n"; // Multiply by 2^2
    cout << "a >> 1 (/2):  " << (a >> 1) << "\n"; // Divide by 2^1
}

int main() {
    demonstrateBitwiseOperators(5, 3); // 5 = 101_2, 3 = 011_2
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ single clock cycle instruction.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Executed directly in CPU arithmetic logic units (ALU).

---

## 6. Dry Run

$a = 5$ (`101_2`), $b = 3$ (`011_2`)

| Step | Action / State Change | Result |
|---|---|---|
| ``5 & 3`` | `101 & 011 = 001` | `1` |
| ``5 | 3`` | `101 | 011 = 111` | `7` |
| ``5 ^ 3`` | `101 ^ 011 = 110` | `6` |
| ``~5`` | `-(5 + 1)` | `-6` |
| ``5 << 1`` | `1010_2` | `10` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Negative integers (Two's complement representation).
- Shifting by $\ge 32$ bits (undefined behavior in C++).

### Common Bugs to Avoid
- Operator precedence: writing `1 << i & n` instead of `(1 << i) & n` (bitwise operators have lower precedence than arithmetic).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is ~x equal to -(x + 1)?**  
  **A**: In Two's Complement representation used by modern CPUs, the negative of integer $x$ is defined as $-x = \sim x + 1$. Rearranging terms yields $\sim x = -x - 1 = -(x + 1)$.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Basics`, `Easy`
- **Related problems to practice next**:
- **Check i-th bit**: Bit masking.
