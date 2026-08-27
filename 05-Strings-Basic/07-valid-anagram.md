# Check if two Strings are Anagrams of each other (Step 5.1 — Basic and Easy String Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check if two Strings are Anagrams of each other](https://takeuforward.org/data-structure/check-if-two-strings-are-anagrams-of-each-other/)
- **Difficulty**: Easy
- **Statement**: Given two strings $s$ and $t$, return `true` if $t$ is an anagram of $s$, and `false` otherwise (an anagram contains the same characters with identical frequencies in any order).

---

## 1. Problem, Restated

Verify if $s$ and $t$ have identical character multiset frequencies.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

If lengths differ, return `false`. Count character frequencies using a single 26-element array: increment `count[s[i] - 'a']++` and decrement `count[t[i] - 'a']--`. If all 26 entries equal zero at the end, $s$ and $t$ are anagrams.

- **Underlying Pattern**: `Frequency Differential Hashing (26-Array Count)`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Sort both strings and compare `s == t` in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;
bool isAnagramSort(string s, string t) {
    if (s.length() != t.length()) return false;
    sort(s.begin(), s.end());
    sort(t.begin(), t.end());
    return s == t;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Sorting overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single 26-element frequency differential array in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;

class Solution {
public:
    bool isAnagram(string s, string t) {
        if (s.length() != t.length()) return false;
        
        int freq[26] = {0};
        
        for (size_t i = 0; i < s.length(); i++) {
            freq[s[i] - 'a']++;
            freq[t[i] - 'a']--;
        }
        
        for (int i = 0; i < 26; i++) {
            if (freq[i] != 0) return false;
        }
        
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ space (26 integers).
- **Why this is optimal**: Direct array hashing with single pass differential counting.

---

## 6. Dry Run

$s = \text{"anagram"}, t = \text{"nagaram"}$

| Step | Action / State Change | Result |
|---|---|---|
| `Frequencies` | a: +3 -3 = 0, n: +1 -1 = 0, g: +1 -1 = 0, r: +1 -1 = 0, m: +1 -1 = 0 | All 26 slots = 0 |
| `Result` | All counts zero | Valid Anagram! ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Different lengths.
- Unicode / UTF-8 strings (use `unordered_map<char, int>`).

### Common Bugs to Avoid
- Forgetting `s.length() != t.length()` check.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to handle Unicode characters (e.g. Japanese, emojis)?**  
  **A**: Use `std::unordered_map<wchar_t, int>` or UTF-8 codepoint parser instead of 26-element array.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Hashing`, `LeetCode-242`, `Easy`
- **Related problems to practice next**:
- **Group Anagrams**: Sorting/Hashing keys.
