# Count Number of Bits to be Flipped to Convert A to B (Step 8.2 — Interview Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Count Number of Bits to be Flipped to Convert A to B](https://takeuforward.org/data-structure/count-number-of-bits-to-be-flipped-to-convert-a-to-b/)
- **Difficulty**: Easy
- **Statement**: Given two integers $start$ and $goal$, return the minimum number of bit flips to convert $start$ to $goal$ (Hamming Distance).

---

## 1. Problem, Restated

Compute the count of differing bits: `__builtin_popcount(start ^ goal)`.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

XOR outputs 1 wherever bits differ between $start$ and $goal$ (`0 ^ 1 = 1` and `1 ^ 0 = 1`). Compute `diff = start ^ goal`, then return the number of set bits in `diff` using `__builtin_popcount(diff)` in $\mathcal{O}(1)$ time.

- **Underlying Pattern**: `XOR Difference Masking + Population Count`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Loop through all 32 bits comparing `(start >> i & 1) != (goal >> i & 1)`.

### C++17 Code
```cpp
int minBitFlipsLoop(int start, int goal) {
    int flips = 0;
    for (int i = 0; i < 32; i++) {
        if (((start >> i) & 1) != ((goal >> i) & 1)) flips++;
    }
    return flips;
}
```

### Java Code
```java
class Solution {
    int minBitFlipsLoop(int start, int goal) {
        int flips = 0;
        for (int i = 0; i < 32; i++) {
            if (((start >> i) & 1) != ((goal >> i) & 1)) flips++;
        }
        return flips;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(32)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Iterates 32 times.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
XOR Difference + Popcount in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class Solution {
public:
    int minBitFlips(int start, int goal) {
        // XOR gives 1 at all differing bit positions
        return __builtin_popcount(start ^ goal);
    }
};
```

### Java Code
```java
class Solution {

    int minBitFlips(int start, int goal) {
        // XOR gives 1 at all differing bit positions
        return __builtin_popcount(start ^ goal);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ single hardware instruction.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Hamming distance computed in two assembly instructions (`xor` + `popcnt`).

---

## 6. Dry Run

$start = 10$ (`1010_2`), $goal = 7$ (`0111_2`)

| Step | Action / State Change | Result |
|---|---|---|
| `XOR` | `1010 ^ 0111 = 1101_2` (13) | Differing bits = 13 |
| `Popcount` | Set bits in `1101_2` = 3 | Flips required = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $start == goal$ (XOR is 0 $\implies$ 0 flips).
- $start = 0, goal = 15$ (4 flips).

### Common Bugs to Avoid
- Using bitwise AND instead of XOR.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the formal name of this metric in computer science?**  
  **A**: It is called the **Hamming Distance**, widely used in error-correcting codes, cryptography, and network packet validation.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Hamming Distance`, `LeetCode-2220`, `Easy`
- **Related problems to practice next**:
- **Count Set Bits**: Popcount.
