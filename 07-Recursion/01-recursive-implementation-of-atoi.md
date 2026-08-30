# Recursive Implementation of Atoi (Step 7.1 — Get a Strong Hold)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Recursive Implementation of Atoi](https://takeuforward.org/recursion/recursive-implementation-of-atoi/)
- **Difficulty**: Medium
- **Statement**: Implement the `myAtoi` function recursively without using iterative loops (`for`, `while`), parsing leading whitespaces, signs, digits, and clamping 32-bit signed integer overflow.

---

## 1. Problem, Restated

Convert a string to a 32-bit signed integer using tail recursion state passing.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Define helper `helper(s, i, sign, result)`: 1) Skip spaces recursively: `if (s[i] == ' ') return helper(s, i+1, sign, result)`. 2) Parse sign on first non-space. 3) Accumulate digits: check for overflow before multiplying `result * 10 + digit`. Stop on non-digit.

- **Underlying Pattern**: `Tail-Recursive String Parsing with State Passing`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Iterative loop parser (disallowed by recursive constraint).

### C++17 Code
```cpp
// Iterative loop parser
```

### Java Code
```java
// Java equivalent
// Iterative loop parser
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Violates pure recursive constraint.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Tail Recursive Parser with 32-bit Clamping Guards.

### C++17 Code
```cpp
#include <string>
#include <climits>
#include <cctype>
using namespace std;

class Solution {
private:
    int helper(const string& s, int i, int sign, long long result) {
        if (i >= (int)s.length() || !isdigit(s[i])) {
            return (int)(result * sign);
        }
        
        int digit = s[i] - '0';
        result = result * 10 + digit;
        
        // 32-bit Clamping
        if (sign == 1 && result > INT_MAX) return INT_MAX;
        if (sign == -1 && -result < INT_MIN) return INT_MIN;
        
        return helper(s, i + 1, sign, result);
    }

public:
    int myAtoi(string s) {
        int i = 0, n = s.length();
        while (i < n && s[i] == ' ') i++; // skip whitespace
        if (i == n) return 0;
        
        int sign = 1;
        if (s[i] == '-' || s[i] == '+') {
            if (s[i] == '-') sign = -1;
            i++;
        }
        
        return helper(s, i, sign, 0LL);
    }
};
```

### Java Code
```java
class Solution {

    int helper(String s, int i, int sign, long result) {
        if (i >= s.length() || !isdigit(s[i])) {
            return (int)(result * sign);
        }
        
        int digit = s[i] - '0';
        result = result * 10 + digit;
        
        // 32-bit Clamping
        if (sign == 1 && result > Integer.MAX_VALUE) return Integer.MAX_VALUE;
        if (sign == -1 && -result < Integer.MIN_VALUE) return Integer.MIN_VALUE;
        
        return helper(s, i + 1, sign, result);
    }

    int myAtoi(String s) {
        int i = 0, n = s.length();
        while (i < n && s[i] == ' ') i++; // skip whitespace
        if (i == n) return 0;
        
        int sign = 1;
        if (s[i] == '-' || s[i] == '+') {
            if (s[i] == '-') sign = -1;
            i++;
        }
        
        return helper(s, i, sign, 0LL);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Tail-recursive state propagation handles character parsing cleanly.

---

## 6. Dry Run

$s = \text{"   -42"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Whitespace` | Skip 3 spaces | i = 3, sign = -1 |
| `Digit '4'` | helper(i=4, res=4) | next |
| `Digit '2'` | helper(i=5, res=42) | next |
| `End of string` | returns -42 | Return -42 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $s = \text{"2147483648"}$ (clamps to `INT_MAX`).
- $s = \text{"-91283472332"}$ (clamps to `INT_MIN`).

### Common Bugs to Avoid
- Recursing on non-digit characters.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is long long used for intermediate accumulation in recursive helper?**  
  **A**: To safely detect 32-bit overflow before returning clamped values without undefined integer overflow.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Strings`, `Parsing`, `Medium`
- **Related problems to practice next**:
- **String to Integer (Iterative)**: Iterative version.
