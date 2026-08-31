# Generate Parentheses (Valid Combinations) (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Generate Parentheses (Valid Combinations)](https://takeuforward.org/recursion/generate-parentheses/)
- **Difficulty**: Medium
- **Statement**: Given $n$ pairs of parentheses, write a function to generate all combinations of well-formed parentheses.

---

## 1. Problem, Restated

Generate all valid balanced parentheses strings of length $2n$ using Catalan numbers $C_n = \frac{1}{n+1}\binom{2n}{n}$.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Add `(` if `openCount < n`. 2) Add `)` if `closeCount < openCount` (ensures string remains balanced at all prefix points). 3) When `current.length() == 2n`, push to results.

- **Underlying Pattern**: `Constrained Backtracking (`open < n` and `close < open`)`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Generate all $2^{2n}$ strings of `(` and `)` and validate balance with stack in $\mathcal{O}(2^{2n} \cdot n)$ time.

### C++17 Code
```cpp
// Generate all 2^(2n) strings
```

### Java Code
```java
// Java equivalent
// Generate all 2^(2n) strings
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \cdot 2^{2n})$ time.
- **Space Complexity**: $\mathcal{O}(n)$.
- **Why it's not good enough**: Explores millions of invalid combinations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Pruned Backtracking: only valid balanced prefixes explored.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    void backtrack(int openCount, int closeCount, int n, string& current, vector<string>& result) {
        // Base case: length reaches 2n
        if ((int)current.length() == 2 * n) {
            result.push_back(current);
            return;
        }
        
        // Choice 1: Add '(' if we still have available opening brackets
        if (openCount < n) {
            current.push_back('(');
            backtrack(openCount + 1, closeCount, n, current, result);
            current.pop_back(); // backtrack
        }
        
        // Choice 2: Add ')' only if closing brackets are fewer than opening brackets
        if (closeCount < openCount) {
            current.push_back(')');
            backtrack(openCount, closeCount + 1, n, current, result);
            current.pop_back(); // backtrack
        }
    }

public:
    vector<string> generateParenthesis(int n) {
        vector<string> result;
        string current = "";
        backtrack(0, 0, n, current, result);
        return result;
    }
};
```

### Java Code
```java
class Solution {

    void backtrack(int openCount, int closeCount, int n, String current, String[] result) {
        // Base case: length reaches 2n
        if (current.length() == 2 * n) {
            result.add(current);
            return;
        }
        
        // Choice 1: Add '(' if we still have available opening brackets
        if (openCount < n) {
            current.add('(');
            backtrack(openCount + 1, closeCount, n, current, result);
            current.remove(); // backtrack
        }
        
        // Choice 2: Add ')' only if closing brackets are fewer than opening brackets
        if (closeCount < openCount) {
            current.add(')');
            backtrack(openCount, closeCount + 1, n, current, result);
            current.remove(); // backtrack
        }
    }

    String[] generateParenthesis(int n) {
        String[] result;
        String current = "";
        backtrack(0, 0, n, current, result);
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\frac{4^n}{\sqrt{n}})$ time (bounded by the $n$-th Catalan number $C_n = \frac{1}{n+1}\binom{2n}{n}$).
- **Space Complexity**: $\mathcal{O}(2n)$ recursion stack space.
- **Why this is optimal**: Every explored path leads to a valid balanced parentheses string with zero wasted operations.

---

## 6. Dry Run

$n = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Branch 1` | "(" -> "((" -> "(()" -> "(())" | Valid string `"(())"` |
| `Branch 2` | "(" -> "()" -> "()(" -> "()()" | Valid string `"()()"` |
| `Result` | Total $C_2 = 2$ combinations | `["(())", "()()"]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n = 1$ (`["()"]`).
- $n = 3$ (5 combinations).
- $n = 4$ (14 combinations).

### Common Bugs to Avoid
- Condition `closeCount < n` instead of `closeCount < openCount` (generates invalid prefixes like `")("`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the Catalan Number formula for total combinations?**  
  **A**: The number of valid parentheses sequences of $n$ pairs is $C_n = \frac{1}{n+1}\binom{2n}{n} = \frac{(2n)!}{(n+1)! n!}$. For $n=3$, $C_3 = 5$. For $n=4$, $C_4 = 14$.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Catalan`, `LeetCode-22`, `Medium`
- **Related problems to practice next**:
- **Valid Parentheses**: Validation stack.
