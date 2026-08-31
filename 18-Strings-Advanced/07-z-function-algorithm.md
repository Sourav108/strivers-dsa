# Z-Function / Z-Algorithm for Pattern Searching (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Z-Function / Z-Algorithm for Pattern Searching](https://takeuforward.org/strings/z-function-algorithm/)
- **Difficulty**: Hard
- **Statement**: The **Z-array** for a string `s` of length $N$ is an array where `Z[i]` is equal to the greatest number of characters starting from the position `i` that coincide with the first characters of `s`. In other words, `Z[i]` is the length of the longest string that is, at the same time, a prefix of `s` and a prefix of the suffix of `s` starting at `i`. Implement the Z-Algorithm to construct the Z-array in linear $\mathcal{O}(N)$ time and use it for pattern searching.

---

## 1. Problem, Restated

Compute the Z-array for string `s` in strictly linear $\mathcal{O}(N)$ time using the $[L, R]$ sliding segment window technique, and apply it to find all pattern occurrences in $\mathcal{O}(N + M)$ time.

- **Input**: Text and pattern strings.
- **Output**: Array of 0-based match indices or Z-array.
- **Complexity Goal**: $\mathcal{O}(N + M)$ time and space.

---

## 2. Intuition & Pattern

**The [L, R] Z-Box Invariant**: 
Maintain a segment $[L, R]$ representing the rightmost interval that matches a prefix of $s$ (i.e. $s[L \dots R] == s[0 \dots R - L]$). 
**Transitions for index $i$**: 
1) **If $i > R$ (Outside the current Z-box)**: 
   We have no prior information. Start direct character-by-character comparison from $i$: 
   - While $i + Z[i] < N$ and $s[Z[i]] == s[i + Z[i]]$: $Z[i]++$. 
   - If $Z[i] > 0$: update the window $[L, R] = [i, i + Z[i] - 1]$. 
2) **If $i \le R$ (Inside the current Z-box)**: 
   Index $i$ corresponds to prefix index $k = i - L$. 
   - **Case 2a ($Z[k] < R - i + 1$)**: The match is completely contained inside the current Z-box $\implies Z[i] = Z[k]$! (No character comparisons needed!). 
   - **Case 2b ($Z[k] \ge R - i + 1$)**: The match extends at least to the boundary $R$. Set $L = i$, and extend $R$ via direct comparisons from $R + 1$: 
     - While $R < N$ and $s[R] == s[R - L]$: $R++$. 
     - Set $Z[i] = R - L$, $R--$. 
**Pattern Searching**: Concatenate $P = \text{pattern} + \text{'\$'} + \text{text}$. Compute $Z$-array on $P$. Whenever $Z[i] == |\text{pattern}|$, a pattern match starts at text index $i - |\text{pattern}| - 1$ in $\mathcal{O}(N + M)$ time!

- **Underlying Pattern**: `Z-Algorithm / $[L, R]$ Sliding Segment Window Optimization`.

---

## 3. Approach 1 — Naive (Quadratic Nested Matching)

### Idea
For every index $i \in [1, N - 1]$, compare characters with prefix from index 0 in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

vector<int> zAlgorithmNaive(string s) {
    int n = s.size();
    vector<int> z(n, 0);
    for (int i = 1; i < n; i++) {
        while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
            z[i]++;
        }
    }
    return z;
}
```

### Java Code
```java
class Solution {
    int[] zAlgorithmNaive(String s) {
        int n = s.length;
        int[] z = new int[n];
        for (int i = 1; i < n; i++) {
            while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
                z[i]++;
            }
        }
        return z;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's not good enough**: Naive scanning re-evaluates overlapping character matches from scratch.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Z-Algorithm below directly achieves optimal $\mathcal{O}(N + M)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Linear Z-Algorithm with [L, R] Z-Box)

### Idea
Linear Z-Algorithm with $[L, R]$ Segment Window in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

class ZAlgorithm {
public:
    // Computes Z-array for string s in strictly linear O(N) time
    static vector<int> computeZ(const string& s) {
        int n = s.size();
        vector<int> z(n, 0);
        
        int l = 0, r = 0; // [l, r] defines the active Z-box
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                // Inside Z-box: copy from mirrored prefix position k = i - l
                z[i] = min(r - i + 1, z[i - l]);
            }
            
            // Try to extend match beyond the current boundary
            while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
                z[i]++;
            }
            
            // Update [l, r] if current match extends beyond r
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
        }
        
        return z;
    }
    
    // Pattern search using Z-algorithm in O(N + M) time
    static vector<int> search(const string& text, const string& pattern) {
        int n = text.size();
        int m = pattern.size();
        
        if (m == 0 || n < m) return {};
        
        // Form combined string: pattern + '$' + text
        // '$' is a sentinel delimiter not present in text or pattern
        string combined = pattern + '$' + text;
        int totalLen = combined.size();
        
        vector<int> z = computeZ(combined);
        vector<int> occurrences;
        
        // Check Z-values in the text section of the combined string
        for (int i = m + 1; i < totalLen; i++) {
            if (z[i] == m) {
                // Pattern match found: convert combined index to text 0-based index
                occurrences.push_back(i - m - 1);
            }
        }
        
        return occurrences;
    }
};
```

### Java Code
```java
class ZAlgorithm {

