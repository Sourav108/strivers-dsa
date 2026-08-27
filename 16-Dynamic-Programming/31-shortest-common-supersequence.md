# Shortest Common Supersequence (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Shortest Common Supersequence](https://takeuforward.org/data-structure/shortest-common-supersequence-dp-31/)
- **Difficulty**: Hard
- **Statement**: Given two strings `str1` and `str2`, return the shortest string that has both `str1` and `str2` as subsequences. If there are multiple valid strings, return any of them.

---

## 1. Problem, Restated

Construct the shortest common supersequence string containing both `str1` and `str2` as subsequences of length $N + M - \text{LCS}$ using 2D LCS Table Backtracking in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Supersequence Theorem**: 
To include both `str1` (length $N$) and `str2` (length $M$) in the shortest possible supersequence: 
1) Common characters in the **Longest Common Subsequence (LCS)** should be included **EXACTLY ONCE**. 
2) All remaining non-common characters from both `str1` and `str2` must be inserted in their relative original order. 
3) Length of the Shortest Common Supersequence is: 
   $$\text{Length(SCS)} = N + M - \text{Length(LCS)}$$ 
**Backtracking Rebuilding Algorithm**: 
- Construct the 2D DP table `dp[n + 1][m + 1]` for LCS. 
- Start from $(i = n, j = m)$: 
  - If `str1[i - 1] == str2[j - 1]`: Add `str1[i - 1]` once, move diagonally `i--, j--`. 
  - Else if `dp[i - 1][j] > dp[i][j - 1]`: Add `str1[i - 1]`, move up `i--`. 
  - Else: Add `str2[j - 1]`, move left `j--`. 
- Add any leftover characters when one index hits 0, then reverse string! Runs in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

- **Underlying Pattern**: `2D LCS Table Backtracking with Non-Matching Character Inclusion`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively generate all supersequence interleavings and pick shortest in $\mathcal{O}(2^{N+M})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^(N+M)) supersequence search
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^{N+M})$ time.
- **Space Complexity**: $\mathcal{O}(N + M)$ stack.
- **Why it's not good enough**: Combinatorial string interleaving search.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard single-pass DP below directly achieves optimal $\mathcal{O}(1)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
2D LCS Table Construction + Non-Matching Character Backtracking in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    string shortestCommonSupersequence(string str1, string str2) {
        int n = str1.size();
        int m = str2.size();
        
        // Step 1: Build the full 2D LCS DP table
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (str1[i - 1] == str2[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        
        // Step 2: Backtrack from (n, m) down to (0, 0)
        string ans = "";
        int i = n, j = m;
        
        while (i > 0 && j > 0) {
            if (str1[i - 1] == str2[j - 1]) {
                // Common character: take once and move diagonally
                ans.push_back(str1[i - 1]);
                i--;
                j--;
            } else if (dp[i - 1][j] > dp[i][j - 1]) {
                // Discarded from str1: must include it in supersequence
                ans.push_back(str1[i - 1]);
                i--;
            } else {
                // Discarded from str2: must include it in supersequence
                ans.push_back(str2[j - 1]);
                j--;
            }
        }
        
        // Add remaining characters from str1 (if any)
        while (i > 0) {
            ans.push_back(str1[i - 1]);
            i--;
        }
        
        // Add remaining characters from str2 (if any)
        while (j > 0) {
            ans.push_back(str2[j - 1]);
            j--;
        }
        
        // Reverse because we traced backwards
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (table fill $\mathcal{O}(N \times M)$, backtracking $\mathcal{O}(N + M)$).
- **Space Complexity**: $\mathcal{O}(N \times M)$ space (full 2D table required for backtracking path).
- **Why this is optimal**: Backtracking extracts the complete merged supersequence in a single $\mathcal{O}(N + M)$ traversal.

---

## 6. Dry Run

`str1 = "abac"`, `str2 = "cab"` ($N = 4, M = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `LCS Table` | LCS of `"abac"` and `"cab"` is `"ab"` (length 2) | dp[4][3] = 2 |
| `Backtrack (4, 3)` | str1[3]='c', str2[2]='b' $\implies$ dp[3][3]=2 vs dp[4][2]=2 $\implies$ take 'c' from str1, move to (3, 3) | ans = `"c"` |
| `Backtrack (3, 3)` | str1[2]='a', str2[2]='b' $\implies$ take 'b' from str2, move to (3, 2) | ans = `"cb"` |
| `Backtrack (3, 2)` | str1[2]='a' == str2[1]='a' $\implies$ take 'a' once, move to (2, 1) | ans = `"cba"` |
| `Backtrack (2, 1)` | str1[1]='b' == str2[0]='b' $\implies$ take 'b' once, move to (1, 0) | ans = `"cbab"` |
| `Remaining` | i=1: take 'a' from str1 $\implies ans = `"cbaba"` | Reverse: `"ababc"` (or `"cabac"`) |
| `Result` | Return `"cabac"` (Length $4 + 3 - 2 = 5$) | Valid SCS ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- One string is a substring of the other (returns the larger string).
- Identical strings (returns `str1`).
- Disjoint character sets (returns `str1 + str2`).

### Common Bugs to Avoid
- Forgetting to append remaining characters in `while (i > 0)` and `while (j > 0)` loops after exiting main while loop.
- Forgetting string reversal.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does SCS length strictly equal N + M - Length(LCS)?**  
  **A**: Because in the merged supersequence, all $N$ characters of $str1$ and all $M$ characters of $str2$ must be represented. The only characters shared between both without duplication are the $LCS$ characters. Thus: $N + M - LCS$!

- **Q2: How does SCS relate to DNA Multiple Sequence Alignment?**  
  **A**: In Bioinformatics, aligning two DNA genomic strands with minimal insertions and deletions is solved using Needleman-Wunsch / SCS dynamic programming.

- **Q3: Can we find the SCS of K strings (K > 2)?**  
  **A**: For $K$ strings, finding the Shortest Common Supersequence is **NP-Hard** (approximated via Greedy Contig Merging).


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `LCS`, `Supersequence`, `LeetCode-1092`, `Hard`
- **Related problems to practice next**:
- **Print Longest Common Subsequence**: Backtracking LCS string.
- **Edit Distance**: String transformation.
