# Remove Outermost Parentheses (Step 5.1 — Basic and Easy String Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Remove Outermost Parentheses](https://takeuforward.org/data-structure/remove-outermost-parentheses/)
- **Difficulty**: Easy
- **Statement**: A valid parentheses string $s$ is primitive if it is nonempty and cannot be split into two valid parentheses strings. Given a valid parentheses string $s$, remove the outermost parentheses of every primitive component.

---

## 1. Problem, Restated

Strip the outermost `(` and `)` of each balanced primitive chunk in the string.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Track open bracket count `opened`. For each character: if `ch == '('`: only append if `opened > 0`, then increment `opened++`. If `ch == ')'`: decrement `opened--`, then only append if `opened > 0`. This filters out outer brackets without allocating a stack.

- **Underlying Pattern**: `Balance Counter Depth Tracking`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Explicit Stack Tracking: Push indices to stack and slice string chunks.

### C++17 Code
```cpp
#include <string>
#include <stack>
using namespace std;
string removeOuterStack(string s) {
    string ans = "";
    stack<char> st;
    for (char c : s) {
        if (c == '(') {
            if (!st.empty()) ans += c;
            st.push(c);
        } else {
            st.pop();
            if (!st.empty()) ans += c;
        }
    }
    return ans;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    String removeOuterStack(String s) {
        String ans = "";
        Stack<Character> st = new Stack<>();
        for (char c : s) {
            if (c == '(') {
                if (!st.isEmpty()) ans += c;
                st.push(c);
            } else {
                st.pop();
                if (!st.isEmpty()) ans += c;
            }
        }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why it's not good enough**: Uses heap stack allocation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Integer Depth Counter without Stack Memory in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
using namespace std;

class Solution {
public:
    string removeOuterParentheses(string s) {
        string result = "";
        result.reserve(s.length());
        int balance = 0;
        
        for (char c : s) {
            if (c == '(') {
                if (balance > 0) result += c;
                balance++;
            } else {
                balance--;
                if (balance > 0) result += c;
            }
        }
        
        return result;
    }
};
```

### Java Code
```java
class Solution {

    String removeOuterParentheses(String s) {
        String result = "";
        result.reserve(s.length());
        int balance = 0;
        
        for (char c : s) {
            if (c == '(') {
                if (balance > 0) result += c;
                balance++;
            } else {
                balance--;
                if (balance > 0) result += c;
            }
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (excluding return string).
- **Why this is optimal**: Single scalar `balance` integer replaces stack data structure.

---

## 6. Dry Run

$s = \text{"(()())(())"}$

| Step | Action / State Change | Result |
|---|---|---|
| ``(`` | balance 0 -> 1 | Skip outer `(` |
| ``(`` | balance 1 -> 2 | Append `(` -> `"("` |
| ``)`` | balance 2 -> 1 | Append `)` -> `"()"` |
| ``(`` | balance 1 -> 2 | Append `(` -> `"()("` |
| ``)`` | balance 2 -> 1 | Append `)` -> `"()()"` |
| ``)`` | balance 1 -> 0 | Skip outer `)` |
| `Chunk 2 `(())`` | Processed identically | Final Result: `"()()()"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single primitive `"()"` -> returns `""`.
- Deeply nested `"(((())))"` -> returns `"((()))"`.

### Common Bugs to Avoid
- Updating `balance` before checking condition on `)` (must decrement first).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does balance > 0 check identify non-outer brackets?**  
  **A**: The outermost opening bracket occurs when `balance == 0`. The outermost closing bracket drops `balance` back to 0. Every internal character has `balance >= 1`.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Stack`, `LeetCode-1021`, `Easy`
- **Related problems to practice next**:
- **Max Nesting Depth**: Parentheses depth counting.
