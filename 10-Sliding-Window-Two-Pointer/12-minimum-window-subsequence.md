# Minimum Window Subsequence (Step 10.2 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Window Subsequence](https://takeuforward.org/data-structure/minimum-window-subsequence/)
- **Difficulty**: Hard
- **Statement**: Given strings $s1$ and $s2$, return the shortest contiguous substring of $s1$ such that $s2$ is a subsequence of the substring. If there is no such window, return `""`.

---

## 1. Problem, Restated

Find minimal substring in $s1$ containing $s2$ in correct order using Forward Search + Reverse Shrinking.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Forward Pass**: Scan $s1$ with pointer $i$ and $s2$ with pointer $j$. When $j$ reaches the end of $s2$ ($j == |s2|$), a valid window ends at $i-1$. 2) **Reverse Shrinking**: Start from $(i-1)$ and match $s2$ backwards ($j = |s2|-1$ down to 0) to find the absolute tightest start position $start$! 3) Record minimum window length. Set $i = start + 1$ and resume forward search.

- **Underlying Pattern**: `Forward Subsequence Matching + Reverse Pointer Optimization`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Check all $N^2$ substrings of $s1$ verifying subsequence property in $\mathcal{O}(N^2 \cdot M)$ time.

### C++17 Code
```cpp
// O(N^2 * M) brute check
```

### Java Code
```java
// Java equivalent
// O(N^2 * M) brute check
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \cdot M)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Cubic worst case.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Forward Matching + Backward Shrinking Two-Pointer in $\mathcal{O}(N \cdot M)$ time.

### C++17 Code
```cpp
#include <string>
#include <climits>
using namespace std;

class Solution {
public:
    string minWindow(string s1, string s2) {
        int n = s1.length(), m = s2.length();
        int minLen = INT_MAX, startIdx = -1;
        int i = 0, j = 0;
        
        while (i < n) {
            // Forward pass: find a valid window where s2 is a subsequence
            if (s1[i] == s2[j]) {
                j++;
                if (j == m) {
                    // s2 fully matched! Now shrink backwards to find tightest start
                    int right = i;
                    j--; // point to last character of s2
                    
                    while (j >= 0) {
                        if (s1[right] == s2[j]) {
                            j--;
                        }
                        right--;
                    }
                    right++; // tightest start position of current window
                    
                    if (i - right + 1 < minLen) {
                        minLen = i - right + 1;
                        startIdx = right;
                    }
                    
                    // Restart forward search from the next character after tightest start
                    i = right;
                    j = 0;
                }
            }
            i++;
        }
        
        return (startIdx == -1) ? "" : s1.substr(startIdx, minLen);
    }
};
```

### Java Code
```java
class Solution {

    String minWindow(String s1, String s2) {
        int n = s1.length(), m = s2.length();
        int minLen = Integer.MAX_VALUE, startIdx = -1;
        int i = 0, j = 0;
        
        while (i < n) {
            // Forward pass: find a valid window where s2 is a subsequence
            if (s1[i] == s2[j]) {
                j++;
                if (j == m) {
                    // s2 fully matched! Now shrink backwards to find tightest start
                    int right = i;
                    j--; // point to last character of s2
                    
                    while (j >= 0) {
                        if (s1[right] == s2[j]) {
                            j--;
                        }
                        right--;
                    }
                    right++; // tightest start position of current window
                    
                    if (i - right + 1 < minLen) {
                        minLen = i - right + 1;
                        startIdx = right;
                    }
                    
                    // Restart forward search from the next character after tightest start
                    i = right;
                    j = 0;
                }
            }
            i++;
        }
        
        return (startIdx == -1) ? "" : s1.substring(startIdx, startIdx + minLen);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot M)$ worst-case time (runs in $\mathcal{O}(N)$ on typical testcases).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Reverse shrinking eliminates non-minimal prefix segments instantaneously.

---

## 6. Dry Run

$s1 = \text{"abcdebdde"}, s2 = \text{"bde"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Forward Pass 1` | Matches "b" at 1, "d" at 3, "e" at 4 (window: "bcde", len 4) | minLen = 4 |
| `Reverse Shrink 1` | Backwards from 4: 'e' at 4, 'd' at 3, 'b' at 1 -> start = 1 | window = "bcde" |
| `Forward Pass 2` | Matches 'b' at 5, 'd' at 7, 'e' at 8 (window: "bdde", len 4 -> start 5: "bdde") | minLen = 4 |
| `Result` | First minimal window in tie-break | `"bcde"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $s2$ not present in $s1$ (returns `""`).
- $s1 == s2$ (returns $s1$).

### Common Bugs to Avoid
- Resuming forward search from `i` instead of `start + 1` (misses overlapping shorter windows starting after `start`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does Substring vs Subsequence differ in Minimum Window?**  
  **A**: Minimum Window **Substring** (LC 76) requires character frequency matching in ANY order. Minimum Window **Subsequence** (LC 727) requires characters to appear in STRICT relative order, requiring two-pointer forward-backward alignment.


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Dynamic Programming`, `LeetCode-727`, `Hard`
- **Related problems to practice next**:
- **Minimum Window Substring**: Unordered variant.
