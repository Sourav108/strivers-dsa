# Postfix to Infix Conversion (Step 9.2 — Prefix, Infix, Postfix Expressions)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Postfix to Infix Conversion](https://takeuforward.org/data-structure/postfix-to-infix/)
- **Difficulty**: Medium
- **Statement**: Given a postfix expression $s$, convert it to an infix expression with parentheses.

---

## 1. Problem, Restated

Scan left-to-right, combine operands: `"(" + op1 + operator + op2 + ")"`.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Iterate left to right: 1) Operand: `st.push(string(1, c))`. 2) Operator: pop `op2 = st.top()`, pop `op1 = st.top()`. Push `"(" + op1 + c + op2 + ")"`. Return `st.top()`.

- **Underlying Pattern**: `Left-to-Right Stack Parenthesization`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Parse tree construction.

### C++17 Code
```cpp
// Tree approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Extra memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Direct Left-to-Right Stack Parenthesization in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <stack>
#include <cctype>
using namespace std;

class Solution {
public:
    string postToInfix(string exp) {
        stack<string> st;
        
        for (char c : exp) {
            if (isalnum(c)) {
                st.push(string(1, c));
            } else {
                string op2 = st.top(); st.pop();
                string op1 = st.top(); st.pop();
                string combined = "(" + op1 + c + op2 + ")";
                st.push(combined);
            }
        }
        
        return st.top();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Single forward pass with parentheses wrapping.

---

## 6. Dry Run

$s = \text{"ab*c+"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Tokens 'a', 'b', '*'` | op2="b", op1="a" -> push `"(a*b)"` | st: `["(a*b)"]` |
| `Token 'c'` | push "c" | st: `["(a*b)", "c"]` |
| `Token '+'` | push `"((a*b)+c)"` | Infix Result ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single variable `"a"`.

### Common Bugs to Avoid
- Forgetting parentheses around combined sub-expressions.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why are parentheses necessary when converting to Infix?**  
  **A**: Because Postfix encodes operator order implicitly. When converted to Infix, explicit parentheses are required to prevent operator precedence ambiguity.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Expression Conversion`, `Medium`
- **Related problems to practice next**:
- **Infix to Postfix**: Inverse.
