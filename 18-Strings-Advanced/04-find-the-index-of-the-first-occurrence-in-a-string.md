# Find the Index of the First Occurrence in a String (KMP LPS Array) (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find the Index of the First Occurrence in a String (KMP LPS Array)](https://takeuforward.org/strings/knuth-morris-pratt-kmp-algorithm/)
- **Difficulty**: Medium
- **Statement**: Given two strings `needle` and `haystack`, return the index of the first occurrence of `needle` in `haystack`, or -1 if `needle` is not part of `haystack`.

---

## 1. Problem, Restated

Find the first 0-based starting index of pattern `needle` in text `haystack` using the Knuth-Morris-Pratt (KMP) Longest Prefix Suffix (LPS) algorithm in strictly linear $\mathcal{O}(N + M)$ time and $\mathcal{O}(M)$ space.

- **Input**: Strings `haystack` and `needle`.
- **Output**: 0-based start index or -1.
- **Complexity Goal**: $\mathcal{O}(N + M)$ time and $\mathcal{O}(M)$ space.

---

## 2. Intuition & Pattern

**Why Naive Search Backtracks ($O(N \times M)$)**: 
When a mismatch occurs at `needle[j] != haystack[i]`, naive search resets `i` back to `i - j + 1` and `j = 0`, re-evaluating previously matched characters. 
**The KMP Breakthrough (Zero Text Backtracking)**: 
Precompute an auxiliary array `lps[M]` where `lps[j]` stores the length of the **Longest Proper Prefix of `needle[0 ... j]` that is also a Suffix of `needle[0 ... j]`**. 
1) **LPS Precomputation**: 
   - Maintain `len = 0` and pointer `i = 1`. 
   - If `needle[i] == needle[len]`: `len++`, `lps[i] = len`, `i++`. 
   - Else if `len > 0`: fallback `len = lps[len - 1]`. 
   - Else: `lps[i] = 0`, `i++`. 
2) **Text Search**: 
   - Maintain `i = 0` (in `haystack`) and `j = 0` (in `needle`). 
   - When `haystack[i] == needle[j]`: advance both `i++`, `j++`. 
   - If `j == M`: full match found at index `i - M`! 
   - When mismatch occurs (`haystack[i] != needle[j]`): 
     - If `j > 0`: shift pattern pointer **`j = lps[j - 1]`** (reusing the longest known matching prefix without ever moving text pointer `i` backwards!). 
     - If `j == 0`: simply advance `i++`. 
**Complexity**: Runs in strictly linear $\mathcal{O}(N + M)$ time and $\mathcal{O}(M)$ space.

- **Underlying Pattern**: `Knuth-Morris-Pratt (KMP) Pattern Matching with LPS Array Precomputation`.

---

## 3. Approach 1 — Naive (Quadratic Nested Matching)

### Idea
Nested loops checking pattern at every starting index in `haystack` in $\mathcal{O}(N \times M)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;

class SolutionNaive {
public:
    int strStr(string haystack, string needle) {
        int n = haystack.size(), m = needle.size();
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && haystack[i + j] == needle[j]) j++;
            if (j == m) return i;
        }
        return -1;
    }
};
```

### Java Code
```java
class SolutionNaive {

