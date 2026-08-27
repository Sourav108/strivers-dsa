# Clear the i-th bit (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Clear the i-th bit](https://takeuforward.org/bit-manipulation/clear-the-ith-bit/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$ and an index $i$, clear the $i^{\text{th}}$ bit of $N$ (set it to 0) and return the resulting integer.

---

## 1. Problem, Restated

Turn off bit $i$ using Bitwise AND with inverted mask `~(1 << i)`.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

To turn off only the $i^{\text{th}}$ bit, we need a mask that has `0` at position $i$ and `1` at all other 31 positions. The mask `1 << i` has `1` at position $i$. Inverting it `~(1 << i)` gives `111...0...111`. Bitwise AND `n & ~(1 << i)` forces the $i^{\text{th}}$ bit to 0 while preserving all other bits.

- **Underlying Pattern**: `Bitwise Inverted AND Masking (`n & ~(1 << i)`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Check if bit is set, subtract $2^i$ if present.

### C++17 Code
```cpp
int clearBitSub(int n, int i) {
    if (n & (1 << i)) n -= (1 << i);
    return n;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Requires conditional branch.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Branchless Inverted Mask: `n & ~(1 << i)`.

### C++17 Code
```cpp
class Solution {
public:
    int clearKthBit(int n, int i) {
        return n & ~(1 << i);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Single branchless ALU instruction sequence.

---

## 6. Dry Run

$n = 13$ (`1101_2`), $i = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Mask `1 << 2`` | `000...0100` | Shift |
| `Invert `~mask`` | `111...1011` | Inverted mask |
| `AND operation` | `1101 & 1011 = 1001_2` (9) | Result = 9 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Bit already 0 ($13$ clear bit 1 $\implies 13$).
- $i = 0$.

### Common Bugs to Avoid
- Operator precedence: `n & ~1 << i` (without parentheses, `~1` evaluates to `-2` first). Must write `~(1 << i)`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is branchless n & ~(1 << i) better than if-statement subtraction?**  
  **A**: Branchless code eliminates CPU branch mispredictions, running in deterministic single clock cycle.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Bitmask`, `Easy`
- **Related problems to practice next**:
- **Toggle the i-th bit**: Bit flipping.
