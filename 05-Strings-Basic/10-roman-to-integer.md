# Roman to Integer Conversion (Step 5.2 — Medium String Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Roman to Integer Conversion](https://takeuforward.org/strings/roman-to-integer/)
- **Difficulty**: Easy
- **Statement**: Given a Roman numeral $s$, convert it to an integer. Symbol values: I:1, V:5, X:10, L:50, C:100, D:500, M:1000. Subtractive rule: IV=4, IX=9, XL=40, XC=90, CD=400, CM=900.

---

## 1. Problem, Restated

Convert Roman numerals to standard integers using the subtractive lookahead rule.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Iterate from left to right. If the current symbol value is strictly smaller than the next symbol value (`val(s[i]) < val(s[i+1])`), subtract `val(s[i])`. Otherwise, add `val(s[i])`.

- **Underlying Pattern**: `Lookahead Subtractive Pattern: if $val[i] < val[i+1]$, subtract $val[i]$`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Replacing two-character substrings with integers.

### C++17 Code
```cpp
// Substring replacement approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Creates intermediate string copies.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct lookup switch table with lookahead comparison.

### C++17 Code
```cpp
#include <string>
using namespace std;

class Solution {
private:
    int value(char c) {
        switch (c) {
            case 'I': return 1;
            case 'V': return 5;
            case 'X': return 10;
            case 'L': return 50;
            case 'C': return 100;
            case 'D': return 500;
            case 'M': return 1000;
            default: return 0;
        }
    }

public:
    int romanToInt(string s) {
        int total = 0;
        int n = s.length();
        
        for (int i = 0; i < n; i++) {
            int curr = value(s[i]);
            // Subtractive rule: if next value is greater, subtract current
            if (i + 1 < n && curr < value(s[i + 1])) {
                total -= curr;
            } else {
                total += curr;
            }
        }
        
        return total;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ where $N \le 15$ ($< 1\mu\text{s}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Switch lookup is $\mathcal{O}(1)$ with zero map allocation overhead.

---

## 6. Dry Run

$s = \text{"MCMXCIV"}$ ($1994$)

| Step | Action / State Change | Result |
|---|---|---|
| `M (1000)` | 1000 >= 100 (C) | +1000 -> total = 1000 |
| `C (100)` | 100 < 1000 (M) | -100  -> total = 900 |
| `M (1000)` | 1000 >= 10 (X) | +1000 -> total = 1900 |
| `X (10)` | 10 < 100 (C) | -10   -> total = 1890 |
| `C (100)` | 100 >= 1 (I) | +100  -> total = 1990 |
| `I (1)` | 1 < 5 (V) | -1    -> total = 1989 |
| `V (5)` | Last character | +5    -> total = 1994 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single numeral `"I"` (1), `"M"` (1000).
- All subtractive pairs `"IV"`, `"IX"`.

### Common Bugs to Avoid
- Out of bounds on `s[i + 1]` without `i + 1 < n` check.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the subtractive rule work universally?**  
  **A**: Roman numerals are written largest to smallest from left to right. A smaller value preceding a larger value uniquely indicates subtraction.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Maths`, `LeetCode-13`, `Easy`
- **Related problems to practice next**:
- **Integer to Roman**: Inverse conversion.
