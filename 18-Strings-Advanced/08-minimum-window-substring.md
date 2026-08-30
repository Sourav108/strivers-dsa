# Minimum Window Substring (Character Frequency Sliding Window) (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Window Substring (Character Frequency Sliding Window)](https://takeuforward.org/data-structure/minimum-window-substring/)
- **Difficulty**: Hard
- **Statement**: Given two strings `s` and `t` of lengths $m$ and $n$ respectively, return the minimum window substring of `s` such that every character in `t` (including duplicates) is included in the window. If there is no such substring, return the empty string `""`. The testcases will be generated such that the answer is unique.

---

## 1. Problem, Restated

Find the shortest substring of `s` containing all characters of `t` with their exact frequencies using an expandable/shrinkable Sliding Window with an ASCII frequency map in $\mathcal{O}(|s| + |t|)$ time and $\mathcal{O}(128) = \mathcal{O}(1)$ space.

- **Input**: Strings `s` and `t`.
- **Output**: Minimum window substring or `""`.
- **Complexity Goal**: $\mathcal{O}(|s| + |t|)$ time and $\mathcal{O}(1)$ space.

---

## 2. Intuition & Pattern

**The Variable-Length Sliding Window Invariant**: 
1) **Target Frequency Setup**: 
   Store character frequencies of `t` in an array `hash[128]`. 
   Maintain `count = 0` (number of characters of `t` currently matched inside the active window) and `minLen = infinity`. 
2) **Expand Right ($r$)**: 
   For each character `s[r]`: 
   - If `hash[s[r]] > 0`: this character is NEEDED by `t`, so increment `count++`. 
   - Decrement `hash[s[r]]--` (negative values represent surplus characters). 
3) **Shrink Left ($l$) when Window is Valid (`count == t.size()`)**: 
   While all characters are matched (`count == t.size()`): 
   - Update minimum window: if $r - l + 1 < \text{minLen}$, update `minLen = r - l + 1`, `sIndex = l`. 
   - Remove `s[l]` from the left: increment `hash[s[l]]++`. 
   - If `hash[s[l]] > 0`: we just removed a strictly required character! Decrement `count--` (breaking validity). 
   - Advance `l++`. 
4) Return `s.substr(sIndex, minLen)`. 
**Complexity**: Pointers $l$ and $r$ traverse $s$ at most once, running in strictly linear $\mathcal{O}(|s| + |t|)$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Two-Pointer Sliding Window with Dynamic Match Counter (`count == t.size()`)`.

---

## 3. Approach 1 — Naive (All Substrings Check)

### Idea
Generate all $\mathcal{O}(N^2)$ substrings of $s$ and check if all characters of $t$ are contained in $\mathcal{O}(N^2 \times M)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class SolutionNaive {
public:
    string minWindow(string s, string t) {
        int n = s.size(), m = t.size(), minLen = 1e9, start = -1;
        vector<int> tFreq(128, 0);
        for (char c : t) tFreq[c]++;
        
        for (int i = 0; i < n; i++) {
            vector<int> sFreq(128, 0);
            for (int j = i; j < n; j++) {
                sFreq[s[j]]++;
                bool ok = true;
                for (int c = 0; c < 128; c++) {
                    if (sFreq[c] < tFreq[c]) { ok = false; break; }
                }
                if (ok) {
                    if (j - i + 1 < minLen) {
                        minLen = j - i + 1;
                        start = i;
                    }
                    break;
                }
            }
        }
        return start == -1 ? "" : s.substr(start, minLen);
    }
};
```

### Java Code
```java
class SolutionNaive {

    String minWindow(String s, String t) {
        int n = s.length, m = t.length, minLen = 1e9, start = -1;
        int[] tFreq = new int[128];
        for (char c : t) tFreq[c]++;
        
        for (int i = 0; i < n; i++) {
            int[] sFreq = new int[128];
            for (int j = i; j < n; j++) {
                sFreq[s[j]]++;
                boolean ok = true;
                for (int c = 0; c < 128; c++) {
                    if (sFreq[c] < tFreq[c]) { ok = false; break; }
                }
                if (ok) {
                    if (j - i + 1 < minLen) {
                        minLen = j - i + 1;
                        start = i;
                    }
                    break;
                }
            }
        }
        return start == -1 ? "" : s.substring(start, start + minLen);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \times 128)$ time.
- **Space Complexity**: $\mathcal{O}(128)$ space.
- **Why it's not good enough**: Quadratic nested window checks.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Two-Pointer Sliding Window below directly achieves optimal $\mathcal{O}(|s| + |t|)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Two-Pointer Sliding Window)

### Idea
Two-Pointer Sliding Window with ASCII Array and Match Counter in $\mathcal{O}(|s| + |t|)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    string minWindow(string s, string t) {
        int n = s.size();
        int m = t.size();
        
        if (n < m || m == 0) return "";
        
        // hash stores character frequencies of t
        // Positive: required characters needed to form t
        // Zero / Negative: surplus characters in active window
        vector<int> hash(128, 0);
        for (char c : t) {
            hash[c]++;
        }
        
        int l = 0, r = 0;
        int minLen = 1e9;
        int sIndex = -1;
        int count = 0; // Number of matched characters from t
        
        while (r < n) {
            // If character was needed, increment matched count
            if (hash[s[r]] > 0) {
                count++;
            }
            // Decrement frequency in map (surplus characters become negative)
            hash[s[r]]--;
            
            // While current window [l ... r] satisfies all characters of t
            while (count == m) {
                // Update shortest valid window found so far
                if (r - l + 1 < minLen) {
                    minLen = r - l + 1;
                    sIndex = l;
                }
                
                // Try shrinking window from the left
                hash[s[l]]++;
                if (hash[s[l]] > 0) {
                    // Removed a character that was strictly required by t
                    count--;
                }
                l++;
            }
            
            r++;
        }
        
        return sIndex == -1 ? "" : s.substr(sIndex, minLen);
    }
};
```

