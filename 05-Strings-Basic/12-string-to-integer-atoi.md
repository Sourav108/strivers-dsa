# Implement Atoi (String to Integer conversion) (Step 5.2 — Medium String Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Atoi (String to Integer conversion)](https://takeuforward.org/strings/implement-atoi/)
- **Difficulty**: Medium
- **Statement**: Implement the `myAtoi(string s)` function which converts a string to a 32-bit signed integer following rules: 1) Discard leading whitespaces. 2) Check for sign `+` or `-`. 3) Read integer digits until non-digit. 4) Clamp to $[-2^{31}, 2^{31}-1]$ on overflow.

---

## 1. Problem, Restated

Parse signed integer from formatted string handling whitespace, sign prefixes, non-digit terminations, and 32-bit integer overflow clamping.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Step 1: Skip leading `' '`. Step 2: Read sign (`sign = -1` if `'-'`, else `1`). Step 3: Accumulate digits. To prevent 32-bit overflow without 64-bit types: check `if (res > INT_MAX / 10 || (res == INT_MAX / 10 && digit > 7))` before multiplying by 10.

- **Underlying Pattern**: `State Machine String Parsing with Boundary Clamping`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Using `std::stringstream` (fails tricky edge cases like `"+0 123"` or custom overflow clamping).

### C++17 Code
```cpp
#include <string>
#include <sstream>
using namespace std;
int atoiSS(string s) {
    stringstream ss(s);
    int x = 0;
    ss >> x;
    return x;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Fails overflow clamping rules of LeetCode.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sequential state-machine parser with safe overflow clamping.

### C++17 Code
```cpp
#include <string>
#include <climits>
using namespace std;

class Solution {
public:
    int myAtoi(string s) {
        int i = 0, n = s.length();
        
        // 1. Skip leading whitespaces
        while (i < n && s[i] == ' ') i++;
        if (i == n) return 0;
        
        // 2. Check sign
        int sign = 1;
        if (s[i] == '-' || s[i] == '+') {
            if (s[i] == '-') sign = -1;
            i++;
        }
        
        // 3. Parse digits with overflow clamping
        int result = 0;
        while (i < n && isdigit(s[i])) {
            int digit = s[i] - '0';
            
            // Check overflow before result * 10
            if (result > INT_MAX / 10 || (result == INT_MAX / 10 && digit > 7)) {
                return (sign == 1) ? INT_MAX : INT_MIN;
            }
            
            result = result * 10 + digit;
            i++;
        }
        
        return result * sign;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Exact bounded state traversal with integer safety.

---

## 6. Dry Run

$s = \text{"   -42 with words"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Spaces` | Skip 3 spaces | i = 3 |
| `Sign` | s[3] == '-' -> sign = -1 | i = 4 |
| `Digit '4'` | result = 4 | i = 5 |
| `Digit '2'` | result = 42 | i = 6 |
| `Non-digit ' '` | Stops parsing | Return -42 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $s = \text{"2147483648"}$ (clamps to `INT_MAX`).
- $s = \text{"-91283472332"}$ (clamps to `INT_MIN`).
- $s = \text{"words and 987"}$ (returns 0).

### Common Bugs to Avoid
- Checking sign more than once (e.g. `"+-12"` -> should parse `+` then hit non-digit `-` and return 0).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why check digit > 7 for overflow?**  
  **A**: `INT_MAX = 2147483647`. If `result == INT_MAX/10` ($214748364$) and digit $\ge 8$, multiplying by 10 and adding exceeds `INT_MAX`.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Parsing`, `LeetCode-8`, `Medium`
- **Related problems to practice next**:
- **Reverse a Number**: Overflow checks.
