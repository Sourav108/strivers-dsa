# Distinct Subsequences (Number of times string S2 occurs in S1) (Step 16.4 — DP on Strings)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Distinct Subsequences (Number of times string S2 occurs in S1)](https://takeuforward.org/data-structure/distinct-subsequences-dp-32/)
- **Difficulty**: Hard
- **Statement**: Given two strings `s` and `t`, return the number of distinct subsequences of `s` which equals `t`. The test cases are generated so that the answer fits in a 32-bit signed integer.

---

## 1. Problem, Restated

Count the number of ways to form `t` as a subsequence of `s` using 1D Backward-Iterating DP in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / String result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**State Definition**: $\text{dp}[i][j] =$ number of distinct subsequences of prefix `s[0 ... i - 1]` that equal `t[0 ... j - 1]`. 
**Transitions**: 
1) **If `s[i - 1] == t[j - 1]` (Characters Match)**: 
   We have two valid choices: 
   - **Take `s[i - 1]`** to match `t[j - 1]`: reduces to $\text{dp}[i - 1][j - 1]$. 
   - **Do NOT take `s[i - 1]`** (search for earlier matches of `t[j - 1]` in `s`): reduces to $\text{dp}[i - 1][j]$. 
   $$\text{dp}[i][j] = \text{dp}[i - 1][j - 1] + \text{dp}[i - 1][j]$$ 
2) **If `s[i - 1] != t[j - 1]` (Characters Mismatch)**: 
   We must skip `s[i - 1]`: 
   $$\text{dp}[i][j] = \text{dp}[i - 1][j]$$ 
**Base Cases**: 
- $\text{dp}[i][0] = 1$ for all $i$ (1 way to match empty string `t`). 
- $\text{dp}[0][j] = 0$ for $j > 0$ (0 ways to match non-empty `t` with empty `s`). 
**Space Optimization**: Iterating $j$ **BACKWARD from $M$ down to 1** allows a single 1D array of size $M + 1$ in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space!

- **Underlying Pattern**: `2D String DP / Dual-Branch Matching (`take` + `notTake`)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all branching choices in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int j, const string& s, const string& t) {
        if (j < 0) return 1;
        if (i < 0) return 0;
        if (s[i] == t[j]) return solve(i - 1, j - 1, s, t) + solve(i - 1, j, s, t);
        return solve(i - 1, j, s, t);
    }
public:
    int numDistinct(string s, string t) {
        return solve(s.size() - 1, t.size() - 1, s, t);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int j, String s, String t) {
        if (j < 0) return 1;
        if (i < 0) return 0;
        if (s[i] == t[j]) return solve(i - 1, j - 1, s, t) + solve(i - 1, j, s, t);
        return solve(i - 1, j, s, t);
    }

    int numDistinct(String s, String t) {
        return solve(s.length - 1, t.length - 1, s, t);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Exponential duplicate branch matching.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N x M) time and O(N x M) space.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution2D {
public:
    int numDistinct(string s, string t) {
        int n = s.size(), m = t.size();
        vector<vector<unsigned long long>> dp(n + 1, vector<unsigned long long>(m + 1, 0));
        for (int i = 0; i <= n; i++) dp[i][0] = 1;
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (s[i - 1] == t[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return (int)dp[n][m];
    }
};
```

### Java Code
```java
class Solution2D {

    int numDistinct(String s, String t) {
        int n = s.length, m = t.length;
        long[][] dp = new long[n + 1][m + 1];
        for (int i = 0; i <= n; i++) dp[i][0] = 1;
        
        for (int i = 1; i <= n; i++) {
            for (int j = 1; j <= m; j++) {
                if (s[i - 1] == t[j - 1]) {
                    dp[i][j] = dp[i - 1][j - 1] + dp[i - 1][j];
                } else {
                    dp[i][j] = dp[i - 1][j];
                }
            }
        }
        return (int)dp[n][m];
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
Single 1D Vector Backward-Iterating DP in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
public:
    int numDistinct(string s, string t) {
        int n = s.size();
        int m = t.size();
        
        // dp[j] stores number of subsequences of s matching prefix t[0 ... j - 1]
        // Using unsigned long long to avoid intermediate integer overflow
        vector<unsigned long long> dp(m + 1, 0);
        dp[0] = 1; // Base case: 1 way to match empty string t
        
        for (int i = 1; i <= n; i++) {
            // CRITICAL: Iterate j BACKWARDS from m down to 1
            // This ensures dp[j - 1] reads the previous row's state (s[0...i-2])!
            for (int j = m; j >= 1; j--) {
                if (s[i - 1] == t[j - 1]) {
                    dp[j] = dp[j - 1] + dp[j];
                }
                // If mismatch, dp[j] remains unchanged (dp[i][j] = dp[i-1][j])!
            }
        }
        
        return (int)dp[m];
    }
};
```

