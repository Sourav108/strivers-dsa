# Letter Combinations of a Phone Number (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Letter Combinations of a Phone Number](https://takeuforward.org/recursion/letter-combinations-of-a-phone-number/)
- **Difficulty**: Medium
- **Statement**: Given a string containing digits from `2-9` inclusive, return all possible letter combinations that the number could represent based on classic phone keypad mapping (`2:"abc", 3:"def", 4:"ghi", 5:"jkl", 6:"mno", 7:"pqrs", 8:"tuv", 9:"wxyz"`).

---

## 1. Problem, Restated

Compute the Cartesian product of character sets corresponding to phone keypad digits.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Define mapping array `mapping = {"", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"}`. At index `ind` of `digits`: loop through all letters in `mapping[digits[ind] - '0']`, append letter, recurse `ind + 1`, backtrack.

- **Underlying Pattern**: `Multi-Choice Tree Backtracking (Cartesian Product)`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Nested loops for up to 4 digits.

### C++17 Code
```cpp
// 4 nested loops
```

### Java Code
```java
// Java equivalent
// 4 nested loops
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(4^N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Inflexible for arbitrary string length.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Backtracking with Keypad Lookup Table.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    const string mapping[10] = {
        "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
    };
    
    void backtrack(int index, const string& digits, string& current, vector<string>& result) {
        if (index == (int)digits.length()) {
            result.push_back(current);
            return;
        }
        
        string letters = mapping[digits[index] - '0'];
        for (char ch : letters) {
            current.push_back(ch);
            backtrack(index + 1, digits, current, result);
            current.pop_back(); // backtrack
        }
    }

public:
    vector<string> letterCombinations(string digits) {
        if (digits.empty()) return {};
        
        vector<string> result;
        string current = "";
        backtrack(0, digits, current, result);
        return result;
    }
};
```

### Java Code
```java
class Solution {

    String mapping[10] = {
        "", "", "abc", "def", "ghi", "jkl", "mno", "pqrs", "tuv", "wxyz"
    };
    
    void backtrack(int index, String digits, String current, String[] result) {
        if (index == digits.length()) {
            result.add(current);
            return;
        }
        
        String letters = mapping[digits[index] - '0'];
        for (char ch : letters) {
            current.add(ch);
            backtrack(index + 1, digits, current, result);
            current.remove(); // backtrack
        }
    }

    String[] letterCombinations(String digits) {
        if (digits.isEmpty()) return {};
        
        String[] result;
        String current = "";
        backtrack(0, digits, current, result);
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(4^N \cdot N)$ time where $N \le 4$ is length of digits string.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Generates all $3^{N_1} \times 4^{N_2}$ valid combinations directly.

---

## 6. Dry Run

$digits = \text{"23"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Digit 2 ('abc')` | Branch 'a', 'b', 'c' | 3 choices |
| `Digit 3 ('def')` | For 'a': "ad", "ae", "af" | 3 combinations |
| `For 'b' and 'c'` | "bd", "be", "bf", "cd", "ce", "cf" | 6 combinations |
| `Result` | Total $3 \times 3 = 9$ strings | `["ad","ae","af","bd","be","bf","cd","ce","cf"]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty input `""` (returns `[]`, not `[""]`).
- Digits with 4 letters (`'7'` and `'9'`).

### Common Bugs to Avoid
- Returning `[""]` for empty input.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the maximum number of combinations for length 4?**  
  **A**: Digits `7777` or `9999` yield $4^4 = 256$ combinations.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `LeetCode-17`, `Medium`
- **Related problems to practice next**:
- **Generate Parentheses**: Tree backtracking.
