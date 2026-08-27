# Remove the Last Set Bit (Rightmost set bit unset) (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Remove the Last Set Bit (Rightmost set bit unset)](https://takeuforward.org/bit-manipulation/remove-the-last-set-bit/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$, unset (turn off) the rightmost set bit (least significant 1-bit) in $\mathcal{O}(1)$ time.

---

## 1. Problem, Restated

Clear the lowest set bit using the identity `n & (n - 1)`.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Subtracting 1 from $N$ flips the lowest set bit to 0 and turns all trailing 0s to 1s. Example: $12 = 1100_2 \implies 11 = 1011_2$. Computing `n & (n - 1)` preserves all leading bits while forcing the lowest set bit and all trailing bits to 0: `1100 & 1011 = 1000_2` (8).

- **Underlying Pattern**: `Brian Kernighan's Bit Identity (`n & (n - 1)`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Find lowest set bit with a loop, then clear it.

### C++17 Code
```cpp
int removeLastBitLoop(int n) {
    for (int i = 0; i < 32; i++) {
        if (n & (1 << i)) return n ^ (1 << i);
    }
    return n;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(32)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Loop is unnecessary.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bitwise Identity: `n & (n - 1)` in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class Solution {
public:
    int removeLastSetBit(int n) {
        return n & (n - 1);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ single clock cycle.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Single bitwise AND arithmetic instruction.

---

## 6. Dry Run

$n = 12$ (`1100_2`)

| Step | Action / State Change | Result |
|---|---|---|
| `n` | `1100_2` (12) | Binary |
| `n - 1` | `1011_2` (11) | Lowest set bit flipped |
| ``n & (n - 1)`` | `1100 & 1011 = 1000_2` (8) | Lowest set bit removed ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 0$ (`0 & -1 = 0`).
- Power of 2 ($8 \implies 0$).

### Common Bugs to Avoid
- Confusing `n & (n - 1)` (removes lowest set bit) with `n & -n` (extracts ONLY the lowest set bit).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the difference between n & (n-1) and n & -n?**  
  **A**: `n & (n-1)` REMOVES the rightmost set bit. `n & -n` ISOLATES the rightmost set bit (used in Fenwick / Binary Indexed Trees).


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Brian Kernighan`, `Easy`
- **Related problems to practice next**:
- **Power of 2**: Uses n & (n-1).
- **Count Set Bits**: Uses n & (n-1).
