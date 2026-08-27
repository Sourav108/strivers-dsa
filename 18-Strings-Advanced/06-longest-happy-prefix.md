# Longest Happy Prefix (Proper Prefix which is also Suffix) (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Longest Happy Prefix (Proper Prefix which is also Suffix)](https://takeuforward.org/strings/longest-happy-prefix/)
- **Difficulty**: Hard
- **Statement**: A string is called a **happy prefix** if is a **non-empty** prefix which is also a suffix (excluding the entire string itself). Given a string `s`, return the **longest happy prefix** of `s`. Return an empty string `""` if no such prefix exists.

---

## 1. Problem, Restated

Find the longest proper prefix of `s` that is identical to a proper suffix of `s` using the KMP LPS array in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space (or Rolling Hash in $\mathcal{O}(1)$ space).

- **Input**: String `s`.
- **Output**: Longest happy prefix string.
- **Complexity Goal**: $\mathcal{O}(N)$ time and space.

---

## 2. Intuition & Pattern

**The Pure LPS Definition**: 
By definition, the KMP `lps[i]` array stores the length of the **Longest Proper Prefix of `s[0 ... i]` that is also a Suffix of `s[0 ... i]`**. 
Therefore: 
$$\text{Length of Longest Happy Prefix} = \text{lps}[N - 1]$$ 
$$\text{Longest Happy Prefix String} = s[0 \dots \text{lps}[N - 1] - 1]$$ 
**KMP LPS Computation Algorithm**: 
- Maintain `len = 0` and pointer `i = 1`. 
- For each character $i \in [1, N - 1]$: 
  - If `s[i] == s[len]`: `len++`, `lps[i] = len`, `i++`. 
  - Else if `len > 0`: `len = lps[len - 1]`. 
  - Else: `lps[i] = 0`, `i++`. 
- Return `s.substr(0, lps[N - 1])`. Runs in strictly linear $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space with 0 hash collisions.

- **Underlying Pattern**: `KMP Longest Prefix Suffix (LPS) Array Direct Extraction / Rolling Hash`.

---

## 3. Approach 1 — Naive (Prefix-Suffix Quadratic Scanning)

### Idea
Check all $N - 1$ prefixes against suffixes of same length from largest down to 1 in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <string>
using namespace std;

class SolutionNaive {
public:
    string longestPrefix(string s) {
        int n = s.size();
        for (int len = n - 1; len >= 1; len--) {
            if (s.substr(0, len) == s.substr(n - len, len)) {
                return s.substr(0, len);
            }
        }
        return "";
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's not good enough**: Quadratic string equality checking.

---

## 4. Approach 2 — Better (Rolling Hash / Rabin-Karp)

### Idea
Rolling Hash (Rabin-Karp) Forward Prefix vs Backward Suffix in O(N) time and O(1) space.

### C++17 Code
```cpp
#include <string>
using namespace std;

class SolutionRollingHash {
public:
    string longestPrefix(string s) {
        int n = s.size();
        long long prefixHash = 0, suffixHash = 0, power = 1, BASE = 31, MOD = 1e9 + 7;
        int maxLen = 0;
        for (int i = 0; i < n - 1; i++) {
            prefixHash = (prefixHash * BASE + (s[i] - 'a' + 1)) % MOD;
            suffixHash = (suffixHash + (s[n - 1 - i] - 'a' + 1) * power) % MOD;
            power = (power * BASE) % MOD;
            if (prefixHash == suffixHash) maxLen = i + 1;
        }
        return s.substr(0, maxLen);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's still not optimal**: Single rolling hash can produce hash collisions without double hashing.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (KMP LPS Array Direct Extraction)

### Idea
Direct KMP Longest Prefix Suffix (LPS) Array in strictly deterministic $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
using namespace std;

class Solution {
public:
    string longestPrefix(string s) {
        int n = s.size();
        if (n <= 1) return "";
        
        // lps[i] stores the length of the longest proper prefix of s[0...i] that is also a suffix
        vector<int> lps(n, 0);
        
        int len = 0; // Length of previous longest prefix suffix
        int i = 1;
        
        while (i < n) {
            if (s[i] == s[len]) {
                len++;
                lps[i] = len;
                i++;
            } else {
                if (len != 0) {
                    len = lps[len - 1]; // Fallback to previous longest prefix suffix
                } else {
                    lps[i] = 0;
                    i++;
                }
            }
        }
        
        // lps[n - 1] is the length of the longest happy prefix
        int happyLen = lps[n - 1];
        return s.substr(0, happyLen);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each character processed in amortized $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary space (single 1D vector of size $N$).
- **Why this is optimal**: LPS construction guarantees finding the exact mathematical longest proper prefix suffix with zero collision risk.

---

## 6. Dry Run

`s = "level"` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 1 ('e')` | s[1]!='l' $\implies lps[1] = 0$ | No match |
| `i = 2 ('v')` | s[2]!='l' $\implies lps[2] = 0$ | No match |
| `i = 3 ('e')` | s[3]!='l' $\implies lps[3] = 0$ | No match |
| `i = 4 ('l')` | s[4]=='l' == s[0]=='l' $\implies len=1, lps[4] = 1$ | Prefix 'l' matches Suffix 'l' |
| `Result` | happyLen = `lps[4] = 1` $\implies s.substr(0, 1) = \text{"l"}$ | Longest Happy Prefix = "l" ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `s = "ababab"` (returns `"abab"` of length 4).
- All identical characters `"aaaa"` (returns `"aaa"` of length 3: excludes entire string).
- No common prefix/suffix `"leetcode"` (returns `""`).

### Common Bugs to Avoid
- Returning entire string when string is homogeneous (problem explicitly specifies PROPER prefix, excluding entire string).
- Off-by-one errors in fallback `len = lps[len - 1]`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does lps[n-1] give the LONGEST proper prefix that is also a suffix?**  
  **A**: Because by mathematical invariant, the KMP LPS array actively maximizes the matching prefix length at every index $i$. Thus, at the final index $N - 1$, `lps[N-1]` is guaranteed to be the global maximum length of any proper prefix that equals a suffix of $s$!

- **Q2: How to check if a string can be constructed by repeating a smaller substring (Repeated Substring Pattern / LeetCode 459)?**  
  **A**: Compute LPS! If `lps[n - 1] > 0` and `n % (n - lps[n - 1]) == 0`, then the string is formed by repeating the substring of length `n - lps[n - 1]`!

- **Q3: How to find the SECOND longest happy prefix?**  
  **A**: Look at `lps[lps[n - 1] - 1]`! Chain hopping via `lps` explores all prefix-suffix lengths in decreasing order!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `KMP`, `LPS`, `String Algorithms`, `LeetCode-1392`, `Hard`
- **Related problems to practice next**:
- **Find Index of First Occurrence**: Base KMP.
- **Shortest Palindrome**: KMP palindrome reduction.
