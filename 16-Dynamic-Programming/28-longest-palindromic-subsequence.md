# Longest Palindromic Subsequence (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Longest Palindromic Subsequence](https://takeuforward.org/data-structure/longest-palindromic-subsequence-dp-28/)
- **Difficulty**: Medium
- **Statement**: Given a string `s`, find the longest palindromic subsequence's length in `s`. A subsequence is a sequence that can be derived from another sequence by deleting some or no elements without changing the order of the remaining elements.

---

## 1. Problem, Restated

Find the length of the longest subsequence in `s` that reads identically forwards and backwards using LCS reduction $\text{LCS}(s, \text{reverse}(s))$ in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Palindrome Inversion Invariant**: 
By definition, a palindrome reads the exact same forwards and backwards. 
Therefore, finding the Longest Palindromic Subsequence of string $S$ is **100% IDENTICAL** to finding the **Longest Common Subsequence between $S$ and its reversed string $S^R$**! 
$$\text{LPS}(S) = \text{LCS}(S, \text{reverse}(S))$$ 
We reverse $S$ to form $S^R$ in $\mathcal{O}(N)$ time, and execute space-optimized LCS in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `LCS String Inversion Reduction $\text{LCS}(s, \text{reverse}(s))$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively check matching endpoints with 2-way branching in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int j, const string& s) {
        if (i > j) return 0;
        if (i == j) return 1;
        if (s[i] == s[j]) return 2 + solve(i + 1, j - 1, s);
        return max(solve(i + 1, j, s), solve(i, j - 1, s));
    }
public:
    int longestPalindromeSubseq(string s) {
        return solve(0, s.size() - 1, s);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Binary recursive tree with repeated interval evaluations.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Interval DP table of size N x N in O(N^2) time and O(N^2) space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class SolutionIntervalDP {
public:
    int longestPalindromeSubseq(string s) {
        int n = s.size();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        for (int i = 0; i < n; i++) dp[i][i] = 1;
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s[i] == s[j]) dp[i][j] = 2 + (len == 2 ? 0 : dp[i + 1][j - 1]);
                else dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
            }
        }
        return dp[0][n - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ table space.
- **Why it's still not optimal**: Full 2D table uses redundant memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
LCS Reduction with 1D Space Optimization in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
private:
    // Helper function computing LCS of two strings in O(M) space
    int lcs(const string& s1, const string& s2) {
        int n = s1.size();
        int m = s2.size();
        vector<int> prev(m + 1, 0);
        
        for (int i = 1; i <= n; i++) {
            vector<int> cur(m + 1, 0);
            for (int j = 1; j <= m; j++) {
                if (s1[i - 1] == s2[j - 1]) {
                    cur[j] = 1 + prev[j - 1];
                } else {
                    cur[j] = max(prev[j], cur[j - 1]);
                }
            }
            prev = cur;
        }
        
        return prev[m];
    }
    
public:
    int longestPalindromeSubseq(string s) {
        string rev_s = s;
        reverse(rev_s.begin(), rev_s.end());
        
        // LPS(s) = LCS(s, reverse(s))
        return lcs(s, rev_s);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (standard LCS matrix traversal).
- **Space Complexity**: $\mathcal{O}(N)$ space (single row array of size $N + 1$).
- **Why this is optimal**: Reduces LPS to space-optimized LCS without needing 2D interval matrices.

---

## 6. Dry Run

`s = "bbbab"`, `rev_s = "babbb"` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `s vs rev_s` | `"bbbab"` vs `"babbb"` | LCS computation |
| `Matching` | Common matching characters: 'b', 'b', 'b', 'b' | LCS = 4 (`"bbbb"`) |
| `Result` | Return 4 (Palindrome: `"bbbb"` formed by indices 0, 1, 2, 4) | LPS = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- String is already a palindrome (returns $N$).
- All characters unique (returns 1).
- Single character string (returns 1).

### Common Bugs to Avoid
- Reversing `s` in-place without creating a copy `rev_s`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does LPS relate to Minimum Insertions to Make String Palindrome (Problem 29)?**  
  **A**: To make $S$ a palindrome with minimum insertions, we KEEP its Longest Palindromic Subsequence intact and insert matching characters for all remaining non-palindromic characters. Thus: $\text{Min Insertions} = N - \text{LPS}(S)$!

- **Q2: How does LPS relate to Minimum Deletions to Make String Palindrome?**  
  **A**: Similarly, deleting all characters not in the LPS yields a palindrome: $\text{Min Deletions} = N - \text{LPS}(S)$!

- **Q3: Can LPS be solved using Interval DP directly?**  
  **A**: Yes: $\text{dp}[i][j] = 2 + \text{dp}[i+1][j-1]$ if $s[i] == s[j]$, else $\max(\text{dp}[i+1][j], \text{dp}[i][j-1])$. Both approaches have $\mathcal{O}(N^2)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `LCS`, `Palindrome`, `LeetCode-516`, `Medium`
- **Related problems to practice next**:
- **Minimum Insertions for Palindrome**: N - LPS formulation.
- **Longest Common Subsequence**: Base reduction.
