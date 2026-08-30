# Longest Common Subsequence (LCS) (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest Common Subsequence (LCS)](https://takeuforward.org/data-structure/longest-common-subsequence-dp-25/)
- **Difficulty**: Medium
- **Statement**: Given two strings `text1` and `text2`, return the length of their longest common subsequence. If there is no common subsequence, return 0. A subsequence of a string is a new string generated from the original string with some characters (can be none) deleted without changing the relative order of the remaining characters.

---

## 1. Problem, Restated

Find the length of the longest subsequence present in both `text1` of length $N$ and `text2` of length $M$ using 2D String Matching DP in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Foundational String Matching Recurrence**: 
Let $\text{dp}[i][j]$ be the LCS length of prefixes `text1[0 ... i - 1]` and `text2[0 ... j - 1]`. 
Compare the last characters `text1[i - 1]` and `text2[j - 1]`: 
1) **Characters Match (`text1[i - 1] == text2[j - 1]`)**: 
   Both characters contribute $+1$ to LCS. Move both pointers diagonally: 
   $$\text{dp}[i][j] = 1 + \text{dp}[i - 1][j - 1]$$ 
2) **Characters Mismatch (`text1[i - 1] != text2[j - 1]`)**: 
   The optimal LCS comes from either dropping `text1[i-1]` or dropping `text2[j-1]`: 
   $$\text{dp}[i][j] = \max(\text{dp}[i - 1][j], \text{dp}[i][j - 1])$$ 
**Base Cases**: $\text{dp}[0][j] = 0$ and $\text{dp}[i][0] = 0$ (empty string gives LCS 0). 
**Space Optimization**: Row $i$ depends only on row $i - 1$. Maintain a 1D vector `prev(M + 1)` in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Underlying Pattern**: `2D String DP / Character Match vs Branching Mismatch`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively match characters with 2-way branching on mismatch in $\mathcal{O}(2^{\min(N, M)})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int j, const string& s1, const string& s2) {
        if (i < 0 || j < 0) return 0;
        if (s1[i] == s2[j]) return 1 + solve(i - 1, j - 1, s1, s2);
        return max(solve(i - 1, j, s1, s2), solve(i, j - 1, s1, s2));
    }
public:
    int longestCommonSubsequence(string text1, string text2) {
        return solve(text1.size() - 1, text2.size() - 1, text1, text2);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int j, String s1, String s2) {
        if (i < 0 || j < 0) return 0;
        if (s1[i] == s2[j]) return 1 + solve(i - 1, j - 1, s1, s2);
        return Math.max(solve(i - 1, j, s1, s2), solve(i, j - 1, s1, s2));
    }

    int longestCommonSubsequence(String text1, String text2) {
        return solve(text1.length - 1, text2.length - 1, text1, text2);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^{\min(N, M)})$ time.
- **Space Complexity**: $\mathcal{O}(N + M)$ recursion stack.
- **Why it's not good enough**: Exponential duplicate prefix evaluation.

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
    int longestCommonSubsequence(string text1, string text2) {
        int n = text1.size(), m = text2.size();
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[n][m];
    }
};
```

### Java Code
```java
class Solution2D {

    int longestCommonSubsequence(String text1, String text2) {
        int n = text1.length, m = text2.length;
        int[][] dp = new int[n + 1][m + 1];
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    dp[i][j] = 1 + dp[i - 1][j - 1];
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[n][m];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$ table space.
- **Why it's still not optimal**: 2D table allocates redundant rows.

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
    int longestCommonSubsequence(string text1, string text2) {
        // Ensure text2 is the shorter string to minimize auxiliary memory
        if (text1.size() < text2.size()) {
            swap(text1, text2);
        }
        
        int n = text1.size();
        int m = text2.size();
        
        // prev[j] stores LCS length with prefix text2[0 ... j - 1]
        vector<int> prev(m + 1, 0);
        
        for (int i = 1; i <= n; i++) {
            vector<int> cur(m + 1, 0);
            for (int j = 1; j <= m; j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    cur[j] = 1 + prev[j - 1];
                } else {
                    cur[j] = max(prev[j], cur[j - 1]);
                }
            }
            prev = cur;
        }
        
        return prev[m];
    }
};
```

### Java Code
```java
class Solution {