    // Computes Z-array for String s in strictly linear O(N) time
    static int[] computeZ(String s) {
        int n = s.length;
        int[] z = new int[n];
        
        int l = 0, r = 0; // [l, r] defines the active Z-box
        
        for (int i = 1; i < n; i++) {
            if (i <= r) {
                // Inside Z-box: copy from mirrored prefix position k = i - l
                z[i] = Math.min(r - i + 1, z[i - l]);
            }
            
            // Try to extend match beyond the current boundary
            while (i + z[i] < n && s[z[i]] == s[i + z[i]]) {
                z[i]++;
            }
            
            // Update [l, r] if current match extends beyond r
            if (i + z[i] - 1 > r) {
                l = i;
                r = i + z[i] - 1;
            }
        }
        
        return z;
    }
    
    // Pattern search using Z-algorithm in O(N + M) time
    static int[] search(String text, String pattern) {
        int n = text.length;
        int m = pattern.length;
        
        if (m == 0 || n < m) return {};
        
        // Form combined String: pattern + '$' + text
        // '$' is a sentinel delimiter not present in text or pattern
        String combined = pattern + '$' + text;
        int totalLen = combined.length;
        
        int[] z = computeZ(combined);
        int[] occurrences;
        
        // Check Z-values in the text section of the combined String
        for (int i = m + 1; i < totalLen; i++) {
            if (z[i] == m) {
                // Pattern match found: convert combined index to text 0-based index
                occurrences.add(i - m - 1);
            }
        }
        
        return occurrences;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$ time (each character in the string is evaluated at most twice as $R$ advances monotonically).
- **Space Complexity**: $\mathcal{O}(N + M)$ space (combined string and Z-array).
- **Why this is optimal**: Z-box mirroring `z[i] = min(r - i + 1, z[i - l])` skips redundant character comparisons, making $R$ strictly monotonic.

---

## 6. Dry Run

`s = "aab$baabaa"`, pattern = `"aab"`, text = `"baabaa"`

| Step | Action / State Change | Result |
|---|---|---|
| `Combined String` | `"aab$baabaa"` (len 10, m = 3) | Ready |
| `i = 1 ('a')` | $s[1] == s[0] ('a' == 'a'), s[2] != s[1] ('b' != 'a') \implies z[1] = 1, [l, r] = [1, 1]$ | z[1] = 1 |
| `i = 2 ('b')` | s[2] != s[0] $\implies z[2] = 0$ | z[2] = 0 |
| `i = 3 ('$')` | Sentinel delimiter $\implies z[3] = 0$ | z[3] = 0 |
| `i = 4 ('b')` | s[4] != s[0] ('b' != 'a') $\implies z[4] = 0$ | z[4] = 0 |
| `i = 5 ('a')` | Matches 'a', 'a', 'b' $\implies z[5] = 3, [l, r] = [5, 7]$ | z[5] = 3 == m! (Match at text index $5 - 3 - 1 = 1$) |
| `i = 6 ('a')` | Inside [5, 7]: $k = 6 - 5 = 1, z[1] = 1 \implies z[6] = 1$ | z[6] = 1 |
| `i = 7 ('b')` | z[7] = 0 | z[7] = 0 |
| `i = 8 ('a')` | Matches 'a', 'a' $\implies z[8] = 2$ | z[8] = 2 |
| `Result` | Occurrences = `[1]` (Substring `"aab"` starts at text index 1) | Match index = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Pattern is empty string (returns empty list).
- Text is shorter than pattern (returns empty list).
- Pattern does not appear in text.

### Common Bugs to Avoid
- Omitting the sentinel delimiter `'$'` (allows Z-matches to overflow across pattern boundary).
- Incorrect 0-based conversion `i - m - 1`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does the Z-Algorithm compare to KMP (Knuth-Morris-Pratt)?**  
  **A**: Both achieve strictly linear $\mathcal{O}(N + M)$ time and $\mathcal{O}(N + M)$ space. KMP computes the Longest Prefix Suffix (suffix matching), whereas Z-Algorithm computes longest prefix matches starting at every index (prefix matching). Z-Algorithm code is conceptually simpler and easier to adapt for substring prefix queries!

- **Q2: Why does the Z-Algorithm guarantee strictly linear O(N) runtime?**  
  **A**: Because the right boundary $R$ strictly moves forward from $0$ to $N - 1$. In the while loop, whenever a character matches, $R$ is incremented. Characters are compared at most once when expanding $R$, and at most once when a mismatch fails the loop. Total character comparisons $\le 2N$!

- **Q3: How to solve Longest Duplicate Substring using Z-Algorithm?**  
  **A**: Compute Z-arrays for all suffixes in $\mathcal{O}(N^2)$ to find the maximum $Z[i]$, or use Binary Search on length with Suffix Automaton / Rabin-Karp in $\mathcal{O}(N \log N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `Z-Algorithm`, `Z-Function`, `String Matching`, `Hard`
- **Related problems to practice next**:
- **KMP Algorithm**: Prefix-suffix matching.
- **Shortest Palindrome**: String matching application.
