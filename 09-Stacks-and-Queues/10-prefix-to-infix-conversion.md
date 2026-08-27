# Prefix to Infix Conversion (Step 9.2 — Prefix, Infix, Postfix Expressions)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Prefix to Infix Conversion](https://takeuforward.org/data-structure/prefix-to-infix/)
- **Difficulty**: Medium
- **Statement**: Given a prefix expression $s$, convert it to a fully parenthesized infix expression using a stack.

---

## 1. Problem, Restated

Scan prefix from right to left, combining operands: `"(" + op1 + operator + op2 + ")"`.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Iterate from right to left ($i = N-1$ down to 0). 1) If character is an operand, push to stack `st.push(string(1, c))`. 2) If operator: pop `op1 = st.top()`, pop `op2 = st.top()`, push `"(" + op1 + c + op2 + ")"`. Return `st.top()`.

- **Underlying Pattern**: `Right-to-Left String Stack Accumulation`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Syntax tree parsing.

### C++17 Code
```cpp
// Tree approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Tree overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Right-to-Left Stack Evaluation in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <stack>
#include <cctype>
using namespace std;

class Solution {
public:
    string preToInfix(string pre_exp) {
        stack<string> st;
        int n = pre_exp.length();
        
        // Scan right to left
        for (int i = n - 1; i >= 0; i--) {
            char c = pre_exp[i];
            
            if (isalnum(c)) {
                st.push(string(1, c));
            } else {
                string op1 = st.top(); st.pop();
                string op2 = st.top(); st.pop();
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
- **Space Complexity**: $\mathcal{O}(N)$ string stack space.
- **Why this is optimal**: Right-to-left scan ensures first popped operand is left child.

---

## 6. Dry Run

$s = \text{"*+AB-CD"}$

| Step | Action / State Change | Result |
|---|---|---|
| `i = 6 ('D'), 5 ('C')` | st: `["D", "C"]` | Operands |
| `i = 4 ('-')` | op1="C", op2="D" -> push `"(C-D)"` | Sub-expression |
| `i = 3 ('B'), 2 ('A')` | st: `["(C-D)", "B", "A"]` | Operands |
| `i = 1 ('+')` | op1="A", op2="B" -> push `"(A+B)"` | Sub-expression |
| `i = 0 ('*')` | push `"((A+B)*(C-D))"` | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single operand `"A"`.
- Complex nested operators.

### Common Bugs to Avoid
- Scanning left to right instead of right to left.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why scan right-to-left for Prefix?**  
  **A**: In Prefix notation, operators precede operands (`+AB`). Scanning backwards encounters operands before their operator, enabling immediate reduction.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Expression Conversion`, `Medium`
- **Related problems to practice next**:
- **Prefix to Postfix**: Postfix counterpart.
