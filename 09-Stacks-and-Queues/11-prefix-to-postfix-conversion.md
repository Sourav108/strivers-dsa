# Prefix to Postfix Conversion (Step 9.2 — Prefix, Infix, Postfix Expressions)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Prefix to Postfix Conversion](https://takeuforward.org/data-structure/prefix-to-postfix/)
- **Difficulty**: Medium
- **Statement**: Given a prefix expression $s$, convert it to a postfix expression using a stack.

---

## 1. Problem, Restated

Scan right-to-left, combine operands: `op1 + op2 + operator`.

- **Input**: Class methods / expression string.
- **Output**: Operation returns / transformed expression.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Iterate from right to left: 1) Operand: `st.push(string(1, c))`. 2) Operator: pop `op1`, pop `op2`, push `op1 + op2 + c`. Return `st.top()`.

- **Underlying Pattern**: `Right-to-Left Stack Concatenation (`op1 + op2 + op`)`.

---

## 3. Approach 1 — Naive / Basic Attempt

### Idea
Prefix -> Infix -> Postfix via two passes.

### C++17 Code
```cpp
// 2-pass conversion
```

### Java Code
```java
// Java equivalent
// 2-pass conversion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Redundant intermediate conversion.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic data structure implementation below directly provides optimal bounds.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Direct 1-Pass Stack Conversion in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <stack>
#include <cctype>
using namespace std;

class Solution {
public:
    string preToPost(string pre_exp) {
        stack<string> st;
        int n = pre_exp.length();
        
        for (int i = n - 1; i >= 0; i--) {
            char c = pre_exp[i];
            
            if (isalnum(c)) {
                st.push(string(1, c));
            } else {
                string op1 = st.top(); st.pop();
                string op2 = st.top(); st.pop();
                string combined = op1 + op2 + c;
                st.push(combined);
            }
        }
        
        return st.top();
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    String preToPost(String pre_exp) {
        Stack<String> st;
        int n = pre_exp.length();
        
        for (int i = n - 1; i >= 0; i--) {
            char c = pre_exp[i];
            
            if (isalnum(c)) {
                st.push(String(1, c));
            } else {
                String op1 = st.peek(); st.pop();
                String op2 = st.peek(); st.pop();
                String combined = op1 + op2 + c;
                st.push(combined);
            }
        }
        
        return st.peek();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why this is optimal**: Single pass direct concatenation.

---

## 6. Dry Run

$s = \text{"/+AB*CD"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Scan right-to-left` | D, C -> *CD -> B, A -> +AB -> /+AB*CD | Pushed |
| `Result` | `"AB+CD*/"` | Postfix Result ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single variable `"x"`.

### Common Bugs to Avoid
- Reversing `op1` and `op2` order.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the difference between Infix and Postfix representation?**  
  **A**: Infix has operators between operands (`A+B`), requiring parentheses to denote precedence. Postfix has operators following operands (`AB+`), with unambiguous evaluation order.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Expression Conversion`, `Medium`
- **Related problems to practice next**:
- **Postfix to Prefix**: Inverse conversion.
