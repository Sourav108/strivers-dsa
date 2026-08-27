# Maximum Nesting Depth of the Parentheses (Step 5.2 — Medium String Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Maximum Nesting Depth of the Parentheses](https://takeuforward.org/strings/maximum-nesting-depth-of-the-parentheses/)
- **Difficulty**: Easy
- **Statement**: Given a valid parentheses string `s`, return the nesting depth of `s` (the maximum number of open parentheses inside each other).

---

## 1. Problem, Restated

Find the maximum depth reached by open brackets in a single pass.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain `currentDepth = 0` and `maxDepth = 0`. Iterate through string: if `ch == '('`: `currentDepth++`, update `maxDepth = max(maxDepth, currentDepth)`. If `ch == ')'`: `currentDepth--`. Ignore all other characters (digits, signs).

- **Underlying Pattern**: `Running Balance Tracking`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Explicit stack push/pop measuring `stack.size()`.

### C++17 Code
```cpp
#include <string>
#include <stack>
#include <algorithm>
using namespace std;
int maxDepthStack(string s) {
    int maxD = 0;
    stack<char> st;
    for (char c : s) {
        if (c == '(') { st.push(c); maxD = max(maxD, (int)st.size()); }
        else if (c == ')') st.pop();
    }
    return maxD;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why it's not good enough**: Stack object overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Integer Depth Counter in $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    int maxDepth(string s) {
        int currentDepth = 0;
        int maxD = 0;
        
        for (char c : s) {
            if (c == '(') {
                currentDepth++;
                maxD = max(maxD, currentDepth);
            } else if (c == ')') {
                currentDepth--;
            }
        }
        
        return maxD;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct scalar variable state tracking.

---

## 6. Dry Run

$s = \text{"(1+(2*3)+((8)/4))+1"}$

| Step | Action / State Change | Result |
|---|---|---|
| ``(`` | depth = 1 | maxD = 1 |
| ``(`` | depth = 2 | maxD = 2 |
| ``)`` | depth = 1 | maxD = 2 |
| ``((`` | depth = 3 | maxD = 3 |
| ``))`` | depth = 1 | maxD = 3 |
| ``)`` | depth = 0 | Final maxDepth = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $s = \text{"1"}$ (no parentheses -> depth 0).
- Empty string.

### Common Bugs to Avoid
- Forgetting to ignore non-parenthesis characters.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is this O(1) space?**  
  **A**: Because the problem assumes string is guaranteed valid parentheses, so no stack validation is needed.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Stack`, `LeetCode-1614`, `Easy`
- **Related problems to practice next**:
- **Remove Outermost Parentheses**: Depth removal.
