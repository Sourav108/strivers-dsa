# Find XOR of Numbers from Range L to R in O(1) (Step 8.2 — Interview Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find XOR of Numbers from Range L to R in O(1)](https://takeuforward.org/bit-manipulation/xor-of-numbers-in-a-given-range/)
- **Difficulty**: Medium
- **Statement**: Given two integers $L$ and $R$, find the XOR sum of all integers in the range $[L, R]$ ($L \oplus (L+1) \oplus \dots \oplus R$) in strict $\mathcal{O}(1)$ time.

---

## 1. Problem, Restated

Compute $\text{XOR}(L, R) = \text{XOR}(0, R) \oplus \text{XOR}(0, L-1)$ using the modulo-4 periodicity theorem.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Notice the cumulative XOR from 1 to $N$ follows a repeating pattern of length 4: 1) if $N \% 4 == 0 \implies N$, 2) if $N \% 4 == 1 \implies 1$, 3) if $N \% 4 == 2 \implies N + 1$, 4) if $N \% 4 == 3 \implies 0$. By XOR prefix subtraction: $\text{XOR}(L, R) = \text{XOR}(0, R) \oplus \text{XOR}(0, L-1)$ in $\mathcal{O}(1)$ time!

- **Underlying Pattern**: `Modulo-4 Cyclic XOR Pattern ($	ext{XOR}(0, N)$ repeats every 4 numbers)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Loop from $L$ to $R$ XOR-ing elements in $\mathcal{O}(R - L)$ time ($R = 10^9 \implies$ TLE).

### C++17 Code
```cpp
int findXORLoop(int l, int r) {
    int ans = 0;
    for (int i = l; i <= r; i++) ans ^= i;
    return ans;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R - L)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Fails for large ranges.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Modulo-4 Formula in $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class Solution {
private:
    int findXORFrom1ToN(int n) {
        if (n % 4 == 0) return n;
        if (n % 4 == 1) return 1;
        if (n % 4 == 2) return n + 1;
        return 0; // n % 4 == 3
    }

public:
    int findXOR(int l, int r) {
        // XOR(l, r) = XOR(0, r) ^ XOR(0, l - 1)
        return findXORFrom1ToN(r) ^ findXORFrom1ToN(l - 1);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ strict time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Mathematical modulo reduction eliminates loop entirely.

---

## 6. Dry Run

$L = 4, R = 8$

| Step | Action / State Change | Result |
|---|---|---|
| `XOR(0, 8)` | $8 \% 4 == 0 \implies 8$ | XOR(0, 8) = 8 |
| `XOR(0, 3)` | $3 \% 4 == 3 \implies 0$ | XOR(0, 3) = 0 |
| `Result` | $8 \oplus 0 = 8$ | Result = 8 (`4^5^6^7^8 = 8`) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $L = 0$ or $L = 1$ (`findXORFrom1ToN(0) = 0`).
- $L == R$.

### Common Bugs to Avoid
- Calling `findXORFrom1ToN(l)` instead of `findXORFrom1ToN(l - 1)` (incorrectly cancels $L$ out of the range).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does XOR(0, N) repeat every 4 numbers?**  
  **A**: Because consecutive pairs $(2k) \oplus (2k+1) = 1$. Two consecutive 1s cancel out: $1 \oplus 1 = 0$. Thus, every block of 4 consecutive numbers $(4k) \oplus (4k+1) \oplus (4k+2) \oplus (4k+3) = 0$! This resets the cumulative XOR to 0 every 4 numbers.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Maths`, `XOR`, `Medium`
- **Related problems to practice next**:
- **Single Number I**: XOR cancellation.
