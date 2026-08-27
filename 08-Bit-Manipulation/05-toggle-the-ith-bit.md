# Toggle the i-th bit (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Toggle the i-th bit](https://takeuforward.org/bit-manipulation/toggle-the-ith-bit/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$ and an index $i$, toggle the $i^{\text{th}}$ bit of $N$ (if it is 1, change to 0; if 0, change to 1) and return the resulting integer.

---

## 1. Problem, Restated

Flip bit $i$ using Bitwise XOR with `(1 << i)`.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

XOR with 1 flips the bit: `0 ^ 1 = 1` and `1 ^ 1 = 0`. XOR with 0 preserves the bit: `x ^ 0 = x`. Applying `n ^ (1 << i)` flips only the $i^{\text{th}}$ bit while leaving all other bits unchanged.

- **Underlying Pattern**: `Bitwise XOR Inversion (`n ^ (1 << i)`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Check bit with if-else, call set or clear bit function.

### C++17 Code
```cpp
int toggleIf(int n, int i) {
    if (n & (1 << i)) return n & ~(1 << i);
    else return n | (1 << i);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Unnecessary branching.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct XOR Mask: `n ^ (1 << i)` in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class Solution {
public:
    int toggleKthBit(int n, int i) {
        return n ^ (1 << i);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ single clock cycle.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Single XOR hardware instruction.

---

## 6. Dry Run

$n = 13$ (`1101_2`), $i = 1$ (bit is 0)

| Step | Action / State Change | Result |
|---|---|---|
| `Mask `1 << 1`` | `0010_2` | Mask ready |
| `XOR operation` | `1101 ^ 0010 = 1111_2` (15) | Toggled from 0 to 1 -> 15 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Toggling 1 to 0 ($15$ toggle bit 1 $\implies 13$).
- $i = 0$.

### Common Bugs to Avoid
- Using bitwise NOT instead of XOR.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does XOR behave as a toggle switch?**  
  **A**: Because $x \oplus 1 = 1 - x$, inverting boolean state dynamically without conditional logic.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Bitmask`, `Easy`
- **Related problems to practice next**:
- **Single Number I**: XOR cancellation.
