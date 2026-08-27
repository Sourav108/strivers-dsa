# Longest Repeating Character Replacement (Step 10.1 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Longest Repeating Character Replacement](https://takeuforward.org/data-structure/longest-repeating-character-replacement/)
- **Difficulty**: Medium
- **Statement**: You are given a string $s$ consisting of uppercase English letters and an integer $k$. You can choose any character of the string and change it to any other uppercase English character at most $k$ times. Return the length of the longest substring containing the same letter.

---

## 1. Problem, Restated

Find longest window where $(\text{windowLen} - \text{maxFreq}) \le k$.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

In any window $[left, right]$, the minimum number of replacements needed to make all characters identical is $\text{windowLen} - \max(\text{freq})$. A window is valid if $(\text{right} - \text{left} + 1) - \text{maxFreq} \le k$. Expand `right`, update `maxFreq = max(maxFreq, ++count[s[right]])`. If invalid ($> k$), shrink `left++` and decrement `count[s[left]]--`. Note: `maxFreq` does NOT need to be decremented when shrinking because a smaller `maxFreq` can never yield a longer valid window!

- **Underlying Pattern**: `Sliding Window Invariant with Max Frequency Tracking`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Check all $N^2$ substrings computing frequency counts in $\mathcal{O}(26 N^2)$ time.

### C++17 Code
```cpp
// O(26*N^2) brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(26 N^2)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic substring scans.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single Pass Sliding Window with Monotonic `maxFreq` in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int characterReplacement(string s, int k) {
        vector<int> count(26, 0);
        int left = 0, maxFreq = 0, maxLen = 0;
        int n = s.length();
        
        for (int right = 0; right < n; right++) {
            count[s[right] - 'A']++;
            maxFreq = max(maxFreq, count[s[right] - 'A']);
            
            // If replacements needed (windowLen - maxFreq) exceed budget k, shrink left
            while ((right - left + 1) - maxFreq > k) {
                count[s[left] - 'A']--;
                left++;
            }
            
            maxLen = max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ linear time.
- **Space Complexity**: $\mathcal{O}(1)$ space (26-element array).
- **Why this is optimal**: Maintains valid character replacement balance dynamically.

---

## 6. Dry Run

$s = \text{"AABABBA"}, k = 1$

| Step | Action / State Change | Result |
|---|---|---|
| `r=0..3 ("AABA")` | len=4, maxFreq=3 ('A') -> replacements = 4 - 3 = 1 <= 1 | maxLen = 4 |
| `r=4 ('B' -> "AABAB")` | len=5, maxFreq=3 -> replacements = 5 - 3 = 2 > 1 -> left advances to 1 | maxLen = 4 |
| `Result` | Longest valid window length | `4` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k \ge N$ (returns $N$).
- All identical characters `"AAAA"` (returns $N$).

### Common Bugs to Avoid
- Recomputing `maxFreq` by scanning all 26 characters on every shrink step (unnecessary $\mathcal{O}(26)$ overhead).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does maxFreq not need to be updated when shrinking left?**  
  **A**: Because to beat our previous record `maxLen`, we need a strictly LARGER `maxFreq`. Stale or slightly overestimated `maxFreq` values will only preserve the previous maximum window size without creating false larger answers!


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Strings`, `LeetCode-424`, `Medium`
- **Related problems to practice next**:
- **Max Consecutive Ones III**: Binary version.
