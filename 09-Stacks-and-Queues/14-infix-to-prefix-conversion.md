# Infix to Prefix Conversion (Step 9.2 — Prefix, Infix, Postfix Expressions)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Infix to Prefix Conversion](https://takeuforward.org/data-structure/infix-to-prefix/)
- **Difficulty**: Medium
- **Statement**: Given an infix expression $s$, convert it to a prefix expression using a stack.

---

## 1. Problem, Restated

Reverse infix, swap `(` and `)`, compute modified postfix with `>` precedence check, reverse result.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Algorithm: 1) Reverse the infix string: `reverse(s)`. 2) Swap brackets: `'(' \leftrightarrow ')'`. 3) Convert to postfix using Shunting-Yard, but strictly pop operators only when `precedence(c) < precedence(top)` (instead of `<=`), giving right-associative preference. 4) Reverse the resulting postfix string to obtain Prefix!

- **Underlying Pattern**: `Reverse Infix $\to$ Modified Shunting Yard $\to$ Reverse Result`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Syntax tree generation.

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
Reversal + Modified Shunting Yard in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <stack>
#include <algorithm>
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
    string infixToPrefix(string s) {
        // Step 1: Reverse string and swap brackets
        reverse(s.begin(), s.end());
        for (char& c : s) {
            if (c == '(') c = ')';
            else if (c == ')') c = '(';
        }
        
        // Step 2: Modified Infix to Postfix
        stack<char> st;
        string postfix = "";
        
        for (char c : s) {
            if (isalnum(c)) {
                postfix += c;
            } else if (c == '(') {
                st.push('(');
            } else if (c == ')') {
                while (!st.empty() && st.top() != '(') {
                    postfix += st.top();
                    st.pop();
                }
                if (!st.empty()) st.pop();
            } else {
                if (c == '^') {
                    while (!st.empty() && precedence(c) <= precedence(st.top())) {
                        postfix += st.top();
                        st.pop();
                    }
                } else {
                    while (!st.empty() && precedence(c) < precedence(st.top())) {
                        postfix += st.top();
                        st.pop();
                    }
                }
                st.push(c);
            }
        }
        
        while (!st.empty()) {
            postfix += st.top();
            st.pop();
        }
        
        // Step 3: Reverse postfix to get prefix
        reverse(postfix.begin(), postfix.end());
        return postfix;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Leverages standard Shunting Yard with string reversal transformation.

---

## 6. Dry Run

$s = \text{"(A+B)*C"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1 (Reverse & Swap)` | `"C*(B+A)"` | Inverted infix |
| `Step 2 (Postfix)` | `"CBA+*"` | Modified postfix |
| `Step 3 (Reverse)` | `"*+ABC"` | Prefix Result ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Expression with `^` (power operator).
- Single token.

### Common Bugs to Avoid
- Using `<=` precedence check for all operators in modified postfix step (must use `<` for standard operators to preserve prefix associativity).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does strict '<' precedence preserve associativity in reverse postfix?**  
  **A**: Because the string was reversed, the rightmost of two equal-precedence operators is processed first. Using `<` delays popping, which correctly places the leftmost operator at higher priority in the final reversed Prefix string!


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Expression Conversion`, `Medium`
- **Related problems to practice next**:
- **Infix to Postfix**: Postfix counterpart.
