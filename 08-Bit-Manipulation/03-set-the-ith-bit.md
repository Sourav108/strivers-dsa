# Set the i-th bit (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Set the i-th bit](https://takeuforward.org/bit-manipulation/set-the-ith-bit/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$ and an index $i$, set the $i^{\text{th}}$ bit of $N$ to 1 (if already 1, leave unchanged) and return the resulting integer.

---

## 1. Problem, Restated

Turn on bit $i$ using bitwise OR.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Bitwise OR with 1 always produces 1 (`x | 1 = 1`), while OR with 0 preserves the original bit (`x | 0 = x`). Applying `n | (1 << i)` sets the $i^{\text{th}}$ bit to 1 while leaving all other 31 bits untouched.

- **Underlying Pattern**: `Bitwise OR Masking (`n | (1 << i)`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Converting to binary string, replacing character at index, parsing back.

### C++17 Code
```cpp
// String conversion approach
```

### Java Code
```java
// Java equivalent
// String conversion approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(32)$.
- **Space Complexity**: $\mathcal{O}(32)$.
- **Why it's not good enough**: Heavy string allocations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bitwise OR Mask: `n | (1 << i)`.

### C++17 Code
```cpp
class Solution {
public:
    int setKthBit(int n, int i) {
        return n | (1 << i);
    }
};
```

### Java Code
```java
class Solution {

    int setKthBit(int n, int i) {
        return n | (1 << i);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Single CPU clock cycle.

---

## 6. Dry Run

$n = 9$ (`1001_2`), $i = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Mask `1 << 2`` | `0100_2` (4) | Mask ready |
| `OR operation` | `1001 | 0100 = 1101_2` (13) | Result = 13 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Bit already set ($9 | 1 = 9$).
- $i = 0$.

### Common Bugs to Avoid
- Shifting 32-bit integer when $i \ge 31$ (use `1LL << i` for 64-bit integers).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to set multiple bits simultaneously?**  
  **A**: Combine bitmasks using OR: `n | (1 << i) | (1 << j) | (1 << k)`.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Bitmask`, `Easy`
- **Related problems to practice next**:
- **Clear the i-th bit**: Inverse operation.
