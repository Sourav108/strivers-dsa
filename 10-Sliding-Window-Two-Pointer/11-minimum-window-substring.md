# Minimum Window Substring (Exact character match) (Step 10.2 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Window Substring (Exact character match)](https://takeuforward.org/data-structure/minimum-window-substring/)
- **Difficulty**: Hard
- **Statement**: Given two strings $s$ and $t$ of lengths $m$ and $n$ respectively, return the minimum window substring of $s$ such that every character in $t$ (including duplicates) is included in the window. If no such substring exists, return `""`.

---

## 1. Problem, Restated

Find the smallest window in $s$ that satisfies character frequency requirements of $t$ in $\mathcal{O}(N)$ time.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Fill frequency map `need` from $t$, tracking `requiredChars = need.size()`. 2) Expand `right`: if `++window[s[right]] == need[s[right]]`, increment `matchedChars++`. 3) While `matchedChars == requiredChars`: update minimum window start and length, then shrink `left++`: if `--window[s[left]] < need[s[left]]`, decrement `matchedChars--`.

- **Underlying Pattern**: `Dynamic Sliding Window with Formed Character Counter (`matchedCount == requiredCount`)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Generate all $N^2$ substrings and check $t$ frequency matching in $\mathcal{O}(N^2 \cdot 256)$ time.

### C++17 Code
```cpp
// O(N^2) brute search
```

### Java Code
```java
// Java equivalent
// O(N^2) brute search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \cdot \Sigma)$.
- **Space Complexity**: $\mathcal{O}(\Sigma)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sliding Window with Matched Distinct Character Counter in $\mathcal{O}(M + N)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <climits>
using namespace std;

class Solution {
public:
    string minWindow(string s, string t) {
        if (s.empty() || t.empty() || s.length() < t.length()) return "";
        
        vector<int> need(128, 0);
        int requiredDistinct = 0;
        for (char c : t) {
            if (need[c] == 0) requiredDistinct++;
            need[c]++;
        }
        
        vector<int> window(128, 0);
        int matchedDistinct = 0;
        int left = 0, minLen = INT_MAX, startIdx = -1;
        int n = s.length();
        
        for (int right = 0; right < n; right++) {
            char c = s[right];
            window[c]++;
            
            if (need[c] > 0 && window[c] == need[c]) {
                matchedDistinct++;
            }
            
            // Try shrinking window from the left while it remains valid
            while (matchedDistinct == requiredDistinct) {
                if (right - left + 1 < minLen) {
                    minLen = right - left + 1;
                    startIdx = left;
                }
                
                char leftChar = s[left];
                if (need[leftChar] > 0 && window[leftChar] == need[leftChar]) {
                    matchedDistinct--;
                }
                window[leftChar]--;
                left++;
            }
        }
        
        return (startIdx == -1) ? "" : s.substr(startIdx, minLen);
    }
};
```

### Java Code
```java
class Solution {

    String minWindow(String s, String t) {
        if (s.isEmpty() || t.isEmpty() || s.length() < t.length()) return "";
        
        int[] need = new int[128];
        int requiredDistinct = 0;
        for (char c : t) {
            if (need[c] == 0) requiredDistinct++;
            need[c]++;
        }
        
        int[] window = new int[128];
        int matchedDistinct = 0;
        int left = 0, minLen = Integer.MAX_VALUE, startIdx = -1;
        int n = s.length();
        
        for (int right = 0; right < n; right++) {
            char c = s[right];
            window[c]++;
            
            if (need[c] > 0 && window[c] == need[c]) {
                matchedDistinct++;
            }
            
            // Try shrinking window from the left while it remains valid
            while (matchedDistinct == requiredDistinct) {
                if (right - left + 1 < minLen) {
                    minLen = right - left + 1;
                    startIdx = left;
                }
                
                char leftChar = s[left];
                if (need[leftChar] > 0 && window[leftChar] == need[leftChar]) {
                    matchedDistinct--;
                }
                window[leftChar]--;
                left++;
            }
        }
        
        return (startIdx == -1) ? "" : s.substring(startIdx, startIdx + minLen);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M + N)$ linear time ($M = |s|, N = |t|$).
- **Space Complexity**: $\mathcal{O}(1)$ space (fixed 128-element ASCII direct arrays).
- **Why this is optimal**: Direct ASCII vector lookup and single pass window expansion/shrinkage.

---

## 6. Dry Run

$s = \text{"ADOBECODEBANC"}, t = \text{"ABC"}$ ($requiredDistinct = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `r=0..5 ("ADOBEC")` | contains A, B, C -> matched=3 -> shrinks to "ADOBEC" (len 6) | minLen = 6 |
| `r=6..10 ("CODEBA")` | contains C, B, A -> shrinks to "ODEBA" -> len 6 | minLen = 6 |
| `r=11..12 ("BANC")` | contains B, A, N, C -> shrinks to "BANC" (len 4!) | minLen = 4 |
| `Result` | Smallest valid substring | `"BANC"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $s = \text{"a"}, t = \text{"a"}$ $\implies$ `"a"`.
- $|s| < |t|$ (returns `""`).
- Duplicate characters in $t$ like `"AAB"`.

### Common Bugs to Avoid
- Checking `window[c] >= need[c]` for distinct match increment instead of `window[c] == need[c]` (increments multiple times for duplicate chars).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is window[c] == need[c] used for matching rather than window[c] >= need[c]?**  
  **A**: Because when `window[c]` transitions from `need[c] - 1` to `need[c]`, the character requirement is satisfied for the FIRST time. Checking `==` ensures `matchedDistinct` is incremented exactly once per distinct character!


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Strings`, `LeetCode-76`, `Hard`
- **Related problems to practice next**:
- **Minimum Window Subsequence**: Subsequence variant.
