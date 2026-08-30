# Minimum Insertions to Make String Palindrome (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Insertions to Make String Palindrome](https://takeuforward.org/data-structure/minimum-insertions-to-make-string-palindrome-dp-29/)
- **Difficulty**: Medium
- **Statement**: Given a string `s`. In one step you can insert any character at any index of the string. Return the minimum number of steps to make `s` palindrome.

---

## 1. Problem, Restated

Compute the minimum number of character insertions needed to turn `s` into a palindrome using the complement formula $N - \text{LPS}(s)$ in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Conservation of Palindromic Subsequence**: 
To make string $S$ a palindrome using the MINIMUM number of insertions: 
1) We should preserve as many existing symmetrical characters as possible $\implies$ identify the **Longest Palindromic Subsequence (LPS)** of $S$! 
2) The remaining $N - \text{LPS}(S)$ characters are the asymmetric culprits that break palindromic symmetry. 
3) For every asymmetric character, we can insert its mirror counterpart on the opposite side of the string with 1 insertion. 
**Master Formula**: 
$$\text{Min Insertions} = N - \text{LPS}(S) = N - \text{LCS}(S, \text{reverse}(S))$$ 
Runs in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `LPS Complement Invariant ($N - \text{LPS}(s)$)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively test all insertion placements at head and tail in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) recursion
};
```

### Java Code
```java
class SolutionNaive {
    // O(2^N) recursion
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Exponential branching tree.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard space-optimized DP below directly achieves optimal $\mathcal{O}(\min(N, M))$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
LPS Reduction ($N - \text{LCS}(S, S^R)$) in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
private:
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
    int minInsertions(string s) {
        int n = s.size();
        string rev_s = s;
        reverse(rev_s.begin(), rev_s.end());
        
        int lpsLength = lcs(s, rev_s);
        
        // Min Insertions = Total Length - LPS Length
        return n - lpsLength;
    }
};
```

### Java Code
```java
class Solution {

    int lcs(String s1, String s2) {
        int n = s1.length;
        int m = s2.length;
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

    int minInsertions(String s) {
        int n = s.length;
        String rev_s = s;
        reverse(rev_s.begin(), rev_s.end());
        
        int lpsLength = lcs(s, rev_s);
        
        // Min Insertions = Total Length - LPS Length
        return n - lpsLength;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (single space-optimized LCS pass).
- **Space Complexity**: $\mathcal{O}(N)$ space (single row array of size $N + 1$).
- **Why this is optimal**: Direct complement reduction evaluates the optimal insertions in minimal linear memory.

---

## 6. Dry Run

`s = "mbadm"` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `LPS Search` | `s = "mbadm"`, `rev_s = "mdabm"` | LCS computation |
| `LPS Found` | LPS is `"mam"` or `"mbm"` or `"mdm"` of length 3 | lpsLength = 3 |
| `Formula` | Min Insertions = $N - \text{LPS} = 5 - 3 = 2$ | Result = 2 |
| `Validation` | Insert 'd' and 'b' $\implies$ `"mbdadbm"` (valid palindrome with 2 insertions!) | Min Steps = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- String is already a palindrome (returns 0).
- All characters distinct (returns $N - 1$).
- $N = 1$ (returns 0).

### Common Bugs to Avoid
- Reversing `s` in-place without preserving original string for LCS comparison.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the number of minimum insertions equal to the number of minimum deletions?**  
  **A**: Because inserting the missing mirror characters and deleting the asymmetric non-matching characters are dual operations! Both resolve the exact same set of $N - \text{LPS}(S)$ asymmetric characters.

- **Q2: How to print the resulting shortest palindromic string after insertions?**  
  **A**: Run Shortest Common Supersequence on $S$ and $\text{reverse}(S)$! The SCS of $S$ and $S^R$ is precisely the shortest palindrome containing $S$ as a subsequence!

- **Q3: Can this be solved if insertions have variable character costs?**  
  **A**: If each character has an insertion cost $C[c]$, we formulate an interval DP: $\text{dp}[i][j] = \min(\text{cost}[s[i]] + \text{dp}[i+1][j], \text{cost}[s[j]] + \text{dp}[i][j-1])$ in $\mathcal{O}(N^2)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `LCS`, `Palindrome`, `LeetCode-1312`, `Medium`
- **Related problems to practice next**:
- **Longest Palindromic Subsequence**: Core subroutine.
- **Min Insertions Deletions Convert**: String conversion counterpart.
