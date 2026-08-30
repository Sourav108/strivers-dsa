# Shortest Palindrome (KMP LPS on s + '#' + rev(s)) (Step 18.1 — String Matching & Hard Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Shortest Palindrome (KMP LPS on s + '#' + rev(s))](https://takeuforward.org/strings/shortest-palindrome/)
- **Difficulty**: Hard
- **Statement**: You are given a string `s`. You can convert `s` to a palindrome by adding characters in front of it. Return the shortest palindrome you can find by performing this transformation.

---

## 1. Problem, Restated

Find the longest palindromic prefix of string `s` using KMP LPS array on the combined string `s + '#' + rev(s)` in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space, then prepend the reversed suffix.

- **Input**: String `s`.
- **Output**: Shortest palindrome string.
- **Complexity Goal**: $\mathcal{O}(N)$ time and space.

---

## 2. Intuition & Pattern

**The Longest Palindromic Prefix Invariant**: 
To make $s$ a palindrome with the MINIMUM number of prepended characters in front: 
1) We should find the **LONGEST prefix of $s$ that is ALREADY a palindrome**! Let its length be $L$. 
2) The remaining suffix $s[L \dots N-1]$ represents the non-palindromic tail. 
3) To balance this tail, we simply take the reverse of $s[L \dots N-1]$ and prepend it to $s$: 
   $$\text{Result} = \text{reverse}(s[L \dots N-1]) + s$$ 
**How KMP LPS Finds $L$ in $\mathcal{O}(N)$**: 
- Construct a combined string: 
  $$P = s + \text{'\#'} + s^R$$ 
  (where $s^R = \text{reverse}(s)$, and `'\#'` is a unique sentinel delimiter). 
- Compute the standard KMP `lps` array on $P$. 
- The last entry `lps[P.size() - 1]` gives the length of the longest prefix of $s$ that is also a suffix of $s^R$! 
- Because a prefix of $s$ that is identical to a suffix of $s^R$ is **BY DEFINITION A PALINDROME**, $L = \text{lps.back()}$! 
- Prepend $s^R[0 \dots (N - 1 - L)]$ to $s$ in strictly linear $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `KMP LPS Reduction on Inverted Combined String (`s + '#' + rev(s)`)`.

---

## 3. Approach 1 — Naive (Prefix Palindrome Scanning)

### Idea
Check all prefixes of $s$ from largest $N$ down to 1 for palindromic symmetry in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;

class SolutionNaive {
    bool isPalin(const string& s, int r) {
        int l = 0;
        while (l < r) if (s[l++] != s[r--]) return false;
        return true;
    }
public:
    string shortestPalindrome(string s) {
        int n = s.size();
        for (int i = n - 1; i >= 0; i--) {
            if (isPalin(s, i)) {
                string rem = s.substr(i + 1);
                reverse(rem.begin(), rem.end());
                return rem + s;
            }
        }
        return "";
    }
};
```

### Java Code
```java
class SolutionNaive {
    boolean isPalin(String s, int r) {
        int l = 0;
        while (l < r) if (s[l++] != s[r--]) return false;
        return true;
    }

