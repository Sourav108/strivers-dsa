# Palindrome Partitioning II (Minimum Cuts for Palindromic Substrings) (Step 16.7 — Matrix Chain Multiplication / Partition DP)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Palindrome Partitioning II (Minimum Cuts for Palindromic Substrings)](https://takeuforward.org/data-structure/palindrome-partitioning-ii-front-partition-dp-53/)
- **Difficulty**: Hard
- **Statement**: Given a string `s`, partition `s` such that every substring of the partition is a palindrome. Return the minimum cuts needed for a palindrome partitioning of `s`.

---

## 1. Problem, Restated

Find the minimum number of cuts to partition `s` into all palindromic substrings using 1D Front Partition DP with $\mathcal{O}(N^2)$ precomputed palindrome table in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Value / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Front Partitioning Invariant**: 
Let $\text{dp}[i]$ denote the minimum cuts needed for suffix `s[i ... n - 1]`. 
From starting index $i$, test all possible prefixes `s[i ... j]` for $j \in [i, n - 1]$: 
1) If `s[i ... j]` is a **Palindrome**: 
   We can make 1 cut after index $j$, reducing the problem to $\text{dp}[j + 1]$: 
   $$\text{cost} = 1 + \text{dp}[j + 1]$$ 
2) Take the minimum over all valid palindromic prefixes: 
   $$\text{dp}[i] = \min_{i \le j < n, \text{isPalin}(i, j)} (1 + \text{dp}[j + 1])$$ 
3) Base Case: $\text{dp}[n] = 0$ (empty suffix requires 0 cuts). 
4) The answer is $\text{dp}[0] - 1$ (since the last substring at the end doesn't need a trailing cut!). 
**Precomputing Palindromes**: An $N \times N$ boolean table `isPalin[i][j]` precomputed in $\mathcal{O}(N^2)$ allows $\mathcal{O}(1)$ palindrome checks, yielding total time $\mathcal{O}(N^2)$ and $\mathcal{O}(N^2)$ space!

- **Underlying Pattern**: `1D Front Partition DP / Palindrome Precomputation`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^{N-1}$ cut placements in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(2^N) recursion
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Exponential cut combination search.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
1D DP with on-the-fly two-pointer palindrome check in O(N^3) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class SolutionN3 {
    bool isPalindrome(int i, int j, const string& s) {
        while (i < j) if (s[i++] != s[j--]) return false;
        return true;
    }
public:
    int minCut(string s) {
        int n = s.size();
        vector<int> dp(n + 1, 0);
        for (int i = n - 1; i >= 0; i--) {
            int minCuts = 1e9;
            for (int j = i; j < n; j++) {
                if (isPalindrome(i, j, s)) {
                    minCuts = min(minCuts, 1 + dp[j + 1]);
                }
            }
            dp[i] = minCuts;
        }
        return dp[0] - 1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: On-the-fly palindrome check adds $\mathcal{O}(N)$ overhead per transition.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Precomputed Palindrome Table + 1D Front Partition DP in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N^2)$ space.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minCut(string s) {
        int n = s.size();
        if (n <= 1) return 0;
        
        // Step 1: Precompute all palindromic substrings in O(N^2)
        vector<vector<bool>> isPalin(n, vector<bool>(n, false));
        for (int i = 0; i < n; i++) isPalin[i][i] = true;
        for (int len = 2; len <= n; len++) {
            for (int i = 0; i <= n - len; i++) {
                int j = i + len - 1;
                if (s[i] == s[j]) {
                    isPalin[i][j] = (len == 2) ? true : isPalin[i + 1][j - 1];
                }
            }
        }
        
        // Step 2: 1D Front Partition DP
        // dp[i] stores min cuts needed for suffix s[i ... n - 1]
        vector<int> dp(n + 1, 0);
        dp[n] = 0; // Base case: 0 cuts for empty string
        
        for (int i = n - 1; i >= 0; i--) {
            int minCuts = 1e9;
            for (int j = i; j < n; j++) {
                if (isPalin[i][j]) {
                    minCuts = min(minCuts, 1 + dp[j + 1]);
                }
            }
            dp[i] = minCuts;
        }
        
        // Subtract 1 because the last piece doesn't require a cut
        return dp[0] - 1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (Palindrome table: $\mathcal{O}(N^2)$, 1D DP: $\mathcal{O}(N^2)$).
- **Space Complexity**: $\mathcal{O}(N^2)$ space (boolean palindrome matrix + $\mathcal{O}(N)$ DP array).
- **Why this is optimal**: $\mathcal{O}(1)$ palindrome queries reduce 1D partition transitions to strictly quadratic $\mathcal{O}(N^2)$ time.

---

## 6. Dry Run

`s = "aab"` ($N = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Palindromes` | isPalin: `"a"` (0,0), `"a"` (1,1), `"b"` (2,2), `"aa"` (0,1) | Precomputed |
| `i = 2 ('b')` | j=2: 'b' is pal $\implies 1 + dp[3] = 1 + 0 = 1$ | dp[2] = 1 |
| `i = 1 ('a')` | j=1: 'a' is pal $\implies 1 + dp[2] = 2$ | dp[1] = 2 |
| `i = 0 ('a')` | j=0: 'a' $\implies 1 + dp[1] = 3$; j=1: 'aa' is pal $\implies 1 + dp[2] = 1 + 1 = 2$ | dp[0] = 2 |
| `Result` | Return dp[0] - 1 = $2 - 1 = 1$ (Partition: `"aa" | "b"`) | Min Cuts = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- String is already a palindrome (returns 0).
- All characters unique `"abc"` (returns $N - 1 = 2$).
- $N = 1$ (returns 0).

### Common Bugs to Avoid
- Forgetting `dp[0] - 1` subtraction at the end.
- Using $\mathcal{O}(N^3)$ interval DP instead of $\mathcal{O}(N^2)$ 1D front partition.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is 1D Front Partition DP O(N^2) rather than standard 2D MCM O(N^3)?**  
  **A**: Because in Palindrome Partitioning, we only make cuts from LEFT to RIGHT in sequence! Once prefix `s[i...j]` is confirmed as a palindrome, we NEVER need to subdivide `s[i...j]` internally. This collapses 2D interval DP $[i, j]$ to 1D suffix DP $[i]$!

- **Q2: How to print all actual palindromic partition pieces?**  
  **A**: In Palindrome Partitioning I (LeetCode 131), use backtracking to collect all palindromic segments in $\mathcal{O}(N \cdot 2^N)$ time.

- **Q3: Can the palindrome precomputation be done during the expansion around centers?**  
  **A**: YES! Expand around centers from every character and between every character pair to populate `isPalin` or directly relax `dp` in $\mathcal{O}(N^2)$ time with $\mathcal{O}(N)$ space!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `MCM`, `Partition DP`, `Palindrome`, `LeetCode-132`, `Hard`
- **Related problems to practice next**:
- **Palindrome Partitioning I**: Backtracking all partitions.
- **Partition Array for Max Sum**: Front partition template.
