# Generate All Binary Strings without Consecutive 1s (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Generate All Binary Strings without Consecutive 1s](https://takeuforward.org/recursion/generate-all-binary-strings/)
- **Difficulty**: Medium
- **Statement**: Given an integer $K$, generate all binary strings of length $K$ such that no two `1`s are adjacent (e.g. `"11"` is not allowed).

---

## 1. Problem, Restated

Generate all valid strings where a '1' can only be appended if previous character was '0'.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

At each position: 1) We can ALWAYS append `'0'`. 2) We can ONLY append `'1'` if the string is currently empty or the last character was `'0'`. Base case: when `str.length() == K`, push to results.

- **Underlying Pattern**: `Constrained State Backtracking (Pick '0' always, Pick '1' conditionally)`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Generate all $2^K$ binary strings and filter out ones containing `"11"`.

### C++17 Code
```cpp
// Filter approach
```

### Java Code
```java
// Java equivalent
// Filter approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K \cdot 2^K)$.
- **Space Complexity**: $\mathcal{O}(K)$.
- **Why it's not good enough**: Generates invalid strings.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Pruned Backtracking: never generate invalid branches.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    void backtrack(int k, string& current, vector<string>& result) {
        if ((int)current.length() == k) {
            result.push_back(current);
            return;
        }
        
        // Choice 1: Always allowed to add '0'
        current.push_back('0');
        backtrack(k, current, result);
        current.pop_back(); // backtrack
        
        // Choice 2: Add '1' only if previous char was not '1'
        if (current.empty() || current.back() == '0') {
            current.push_back('1');
            backtrack(k, current, result);
            current.pop_back(); // backtrack
        }
    }

public:
    vector<string> generateBinaryStrings(int k) {
        vector<string> result;
        string current = "";
        backtrack(k, current, result);
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void backtrack(int k, String current, String[] result) {
        if (current.length() == k) {
            result.add(current);
            return;
        }
        
        // Choice 1: Always allowed to add '0'
        current.add('0');
        backtrack(k, current, result);
        current.remove(); // backtrack
        
        // Choice 2: Add '1' only if previous char was not '1'
        if (current.isEmpty() || current.peekLast() == '0') {
            current.add('1');
            backtrack(k, current, result);
            current.remove(); // backtrack
        }
    }

    String[] generateBinaryStrings(int k) {
        List<String> result = new ArrayList<>();
        String current = "";
        backtrack(k, current, result);
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(F(K + 2) \cdot K)$ time where $F(K)$ is Fibonacci count of valid strings.
- **Space Complexity**: $\mathcal{O}(K)$ recursion call stack space.
- **Why this is optimal**: Only explores valid string configurations.

---

## 6. Dry Run

$K = 3$

| Step | Action / State Change | Result |
|---|---|---|
| `Branches` | "000", "001", "010", "100", "101" | Total 5 strings ($F_5 = 5$) |
| `Result` | No consecutive 1s in any string | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $K = 1$ (`["0", "1"]`).
- $K = 0$ (`[""]`).

### Common Bugs to Avoid
- Missing `current.pop_back()` backtrack step.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does total count follow the Fibonacci sequence?**  
  **A**: Let $C(n)$ be valid strings of length $n$. If ending in 0, there are $C(n-1)$ choices. If ending in 1, previous must be 0, giving $C(n-2)$ choices. Thus $C(n) = C(n-1) + C(n-2) = F(n+2)$!


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Fibonacci`, `Medium`
- **Related problems to practice next**:
- **Generate Parentheses**: Constrained backtracking.
