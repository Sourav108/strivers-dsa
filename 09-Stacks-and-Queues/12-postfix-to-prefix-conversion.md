# Postfix to Prefix Conversion (Step 9.2 — Prefix, Infix, Postfix Expressions)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Postfix to Prefix Conversion](https://takeuforward.org/data-structure/postfix-to-prefix/)
- **Difficulty**: Medium
- **Statement**: Given a postfix expression $s$, convert it to a prefix expression using a stack.

---

## 1. Problem, Restated

Scan left-to-right, combine operands: `operator + op1 + op2` where `op1` is 2nd popped.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Iterate from left to right: 1) Operand: push to stack. 2) Operator: pop `op2 = st.top()`, pop `op1 = st.top()`. Push `string(1, c) + op1 + op2`. Return `st.top()`.

- **Underlying Pattern**: `Left-to-Right Stack Concatenation (`op + op1 + op2`)`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Postfix -> Infix -> Prefix two-pass approach.

### C++17 Code
```cpp
// 2-pass approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Redundant intermediate pass.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Direct Left-to-Right Stack Concatenation in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <stack>
#include <cctype>
using namespace std;

class Solution {
public:
    string postToPre(string post_exp) {
        stack<string> st;
        
        for (char c : post_exp) {
            if (isalnum(c)) {
                st.push(string(1, c));
            } else {
                string op2 = st.top(); st.pop();
                string op1 = st.top(); st.pop();
                string combined = c + op1 + op2;
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
- **Why this is optimal**: Direct 1-pass conversion.

---

## 6. Dry Run

$s = \text{"AB+CD-*"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Tokens 'A', 'B'` | st: `["A", "B"]` | Operands |
| `'+'` | op2="B", op1="A" -> push `"+AB"` | st: `["+AB"]` |
| `Tokens 'C', 'D', '-'` | push `"-CD"` | st: `["+AB", "-CD"]` |
| `'*'` | push `"*+AB-CD"` | Prefix Result ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single operand.

### Common Bugs to Avoid
- Popping `op1` first instead of `op2` (inverts operand ordering).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is op2 popped first in Left-to-Right Postfix scan?**  
  **A**: Because in Postfix `AB+`, `B` was pushed after `A`. On LIFO stack, `B` sits on top, so the first popped element is the right operand `op2`!


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Expression Conversion`, `Medium`
- **Related problems to practice next**:
- **Prefix to Postfix**: Inverse.
