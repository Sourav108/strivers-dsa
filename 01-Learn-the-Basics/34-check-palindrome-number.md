# Check Palindrome Number (LeetCode 9) (Step 1.4 — Know Basic Maths)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Check Palindrome Number (LeetCode 9)](https://takeuforward.org/data-structure/check-if-a-number-is-palindrome-or-not/)
- **Difficulty**: Easy
- **Statement**: Given an integer $x$, return `true` if $x$ is a palindrome, and `false` otherwise.

---

## 1. Problem, Restated

Determine if integer reads the same forwards and backwards without converting to string.

- **Input**: Parameters specified.
- **Output**: Result meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Negative numbers are never palindromes (e.g. `-121 != 121-`). Numbers ending in 0 (except 0 itself) are never palindromes. Instead of reversing the entire number (which could overflow 32-bit ints), reverse only the **SECOND HALF** of the number until `x <= revertedHalf`. If $x == \text{revertedHalf}$ (even digits) or $x == \text{revertedHalf} / 10$ (odd digits), it is a palindrome!

- **Underlying Pattern**: `Reverting Half of the Number`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Convert to string and check two pointers in $\mathcal{O}(\log N)$ time and space.

### C++17 Code
```cpp
#include <string>
using namespace std;
bool isPalindromeString(int x) {
    if (x < 0) return false;
    string s = to_string(x);
    int l = 0, r = s.length() - 1;
    while (l < r) {
        if (s[l++] != s[r--]) return false;
    }
    return true;
}
```

### Java Code
```java
class Solution {
    boolean isPalindromeString(int x) {
        if (x < 0) return false;
        String s = String.valueOf(x);
        int l = 0, r = s.length() - 1;
        while (l < r) {
            if (s[l++] != s[r--]) return false;
        }
        return true;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} X)$ time.
- **Space Complexity**: $\mathcal{O}(\log_{10} X)$ string space.
- **Why it's not good enough**: Uses string allocation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard mathematical / optimal algorithmic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Revert only the second half of the integer arithmetic.

### C++17 Code
```cpp
class Solution {
public:
    bool isPalindrome(int x) {
        // Negative numbers and non-zero numbers ending in 0 are not palindromes
        if (x < 0 || (x % 10 == 0 && x != 0)) {
            return false;
        }
        
        int revertedNumber = 0;
        while (x > revertedNumber) {
            revertedNumber = revertedNumber * 10 + (x % 10);
            x /= 10;
        }
        
        // Even length: x == revertedNumber (1221 -> x=12, rev=12)
        // Odd length: x == revertedNumber / 10 (12321 -> x=12, rev=123 -> 123/10=12)
        return x == revertedNumber || x == revertedNumber / 10;
    }
};
```

### Java Code
```java
class Solution {

    boolean isPalindrome(int x) {
        // Negative numbers and non-zero numbers ending in 0 are not palindromes
        if (x < 0 || (x % 10 == 0 && x != 0)) {
            return false;
        }
        
        int revertedNumber = 0;
        while (x > revertedNumber) {
            revertedNumber = revertedNumber * 10 + (x % 10);
            x /= 10;
        }
        
        // Even length: x == revertedNumber (1221 . x=12, rev=12)
        // Odd length: x == revertedNumber / 10 (12321 . x=12, rev=123 . 123/10=12)
        return x == revertedNumber || x == revertedNumber / 10;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_{10} X / 2) = \mathcal{O}(\log_{10} X)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Reversing half the integer completely avoids integer overflow without auxiliary memory.

---

## 6. Dry Run

Trace for $x = 12321$

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | x = 1232, rev = 1 | x > rev (1232 > 1) |
| `Iter 2` | x = 123, rev = 12 | x > rev (123 > 12) |
| `Iter 3` | x = 12, rev = 123 | x <= rev (12 <= 123) -> loop stops |
| `Check` | x (12) == rev / 10 (123/10 = 12) -> TRUE | Palindrome! ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $x = 0$ (true)
- $x < 0$ (false)
- $x = 10$ (false, ends in 0).

### Common Bugs to Avoid
- Reversing full number without checking overflow on large numbers like $2147483647$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does reversing half the number prevent overflow?**  
  **A**: Since the second half contains at most half the digits ($\le 5$ digits for 32-bit int), `revertedNumber` never exceeds $pprox 99999 \ll 2 	imes 10^9$.


---

## 9. Tags & Related Problems

- **Tags**: `Maths`, `Palindrome`, `LeetCode-9`, `Easy`
- **Related problems to practice next**:
- **Reverse a Number**: Full reversal.