### Java Code
```java
class Solution {

    String minWindow(String s, String t) {
        int n = s.length;
        int m = t.length;
        
        if (n < m || m == 0) return "";
        
        // hash stores character frequencies of t
        // Positive: required characters needed to form t
        // Zero / Negative: surplus characters in active window
        int[] hash = new int[128];
        for (char c : t) {
            hash[c]++;
        }
        
        int l = 0, r = 0;
        int minLen = 1e9;
        int sIndex = -1;
        int count = 0; // Number of matched characters from t
        
        while (r < n) {
            // If character was needed, increment matched count
            if (hash[s[r]] > 0) {
                count++;
            }
            // Decrement frequency in map (surplus characters become negative)
            hash[s[r]]--;
            
            // While current window [l ... r] satisfies all characters of t
            while (count == m) {
                // Update shortest valid window found so far
                if (r - l + 1 < minLen) {
                    minLen = r - l + 1;
                    sIndex = l;
                }
                
                // Try shrinking window from the left
                hash[s[l]]++;
                if (hash[s[l]] > 0) {
                    // Removed a character that was strictly required by t
                    count--;
                }
                l++;
            }
            
            r++;
        }
        
        return sIndex == -1 ? "" : s.substring(sIndex, sIndex + minLen);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(|s| + |t|)$ time (frequency map init: $\mathcal{O}(|t|)$; $l$ and $r$ pointers advance at most $|s|$ times $\implies \mathcal{O}(|s|)$).
- **Space Complexity**: $\mathcal{O}(128) = \mathcal{O}(1)$ auxiliary space (fixed ASCII array).
- **Why this is optimal**: Two-pointer sliding window ensures each character in $s$ enters and exits the window at most once.

---

## 6. Dry Run

`s = "ADOBECODEBANC"`, `t = "ABC"` ($m = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `r = 0..5 ("ADOBEC")` | Contains 'A', 'B', 'C' $\implies count = 3 == m$. Valid window `"ADOBEC"` (len 6) | minLen = 6, sIndex = 0 |
| `Shrink l=0..1` | Remove 'A' (hash['A']=1 > 0 $\implies count=2$). l becomes 1 (`"DOBEC"`) | Window broken, resume expand |
| `r = 6..10 ("CODEBA")` | Finds 'A' at r=10 $\implies count = 3$. Valid window `"DOBECODEBA"` | count = 3 |
| `r = 12 ("BANC")` | Window contains 'B', 'A', 'N', 'C'. Shrinks to `"BANC"` of length 4 $\implies minLen = 4, sIndex = 9$ | minLen = 4 (`"BANC"`) |
| `Result` | Return `s.substr(9, 4) = "BANC"` | Minimum Window = "BANC" ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $|s| < |t|$ (returns `""`).
- String $s == t$ (returns $s$).
- $t$ has duplicate characters (e.g. `"AA"`).
- No matching window exists.

### Common Bugs to Avoid
- Using boolean array instead of integer count (fails on duplicate characters like `"AABC"`).
- Shrinking with `l <= r` when `count == m` without updating `minLen` correctly.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does tracking surplus characters with negative numbers optimize hash map checks?**  
  **A**: By allowing `hash[c]` to become negative, `hash[c] > 0` directly and instantly indicates whether a character is strictly needed! We never have to iterate through the entire 128-element hash array to verify window validity, reducing inner loop check to $\mathcal{O}(1)$!

- **Q2: How to optimize when s has massive runs of irrelevant characters (Filtered S list)?**  
  **A**: Create a filtered list `filtered_s` containing only pairs `(index, char)` for characters present in $t$. Slide window over `filtered_s` in $\mathcal{O}(|filtered\_s|)$ time!

- **Q3: How does Minimum Window Substring differ from Longest Substring Without Repeating Characters?**  
  **A**: In Longest Substring, we maximize window size subject to uniqueness. In Minimum Window Substring, we MINIMIZE window size subject to completeness of multiset $t$!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Sliding Window`, `Two Pointers`, `Hash Table`, `LeetCode-76`, `Hard`
- **Related problems to practice next**:
- **Longest Substring Without Repeating Characters**: Sliding window.
- **Find All Anagrams**: Fixed window size.
