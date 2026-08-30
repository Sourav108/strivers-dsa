# Longest Substring with At Most K Distinct Characters (Step 10.2 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest Substring with At Most K Distinct Characters](https://takeuforward.org/data-structure/longest-substring-with-at-most-k-distinct-characters/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$ and an integer $k$, find the length of the longest substring that contains at most $k$ distinct characters.

---

## 1. Problem, Restated

Generalize Fruit Into Baskets from 2 to $k$ distinct characters.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain frequency map `mp`. Expand `right`: `mp[s[right]]++`. While `mp.size() > k`: decrement `mp[s[left]]--`, erase if count is 0, advance `left++`. Update `maxLen = max(maxLen, right - left + 1)`.

- **Underlying Pattern**: `Dynamic Sliding Window with Frequency Map ($\le k$ Keys)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Check all $N^2$ substrings with set in $\mathcal{O}(N^2)$ time.

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
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(k)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sliding Window Frequency Hash Map in $\mathcal{O}(N)$ time and $\mathcal{O}(k)$ space.

### C++17 Code
```cpp
#include <string>
#include <unordered_map>
#include <algorithm>
using namespace std;

class Solution {
public:
    int kDistinctChars(int k, string& s) {
        if (k == 0 || s.empty()) return 0;
        
        unordered_map<char, int> mp;
        int left = 0, maxLen = 0;
        int n = s.length();
        
        for (int right = 0; right < n; right++) {
            mp[s[right]]++;
            
            while ((int)mp.size() > k) {
                mp[s[left]]--;
                if (mp[s[left]] == 0) {
                    mp.erase(s[left]);
                }
                left++;
            }
            
            maxLen = max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int kDistinctChars(int k, String s) {
        if (k == 0 || s.isEmpty()) return 0;
        
        Map<Character, Integer> mp = new HashMap<>();
        int left = 0, maxLen = 0;
        int n = s.length();
        
        for (int right = 0; right < n; right++) {
            mp[s[right]]++;
            
            while (mp.length > k) {
                mp[s[left]]--;
                if (mp[s[left]] == 0) {
                    mp.remove(s[left]);
                }
                left++;
            }
            
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(k)$ space for hash map.
- **Why this is optimal**: Each character is inserted and deleted from the map at most once.

---

## 6. Dry Run

$s = \text{"eceba"}, k = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `r=0..2 ("ece")` | mp: `{e:2, c:1}`, size=2 <= 2 | maxLen = 3 |
| `r=3 ('b')` | mp: `{e:2, c:1, b:1}`, size=3 > 2 -> left advances past 'e', 'c' | mp: `{e:1, b:1}`, len = 3 |
| `Result` | Max length with 2 distinct chars | `3` (`"ece"`) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 0$ (returns 0).
- $k \ge 26$ on lowercase string (returns $N$).

### Common Bugs to Avoid
- Not erasing key when count hits 0.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How can hash map overhead be eliminated for ASCII strings?**  
  **A**: Use a 256-element integer frequency array `int count[256]` and an integer scalar `distinctCount` tracking active non-zero slots.


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Hash Map`, `Medium`
- **Related problems to practice next**:
- **Fruit Into Baskets**: K=2 instance.
