# Minimum Add to Make Parentheses Valid (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Minimum Add to Make Parentheses Valid](https://takeuforward.org/strings/minimum-add-to-make-parentheses-valid/)
- **Difficulty**: Medium
- **Statement**: A parentheses string is valid if and only if: (1) It is the empty string, (2) It can be written as `AB` (`A` concatenated with `B`), where `A` and `B` are valid strings, or (3) It can be written as `(A)`, where `A` is a valid string. You are given a parentheses string `s`. In one move, you can insert a parenthesis at any position of the string. Return the minimum number of moves required to make `s` valid.

---

## 1. Problem, Restated

Compute the minimum number of parentheses to insert to balance `s` by tracking unmatched open brackets and unmatched closing brackets in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Parentheses string `s`.
- **Output**: Minimum integer insertions.
- **Complexity Goal**: $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

---

## 2. Intuition & Pattern

**The Dual Imbalance Invariant**: 
In any parentheses string, there are only two types of imbalances: 
1) **Unmatched Closing Parentheses `')'`**: Occurs when a `')'` appears with no preceding unmatched `'('` available $\implies$ requires inserting an open `'('` immediately: `insertions++`! 
2) **Unmatched Opening Parentheses `'('`**: Occurs when extra `'('` remain at the end of the string without matching `')'` $\implies$ requires inserting `openCount` closing parentheses: `insertions += openCount`! 
**Algorithm**: 
- Maintain `openCount = 0` (unmatched opening parentheses) and `insertions = 0` (inserted opening parentheses). 
- Traverse characters: 
  - If `c == '('`: `openCount++` 
  - If `c == ')'`: 
    - If `openCount > 0`: `openCount--` (matched successfully!) 
    - Else: `insertions++` (must insert an opening `'('` to balance this stray `')'`) 
- Return `insertions + openCount`. Runs in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Balance Counter State Tracking (`openNeeded` & `closeNeeded`)`.

---

## 3. Approach 1 — Naive (Explicit Stack Simulation)

### Idea
Use an explicit `std::stack<char>` to simulate matching in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <string>
#include <stack>
using namespace std;

class SolutionStack {
public:
    int minAddToMakeValid(string s) {
        stack<char> st;
        for (char c : s) {
            if (c == '(') {
                st.push(c);
            } else {
                if (!st.empty() && st.top() == '(') st.pop();
                else st.push(c);
            }
        }
        return st.size();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack space.
- **Why it's not good enough**: Allocates dynamic stack nodes for every character when two scalar integer counters suffice.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard balance counter below directly achieves optimal $\mathcal{O}(1)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Two-Variable Balance Counter)

### Idea
Two-Variable Balance Counter in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minAddToMakeValid(string s) {
        int openCount = 0;   // Count of currently unmatched '('
        int insertions = 0;  // Count of required inserted '('
        
        for (char c : s) {
            if (c == '(') {
                openCount++;
            } else { // c == ')'
                if (openCount > 0) {
                    openCount--; // Matched with an available '('
                } else {
                    insertions++; // No matching '(' available -> must insert one
                }
            }
        }
        
        // Total insertions = (missing '(' for stray ')') + (missing ')' for stray '(')
        return insertions + openCount;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (single pass through string of length $N$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (two scalar integer variables).
- **Why this is optimal**: Counter invariant captures all stack states in register memory with zero allocations.

---

## 6. Dry Run

`s = "()))(("` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0 ('(')` | openCount = 1, insertions = 0 | Unmatched '(' = 1 |
| `i = 1 (')')` | openCount > 0 $\implies$ openCount = 0 | Matched pair |
| `i = 2 (')')` | openCount == 0 $\implies$ insertions = 1 | Stray ')' needs 1 '(' |
| `i = 3 (')')` | openCount == 0 $\implies$ insertions = 2 | Stray ')' needs 1 '(' |
| `i = 4 ('(')` | openCount = 1 | Unmatched '(' = 1 |
| `i = 5 ('(')` | openCount = 2 | Unmatched '(' = 2 |
| `Result` | insertions + openCount = $2 + 2 = 4$ | Min Add = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already valid string `"()()"` (returns 0).
- All closing parentheses `")))"` (returns 3).
- All opening parentheses `"((("` (returns 3).
- Empty string `""` (returns 0).

### Common Bugs to Avoid
- Setting `openCount = max(0, openCount - 1)` without recording stray `')'` in `insertions` (would undercount needed open brackets!).
- Returning `openCount` only.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does this problem differ from Minimum Insertions to Balance a Parentheses String (LeetCode 1541)?**  
  **A**: In LeetCode 1541, each open parenthesis `'('` MUST be followed by EXACTLY TWO consecutive closing parentheses `"))"`. This requires tracking single `')'` lookaheads and inserting extra closing brackets when pairs are broken!

- **Q2: Can we reconstruct the balanced string with minimum insertions?**  
  **A**: YES! In a forward pass, whenever `openCount == 0 && c == ')'`, prepend `'('`. In a post-processing step, append `openCount` copies of `')'` at the end in $\mathcal{O}(N)$ time.

- **Q3: What if the string contains multiple types of brackets: '()', '{}', '[]'?**  
  **A**: With multiple bracket types, scalar counters are INSUFFICIENT because bracket matching order matters (e.g. `"([)]"` is invalid). An explicit `std::stack<char>` is strictly required in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Parentheses`, `Greedy`, `Stack`, `LeetCode-921`, `Medium`
- **Related problems to practice next**:
- **Valid Parentheses**: Stack validation.
- **Minimum Insertions to Balance 1541**: Double closing bracket variant.
