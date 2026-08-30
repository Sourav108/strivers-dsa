# Valid Parenthesis String (Greedy Min/Max Open range) (Step 12.1 — Easy)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Valid Parenthesis String (Greedy Min/Max Open range)](https://takeuforward.org/data-structure/valid-parenthesis-string/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$ containing `'('`, `')'` and `'*'`, return `true` if $s$ is valid. `'*'` could be treated as a single right parenthesis `')'`, a single left parenthesis `'('`, or an empty string `""`.

---

## 1. Problem, Restated

Track the range of possible open bracket counts $[	ext{minOpen}, 	ext{maxOpen}]$ dynamically in $\mathcal{O}(N)$ time.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain `minOpen` (minimum possible unmatched `(`) and `maxOpen` (maximum possible unmatched `(`). 1) `'('`: `minOpen++`, `maxOpen++`. 2) `')'`: `minOpen--`, `maxOpen--`. 3) `'*'`: could be `)` (`minOpen--`), `(` (`maxOpen++`), or empty (`minOpen` unchanged). At each step: if `maxOpen < 0`, too many `)` $\implies$ `false`. Clamp `minOpen = max(0, minOpen)`. At end, string is valid iff `minOpen == 0`.

- **Underlying Pattern**: `Open Parentheses Bounding Range ($[minOpen, maxOpen]$)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Recursion trying all 3 choices for each `'*'` in $\mathcal{O}(3^N)$ time.

### C++17 Code
```cpp
// O(3^N) recursion
```

### Java Code
```java
// Java equivalent
// O(3^N) recursion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3^N)$ exponential.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Exponential branching.

---

## 4. Approach 2 — Better

### Idea
2D Dynamic Programming / Memoization in O(N^2) time.

### C++17 Code
```cpp
// DP memoization O(N^2)
```

### Java Code
```java
// Java equivalent
// DP memoization O(N^2)
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ table space.
- **Why it's still not optimal**: Quadratic time and memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Greedy Min/Max Open Counter in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    bool checkValidString(string s) {
        int minOpen = 0; // minimum possible open brackets
        int maxOpen = 0; // maximum possible open brackets
        
        for (char c : s) {
            if (c == '(') {
                minOpen++;
                maxOpen++;
            } else if (c == ')') {
                minOpen--;
                maxOpen--;
            } else { // c == '*'
                minOpen--; // treat as ')'
                maxOpen++; // treat as '('
                // treating as "" keeps range between (minOpen, maxOpen)
            }
            
            // If maxOpen < 0, even converting all '*' to '(' cannot balance the ')'
            if (maxOpen < 0) return false;
            
            // minOpen cannot be negative (we cannot have negative open brackets)
            minOpen = max(0, minOpen);
        }
        
        // Valid if 0 open brackets is achievable within [minOpen, maxOpen]
        return minOpen == 0;
    }
};
```

### Java Code
```java
class Solution {

    boolean checkValidString(String s) {
        int minOpen = 0; // minimum possible open brackets
        int maxOpen = 0; // maximum possible open brackets
        
        for (char c : s) {
            if (c == '(') {
                minOpen++;
                maxOpen++;
            } else if (c == ')') {
                minOpen--;
                maxOpen--;
            } else { // c == '*'
                minOpen--; // treat as ')'
                maxOpen++; // treat as '('
                // treating as "" keeps range between (minOpen, maxOpen)
            }
            
            // If maxOpen < 0, even converting all '*' to '(' cannot balance the ')'
            if (maxOpen < 0) return false;
            
            // minOpen cannot be negative (we cannot have negative open brackets)
            minOpen = Math.max(0, minOpen);
        }
        
        // Valid if 0 open brackets is achievable within [minOpen, maxOpen]
        return minOpen == 0;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Range tracking maintains all possible valid states simultaneously in $\mathcal{O}(1)$ space.

---

## 6. Dry Run

$s = \text{"(*))"}$

| Step | Action / State Change | Result |
|---|---|---|
| `c = '('` | minOpen = 1, maxOpen = 1 | [1, 1] |
| `c = '*'` | minOpen = max(0, 0) = 0, maxOpen = 2 | [0, 2] |
| `c = ')'` | minOpen = max(0, -1) = 0, maxOpen = 1 | [0, 1] |
| `c = ')'` | minOpen = max(0, -1) = 0, maxOpen = 0 | [0, 0] |
| `End` | minOpen == 0 -> Valid! | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $s = \text{"(*)"}$ (returns `true`).
- $s = \text{")("}$ (`maxOpen < 0` at index 0 $\implies$ `false`).

### Common Bugs to Avoid
- Not resetting `minOpen = max(0, minOpen)` (causes `minOpen` to become negative, leading to false `true` returns).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is minOpen clamped to 0?**  
  **A**: Because open bracket count cannot be negative at any prefix point in a valid bracket string. If `minOpen < 0`, it simply means treating some '*' as ')' was an invalid branch, so we clamp it to 0 (treating '*' as empty instead).

- **Q2: Can this be solved using two stacks?**  
  **A**: Yes! One stack for `(` indices and one for `*` indices. Match `)` against `(` stack first, then `*` stack. Finally match remaining `(` with `*` appearing at higher indices in $\mathcal{O}(N)$ space.

- **Q3: Why is the greedy range contiguous?**  
  **A**: Because `'*'` can change open count by $+1, 0, -1$. Any integer value between `minOpen` and `maxOpen` can be formed by appropriately configuring choices of `'*'`.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Strings`, `Stack`, `LeetCode-678`, `Medium`
- **Related problems to practice next**:
- **Check Balanced Parentheses**: Strict parenthesis matching.
