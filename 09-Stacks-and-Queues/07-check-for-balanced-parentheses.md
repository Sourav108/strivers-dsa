# Check for Balanced Parentheses (Step 9.1 — Learning)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check for Balanced Parentheses](https://takeuforward.org/data-structure/check-for-balanced-parentheses/)
- **Difficulty**: Easy
- **Statement**: Given a string $s$ containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid (open brackets must be closed by same type in correct order).

---

## 1. Problem, Restated

Validate bracket matching using a LIFO Stack.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Push opening brackets `(`, `{`, `[` onto stack. For closing brackets `)`, `}`, `]`: if stack is empty, return `false`. Otherwise, check if `st.top()` matches the corresponding opening bracket: if matched, `st.pop()`; else return `false`. Return `st.empty()` at the end.

- **Underlying Pattern**: `LIFO Stack Bracket Matching`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Repeatedly replace `"()"`, `"{}"`, `"[]"` with `""` until no pairs remain in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;
bool isValidStringReplace(string s) {
    while (s.find("()") != string::npos || s.find("{}") != string::npos || s.find("[]") != string::npos) {
        if (s.find("()") != string::npos) s.erase(s.find("()"), 2);
        else if (s.find("{}") != string::npos) s.erase(s.find("{}"), 2);
        else s.erase(s.find("[]"), 2);
    }
    return s.empty();
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ string memory.
- **Why it's not good enough**: Quadratic string scanning.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Single Pass LIFO Stack Matching in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <stack>
using namespace std;

class Solution {
public:
    bool isValid(string s) {
        stack<char> st;
        
        for (char c : s) {
            if (c == '(' || c == '{' || c == '[') {
                st.push(c);
            } else {
                if (st.empty()) return false; // unmatched closing bracket
                
                char top = st.top();
                if ((c == ')' && top == '(') ||
                    (c == '}' && top == '{') ||
                    (c == ']' && top == '[')) {
                    st.pop();
                } else {
                    return false; // mismatch
                }
            }
        }
        
        return st.empty();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Each character is pushed and popped at most once.

---

## 6. Dry Run

$s = \text{"{[()]}"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Push '{', '[', '('` | st: `['{', '[', '(']` | Open brackets |
| `Char ')'` | matches top '(', pop | st: `['{', '[']` |
| `Char ']'` | matches top '[', pop | st: `['{']` |
| `Char '}'` | matches top '{', pop | st: `[]` (Empty -> Valid ✅) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Closing bracket first `"]"` (returns `false`).
- Unclosed opening brackets `"(("` (returns `false`).
- Odd length strings (instant `false`).

### Common Bugs to Avoid
- Forgetting `st.empty()` check before calling `st.top()` (crashes on closing brackets).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can odd length strings be rejected in O(1)?**  
  **A**: Yes! A balanced bracket sequence must have an even number of characters. If `s.length() % 2 != 0`, return `false` immediately.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Strings`, `LeetCode-20`, `Easy`
- **Related problems to practice next**:
- **Min Stack**: Stack augmented design.
