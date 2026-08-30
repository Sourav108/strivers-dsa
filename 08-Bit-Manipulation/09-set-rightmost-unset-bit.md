# Set / Unset the Rightmost Unset Bit (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Set / Unset the Rightmost Unset Bit](https://takeuforward.org/bit-manipulation/set-rightmost-unset-bit/)
- **Difficulty**: Easy
- **Statement**: Given a non-negative integer $N$, set the rightmost unset bit (the least significant 0-bit) to 1. If all bits are already 1, leave $N$ unchanged.

---

## 1. Problem, Restated

Turn on the lowest 0-bit using bitwise OR with `(N + 1)`.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Adding 1 to $N$ changes the rightmost 0-bit into a 1 and sets all trailing 1s to 0s. Example: $9 = 1001_2 \implies 10 = 1010_2$. Performing `n | (n + 1)` sets that 0-bit to 1 while preserving all other bits: `1001 | 1010 = 1011_2` (11).

- **Underlying Pattern**: `Rightmost 0-Bit Setting (`n | (n + 1)`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Loop through bits finding first 0-bit, then set it.

### C++17 Code
```cpp
int setRightmost0Loop(int n) {
    for (int i = 0; i < 32; i++) {
        if (!(n & (1 << i))) return n | (1 << i);
    }
    return n;
}
```

### Java Code
```java
class Solution {
    int setRightmost0Loop(int n) {
        for (int i = 0; i < 32; i++) {
            if (!(n & (1 << i))) return n | (1 << i);
        }
        return n;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(32)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Unnecessary loop.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Branchless Bitwise Identity: `n | (n + 1)` in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class Solution {
public:
    int setBit(int N) {
        return N | (N + 1);
    }
};
```

### Java Code
```java
class Solution {

    int setBit(int N) {
        return N | (N + 1);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ single clock cycle.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Single bitwise OR instruction.

---

## 6. Dry Run

$N = 9$ (`1001_2`)

| Step | Action / State Change | Result |
|---|---|---|
| `N` | `1001_2` (9) | Bit 1 is lowest 0-bit |
| `N + 1` | `1010_2` (10) | Bit 1 became 1 |
| ``N | (N + 1)`` | `1001 | 1010 = 1011_2` (11) | Rightmost 0-bit set ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 7$ (`111_2` $\implies 7 | 8 = 15 = 1111_2$).
- $N = 0$ ($0 | 1 = 1$).

### Common Bugs to Avoid
- Confusing with set bit removal (`n & (n - 1)`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does N | (N + 1) set the rightmost unset bit?**  
  **A**: Because $(N + 1)$ carries through all trailing 1s until it hits the first 0-bit, flipping it to 1. OR-ing with original $N$ combines the newly flipped 1 with original trailing 1s.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Bitmask`, `Easy`
- **Related problems to practice next**:
- **Remove Last Set Bit**: Complementary identity.
