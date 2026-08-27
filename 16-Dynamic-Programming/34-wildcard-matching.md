# Wildcard Matching ('?' and '*' pattern matching) (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Wildcard Matching ('?' and '*' pattern matching)](https://takeuforward.org/data-structure/wildcard-matching-dp-34/)
- **Difficulty**: Hard
- **Statement**: Given an input string `s` and a pattern `p`, implement wildcard pattern matching with support for '?' and '*' where: '?' matches any single character, and '*' matches any sequence of characters (including the empty sequence). The matching should cover the entire input string (not partial).

---

## 1. Problem, Restated

Determine if pattern `p` matches string `s` where `?` matches 1 char and `*` matches 0 or more chars using 1D Space-Optimized DP in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**State Definition**: Let $\text{dp}[i][j]$ denote whether prefix `p[0 ... i - 1]` matches prefix `s[0 ... j - 1]`. 
**Transitions**: 
1) **Direct Match or '?' (`p[i - 1] == s[j - 1] || p[i - 1] == '?'`)**: 
   $$\text{dp}[i][j] = \text{dp}[i - 1][j - 1]$$ 
2) **Star Wildcard (`p[i - 1] == '*'`)**: 
   A star `*` can match **0 characters** (skip `*` $\implies \text{dp}[i - 1][j]$) OR **1 or more characters** (consume 1 character from `s` and keep `*` $\implies \text{dp}[i][j - 1]$): 
   $$\text{dp}[i][j] = \text{dp}[i - 1][j] \lor \text{dp}[i][j - 1]$$ 
3) **Mismatch**: $\text{dp}[i][j] = \text{false}$. 
**Base Cases**: 
- $\text{dp}[0][0] = \text{true}$ (empty pattern matches empty string). 
- $\text{dp}[0][j] = \text{false}$ for $j > 0$ (empty pattern cannot match non-empty string). 
- $\text{dp}[i][0] = \text{true}$ if and only if ALL characters in `p[0 ... i - 1]` are `'*'`. 
**Space Optimization**: 1D vector `prev(M + 1)` in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Underlying Pattern**: `2D String DP / Wildcard Star Expansion (`match 0` vs `match 1+`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursive backtracking exploring 0 to $M$ matches for every '*' in $\mathcal{O}(2^{N+M})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^(N+M)) recursive wildcard search
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^{N+M})$ time.
- **Space Complexity**: $\mathcal{O}(N + M)$ recursion stack.
- **Why it's not good enough**: Exponential branching on consecutive '*' wildcards.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table of size (N+1) x (M+1) in O(N x M) time and O(N x M) space.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution2D {
public:
    bool isMatch(string s, string p) {
        int n = p.size(), m = s.size();
        vector<vector<bool>> dp(n + 1, vector<bool>(m + 1, false));
        dp[0][0] = true;
        for (int i = 1; i <= n; i++) {
            if (p[i - 1] == '*') dp[i][0] = dp[i - 1][0];
        }
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (p[i - 1] == s[j - 1] || p[i - 1] == '?') {
                    dp[i][j] = dp[i - 1][j - 1];
                } else if (p[i - 1] == '*') {
                    dp[i][j] = dp[i - 1][j] || dp[i][j - 1];
                }
            }
        }
        return dp[n][m];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$ space.
- **Why it's still not optimal**: 2D table allocates redundant row memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Space-Optimized Row DP in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    bool isMatch(string s, string p) {
        int n = p.size(); // Pattern length
        int m = s.size(); // String length
        
        // prev[j] denotes whether pattern prefix matches s[0 ... j - 1]
        vector<bool> prev(m + 1, false);
        prev[0] = true; // Base case: empty pattern matches empty string
        
        for (int i = 1; i <= n; i++) {
            vector<bool> cur(m + 1, false);
            
            // Base case for empty string (j = 0): true only if all preceding pattern chars are '*'
            if (p[i - 1] == '*') {
                cur[0] = prev[0];
            } else {
                cur[0] = false;
            }
            
            for (int j = 1; j <= m; j++) {
                if (p[i - 1] == s[j - 1] || p[i - 1] == '?') {
                    cur[j] = prev[j - 1];
                } else if (p[i - 1] == '*') {
                    // Star matches 0 chars (prev[j]) OR 1+ chars (cur[j - 1])
                    cur[j] = prev[j] || cur[j - 1];
                } else {
                    cur[j] = false;
                }
            }
            
            prev = cur;
        }
        
        return prev[m];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each cell evaluates in $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(M)$ auxiliary space (two 1D boolean vectors).
- **Why this is optimal**: 1D boolean buffer minimizes memory footprint while maintaining 100% regex correctness.

---

## 6. Dry Run

`s = "cb"`, `p = "?a"` ($N = 2, M = 2$)

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | prev = `[T, F, F]` | Base ready |
| `i=1 '?'` | cur[0]=F; j=1: prev[0]=T $\implies cur[1]=T$; j=2: prev[1]=F $\implies cur[2]=F$ | prev = `[F, T, F]` |
| `i=2 'a'` | cur[0]=F; j=1: 'a'!='c' $\implies F$; j=2: 'a'!='b' $\implies F$ | prev = `[F, F, F]` |
| `Result` | Return prev[2] = false ('?a' does not match 'cb') | Match = False ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Pattern is `"*"` (matches any string of any length $\implies$ true).
- Empty pattern and non-empty string (false).
- Multiple consecutive stars `"***"`.

### Common Bugs to Avoid
- Forgetting base case where pattern starts with multiple `*` characters matching empty string `s` (`cur[0] = prev[0]`).
- Confusing LeetCode 44 Wildcard (`*` matches any sequence) with LeetCode 10 Regular Expression (`*` matches 0 or more of PRECEDING character).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does Wildcard Matching (LeetCode 44) differ from Regular Expression Matching (LeetCode 10)?**  
  **A**: In **Wildcard Matching**, `*` stands alone and matches any sequence of arbitrary characters ($0 \dots \infty$). In **Regular Expression Matching**, `*` MUST be paired with the preceding character `c*` and matches 0 or more repetitions of THAT specific preceding character!

- **Q2: Can Wildcard Matching be solved in O(1) space with Greedy Two Pointers?**  
  **A**: YES! Because `*` matches any character unconditionally, we can remember the last seen `starIdx` and `matchIdx` in $s$. If a mismatch occurs, backtrack to the last `*` and extend its match by 1 character in $\mathcal{O}(N \times M)$ worst-case and $\mathcal{O}(N + M)$ average time and $\mathcal{O}(1)$ space!

- **Q3: Why does DP remain superior for Regex (LeetCode 10) while Greedy works for Wildcard (LeetCode 44)?**  
  **A**: Regex `c*` depends on character identity and can produce ambiguous overlapping branches that require DP memoization. Wildcard `*` matches anything uniformly, so greedily taking the latest star is always optimal.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `Wildcard`, `Pattern Matching`, `LeetCode-44`, `Hard`
- **Related problems to practice next**:
- **Regular Expression Matching**: Preceding char regex.
- **Edit Distance**: String transformation.
