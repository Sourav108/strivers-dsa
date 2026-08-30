# Longest Common Substring (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest Common Substring](https://takeuforward.org/data-structure/longest-common-substring-dp-27/)
- **Difficulty**: Medium
- **Statement**: Given two strings `s1` and `s2`, find the length of the longest common substring. A substring is a contiguous sequence of characters within a string.

---

## 1. Problem, Restated

Find the maximum length contiguous matching substring between `s1` and `s2` using 2D String DP with zero-reset on mismatch in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Subsequence vs Substring (The Contiguity Invariant)**: 
- In **Subsequence** DP, on mismatch we carry over the best past prefix: `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`. 
- In **Substring** DP, matching characters MUST be strictly contiguous! If `s1[i - 1] != s2[j - 1]`, the continuous chain is broken $\implies \text{dp}[i][j] = 0$! 
**Recurrence**: 
$$\text{dp}[i][j] = \begin{cases} 1 + \text{dp}[i - 1][j - 1] & \text{if } s_1[i - 1] == s_2[j - 1] \\ 0 & \text{if } s_1[i - 1] \ne s_2[j - 1] \end{cases}$$ 
**Global Maximum**: The answer is the maximum value found across the ENTIRE table: $\max_{i, j} (\text{dp}[i][j])$. 
**Space Optimization**: Row $i$ only depends on diagonal $(i-1, j-1)$. Maintain a 1D vector `prev(M + 1)` in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Underlying Pattern**: `2D String DP with Strict Contiguity Reset (`dp[i][j] = 0` on mismatch)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Generate all $N^2$ substrings of `s1` and check existence in `s2` in $\mathcal{O}(N^3)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(N^3) brute substring check
};
```

### Java Code
```java
class SolutionNaive {
    // O(N^3) brute substring check
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Cubic substring generation and string comparison.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x M) time and O(N x M) space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int longestCommonSubstr(string s1, string s2) {
        int n = s1.size(), m = s2.size(), maxLen = 0;
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (s1[i - 1] == s2[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                    maxLen = max(maxLen, dp[i][j]);
                } else {
                    dp[i][j] = 0; // Strict contiguity reset
                }
            }
        }
        return maxLen;
    }
};
```

### Java Code
```java
class Solution2D {

    int longestCommonSubstr(String s1, String s2) {
        int n = s1.length, m = s2.length, maxLen = 0;
        int[][] dp = new int[n + 1][m + 1];
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (s1[i - 1] == s2[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                    maxLen = Math.max(maxLen, dp[i][j]);
                } else {
                    dp[i][j] = 0; // Strict contiguity reset
                }
            }
        }
        return maxLen;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$ space.
- **Why it's still not optimal**: 2D table uses redundant memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Row Space-Optimized DP in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(\min(N, M))$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    int longestCommonSubstr(string s1, string s2) {
        int n = s1.size();
        int m = s2.size();
        
        // prev[j] stores matching substring length ending at s2[j - 1]
        vector<int> prev(m + 1, 0);
        int maxLen = 0;
        
        for (int i = 1; i <= n; i++) {
            vector<int> cur(m + 1, 0);
            for (int j = 1; j <= m; j++) {
                if (s1[i - 1] == s2[j - 1]) {
                    cur[j] = 1 + prev[j - 1];
                    maxLen = max(maxLen, cur[j]);
                } else {
                    cur[j] = 0; // Reset contiguity on character mismatch
                }
            }
            prev = cur;
        }
        
        return maxLen;
    }
};
```

### Java Code
```java
class Solution {

    int longestCommonSubstr(String s1, String s2) {
        int n = s1.length;
        int m = s2.length;
        
        // prev[j] stores matching substring length ending at s2[j - 1]
        int[] prev = new int[m + 1];
        int maxLen = 0;
        
        for (int i = 1; i <= n; i++) {
            int[] cur = new int[m + 1];
            for (int j = 1; j <= m; j++) {
                if (s1[i - 1] == s2[j - 1]) {
                    cur[j] = 1 + prev[j - 1];
                    maxLen = Math.max(maxLen, cur[j]);
                } else {
                    cur[j] = 0; // Reset contiguity on character mismatch
                }
            }
            prev = cur;
        }
        
        return maxLen;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each character pair compared once).
- **Space Complexity**: $\mathcal{O}(\min(N, M))$ auxiliary space (single row array).
- **Why this is optimal**: Resetting on mismatch allows 1D array to track contiguous runs in linear space.

---

## 6. Dry Run

`s1 = "abcjkl"`, `s2 = "acjkp"` ($N = 6, M = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 1 ('a')` | Matches 'a' at j=1 $\implies cur[1] = 1, maxLen = 1$ | Match 1 |
| `i = 2 ('b')` | No matches $\implies cur = [0, 0, 0, 0, 0, 0]$ | Reset |
| `i = 3 ('c')` | Matches 'c' at j=2 $\implies cur[2] = 1, maxLen = 1$ | Match 1 |
| `i = 4 ('j')` | Matches 'j' at j=3 $\implies cur[3] = 1 + prev[2] = 1 + 1 = 2, maxLen = 2$ | Match 2 |
| `i = 5 ('k')` | Matches 'k' at j=4 $\implies cur[4] = 1 + prev[3] = 1 + 2 = 3, maxLen = 3$ | Match 3 (`"cjk"`) |
| `Result` | Return maxLen = 3 (Substring `"cjk"`) | Max Length = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No common substring (returns 0).
- Identical strings (returns $N$).
- Single character substring.

### Common Bugs to Avoid
- Returning `dp[n][m]` instead of `maxLen` (in Substring DP, optimal substring can end ANYWHERE in the middle of strings!).
- Carrying over `max(up, left)` on mismatch (turns it into LCS!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can Longest Common Substring be solved in linear O(N + M) time?**  
  **A**: YES! Using a **Suffix Automaton (SAM)** or **Suffix Tree (Ukkonen's Algorithm)**, Longest Common Substring can be computed in strictly linear $\mathcal{O}(N + M)$ time and $\mathcal{O}(N + M)$ space! Alternatively, **Binary Search on Length + Rolling Hash (Rabin-Karp)** solves it in $\mathcal{O}((N + M) \log(\min(N, M)))$ time.

- **Q2: How to print the actual substring?**  
  **A**: Track `endIndex` when updating `maxLen = cur[j]`. Substring is `s1.substr(endIndex - maxLen, maxLen)`!

- **Q3: Why is returning dp[n][m] WRONG for Substring DP?**  
  **A**: Because `dp[i][j]` ONLY stores the length of a substring ending EXACTLY at `s1[i-1]` and `s2[j-1]`. If the last characters don't match, `dp[n][m] == 0` even if a 100-character common substring exists earlier!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `Substring`, `Space Optimization`, `Medium`
- **Related problems to practice next**:
- **Longest Common Subsequence**: Non-contiguous counterpart.
- **Edit Distance**: String transformation.
