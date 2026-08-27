# Longest Substring Without Repeating Characters (Step 10.1 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Longest Substring Without Repeating Characters](https://takeuforward.org/data-structure/length-of-longest-substring-without-any-repeating-character/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$, find the length of the longest substring without repeating characters.

---

## 1. Problem, Restated

Find the maximum window length $[left, right]$ containing all unique characters.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain an array `lastSeen[256]` initialized to -1. Expand `right` pointer across $s$. If character $s[right]$ was previously seen at `lastSeen[s[right]] >= left`, jump `left` forward directly to `lastSeen[s[right]] + 1` (avoiding step-by-step while-loop shrinking!). Update `lastSeen[s[right]] = right` and `maxLen = max(maxLen, right - left + 1)`. Runs in strict $\mathcal{O}(N)$ single pass!

- **Underlying Pattern**: `Sliding Window with Last-Seen Index Direct Jumping (`left = max(left, lastSeen[c] + 1)`)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Generate all $N^2$ substrings, checking uniqueness with a set in $\mathcal{O}(N^3)$ time.

### C++17 Code
```cpp
#include <string>
#include <unordered_set>
#include <algorithm>
using namespace std;
int lengthOfLongestSubstringBrute(string s) {
    int maxLen = 0, n = s.length();
    for (int i = 0; i < n; i++) {
        unordered_set<char> st;
        for (int j = i; j < n; j++) {
            if (st.count(s[j])) break;
            st.insert(s[j]);
            maxLen = max(maxLen, j - i + 1);
        }
    }
    return maxLen;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(\min(N, \Sigma))$.
- **Why it's not good enough**: Quadratic substring scans.

---

## 4. Approach 2 — Better

### Idea
Sliding Window with unordered_set shrinking left one step at a time in O(2N) time.

### C++17 Code
```cpp
#include <string>
#include <unordered_set>
#include <algorithm>
using namespace std;
int lengthOfLongestSubstringSet(string s) {
    unordered_set<char> st;
    int left = 0, maxLen = 0, n = s.length();
    for (int right = 0; right < n; right++) {
        while (st.count(s[right])) {
            st.erase(s[left]);
            left++;
        }
        st.insert(s[right]);
        maxLen = max(maxLen, right - left + 1);
    }
    return maxLen;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2N)$ time.
- **Space Complexity**: $\mathcal{O}(\Sigma)$ space.
- **Why it's still not optimal**: Left pointer advances incrementally.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct Last-Seen Index Jumping in $\mathcal{O}(N)$ single pass and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        vector<int> lastSeen(256, -1);
        int left = 0, maxLen = 0;
        int n = s.length();
        
        for (int right = 0; right < n; right++) {
            char c = s[right];
            
            // If character was seen inside the current window, jump left directly
            if (lastSeen[(unsigned char)c] != -1) {
                left = max(left, lastSeen[(unsigned char)c] + 1);
            }
            
            lastSeen[(unsigned char)c] = right; // update last seen index
            maxLen = max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ strict single pass.
- **Space Complexity**: $\mathcal{O}(1)$ space (fixed 256-element direct ASCII lookup array).
- **Why this is optimal**: Direct index jumping eliminates repetitive left pointer while-loops.

---

## 6. Dry Run

$s = \text{"abcabcbb"}$

| Step | Action / State Change | Result |
|---|---|---|
| `r=0 ('a')` | left=0, maxLen=1 | lastSeen['a']=0 |
| `r=1 ('b')` | left=0, maxLen=2 | lastSeen['b']=1 |
| `r=2 ('c')` | left=0, maxLen=3 | lastSeen['c']=2 |
| `r=3 ('a')` | seen at 0 >= left(0) -> left = 0+1 = 1, maxLen=3 | lastSeen['a']=3 |
| `r=4 ('b')` | seen at 1 >= left(1) -> left = 1+1 = 2, maxLen=3 | lastSeen['b']=4 |
| `Result` | Max unique window length = 3 (`"abc"`) | Result = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty string `""` (returns 0).
- All identical characters `"bbbbb"` (returns 1).
- All unique `"abcdef"` (returns 6).

### Common Bugs to Avoid
- Setting `left = lastSeen[c] + 1` without `max(left, ...)` (moves left backwards if character was seen outside current window).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is left = max(left, lastSeen[c] + 1) mandatory?**  
  **A**: Because `lastSeen[c]` might store an index that appeared BEFORE the current `left` pointer. Taking the maximum ensures `left` only moves forward.


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Strings`, `LeetCode-3`, `Medium`
- **Related problems to practice next**:
- **Longest Substring with At Most K Distinct**: K distinct variant.
