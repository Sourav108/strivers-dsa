# Largest Odd Number in a String (Step 5.1 — Basic and Easy String Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Largest Odd Number in a String](https://takeuforward.org/strings/largest-odd-number-in-a-string/)
- **Difficulty**: Easy
- **Statement**: Given a string `num` representing a large integer, return the largest-valued odd integer (as a substring) that is a non-empty substring of `num`, or an empty string `""` if no odd integer exists.

---

## 1. Problem, Restated

Find the longest prefix ending at the rightmost odd digit.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

A decimal integer is odd if and only if its **last digit is odd** (1, 3, 5, 7, 9). To maximize substring value, we should keep the longest possible prefix. Scan from the end of the string to the left: the first odd digit found at index $i$ gives the maximum substring $num[0..i]$.

- **Underlying Pattern**: `Right-to-Left Boundary Scanning`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Generate all substrings and check odd parity.

### C++17 Code
```cpp
// O(N^2) substring generation
```

### Java Code
```java
// Java equivalent
// O(N^2) substring generation
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Right-to-left linear scan for the first odd digit in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;

class Solution {
public:
    string largestOddNumber(string num) {
        // Scan backwards from the last character
        for (int i = (int)num.length() - 1; i >= 0; i--) {
            int digit = num[i] - '0';
            if (digit % 2 != 0) {
                return num.substr(0, i + 1); // longest odd prefix found
            }
        }
        return "";
    }
};
```

### Java Code
```java
class Solution {

    String largestOddNumber(String num) {
        // Scan backwards from the last character
        for (int i = num.length() - 1; i >= 0; i--) {
            int digit = num[i] - '0';
            if (digit % 2 != 0) {
                return num.substring(0, 0 + i + 1); // longest odd prefix found
            }
        }
        return "";
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Stops on the first odd digit from right.

---

## 6. Dry Run

$num = \text{"35427"}$

| Step | Action / State Change | Result |
|---|---|---|
| `$i = 4$` | num[4] = '7' -> odd! | Return `num.substr(0, 5)` -> `"35427"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No odd digits `"4206"` -> `""`.
- All odd digits `"357"` -> `"357"`.
- Single digit `"5"` -> `"5"`.

### Common Bugs to Avoid
- Parsing to `long long` (strings can have $10^5$ digits, exceeding 64-bit integers).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does rightmost odd digit guarantee maximum value?**  
  **A**: In decimal representation, substring length determines magnitude. The prefix from index 0 to the rightmost odd digit has maximum possible digit length.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Greedy`, `Maths`, `LeetCode-1903`, `Easy`
- **Related problems to practice next**:
- **Count Digits**: Digit properties.