    int longestCommonSubsequence(String text1, String text2) {
        // Ensure text2 is the shorter String to minimize auxiliary memory
        if (text1.length < text2.length) {
            int temp = text1; text1 = text2; text2 = temp;
        }
        
        int n = text1.length;
        int m = text2.length;
        
        // prev[j] stores LCS length with prefix text2[0 ... j - 1]
        int[] prev = new int[m + 1];
        
        for (int i = 1; i <= n; i++) {
            int[] cur = new int[m + 1];
            for (int j = 1; j <= m; j++) {
                if (text1[i - 1] == text2[j - 1]) {
                    cur[j] = 1 + prev[j - 1];
                } else {
                    cur[j] = Math.max(prev[j], cur[j - 1]);
                }
            }
            prev = cur;
        }
        
        return prev[m];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each pair of characters evaluated in $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(\min(N, M))$ space (two 1D vectors of size $\min(N, M) + 1$).
- **Why this is optimal**: String swap ensures minimum memory allocation, providing high L1 cache locality.

---

## 6. Dry Run

`text1 = "abcde"`, `text2 = "ace"` ($N = 5, M = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 1 ('a')` | Matches 'a' at j=1 $\implies cur = [0, 1, 1, 1]$ | prev = `[0, 1, 1, 1]` |
| `i = 2 ('b')` | No match $\implies cur = [0, 1, 1, 1]$ | prev = `[0, 1, 1, 1]` |
| `i = 3 ('c')` | Matches 'c' at j=2 $\implies cur[2] = 1+prev[1]=2 \implies cur = [0, 1, 2, 2]$ | prev = `[0, 1, 2, 2]` |
| `i = 4 ('d')` | No match $\implies cur = [0, 1, 2, 2]$ | prev = `[0, 1, 2, 2]` |
| `i = 5 ('e')` | Matches 'e' at j=3 $\implies cur[3] = 1+prev[2]=3 \implies cur = [0, 1, 2, 3]$ | prev = `[0, 1, 2, 3]` |
| `Result` | Return prev[3] = 3 (LCS = `"ace"`) | LCS Length = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No common characters (returns 0).
- Identical strings (returns $N$).
- Single character strings.

### Common Bugs to Avoid
- Using 0-indexed string pointers directly in 1-indexed DP without subtracting 1 (`text1[i-1]` vs `dp[i]`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to print the actual LCS string (Problem 26)?**  
  **A**: From the full 2D table `dp[n][m]`: if `s1[i-1] == s2[j-1]`, append character and move diagonally $(i-1, j-1)$; else move towards $\max(\text{dp}[i-1][j], \text{dp}[i][j-1])$. Reverse the collected string at the end in $\mathcal{O}(N + M)$ time!

- **Q2: How is LCS used to solve Longest Palindromic Subsequence (LPS / Problem 28)?**  
  **A**: LPS of string $S$ is EXACTLY the Longest Common Subsequence between $S$ and its reverse $\text{reverse}(S)$: $\text{LPS}(S) = \text{LCS}(S, \text{reverse}(S))$!

- **Q3: What is the difference between Subsequence and Substring DP?**  
  **A**: In Subsequence DP, a mismatch branches to $\max(\text{dp}[i-1][j], \text{dp}[i][j-1])$. In **Substring DP**, characters MUST be contiguous, so any mismatch resets $\text{dp}[i][j] = 0$ immediately!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `LCS`, `Space Optimization`, `LeetCode-1143`, `Medium`
- **Related problems to practice next**:
- **Print Longest Common Subsequence**: Backtracking LCS string.
- **Longest Palindromic Subsequence**: LCS with reverse string.
