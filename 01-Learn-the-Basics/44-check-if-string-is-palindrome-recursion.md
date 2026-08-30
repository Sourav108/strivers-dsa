# Check if a String is Palindrome using Recursion (Step 1.5 — Learn Basic Recursion)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Check if a String is Palindrome using Recursion](https://takeuforward.org/data-structure/check-if-the-given-string-is-palindrome-or-not/)
- **Difficulty**: Easy
- **Statement**: Given a string $S$, check if it is a palindrome using recursion.

---

## 1. Problem, Restated

Verify if $S[i] == S[n - 1 - i]$ for all $i \in [0, n/2]$.

- **Input**: Parameters specified.
- **Output**: Value or side-effect meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Base case: if $i \ge n / 2$, all pairs matched $\implies$ return `true`. If $S[i] \ne S[n - 1 - i]$, mismatch found $\implies$ return `false`. Otherwise, return recursive call `isPal(i + 1)`.

- **Underlying Pattern**: `Recursive Character Symmetry Check`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Reversing string and comparing `s == rev` in $\mathcal{O}(N)$ time and space.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;
bool isPalSlow(string s) {
    string rev = s;
    reverse(rev.begin(), rev.end());
    return s == rev;
}
```

### Java Code
```java
class Solution {
    boolean isPalSlow(String s) {
        String rev = s;
        reverse(rev.begin(), rev.end());
        return s == rev;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ string memory.
- **Why it's not good enough**: Creates redundant copy.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Zero-copy recursive symmetry check on `const string&`.

### C++17 Code
```cpp
#include <string>
using namespace std;

class Solution {
private:
    bool isPalHelper(const string& s, int i, int n) {
        if (i >= n / 2) return true;
        if (s[i] != s[n - 1 - i]) return false;
        return isPalHelper(s, i + 1, n);
    }

public:
    bool isPalindrome(const string& s) {
        return isPalHelper(s, 0, s.length());
    }
};
```

### Java Code
```java
class Solution {

    boolean isPalHelper(String s, int i, int n) {
        if (i >= n / 2) return true;
        if (s[i] != s[n - 1 - i]) return false;
        return isPalHelper(s, i + 1, n);
    }

    boolean isPalindrome(String s) {
        return isPalHelper(s, 0, s.length());
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N / 2) = \mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N / 2)$ stack frames.
- **Why this is optimal**: Zero heap allocations, early exit on first mismatch.

---

## 6. Dry Run

Check $S = \text{"racecar"}$ ($n = 7$)

| Step | Action / State Change | Result |
|---|---|---|
| `$i = 0$` | 'r' == 'r' | Match, recurse i=1 |
| `$i = 1$` | 'a' == 'a' | Match, recurse i=2 |
| `$i = 2$` | 'c' == 'c' | Match, recurse i=3 |
| `$i = 3$` | $i \ge 7/2 (3 \ge 3)$ | Base case returns TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty string (true)
- Single character (true)
- Case sensitivity and alphanumeric filtering (LeetCode 125).

### Common Bugs to Avoid
- Passing `string` by value.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to handle LeetCode 125 Valid Palindrome with spaces and punctuation?**  
  **A**: Clean non-alphanumeric characters with `isalnum()` and `tolower()` during two-pointer traversal.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Strings`, `Easy`
- **Related problems to practice next**:
- **Reverse an Array**: Array reversal.
