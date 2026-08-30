# Minimum Insertions/Deletions to Convert String A to String B (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Insertions/Deletions to Convert String A to String B](https://takeuforward.org/data-structure/minimum-insertions-deletions-to-convert-string-dp-30/)
- **Difficulty**: Medium
- **Statement**: Given two strings `word1` and `word2`, return the minimum number of steps required to make `word1` and `word2` the same. In one step, you can delete exactly one character in either string (equivalent to deleting from `word1` and inserting into `word2`).

---

## 1. Problem, Restated

Find the minimum number of deletions and insertions to convert `word1` into `word2` using $(N - \text{LCS}) + (M - \text{LCS})$ in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(\min(N, M))$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Common Anchor (LCS)**: 
To convert string $A$ (length $N$) into string $B$ (length $M$) with minimum operations: 
1) We should NEVER delete characters that $A$ and $B$ already share in the same relative order $\implies$ find $\text{LCS}(A, B)$ of length $L$. 
2) **Deletions from A**: Delete all characters in $A$ that are not in the LCS $\implies N - L$ deletions. 
3) **Insertions into A**: Insert all characters in $B$ that are not in the LCS $\implies M - L$ insertions. 
**Master Formula**: 
$$\text{Total Operations} = (N - L) + (M - L) = N + M - 2 \times \text{LCS}(A, B)$$ 
Runs in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(\min(N, M))$ space.

- **Underlying Pattern**: `LCS Invariant Decomposition: $\text{Operations} = (N - \text{LCS}) + (M - \text{LCS})$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all insert/delete choices in $\mathcal{O}(2^{\min(N, M)})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^(N+M)) recursion
};
```

### Java Code
```java
class SolutionNaive {
    // O(2^(N+M)) recursion
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^{\min(N, M)})$ time.
- **Space Complexity**: $\mathcal{O}(N + M)$ recursion stack.
- **Why it's not good enough**: Exponential duplicate branch exploration.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard space-optimized DP below directly achieves optimal $\mathcal{O}(\min(N, M))$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized LCS Reduction in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(\min(N, M))$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
private:
    // Helper function to compute LCS length in O(min(N, M)) space
    int lcs(string s1, string s2) {
        if (s1.size() < s2.size()) swap(s1, s2);
        int n = s1.size(), m = s2.size();
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
    int minDistance(string word1, string word2) {
        int n = word1.size();
        int m = word2.size();
        
        int lcsLength = lcs(word1, word2);
        
        // Deletions from word1: (n - lcsLength)
        // Insertions for word2: (m - lcsLength)
        // Total steps = (n - lcsLength) + (m - lcsLength) = n + m - 2 * lcsLength
        return (n - lcsLength) + (m - lcsLength);
    }
};
```

### Java Code
```java
class Solution {

    // Helper function to compute LCS length in O(Math.min(N, M)) space
    int lcs(String s1, String s2) {
        if (s1.length < s2.length) int temp = s1; s1 = s2; s2 = temp;
        int n = s1.length, m = s2.length;
        int[] prev = new int[m + 1];
        
        for (int i = 1; i <= n; i++) {
            int[] cur = new int[m + 1];
            for (int j = 1; j <= m; j++) {
                if (s1[i - 1] == s2[j - 1]) {
                    cur[j] = 1 + prev[j - 1];
                } else {
                    cur[j] = Math.max(prev[j], cur[j - 1]);
                }
            }
            prev = cur;
        }
        return prev[m];
    }

    int minDistance(String word1, String word2) {
        int n = word1.length;
        int m = word2.length;
        
        int lcsLength = lcs(word1, word2);
        
        // Deletions from word1: (n - lcsLength)
        // Insertions for word2: (m - lcsLength)
        // Total steps = (n - lcsLength) + (m - lcsLength) = n + m - 2 * lcsLength
        return (n - lcsLength) + (m - lcsLength);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (standard LCS matrix evaluation).
- **Space Complexity**: $\mathcal{O}(\min(N, M))$ auxiliary space (single row array).
- **Why this is optimal**: LCS reduces the problem to basic arithmetic $N + M - 2 \times \text{LCS}$ in minimal linear memory.

---

## 6. Dry Run

`word1 = "sea"`, `word2 = "eat"` ($N = 3, M = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `LCS Calculation` | LCS of `"sea"` and `"eat"` is `"ea"` | lcsLength = 2 |
| `Deletions from word1` | $N - \text{LCS} = 3 - 2 = 1$ (delete 's' $\implies$ `"ea"`) | 1 deletion |
| `Insertions for word2` | $M - \text{LCS} = 3 - 2 = 1$ (insert 't' $\implies$ `"eat"`) | 1 insertion |
| `Total Steps` | $1 + 1 = 2$ | Steps = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Identical strings (returns 0).
- Completely disjoint strings (returns $N + M$).
- One string is empty (returns length of other string).

### Common Bugs to Avoid
- Using Edit Distance directly with replacement cost = 1 (this problem strictly permits only Insert and Delete, so replacement requires 2 steps: 1 delete + 1 insert!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does this problem differ from Edit Distance (Levenshtein Distance / Problem 33)?**  
  **A**: In Levenshtein Edit Distance, **Replace** is an allowed primitive operation with cost 1. In this problem, Replace is NOT allowed (or equivalently has cost 2: delete + insert). Therefore, this problem reduces directly to LCS, whereas Edit Distance requires a 3-way DP branch!

- **Q2: How to print the exact edit script (sequence of insertions and deletions)?**  
  **A**: Backtrack the 2D LCS table: moving up corresponds to `DELETE word1[i-1]`, moving left corresponds to `INSERT word2[j-1]`, and moving diagonally corresponds to `KEEP word1[i-1]` (matching character) in $\mathcal{O}(N + M)$ time.

- **Q3: Can this formula be used for Git diff / file comparison tools?**  
  **A**: YES! **Myers Diff Algorithm** (used in `git diff`) is an optimized version of LCS-based shortest edit script calculation.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `LCS`, `Edit Distance`, `LeetCode-583`, `Medium`
- **Related problems to practice next**:
- **Longest Common Subsequence**: Base algorithm.
- **Edit Distance**: Includes replacement.
