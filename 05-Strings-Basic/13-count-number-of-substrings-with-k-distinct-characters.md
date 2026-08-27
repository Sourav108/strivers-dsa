# Count Number of Substrings with Exactly K Distinct Characters (Step 5.2 — Medium String Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Count Number of Substrings with Exactly K Distinct Characters](https://takeuforward.org/strings/count-number-of-substrings-with-k-distinct-characters/)
- **Difficulty**: Medium
- **Statement**: Given a string $s$ of lowercase alphabets and an integer $k$, count the total number of substrings that contain **exactly** $k$ distinct characters.

---

## 1. Problem, Restated

Count substrings with exactly $k$ distinct characters in $\mathcal{O}(N)$ time using sliding window difference identity.

- **Input**: Problem parameters.
- **Output**: Transformed string or calculated integer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Direct sliding window for **exact $k$** is tricky because shrinking the window when distinct $> k$ can miss valid substrings. However, counting substrings with **at most $k$** distinct characters is monotonically increasing: whenever window $[l, r]$ has $\le k$ distinct characters, every substring ending at $r$ starting from $[l..r]$ ($r - l + 1$ substrings) is valid. Therefore: $\text{Exact}(k) = \text{AtMost}(k) - \text{AtMost}(k - 1)$.

- **Underlying Pattern**: `Sliding Window Exact(k) = AtMost(k) - AtMost(k - 1)`.

---

## 3. Approach 1 — Brute Force / Naive

### Idea
Nested loops generating all $N^2$ substrings and counting distinct characters with hash set in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <string>
#include <unordered_set>
using namespace std;
long long countKDistinctBrute(string s, int k) {
    long long count = 0;
    int n = s.length();
    for (int i = 0; i < n; i++) {
        unordered_set<char> distinct;
        for (int j = i; j < n; j++) {
            distinct.insert(s[j]);
            if (distinct.size() == k) count++;
            else if (distinct.size() > k) break;
        }
    }
    return count;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(26)$ space.
- **Why it's not good enough**: Quadratic nested loops fail for $N = 10^5$.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic approach below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sliding Window `atMostK(s, k) - atMostK(s, k - 1)` in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
private:
    long long countAtMostK(const string& s, int k) {
        if (k <= 0) return 0;
        
        int n = s.length();
        int freq[26] = {0};
        int distinctCount = 0;
        int l = 0;
        long long totalSubstrings = 0;
        
        for (int r = 0; r < n; r++) {
            if (freq[s[r] - 'a'] == 0) {
                distinctCount++;
            }
            freq[s[r] - 'a']++;
            
            // Shrink window if distinct characters exceed k
            while (distinctCount > k) {
                freq[s[l] - 'a']--;
                if (freq[s[l] - 'a'] == 0) {
                    distinctCount--;
                }
                l++;
            }
            
            // All substrings ending at r starting between l and r are valid
            totalSubstrings += (r - l + 1);
        }
        
        return totalSubstrings;
    }

public:
    long long countSubStrings(string s, int k) {
        return countAtMostK(s, k) - countAtMostK(s, k - 1);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time — each character is visited at most twice (by $r$ and $l$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (26-element array).
- **Why this is optimal**: Linear sliding window avoids quadratic nested scanning.

---

## 6. Dry Run

$s = \text{"aba"}, k = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `atMost(2)` | r=0("a"): 1, r=1("ab"): 2, r=2("aba"): 3 -> Total = 6 | Substrings with <= 2 distinct: 6 |
| `atMost(1)` | r=0("a"): 1, r=1("b"): 1, r=2("a"): 1 -> Total = 3 | Substrings with <= 1 distinct: 3 |
| `Result` | atMost(2) - atMost(1) = 6 - 3 = 3 | Exact k=2 substrings: 3 (`"ab"`, `"ba"`, `"aba"`) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 1$ (`atMost(0)` returns 0).
- $k > \text{unique chars in } s$ (returns 0).

### Common Bugs to Avoid
- Using `countAtMostK(k - 1)` without handling $k - 1 = 0$ base check.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does (r - l + 1) count all valid substrings ending at r?**  
  **A**: If window $[l, r]$ has $\le k$ distinct characters, any sub-window $[i, r]$ for $l \le i \le r$ also has $\le k$ distinct characters (subsets have $\le$ distinct characters). There are exactly $r - l + 1$ such starting indices.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Sliding Window`, `Two Pointers`, `Medium`
- **Related problems to practice next**:
- **Longest Substring Without Repeating Characters**: Sliding window.
