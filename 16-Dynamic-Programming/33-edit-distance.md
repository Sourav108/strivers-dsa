# Edit Distance (Levenshtein Distance: Insert, Delete, Replace) (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Edit Distance (Levenshtein Distance: Insert, Delete, Replace)](https://takeuforward.org/data-structure/edit-distance-dp-33/)
- **Difficulty**: Hard
- **Statement**: Given two strings `word1` and `word2`, return the minimum number of operations required to convert `word1` to `word2`. You have the following three operations permitted on a word: (1) Insert a character, (2) Delete a character, (3) Replace a character.

---

## 1. Problem, Restated

Find the minimum Levenshtein edit operations to convert `word1` to `word2` using 1D Space-Optimized DP in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The 3 Elementary Edit Operations**: 
Let $\text{dp}[i][j]$ be the minimum operations to convert `word1[0 ... i - 1]` to `word2[0 ... j - 1]`. 
1) **If `word1[i - 1] == word2[j - 1]` (Characters Match)**: 
   No edit operation required! Move both pointers: 
   $$\text{dp}[i][j] = \text{dp}[i - 1][j - 1]$$ 
2) **If `word1[i - 1] != word2[j - 1]` (Characters Mismatch)**: 
   We explore all 3 operations and take the minimum $+ 1$: 
   - **Insert into `word1`**: Matches `word2[j - 1]`, `word1` pointer stays at $i$, `word2` pointer decrements $\implies 1 + \text{dp}[i][j - 1]$ 
   - **Delete from `word1`**: Discards `word1[i - 1]`, `word1` decrements, `word2` stays at $j \implies 1 + \text{dp}[i - 1][j]$ 
   - **Replace `word1[i - 1]` with `word2[j - 1]`**: Both matched, both decrement $\implies 1 + \text{dp}[i - 1][j - 1]$ 
   $$\text{dp}[i][j] = 1 + \min(\text{dp}[i][j - 1], \text{dp}[i - 1][j], \text{dp}[i - 1][j - 1])$$ 
