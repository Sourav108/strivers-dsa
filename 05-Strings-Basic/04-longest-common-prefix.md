# Longest Common Prefix (Step 5.1 — Basic and Easy String Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Longest Common Prefix](https://takeuforward.org/data-structure/longest-common-prefix/)
- **Difficulty**: Easy
- **Statement**: Write a function to find the longest common prefix string amongst an array of strings. If there is no common prefix, return an empty string `""`.

---

## 1. Problem, Restated

Find the maximum initial character sequence shared across all strings in an array.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

When an array of strings is sorted lexicographically, the most divergent strings in the entire array will be the **first string** `strs[0]` and the **last string** `strs[n-1]`. The common prefix of the entire array is simply the common prefix between `strs[0]` and `strs[n-1]`!

- **Underlying Pattern**: `Lexicographical Sort Comparison / Vertical Character Scanning`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Horizontal prefix scanning comparing `strs[0]` against all strings in $\mathcal{O}(N \times L)$.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;
string lcpHorizontal(vector<string>& strs) {
    if (strs.empty()) return "";
    string prefix = strs[0];
    for (size_t i = 1; i < strs.size(); i++) {
        while (strs[i].find(prefix) != 0) {
            prefix = prefix.substr(0, prefix.length() - 1);
            if (prefix.empty()) return "";
        }
    }
    return prefix;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times L)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Standard horizontal scan.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Lexicographical Sorting: compare only first and last string.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    string longestCommonPrefix(vector<string>& strs) {
        if (strs.empty()) return "";
        
        // Lexicographically sort array
        sort(strs.begin(), strs.end());
        
        string first = strs.front();
        string last = strs.back();
        string ans = "";
        
        for (size_t i = 0; i < min(first.length(), last.length()); i++) {
            if (first[i] != last[i]) break;
            ans += first[i];
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N \cdot L)$ sorting time, $\mathcal{O}(L)$ prefix matching.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: First and last strings encapsulate the maximum character discrepancy.

---

## 6. Dry Run

`strs = ["flower", "flow", "flight"]`

| Step | Action / State Change | Result |
|---|---|---|
| `Sort` | Sorted: `["flight", "flow", "flower"]` | First: `"flight"`, Last: `"flower"` |
| `Compare` | i=0: 'f'=='f', i=1: 'l'=='l', i=2: 'i'!='o' | Common Prefix: `"fl"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single string `["a"]` -> `"a"`.
- No common prefix `["dog", "racecar"]` -> `""`.
- Empty string in array `[""]`.

### Common Bugs to Avoid
- Out of bounds access on strings of differing lengths.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does comparing first and last string after sorting work?**  
  **A**: Lexicographical sorting places strings with least matching prefixes at opposite ends of the array. If first and last share prefix $P$, all intermediate strings must share $P$.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Sorting`, `LeetCode-14`, `Easy`
- **Related problems to practice next**:
- **Trie Prefix Search**: Prefix trees.