    String shortestPalindrome(String s) {
        int n = s.length;
        for (int i = n - 1; i >= 0; i--) {
            if (isPalin(s, i)) {
                String rem = s.substring(i + 1);
                reverse(rem.begin(), rem.end());
                return rem + s;
            }
        }
        return "";
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ string memory.
- **Why it's not good enough**: Checking $N$ prefix candidates with $\mathcal{O}(N)$ palindrome verification per candidate.

---

## 4. Approach 2 — Better (Rolling Hash / Rabin-Karp)

### Idea
Rolling Hash (Rabin-Karp) Forward and Backward Hash Matching in O(N) time.

### C++17 Code
```cpp
#include <string>
#include <algorithm>
using namespace std;

class SolutionRollingHash {
public:
    string shortestPalindrome(string s) {
        int n = s.size();
        long long fHash = 0, bHash = 0, power = 1, BASE = 31, MOD = 1e9 + 7;
        int longestPrefix = 0;
        for (int i = 0; i < n; i++) {
            fHash = (fHash * BASE + (s[i] - 'a' + 1)) % MOD;
            bHash = (bHash + (s[i] - 'a' + 1) * power) % MOD;
            power = (power * BASE) % MOD;
            if (fHash == bHash) longestPrefix = i + 1;
        }
        string rem = s.substr(longestPrefix);
        reverse(rem.begin(), rem.end());
        return rem + s;
    }
};
```

### Java Code
```java
class SolutionRollingHash {

    String shortestPalindrome(String s) {
        int n = s.length;
        long fHash = 0, bHash = 0, power = 1, BASE = 31, MOD = 1e9 + 7;
        int longestPrefix = 0;
        for (int i = 0; i < n; i++) {
            fHash = (fHash * BASE + (s[i] - 'a' + 1)) % MOD;
            bHash = (bHash + (s[i] - 'a' + 1) * power) % MOD;
            power = (power * BASE) % MOD;
            if (fHash == bHash) longestPrefix = i + 1;
        }
        String rem = s.substring(longestPrefix);
        reverse(rem.begin(), rem.end());
        return rem + s;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: Single rolling hash can face hash collisions on adversarial inputs.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (KMP LPS on Combined Inverted String)

### Idea
Combined String KMP LPS Array Extraction in strictly linear $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <string>
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    string shortestPalindrome(string s) {
        int n = s.size();
        if (n <= 1) return s;
        
        string rev_s = s;
        reverse(rev_s.begin(), rev_s.end());
        
        // Construct combined string: s + '#' + rev(s)
        // '#' acts as a boundary preventing LPS from overflowing past length n
        string combined = s + '#' + rev_s;
        int m = combined.size();
        
        // Compute KMP Longest Prefix Suffix (LPS) array for combined string in O(N)
        vector<int> lps(m, 0);
        for (int i = 1; i < m; i++) {
            int len = lps[i - 1];
            while (len > 0 && combined[i] != combined[len]) {
                len = lps[len - 1];
            }
            if (combined[i] == combined[len]) {
                len++;
            }
            lps[i] = len;
        }
        
        // lps.back() gives the length of the longest palindromic prefix of s
        int longestPalinPrefixLen = lps.back();
        
        // The characters that must be added in front are the reversed remaining suffix
        string remSuffix = s.substr(longestPalinPrefixLen);
        reverse(remSuffix.begin(), remSuffix.end());
        
        return remSuffix + s;
    }
};
```

### Java Code
```java
class Solution {

    String shortestPalindrome(String s) {
        int n = s.length;
        if (n <= 1) return s;
        
        String rev_s = s;
        reverse(rev_s.begin(), rev_s.end());
        
        // Construct combined String: s + '#' + rev(s)
        // '#' acts as a boundary preventing LPS from overflowing past length n
        String combined = s + '#' + rev_s;
        int m = combined.length;
        
        // Compute KMP Longest Prefix Suffix (LPS) array for combined String in O(N)
        int[] lps = new int[m];
        for (int i = 1; i < m; i++) {
            int len = lps[i - 1];
            while (len > 0 && combined[i] != combined[len]) {
                len = lps[len - 1];
            }
            if (combined[i] == combined[len]) {
                len++;
            }
            lps[i] = len;
        }
        
        // lps.peekLast() gives the length of the longest palindromic prefix of s
        int longestPalinPrefixLen = lps.peekLast();
        
        // The characters that must be added in front are the reversed remaining suffix
        String remSuffix = s.substring(longestPalinPrefixLen);
        reverse(remSuffix.begin(), remSuffix.end());
        
        return remSuffix + s;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (combined string of length $2N + 1$, LPS computation takes $\mathcal{O}(2N + 1)$).
- **Space Complexity**: $\mathcal{O}(N)$ space (combined string and LPS array of size $2N + 1$).
- **Why this is optimal**: KMP LPS on inverted string computes the longest palindromic prefix in a single deterministic pass with zero hash collisions.

---

## 6. Dry Run

`s = "aacecaaa"` ($N = 8$)

| Step | Action / State Change | Result |
|---|---|---|
| `Reverse` | `rev_s = "aaacecaa"` | Reversed |
| `Combined String` | `combined = "aacecaaa#aaacecaa"` (len 17) | Combined formed |
| `LPS Computation` | LPS of combined string: `lps[16] = 7` (`"aacecaa"` matches prefix of $s$ and suffix of $s^R$) | LPS = 7 |
| `Longest Prefix` | Longest palindromic prefix of $s$ has length 7 (`"aacecaa"`) | Prefix of length 7 |
| `Remaining Suffix` | $s[7 \dots 7] = \text{"a"} \implies \text{rem} = \text{"a"}$ | Reversed rem = "a" |
| `Result` | $"a" + "aacecaaa" = "aaacecaaa"$ (valid palindrome of length 9!) | Shortest Palindrome = "aaacecaaa" ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- String is already a palindrome `"racecar"` (returns `"racecar"`).
- All unique characters `"abcd"` (returns `"dcbabcd"`).
- $N = 0$ or $N = 1$.

### Common Bugs to Avoid
- Omitting the delimiter `'#'` (without delimiter, LPS can match across strings of length $> N$ like `s = "aaaa"` $\implies lps = 8$).
- Accessing `s.substr(longestPalinPrefixLen)` with out-of-bounds index.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the delimiter '#' absolutely necessary between s and rev(s)?**  
  **A**: Because without `'#'`, if $s = \text{"aaa"}$, `combined = "aaaaaa"`, and the LPS would compute length 5 instead of max possible prefix length 3! The delimiter `'#'` guarantees that the matched prefix NEVER exceeds the original string boundary length $N$!

- **Q2: Can Manacher's Algorithm solve Shortest Palindrome?**  
  **A**: YES! **Manacher's Algorithm** finds all palindromic radii in $\mathcal{O}(N)$ time. By checking which palindrome center expands all the way to index 0, the longest palindromic prefix is found in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space!

- **Q3: How does this compare to Longest Happy Prefix (Problem 06)?**  
  **A**: In Longest Happy Prefix, we run KMP directly on $s$ without reversing to find a proper prefix that is also a suffix. In Shortest Palindrome, reversing $s$ turns the prefix-suffix symmetry into a palindrome detector!


---

## 9. Tags & Related Problems

- **Tags**: `Strings`, `KMP`, `LPS`, `Palindrome`, `LeetCode-214`, `Hard`
- **Related problems to practice next**:
- **Find Index of First Occurrence**: KMP LPS algorithm.
- **Longest Happy Prefix**: Direct LPS length.
