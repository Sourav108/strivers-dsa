# Expression Add Operators (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Expression Add Operators](https://takeuforward.org/recursion/expression-add-operators/)
- **Difficulty**: Hard
- **Statement**: Given a string `num` that contains only digits and an integer `target`, return all possibilities to insert binary operators `'+'`, `'-'`, and `'*'` between the digits so that the resultant expression evaluates to `target`.

---

## 1. Problem, Restated

Generate all valid arithmetic expressions with operator precedence tracking for multiplication.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Multiplication has higher precedence than addition and subtraction. When applying `'*'`: subtract `prevVal` from accumulated result, and add `prevVal * currVal`. Track `(index, path, currRes, prevVal)`. Avoid numbers with leading zeros (e.g. `"05"`).

- **Underlying Pattern**: `Precedence-Aware Backtracking (`currRes - prevVal + prevVal * currVal`)`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Generate all $4^{N-1}$ strings and evaluate with stack in $\mathcal{O}(N \cdot 4^N)$ time.

### C++17 Code
```cpp
// Generate and evaluate with stack
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 4^N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Redundant expression re-parsing.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
In-Flight Arithmetic Evaluation with Multiplication Precedence Compensation.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    void backtrack(int index, const string& num, int target, string path,
                   long long currRes, long long prevVal, vector<string>& result) {
        if (index == (int)num.length()) {
            if (currRes == target) {
                result.push_back(path);
            }
            return;
        }
        
        string curStr = "";
        long long curVal = 0;
        
        for (int i = index; i < (int)num.length(); i++) {
            // Disallow leading zeros (e.g. "05")
            if (i > index && num[index] == '0') break;
            
            curStr += num[i];
            curVal = curVal * 10 + (num[i] - '0');
            
            if (index == 0) {
                // First number (no leading operator)
                backtrack(i + 1, num, target, curStr, curVal, curVal, result);
            } else {
                // Addition: + curVal
                backtrack(i + 1, num, target, path + "+" + curStr, currRes + curVal, curVal, result);
                
                // Subtraction: - curVal
                backtrack(i + 1, num, target, path + "-" + curStr, currRes - curVal, -curVal, result);
                
                // Multiplication: - prevVal + (prevVal * curVal)
                backtrack(i + 1, num, target, path + "*" + curStr, currRes - prevVal + (prevVal * curVal), prevVal * curVal, result);
            }
        }
    }

public:
    vector<string> addOperators(string num, int target) {
        vector<string> result;
        if (num.empty()) return result;
        backtrack(0, num, target, "", 0, 0, result);
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(4^N)$ time where $N$ is length of `num`.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Evaluates expressions on the fly in $\mathcal{O}(1)$ without string evaluation parsers.

---

## 6. Dry Run

`num = "123", target = 6`

| Step | Action / State Change | Result |
|---|---|---|
| `Path 1` | "1+2+3" -> currRes = 6 == 6 | Valid expression ✅ |
| `Path 2` | "1*2*3" -> currRes = 6 == 6 | Valid expression ✅ |
| `Result` | All target matches | `["1+2+3", "1*2*3"]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Leading zeros `num = "105", target = 5` $\implies$ `["1*0+5", "10-5"]` (disallows `"1+05"`).
- 64-bit integer overflow during multiplication.

### Common Bugs to Avoid
- Forgetting `break` on leading zeros `num[index] == '0'`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is subtraction passed as -curVal for prevVal?**  
  **A**: Because if subsequent operation is multiplication: $a - b \times c = a - b + (-b \times c) - (-b) = a - b + (-b \cdot c + b)$. Passing $-curVal$ correctly applies the negative coefficient to subsequent multiplications.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Maths`, `LeetCode-282`, `Hard`
- **Related problems to practice next**:
- **Basic Calculator**: Expression evaluation.