**Base Cases**: $\text{dp}[0][j] = j$ (insert $j$ characters) and $\text{dp}[i][0] = i$ (delete $i$ characters). 
**Space Optimization**: 1D vector `prev(M + 1)` in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Underlying Pattern**: `2D String DP / 3-Way Operation Branching (Insert, Delete, Replace)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively test all 3 operations at every mismatch in $\mathcal{O}(3^{\max(N, M)})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int j, const string& s1, const string& s2) {
        if (i < 0) return j + 1; // Insert remaining s2 characters
        if (j < 0) return i + 1; // Delete remaining s1 characters
        if (s1[i] == s2[j]) return solve(i - 1, j - 1, s1, s2);
        int ins = 1 + solve(i, j - 1, s1, s2);
        int del = 1 + solve(i - 1, j, s1, s2);
        int rep = 1 + solve(i - 1, j - 1, s1, s2);
        return min({ins, del, rep});
    }
public:
    int minDistance(string word1, string word2) {
        return solve(word1.size() - 1, word2.size() - 1, word1, word2);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3^{\max(N, M)})$ time.
- **Space Complexity**: $\mathcal{O}(N + M)$ recursion stack.
- **Why it's not good enough**: Ternary recursive tree with massive overlapping prefixes.

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
    int minDistance(string word1, string word2) {
        int n = word1.size(), m = word2.size();
        vector<vector<int>> dp(n + 1, vector<int>(m + 1, 0));
        for (int i = 0; i <= n; i++) dp[i][0] = i;
        for (int j = 0; j <= m; j++) dp[0][j] = j;
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else {
                    dp[i][j] = 1 + min({dp[i][j - 1], dp[i - 1][j], dp[i - 1][j - 1]});
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
1D Row Space-Optimized DP in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(\min(N, M))$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minDistance(string word1, string word2) {
        int n = word1.size();
        int m = word2.size();
        
        // prev[j] stores edit distance for previous row (i - 1)
        vector<int> prev(m + 1, 0);
        for (int j = 0; j <= m; j++) {
            prev[j] = j; // Base case: converting empty word1 to word2 of length j
        }
        
        for (int i = 1; i <= n; i++) {
            vector<int> cur(m + 1, 0);
            cur[0] = i; // Base case: converting word1 of length i to empty word2 (i deletions)
            
            for (int j = 1; j <= m; j++) {
                if (word1[i - 1] == word2[j - 1]) {
                    cur[j] = prev[j - 1]; // Matching character: 0 cost
                } else {
                    int insertOp  = cur[j - 1];  // Insert
                    int deleteOp  = prev[j];     // Delete
                    int replaceOp = prev[j - 1]; // Replace
                    
                    cur[j] = 1 + min({insertOp, deleteOp, replaceOp});
                }
            }
            
            prev = cur;
        }
        
        return prev[m];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each cell evaluates $\min$ of 3 values in $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(\min(N, M))$ auxiliary space (two 1D row vectors).
- **Why this is optimal**: Compresses the 2D grid to two rows using minimal register-allocated memory.

---

## 6. Dry Run

`word1 = "horse"`, `word2 = "ros"` ($N = 5, M = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Init (Row 0)` | prev = `[0, 1, 2, 3]` | Base ready |
| `i=1 'h'` | cur[0]=1; 'h'!='r': 1+min(1,0,1)=1; 'h'!='o': 1+min(1,1,2)=2; 'h'!='s': 1+min(2,2,3)=3 $\implies prev = [1, 1, 2, 3]$ | Row 'h' done |
| `i=2 'o'` | 'o'=='o' at j=2 $\implies cur[2] = prev[1] = 1 \implies prev = [2, 2, 1, 2]$ | Row 'o' done |
| `i=3 'r'` | 'r'=='r' at j=1 $\implies cur[1] = prev[0] = 2 \implies prev = [3, 2, 2, 2]$ | Row 'r' done |
| `i=4 's'` | 's'=='s' at j=3 $\implies cur[3] = prev[2] = 2 \implies prev = [4, 3, 3, 2]$ | Row 's' done |
| `i=5 'e'` | cur = `[5, 4, 4, 3]` $\implies prev[3] = 3$ | Row 'e' done |
| `Result` | Return 3 (Operations: replace 'h'->'r', remove 'r', remove 'e') | Edit Distance = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- One or both strings empty (returns length of other string).
- Identical strings (returns 0).
- Single character differences.

### Common Bugs to Avoid
- Setting `cur[0] = 0` instead of `cur[0] = i` (deleting $i$ characters to reach empty string costs $i$).
- Swapping insert and delete pointer logic.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the physical interpretation of each direction in the 2D DP matrix?**  
  **A**: 1) **Diagonal $(i-1, j-1)$**: Replace `word1[i-1]` with `word2[j-1]`. 2) **Vertical Up $(i-1, j)$**: Delete `word1[i-1]`. 3) **Horizontal Left $(i, j-1)$**: Insert `word2[j-1]` into `word1`!

- **Q2: How does Edit Distance differ from Myers Diff and LCS?**  
  **A**: In LCS/Myers Diff, character replacement is forbidden (cost = $\infty$). In Levenshtein distance, replacement costs 1. In Damerau-Levenshtein distance, transposition of adjacent characters also costs 1!

- **Q3: How to prune Edit Distance if we only care if distance is <= K (e.g. spell checkers)?**  
  **A**: Use **Ukkonen's Banded DP**: only compute cells within a diagonal band $|i - j| \le K$, running in $\mathcal{O}(K \cdot \min(N, M))$ time!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `Edit Distance`, `Levenshtein`, `LeetCode-72`, `Hard`
- **Related problems to practice next**:
- **Minimum Insertions Deletions Convert**: No replacement variant.
- **Wildcard Matching**: Pattern matching.