    int strStr(String haystack, String needle) {
        int n = haystack.length, m = needle.length;
        for (int i = 0; i <= n - m; i++) {
            int j = 0;
            while (j < m && haystack[i + j] == needle[j]) j++;
            if (j == m) return i;
        }
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ worst-case time (e.g. `haystack = "aaaa...a"`, `needle = "aaaab"`).
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Repeatedly rolls text pointer backwards on every mismatch.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard KMP below directly achieves optimal strictly linear $\mathcal{O}(N + M)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (KMP Algorithm with LPS Array)

### Idea
Knuth-Morris-Pratt (KMP) with LPS Array Precomputation in $\mathcal{O}(N + M)$ time and $\mathcal{O}(M)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
private:
    // Helper function to build Longest Prefix Suffix (LPS) array in O(M)
    vector<int> buildLPS(const string& pat) {
        int m = pat.size();
        vector<int> lps(m, 0);
        
        int len = 0; // Length of previous longest prefix suffix
        int i = 1;
        
        while (i < m) {
            if (pat[i] == pat[len]) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    // Fallback to previous longest prefix length
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        
        return lps;
    }
    
public:
    int strStr(string haystack, string needle) {
        int n = haystack.size();
        int m = needle.size();
        
        if (m == 0) return 0;
        if (n < m) return -1;
        
        // Step 1: Precompute LPS array for needle in O(M) time
        vector<int> lps = buildLPS(needle);
        
        // Step 2: Search pattern in text with zero text backtracking in O(N) time
        int i = 0; // Index for haystack
        int j = 0; // Index for needle
        
        while (i < n) {
            if (haystack[i] == needle[j]) {
                i++;
                j++;
            }
            
            // Full pattern match found
            if (j == m) {
                return i - m; // 0-based starting index
            } else if (i < n && haystack[i] != needle[j]) {
                if (j != 0) {
                    // Fallback pattern pointer using LPS without moving i backwards
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }
        
        return -1;
    }
};
```

### Java Code
```java
class Solution {

    // Helper function to build Longest Prefix Suffix (LPS) array in O(M)
    int[] buildLPS(String pat) {
        int m = pat.length;
        int[] lps = new int[m];
        
        int len = 0; // Length of previous longest prefix suffix
        int i = 1;
        
        while (i < m) {
            if (pat[i] == pat[len]) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    // Fallback to previous longest prefix length
                    len = lps[len - 1];
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        
        return lps;
    }

    int strStr(String haystack, String needle) {
        int n = haystack.length;
        int m = needle.length;
        
        if (m == 0) return 0;
        if (n < m) return -1;
        
        // Step 1: Precompute LPS array for needle in O(M) time
        int[] lps = buildLPS(needle);
        
        // Step 2: Search pattern in text with zero text backtracking in O(N) time
        int i = 0; // Index for haystack
        int j = 0; // Index for needle
        
        while (i < n) {
            if (haystack[i] == needle[j]) {
                i++;
                j++;
            }
            
            // Full pattern match found
            if (j == m) {
                return i - m; // 0-based starting index
            } else if (i < n && haystack[i] != needle[j]) {
                if (j != 0) {
                    // Fallback pattern pointer using LPS without moving i backwards
                    j = lps[j - 1];
                } else {
                    i++;
                }
            }
        }
        
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$ time (LPS build: $\mathcal{O}(M)$; Text scan: $\mathcal{O}(N)$).
- **Space Complexity**: $\mathcal{O}(M)$ auxiliary space (single 1D vector for LPS array).
- **Why this is optimal**: KMP guarantees the text pointer $i$ strictly moves forward $0 \to N$, yielding strictly linear time.

---

## 6. Dry Run

`haystack = "sadbutsad"`, `needle = "sad"` ($N = 9, M = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `LPS Build` | `needle = "sad"` $\implies lps = [0, 0, 0]$ | LPS computed |
| `i = 0, j = 0` | haystack[0]=='s' == needle[0]=='s' $\implies i=1, j=1$ | Match 's' |
| `i = 1, j = 1` | haystack[1]=='a' == needle[1]=='a' $\implies i=2, j=2$ | Match 'a' |
| `i = 2, j = 2` | haystack[2]=='d' == needle[2]=='d' $\implies i=3, j=3$ | Match 'd' |
| `Match Trigger` | `j == 3 == M` $\implies$ return `i - m = 3 - 3 = 0` | First occurrence at 0 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `needle` is empty string (returns 0).
- `needle` longer than `haystack` (returns -1).
- Repeated prefixes `haystack = "mississippi"`, `needle = "issip"`.

### Common Bugs to Avoid
- Moving $i$ backwards on mismatch (defeats KMP linear guarantee!).
- Accessing `lps[j]` instead of `lps[j - 1]` upon mismatch.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the KMP algorithm strictly guarantee O(N + M) time?**  
  **A**: Because pointer $i$ strictly increments $N$ times. While pointer $j$ can jump backwards via `j = lps[j-1]`, $j$ only increments when $i$ increments. Therefore, $j$ can decrement at most $N$ times across the entire algorithm, bounding total pointer adjustments to $2N + 2M = \mathcal{O}(N + M)$!

- **Q2: How to find ALL occurrences of needle in haystack?**  
  **A**: When `j == m`, record `i - m`, and then instead of returning, reset `j = lps[j - 1]` and continue scanning!

- **Q3: How does KMP compare to Boyer-Moore and Rabin-Karp in practical industry applications?**  
  **A**: In standard text editors (e.g. `grep`), **Boyer-Moore** (Bad Character + Good Suffix heuristic) is faster on average (sub-linear $\mathcal{O}(N/M)$ time). **KMP** is preferred in streaming systems because it NEVER backtracks on the input text stream. **Rabin-Karp** is best for multi-pattern searches!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `KMP`, `LPS`, `String Matching`, `LeetCode-28`, `Medium`
- **Related problems to practice next**:
- **Shortest Palindrome**: KMP LPS application.
- **Longest Happy Prefix**: Direct LPS length.
