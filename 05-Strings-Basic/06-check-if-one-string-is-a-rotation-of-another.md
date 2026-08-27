# Check if one String is a Rotation of Another (Step 5.1 — Basic and Easy String Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check if one String is a Rotation of Another](https://takeuforward.org/strings/check-if-one-string-is-a-rotation-of-another/)
- **Difficulty**: Easy
- **Statement**: Given two strings $s$ and $goal$, return `true` if and only if $s$ can become $goal$ after some number of shifts (a shift consists of moving the leftmost character of $s$ to the rightmost position).

---

## 1. Problem, Restated

Determine if $goal$ is a cyclic shift of $s$.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Every possible cyclic rotation of string $s$ of length $N$ is a contiguous substring of length $N$ inside the doubled string $s + s$. For example, $s = \text{"abcde"} \implies s + s = \text{"abcdeabcde"}$, which contains `"bcdea"`, `"cdeab"`, `"deabc"`, `"eabcd"`, and `"abcde"`! Check `if (s.length() == goal.length() && (s + s).find(goal) != string::npos)`.

- **Underlying Pattern**: `String Concatenation Substring Invariant ($s + s$ contains all rotations)`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Simulate all $N$ manual rotations and compare in $\mathcal{O}(N^2)$.

### C++17 Code
```cpp
#include <string>
using namespace std;
bool rotateStringBrute(string s, string goal) {
    if (s.length() != goal.length()) return false;
    for (size_t i = 0; i < s.length(); i++) {
        s = s.substr(1) + s[0];
        if (s == goal) return true;
    }
    return false;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ substring memory.
- **Why it's not good enough**: Repeated rotation substrings.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Doubled string search: `(s + s).find(goal) != string::npos`.

### C++17 Code
```cpp
#include <string>
using namespace std;

class Solution {
public:
    bool rotateString(string s, string goal) {
        if (s.length() != goal.length()) return false;
        
        // All rotations of s exist as substrings within (s + s)
        string doubled = s + s;
        return doubled.find(goal) != string::npos;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ using KMP substring search.
- **Space Complexity**: $\mathcal{O}(N)$ for doubled string.
- **Why this is optimal**: Mathematical invariant captures all cyclic permutations in single string.

---

## 6. Dry Run

$s = \text{"abcde"}, goal = \text{"cdeab"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Double string` | s + s = "abcdeabcde" | Length = 10 |
| `Search goal` | "abcdeabcde".find("cdeab") -> Index 2 | Substring Found -> Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Different string lengths (instant `false`).
- Empty strings.

### Common Bugs to Avoid
- Forgetting length check `s.length() == goal.length()` (without it, `s = "a", goal = "aa"` returns true).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does (s + s) contain all rotations?**  
  **A**: Any rotation partitions $s = A + B$ and transforms it into $B + A$. Notice $(s + s) = (A + B) + (A + B) = A + (B + A) + B$, which explicitly contains $B + A$ as an interior substring!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Cyclic Shift`, `LeetCode-796`, `Easy`
- **Related problems to practice next**:
- **KMP Algorithm**: Linear substring search.
