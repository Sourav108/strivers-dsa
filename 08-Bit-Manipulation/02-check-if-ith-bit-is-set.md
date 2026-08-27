# Check if the i-th bit is set or not (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check if the i-th bit is set or not](https://takeuforward.org/data-structure/check-if-the-i-th-bit-is-set-or-not/)
- **Difficulty**: Easy
- **Statement**: Given an integer $N$ and an index $i$ (0-indexed), return `true` if the $i^{\text{th}}$ bit of $N$ is set (1), and `false` otherwise.

---

## 1. Problem, Restated

Test if $(1 \ll i)$ bit is present in binary representation of $N$.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Two $\mathcal{O}(1)$ methods: 1) **Left Shift**: Create mask `1 << i`. Compute `(n & (1 << i)) != 0`. 2) **Right Shift**: Shift $N$ right by $i$ positions `n >> i`, then check least significant bit `((n >> i) & 1) == 1`.

- **Underlying Pattern**: `Left Shift Masking (`(n & (1 << i)) != 0`) / Right Shift (`((n >> i) & 1) == 1`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Repeated division by 2 in a loop to extract bits in $\mathcal{O}(i)$ time.

### C++17 Code
```cpp
bool checkBitLoop(int n, int i) {
    for (int step = 0; step < i; step++) n /= 2;
    return (n % 2 == 1);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(i)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Loop-based division is slower than single bitwise instruction.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bitwise Left Shift Masking in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class Solution {
public:
    bool checkKthBit(int n, int i) {
        // Method 1: Left shift mask
        return (n & (1 << i)) != 0;
        
        // Method 2: Right shift
        // return ((n >> i) & 1) == 1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Single CPU bitwise instruction.

---

## 6. Dry Run

$n = 13$ (`1101_2`), $i = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Mask `1 << 2`` | `0100_2` (val 4) | Mask constructed |
| `AND operation` | `1101 & 0100 = 0100` (4 != 0) | 2nd bit is SET -> Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $i = 0$ (LSB).
- $i = 31$ (MSB, use `1LL << i` for 64-bit).

### Common Bugs to Avoid
- Writing `(n & (1 << i)) == 1` (the result is `1 << i`, which is non-zero, not necessarily 1!). Always compare with `!= 0`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is (n & (1 << i)) == 1 a common bug?**  
  **A**: Because for $i=2$, `n & (1 << 2)` equals $4$, which evaluates to `4 == 1` (false)! Correct check is `(n & (1 << i)) != 0` or `((n >> i) & 1) == 1`.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Bitmask`, `Easy`
- **Related problems to practice next**:
- **Set the i-th bit**: Bit setting.
