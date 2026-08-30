# Longest Palindromic Substring (Step 5.2 — Medium String Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest Palindromic Substring](https://takeuforward.org/data-structure/longest-palindromic-substring/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$, return the longest palindromic substring in $s$.

---

## 1. Problem, Restated

Find the maximum-length contiguous substring of $s$ that reads identically forwards and backwards.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Every palindrome is symmetrical around its center. There are $2N - 1$ possible centers: $N$ single character centers (odd length e.g. `"aba"`) and $N - 1$ character-pair centers (even length e.g. `"abba"`). For each center $i$, expand two pointers $(l, r)$ outwards while $s[l] == s[r]$ in $\mathcal{O}(N)$ per center, taking $\mathcal{O}(N^2)$ time total and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Expand Around Center (Odd & Even Centroids)`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Generate all $N^2$ substrings and check palindrome in $\mathcal{O}(N^3)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;
string longestPalinBrute(string s) {
    int n = s.length();
    string best = "";
    for (int i = 0; i < n; i++) {
        for (int j = i; j < n; j++) {
            int l = i, r = j;
            bool ok = true;
            while (l < r) if (s[l++] != s[r--]) { ok = false; break; }
            if (ok && (j - i + 1) > (int)best.length()) best = s.substr(i, j - i + 1);
        }
    }
    return best;
}
```

### Java Code
```java
class Solution {
    String longestPalinBrute(String s) {
        int n = s.length();
        String best = "";
        for (int i = 0; i < n; i++) {
            for (int j = i; j < n; j++) {
                int l = i, r = j;
                boolean ok = true;
                while (l < r) if (s[l++] != s[r--]) { ok = false; break; }
                if (ok && (j - i + 1) > best.length()) best = s.substring(i, i + j - i + 1);
            }
        }
        return best;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Cubic string checks.

---

## 4. Approach 2 — Better

### Idea
Dynamic Programming Table dp[i][j] in O(N^2) time and O(N^2) memory.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;
string longestPalinDP(string s) {
    int n = s.length();
    vector<vector<bool>> dp(n, vector<bool>(n, false));
    int start = 0, maxLen = 1;
    for (int i = 0; i < n; i++) dp[i][i] = true;
    for (int len = 2; len <= n; len++) {
        for (int i = 0; i <= n - len; i++) {
            int j = i + len - 1;
            if (s[i] == s[j]) {
                if (len == 2 || dp[i + 1][j - 1]) {
                    dp[i][j] = true;
                    if (len > maxLen) { start = i; maxLen = len; }
                }
            }
        }
    }
    return s.substr(start, maxLen);
}
```

### Java Code
```java
class Solution {
    String longestPalinDP(String s) {
        int n = s.length();
        boolean[][] dp = new boolean[n][n];
        int start = 0, maxLen = 1;
        for (int i = 0; i < n; i++) dp[i][i] = true;
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s[i] == s[j]) {
                    if (len == 2 || dp[i + 1][j - 1]) {
                        dp[i][j] = true;
                        if (len > maxLen) { start = i; maxLen = len; }
                    }
                }
            }
        }
        return s.substring(start, start + maxLen);
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ matrix memory.
- **Why it's still not optimal**: Uses $N^2$ heap table.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Expand Around Center in $\mathcal{O}(N^2)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;

class Solution {
private:
    int expandAroundCenter(const string& s, int left, int right) {
        int n = s.length();
        while (left >= 0 && right < n && s[left] == s[right]) {
            left--;
            right++;
        }
        // Length of palindrome: (right - 1) - (left + 1) + 1 = right - left - 1
        return right - left - 1;
    }

public:
    string longestPalindrome(string s) {
        if (s.empty()) return "";
        
        int start = 0, maxLen = 0;
        int n = s.length();
        
        for (int i = 0; i < n; i++) {
            int lenOdd  = expandAroundCenter(s, i, i);     // odd length palindrome (center at i)
            int lenEven = expandAroundCenter(s, i, i + 1); // even length palindrome (center between i and i+1)
            int len = max(lenOdd, lenEven);
            
            if (len > maxLen) {
                maxLen = len;
                start = i - (len - 1) / 2;
            }
        }
        
        return s.substr(start, maxLen);
    }
};
```

### Java Code
```java
class Solution {

    int expandAroundCenter(String s, int left, int right) {
        int n = s.length();
        while (left >= 0 && right < n && s[left] == s[right]) {
            left--;
            right++;
        }
        // Length of palindrome: (right - 1) - (left + 1) + 1 = right - left - 1
        return right - left - 1;
    }

    String longestPalindrome(String s) {
        if (s.isEmpty()) return "";
        
        int start = 0, maxLen = 0;
        int n = s.length();
        
        for (int i = 0; i < n; i++) {
            int lenOdd  = expandAroundCenter(s, i, i);     // odd length palindrome (center at i)
            int lenEven = expandAroundCenter(s, i, i + 1); // even length palindrome (center between i and i+1)
            int len = Math.max(lenOdd, lenEven);
            
            if (len > maxLen) {
                maxLen = len;
                start = i - (len - 1) / 2;
            }
        }
        
        return s.substring(start, start + maxLen);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Avoids $\mathcal{O}(N^2)$ DP matrix allocation while achieving same runtime.

---

## 6. Dry Run

$s = \text{"babad"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Center $i = 0$ ('b')` | Odd: "b" (1), Even: "" (0) | maxLen = 1, start = 0 |
| `Center $i = 1$ ('a')` | Odd: expand "bab" (3) | maxLen = 3, start = 0 (`"bab"`) |
| `Center $i = 2$ ('b')` | Odd: expand "aba" (3) | maxLen = 3 (`"bab"` or `"aba"`) |
| `Result` | Substring of length 3 | `"bab"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single character $s = \text{"a"}$ (returns `"a"`).
- All same characters `"aaaa"` (returns `"aaaa"`).

### Common Bugs to Avoid
- Formula for start index: must use `i - (len - 1) / 2` to work uniformly for both odd and even lengths.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this be solved in linear O(N) time?**  
  **A**: Yes, **Manacher's Algorithm** computes the longest palindromic substring in strictly $\mathcal{O}(N)$ time using palindrome radius symmetry.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Two Pointers`, `Dynamic Programming`, `LeetCode-5`, `Medium`
- **Related problems to practice next**:
- **Check Palindrome String**: Base palindrome check.
