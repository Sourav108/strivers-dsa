# Reverse a Number (LeetCode 7) (Step 1.4 — Know Basic Maths)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Reverse a Number (LeetCode 7)](https://takeuforward.org/c-programs/reverse-digits-of-a-number/)
- **Difficulty**: Easy
- **Statement**: Given a signed 32-bit integer $x$, return $x$ with its digits reversed. If reversing $x$ causes the value to go outside the signed 32-bit integer range $[-2^{31}, 2^{31} - 1]$, return 0.

---

## 1. Problem, Restated

Reverse decimal digits of $x$ while safely detecting 32-bit signed overflow before it occurs.

- **Input**: Parameters specified.
- **Output**: Result meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Extract least significant digit `pop = x % 10`, then append to reversed integer `rev = rev * 10 + pop`. To prevent 32-bit overflow without using 64-bit types, check `if (rev > INT_MAX / 10 || (rev == INT_MAX / 10 && pop > 7)) return 0;` before multiplying.

- **Underlying Pattern**: `Digit Reversal with Safe Pre-Overflow Detection`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
String Reversal: Convert to string, reverse, and parse with `stol` inside a try-catch block.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
#include <climits>
using namespace std;
int reverseString(int x) {
    string s = to_string(x);
    bool neg = (x < 0);
    if (neg) s = s.substr(1);
    reverse(s.begin(), s.end());
    try {
        long long val = stoll(s);
        if (neg) val = -val;
        if (val < INT_MIN || val > INT_MAX) return 0;
        return (int)val;
    } catch (...) { return 0; }
}
```

### Java Code
```java
class Solution {
    int reverseString(int x) {
        String s = String.valueOf(x);
        boolean neg = (x < 0);
        if (neg) s = s.substring(1);
        reverse(s.begin(), s.end());
        try {
            long val = stoll(s);
            if (neg) val = -val;
            if (val < Integer.MIN_VALUE || val > Integer.MAX_VALUE) return 0;
            return (int)val;
        } catch (...) { return 0; }
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} X)$ time.
- **Space Complexity**: $\mathcal{O}(\log_{10} X)$ memory.
- **Why it's not good enough**: Heavy string allocation and exception handling.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard mathematical / optimal algorithmic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Arithmetic digit extraction with safe overflow boundary guards.

### C++17 Code
```cpp
#include <climits>
using namespace std;

class Solution {
public:
    int reverse(int x) {
        int rev = 0;
        while (x != 0) {
            int pop = x % 10;
            x /= 10;
            
            // Check positive overflow before rev * 10
            if (rev > INT_MAX / 10 || (rev == INT_MAX / 10 && pop > 7)) return 0;
            // Check negative overflow before rev * 10
            if (rev < INT_MIN / 10 || (rev == INT_MIN / 10 && pop < -8)) return 0;
            
            rev = rev * 10 + pop;
        }
        return rev;
    }
};
```

### Java Code
```java
class Solution {

    int reverse(int x) {
        int rev = 0;
        while (x != 0) {
            int pop = x % 10;
            x /= 10;
            
            // Check positive overflow before rev * 10
            if (rev > Integer.MAX_VALUE / 10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) return 0;
            // Check negative overflow before rev * 10
            if (rev < Integer.MIN_VALUE / 10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) return 0;
            
            rev = rev * 10 + pop;
        }
        return rev;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} |X|)$ — runs at most 10 iterations for 32-bit integers.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Strict $\mathcal{O}(1)$ integer arithmetic with 0 risk of undefined overflow.

---

## 6. Dry Run

Reverse $x = -123$

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | pop = -3, x = -12 | rev = -3 |
| `Iter 2` | pop = -2, x = -1 | rev = -32 |
| `Iter 3` | pop = -1, x = 0 | rev = -321 |
| `Exit` | x == 0 -> terminates | Return -321 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $x = 0$ (returns 0)
- $x = 1534236469$ (reversed $9646324351 > \text{INT\_MAX} \implies$ returns 0)
- Trailing zeroes: $x = 120 \implies 21$.

### Common Bugs to Avoid
- Multiplying `rev * 10` before checking limits (triggers undefined behavior on signed overflow).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why are 7 and -8 the threshold bounds in the overflow check?**  
  **A**: `INT_MAX = 2147483647` (ends in 7). `INT_MIN = -2147483648` (ends in -8). When `rev == INT_MAX / 10`, adding a digit $> 7$ causes overflow.


---

## 9. Tags & Related Problems

- **Tags**: `Maths`, `Digits`, `LeetCode-7`, `Easy`
- **Related problems to practice next**:
- **Check Palindrome Number**: Uses reversed integer logic.
