# Count the Number of Set Bits (Brian Kernighan's Algorithm) (Step 8.1 — Learn Bit Manipulation)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Count the Number of Set Bits (Brian Kernighan's Algorithm)](https://takeuforward.org/data-structure/count-number-of-bits-to-be-flipped-to-convert-a-to-b/)
- **Difficulty**: Easy
- **Statement**: Given a positive integer $N$, count the number of set bits (1s) in its binary representation in $\mathcal{O}(\text{set\_bits})$ time.

---

## 1. Problem, Restated

Compute population count (Hamming weight) using Brian Kernighan's algorithm.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Instead of checking all 32 bits, `n = n & (n - 1)` removes one set bit per iteration. The loop runs EXACTLY $K$ times where $K$ is the number of set bits (e.g. for $N = 1024 = 10000000000_2$, it runs only 1 iteration!). In modern C++, `__builtin_popcount(n)` maps directly to the hardware CPU instruction `POPCNT` in $\mathcal{O}(1)$ time.

- **Underlying Pattern**: `Brian Kernighan's Set Bit Unsetting Loop (`n = n & (n - 1)`)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Loop through all 32 bits checking `(n >> i) & 1` in $\mathcal{O}(32)$ iterations.

### C++17 Code
```cpp
int countSetBitsLinear(int n) {
    int count = 0;
    while (n > 0) { count += (n & 1); n >>= 1; }
    return count;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int countSetBitsLinear(int n) {
        int count = 0;
        while (n > 0) { count += (n 1); n >>= 1; }
        return count;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log N)$ time (runs for total bit length).
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Visits 0-bits unnecessarily.

---

## 4. Approach 2 — Better

### Idea
Brian Kernighan's Algorithm in O(set_bits).

### C++17 Code
```cpp
int countSetBitsKernighan(int n) {
    int count = 0;
    while (n > 0) {
        n = n & (n - 1);
        count++;
    }
    return count;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int countSetBitsKernighan(int n) {
        int count = 0;
        while (n > 0) {
            n = n & (n - 1);
            count++;
        }
        return count;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K)$ where $K = \text{set bits}$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's still not optimal**: Excellent algorithmic approach.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Hardware Intrinsic `__builtin_popcount` in $\mathcal{O}(1)$ CPU instruction.

### C++17 Code
```cpp
#include <iostream>
using namespace std;

class Solution {
public:
    int setBits(int N) {
        // Method 1: Hardware POPCNT instruction O(1)
        return __builtin_popcount(N);
        
        // Method 2: Brian Kernighan's algorithm O(K)
        /*
        int count = 0;
        while (N > 0) {
            N = N & (N - 1);
            count++;
        }
        return count;
        */
    }
};
```

### Java Code
```java
class Solution {

    int setBits(int N) {
        // Method 1: Hardware POPCNT instruction O(1)
        return __builtin_popcount(N);
        
        // Method 2: Brian Kernighan's algorithm O(K)
        /*
        int count = 0;
        while (N > 0) {
            N = N & (N - 1);
            count++;
        }
        return count;
        */
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ hardware CPU instruction (`POPCNT`).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct x86/ARM hardware assembly instruction.

---

## 6. Dry Run

$N = 13$ (`1101_2`, 3 set bits)

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | N = 13 & 12 = 12 (`1100_2`) | count = 1 |
| `Iter 2` | N = 12 & 11 = 8  (`1000_2`) | count = 2 |
| `Iter 3` | N = 8 & 7   = 0  (`0000_2`) | count = 3 |
| `Exit` | N == 0 -> Loop terminates | Total set bits = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 0$ (returns 0).
- $N = 2^{31} - 1$ (returns 31).

### Common Bugs to Avoid
- Using `__builtin_popcount` on 64-bit `long long` integers (must use `__builtin_popcountll`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the 64-bit version of __builtin_popcount?**  
  **A**: For 64-bit `long long`, use `__builtin_popcountll(x)`. Using 32-bit `__builtin_popcount` truncates the upper 32 bits!


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Brian Kernighan`, `Easy`
- **Related problems to practice next**:
- **Count Bits to Flip A to B**: Popcount of XOR.
