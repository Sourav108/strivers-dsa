# Infix to Postfix Conversion using Stack (Step 9.2 — Prefix, Infix, Postfix Expressions)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Infix to Postfix Conversion using Stack](https://takeuforward.org/data-structure/infix-to-postfix/)
- **Difficulty**: Medium
- **Statement**: Given an infix expression $s$, convert it to a postfix expression (Reverse Polish Notation) using operator precedence and associativity.

---

## 1. Problem, Restated

Transform $A + B \times C$ into $A B C \times +$ using an operator stack.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Operands (alphanumeric) are appended directly to output. 2) `'('` is pushed to stack. 3) `')'` pops and appends operators until `'('` is popped. 4) Operator: while `stack.top()` has $\ge$ precedence (or $>$ for right-associative `^`), pop and append, then push current operator.

- **Underlying Pattern**: `Shunting-Yard Operator Precedence Stack`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Expression parse trees.

### C++17 Code
```cpp
// Parse tree generation
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(N)$ tree nodes.
- **Why it's not good enough**: Heavy tree allocations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Shunting-Yard Stack Algorithm in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <stack>
#include <cctype>
using namespace std;

class Solution {
private:
    int precedence(char c) {
        if (c == '^') return 3;
        if (c == '*' || c == '/') return 2;
        if (c == '+' || c == '-') return 1;
        return -1;
    }

public:
    string infixToPostfix(string s) {
        stack<char> st;
        string result = "";
        
        for (char c : s) {
            // If operand, add to output
            if (isalnum(c)) {
                result += c;
            } else if (c == '(') {
                st.push('(');
            } else if (c == ')') {
                while (!st.empty() && st.top() != '(') {
                    result += st.top();
                    st.pop();
                }
                if (!st.empty()) st.pop(); // remove '('
            } else {
                // Operator
                while (!st.empty() && precedence(c) <= precedence(st.top())) {
                    if (c == '^' && st.top() == '^') break; // '^' is right-associative
                    result += st.top();
                    st.pop();
                }
                st.push(c);
            }
        }
        
        while (!st.empty()) {
            result += st.top();
            st.pop();
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Each token is pushed and popped at most once.

---

## 6. Dry Run

$s = \text{"a+b*(c^d-e)"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Tokens parsed` | a -> + in stack -> b -> * in stack -> ( -> c -> ^ -> d -> - -> e -> ) | Step-by-step |
| `Postfix Output` | `"abcd^e-*+"` | Result ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Right-associative power operator `^` (`2^3^4` $\implies$ `234^^`).
- Parentheses nested deeply.

### Common Bugs to Avoid
- Treating `^` as left-associative (must not pop equal precedence `^`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is postfix expression preferred for compilers and calculators?**  
  **A**: Postfix expressions require no parentheses and no operator precedence lookaheads; they can be evaluated in a single forward pass using a simple operand stack.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Expression Conversion`, `Shunting-Yard`, `Medium`
- **Related problems to practice next**:
- **Infix to Prefix**: Prefix conversion.
