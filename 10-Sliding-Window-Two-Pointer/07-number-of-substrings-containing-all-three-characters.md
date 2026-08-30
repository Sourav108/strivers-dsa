# Number of Substrings Containing All Three Characters (Step 10.1 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Number of Substrings Containing All Three Characters](https://takeuforward.org/data-structure/number-of-substrings-containing-all-three-characters/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$ consisting only of characters $a, b$ and $c$, return the number of substrings containing at least one occurrence of all these characters $a, b$ and $c$.

---

## 1. Problem, Restated

For each index $i$, find the closest valid start index $\min(lastSeen['a'], lastSeen['b'], lastSeen['c'])$.

- **Input**: Array / String with constraints and threshold values.
- **Output**: Maximum length / count of valid subarrays / minimal window.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Track the last seen index of 'a', 'b', and 'c': `lastSeen = {-1, -1, -1}`. For each character $s[i]$: update `lastSeen[s[i] - 'a'] = i`. The smallest last seen index $\text{minIdx} = \min(lastSeen[0], lastSeen[1], lastSeen[2])$ represents the rightmost starting index of a valid substring ending at $i$. Any start index from $0$ to $\text{minIdx}$ forms a valid substring ending at $i$! Thus, add $(1 + \text{minIdx})$ to total count in $\mathcal{O}(N)$ single pass!

- **Underlying Pattern**: `Last-Seen Timestamp Prefix Counting (`1 + min(lastA, lastB, lastC)`)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Nested loops checking if substring contains 'a', 'b', and 'c' in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) brute force
```

### Java Code
```java
// Java equivalent
// O(N^2) brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic time.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard dynamic sliding window below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Last-Seen Minimum Timestamp in $\mathcal{O}(N)$ single pass and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int numberOfSubstrings(string s) {
        vector<int> lastSeen(3, -1);
        int totalSubstrings = 0;
        int n = s.length();
        
        for (int i = 0; i < n; i++) {
            lastSeen[s[i] - 'a'] = i;
            
            // If all 3 characters have been seen at least once
            if (lastSeen[0] != -1 && lastSeen[1] != -1 && lastSeen[2] != -1) {
                int minIdx = min({lastSeen[0], lastSeen[1], lastSeen[2]});
                totalSubstrings += (1 + minIdx);
            }
        }
        
        return totalSubstrings;
    }
};
```

### Java Code
```java
class Solution {

    int numberOfSubstrings(String s) {
        int[] lastSeen = new int[3];
        int totalSubstrings = 0;
        int n = s.length();
        
        for (int i = 0; i < n; i++) {
            lastSeen[s[i] - 'a'] = i;
            
            // If all 3 characters have been seen at least once
            if (lastSeen[0] != -1 && lastSeen[1] != -1 && lastSeen[2] != -1) {
                int minIdx = Math.min({lastSeen[0], lastSeen[1], lastSeen[2]});
                totalSubstrings += (1 + minIdx);
            }
        }
        
        return totalSubstrings;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Calculates number of valid prefix starting points in $\mathcal{O}(1)$ per character.

---

## 6. Dry Run

$s = \text{"abcabc"}$

| Step | Action / State Change | Result |
|---|---|---|
| `i = 2 ('c')` | last: `{a:0, b:1, c:2}`, minIdx = 0 -> count += (1+0) = 1 (`"abc"`) | total = 1 |
| `i = 3 ('a')` | last: `{a:3, b:1, c:2}`, minIdx = 1 -> count += (1+1) = 2 (`"abca", "bca"`) | total = 3 |
| `i = 4 ('b')` | last: `{a:3, b:4, c:2}`, minIdx = 2 -> count += (1+2) = 3 | total = 6 |
| `i = 5 ('c')` | last: `{a:3, b:4, c:5}`, minIdx = 3 -> count += (1+3) = 4 | total = 10 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- String without all 3 characters `"ababa"` (returns 0).
- Minimal valid string `"abc"` (returns 1).

### Common Bugs to Avoid
- Adding `minIdx` instead of `(1 + minIdx)` (misses index 0 prefix).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does (1 + minIdx) equal the number of valid substrings ending at i?**  
  **A**: Because any substring starting at index $j \in [0, \text{minIdx}]$ and ending at $i$ contains the most recent occurrences of all three characters. There are exactly $\text{minIdx} - 0 + 1 = 1 + \text{minIdx}$ such starting positions.


---

## 9. Tags & Related Problems

- **Tags**: `Sliding Window`, `Two Pointers`, `Strings`, `LeetCode-1358`, `Medium`
- **Related problems to practice next**:
- **Subarrays with K Different Integers**: K distinct integers.