### Java Code
```java
class Solution {

    int numDistinct(String s, String t) {
        int n = s.length;
        int m = t.length;
        
        // dp[j] stores number of subsequences of s matching prefix t[0 ... j - 1]
        // Using long to avoid intermediate integer overflow
        long[] dp = new long[m + 1];
        dp[0] = 1; // Base case: 1 way to match empty String t
        
        for (int i = 1; i <= n; i++) {
            // CRITICAL: Iterate j BACKWARDS from m down to 1
            // This ensures dp[j - 1] reads the previous row's state (s[0...i-2])!
            for (int j = m; j >= 1; j--) {
                if (s[i - 1] == t[j - 1]) {
                    dp[j] = dp[j - 1] + dp[j];
                }
                // If mismatch, dp[j] remains unchanged (dp[i][j] = dp[i-1][j])!
            }
        }
        
        return (int)dp[m];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each character pair processed in $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(M)$ auxiliary space (a single 1D vector of size $M + 1$).
- **Why this is optimal**: Backward iteration updates the single 1D array in-place with zero auxiliary allocations.

---

## 6. Dry Run

`s = "rabbbit"`, `t = "rabbit"` ($N = 7, M = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dp = `[1, 0, 0, 0, 0, 0, 0]` | Ready |
| `i=1 'r'` | dp[1] = 1 $\implies dp = [1, 1, 0, 0, 0, 0, 0]$ | Matched 'r' |
| `i=2 'a'` | dp[2] = 1 $\implies dp = [1, 1, 1, 0, 0, 0, 0]$ | Matched 'a' |
| `i=3 'b'` | dp[3] = 1 $\implies dp = [1, 1, 1, 1, 0, 0, 0]$ | First 'b' |
| `i=4 'b'` | dp[4] = 1, dp[3] = 1+1=2 $\implies dp = [1, 1, 1, 2, 1, 0, 0]$ | Second 'b' |
| `i=5 'b'` | dp[4] = 1+2=3, dp[3] = 2+1=3 $\implies dp = [1, 1, 1, 3, 3, 0, 0]$ | Third 'b' ($\binom{3}{2} = 3$ ways) |
| `i=6 'i', i=7 't'` | dp[5] = 3, dp[6] = 3 | Propagated |
| `Result` | Return dp[6] = 3 (Three distinct 'b' pair choices) | Distinct Subsequences = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N < M$ (returns 0 immediately).
- String `s == t` (returns 1).
- No matching characters (returns 0).

### Common Bugs to Avoid
- Iterating forward $1 \to M$ in single 1D array (overwrites `dp[j-1]` before it can be used by `dp[j]`).
- Signed 32-bit integer overflow during intermediate accumulations.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does iterating backwards allow a single 1D array for Distinct Subsequences?**  
  **A**: Because $\text{dp}[i][j] = \text{dp}[i-1][j-1] + \text{dp}[i-1][j]$. It needs the OLD value of $\text{dp}[j-1]$ (from row $i-1$). If we iterate from $M$ down to 1, when computing $\text{dp}[j]$, the entry $\text{dp}[j-1]$ has not been overwritten yet!

- **Q2: What if s contains duplicate characters of t?**  
  **A**: The algorithm naturally accounts for combinatorial occurrences. For example, matching $k$ identical characters in $s$ out of $n$ available characters computes $\binom{n}{k}$ automatically!

- **Q3: Can this be used for regex matching?**  
  **A**: Yes, this pattern formulates the core foundation for NFA / DFA pattern transition counts.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Strings`, `Subsequences`, `LeetCode-115`, `Hard`
- **Related problems to practice next**:
- **Longest Common Subsequence**: Length maximization.
- **Edit Distance**: Min operations.
