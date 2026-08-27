# Print Longest Common Subsequence (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Print Longest Common Subsequence](https://takeuforward.org/data-structure/print-longest-common-subsequence-dp-26/)
- **Difficulty**: Medium
- **Statement**: Given two strings `s1` and `s2`, find and print the actual string that forms the Longest Common Subsequence (LCS). If there are multiple LCS strings of maximum length, return any one of them.

---

## 1. Problem, Restated

Reconstruct and output the actual Longest Common Subsequence string between `s1` and `s2` using 2D DP Table Backtracking in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The 2-Phase Process**: 
1) **Phase 1 (Build DP Table)**: Compute the standard 2D LCS table `dp[n + 1][m + 1]`: 
   - If `s1[i - 1] == s2[j - 1]`: `dp[i][j] = 1 + dp[i - 1][j - 1]` 
   - Else: `dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])` 
2) **Phase 2 (Backtrack from (n, m) to (0, 0))**: 
   - If `s1[i - 1] == s2[j - 1]`: This character MUST be part of the LCS! Append `s1[i - 1]` to result string and move diagonally `i--, j--`. 
   - Else if `dp[i - 1][j] > dp[i][j - 1]`: The optimal choice came from above, so move up `i--`. 
   - Else: Move left `j--`. 
3) Reverse the accumulated string. Runs in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

- **Underlying Pattern**: `2D String DP Table Backtracking / Diagonal Character Extraction`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Generate all $2^N$ subsequences of `s1`, filter those present in `s2`, and pick the longest in $\mathcal{O}(2^N \times M)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N * M) exponential subsequence generation
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Combinatorial subsequence generation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard space-optimized DP below directly achieves optimal $\mathcal{O}(\min(N, M))$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
2D DP Table Construction + Single Backtracking Walk in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    string findLCS(int n, int m, string s1, string s2) {
        // Step 1: Build standard 2D DP Table
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (s1[i - 1] == s2[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        
        // Step 2: Backtrack to reconstruct the LCS string
        string lcsStr = "";
        int i = n, j = m;
        
        while (i > 0 && j > 0) {
            if (s1[i - 1] == s2[j - 1]) {
                lcsStr.push_back(s1[i - 1]);
                i--;
                j--; // Move diagonally
            } else if (dp[i - 1][j] > dp[i][j - 1]) {
                i--; // Move up
            } else {
                j--; // Move left
            }
        }
        
        // Step 3: Reverse the string because backtracking traces backwards
        reverse(lcsStr.begin(), lcsStr.end());
        return lcsStr;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (DP table fill: $\mathcal{O}(N \times M)$; Backtracking walk: $\mathcal{O}(N + M)$).
- **Space Complexity**: $\mathcal{O}(N \times M)$ space (full 2D table required to guide backtracking).
- **Why this is optimal**: Backtracking extracts the character sequence in a single linear traversal of length at most $N + M$.

---

## 6. Dry Run

`s1 = "abcde"`, `s2 = "bdgek"` ($N = 5, M = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `DP Build` | dp[5][5] = 3 | LCS length is 3 |
| `Backtrack (5, 5)` | s1[4]='e', s2[4]='k' $\implies$ mismatch. dp[4][5] vs dp[5][4] $\implies$ moves to (5, 4) | Move left |
| `Backtrack (5, 4)` | s1[4]='e' == s2[3]='e' $\implies$ Append 'e', move to (4, 3) | lcs = `"e"` |
| `Backtrack (4, 3)` | s1[3]='d' == s2[2]='d' $\implies$ Append 'd', move to (3, 2) | lcs = `"ed"` |
| `Backtrack (2, 1)` | s1[1]='b' == s2[0]='b' $\implies$ Append 'b', move to (1, 0) | lcs = `"edb"` |
| `Result` | Reverse `"edb"` $\implies$ `"bde"` | Output = `"bde"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No common characters (returns `""`).
- Strings are identical (returns `s1`).
- Single character overlap.

### Common Bugs to Avoid
- Forgetting `reverse(lcsStr.begin(), lcsStr.end())` (string was constructed back-to-front).
- Off-by-one errors when comparing `s1[i-1]` against 1-based table indices.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can we reconstruct the LCS string in O(min(N, M)) space?**  
  **A**: Yes, using **Hirschberg's Algorithm** (Divide and Conquer with space-optimized linear DP) in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(\min(N, M))$ space!

- **Q2: How to print ALL unique LCS strings if multiple exist?**  
  **A**: Use recursive backtracking on the DP table branching whenever `dp[i-1][j] == dp[i][j-1]`, caching paths using a `unordered_set<string>` memoization table.

- **Q3: How does printing LCS differ from printing Shortest Common Supersequence (Problem 31)?**  
  **A**: In SCS, when characters mismatch, we ADD the discarded non-matching character to the supersequence before moving up or left, whereas in LCS we only record matched characters!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `LCS`, `Backtracking`, `Medium`
- **Related problems to practice next**:
- **Longest Common Subsequence**: Length calculation.
- **Shortest Common Supersequence**: Supersequence reconstruction.
