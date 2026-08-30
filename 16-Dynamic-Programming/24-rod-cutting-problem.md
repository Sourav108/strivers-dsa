# Rod Cutting Problem (Step 16.3 — DP on Subsequences)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Rod Cutting Problem](https://takeuforward.org/data-structure/rod-cutting-problem-dp-24/)
- **Difficulty**: Medium
- **Statement**: Given a rod of length $N$ inches and an array of prices `price` of size $N$ that includes prices of all pieces of size smaller than or equal to $N$. Determine the maximum value obtainable by cutting up the rod and selling the pieces. You can cut the rod into any number of pieces of integer lengths.

---

## 1. Problem, Restated

Maximize revenue by partitioning rod of length $N$ into integer pieces $l \in [1, N]$ with price `price[l - 1]` using Unbounded Knapsack DP in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Reduction to Unbounded Knapsack**: 
- Rod length $N \iff$ Knapsack capacity $W = N$. 
- Piece length $i + 1 \iff$ Item weight `wt[i] = i + 1`. 
- Piece price `price[i]` $\iff$ Item value `val[i]`. 
- We can cut multiple pieces of the same length $\iff$ Unbounded Knapsack! 
**Recurrence**: 
$$\text{dp}[l] = \max_{1 \le \text{len} \le l} (\text{price}[\text{len} - 1] + \text{dp}[l - \text{len}])$$ 
Base case: $\text{dp}[0] = 0$. 
Forward iteration in a single 1D array of size $N + 1$ runs in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `Unbounded Knapsack / Length-Partitioning Dynamic Programming`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $2^{N-1}$ ways to cut the rod in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int n, const vector<int>& p) {
        if (i == 0) return n * p[0];
        int notTake = solve(i - 1, n, p);
        int take = 0;
        int rodLength = i + 1;
        if (rodLength <= n) take = p[i] + solve(i, n - rodLength, p);
        return max(notTake, take);
    }
public:
    int cutRod(vector<int>& price, int n) {
        return solve(n - 1, n, price);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int n, int[] p) {
        if (i == 0) return n * p[0];
        int notTake = solve(i - 1, n, p);
        int take = 0;
        int rodLength = i + 1;
        if (rodLength <= n) take = p[i] + solve(i, n - rodLength, p);
        return Math.max(notTake, take);
    }

    int cutRod(int[] price, int n) {
        return solve(n - 1, n, price);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Exponential cut combination search.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N^2) time and O(N^2) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int cutRod(vector<int>& price, int n) {
        vector<vector<int>> dp(n, vector<int>(n + 1, 0));
        for (int l = 0; l <= n; l++) dp[0][l] = l * price[0];
        
        for (int i = 1; i < n; i++) {
            int rodLen = i + 1;
            for (int l = 0; l <= n; l++) {
                int notTake = dp[i - 1][l];
                int take = (l >= rodLen) ? price[i] + dp[i][l - rodLen] : 0;
                dp[i][l] = max(notTake, take);
            }
        }
        return dp[n - 1][n];
    }
};
```

### Java Code
```java
class Solution2D {

    int cutRod(int[] price, int n) {
        int[][] dp = new int[n][n + 1];
        for (int l = 0; l <= n; l++) dp[0][l] = l * price[0];
        
        for (int i = 1; i < n; i++) {
            int rodLen = i + 1;
            for (int l = 0; l <= n; l++) {
                int notTake = dp[i - 1][l];
                int take = (l >= rodLen) ? price[i] + dp[i][l - rodLen] : 0;
                dp[i][l] = Math.max(notTake, take);
            }
        }
        return dp[n - 1][n];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ table space.
- **Why it's still not optimal**: 2D table uses redundant memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Single-Vector Forward Unbounded DP in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int cutRod(vector<int>& price, int n) {
        // dp[l] stores max profit obtainable from a rod of length l
        vector<int> dp(n + 1, 0);
        
        // Iterate through all possible piece lengths (1 to n)
        for (int i = 0; i < n; i++) {
            int pieceLength = i + 1;
            int piecePrice = price[i];
            
            // Forward iteration from pieceLength to n allows infinite pieces
            for (int l = pieceLength; l <= n; l++) {
                dp[l] = max(dp[l], piecePrice + dp[l - pieceLength]);
            }
        }
        
        return dp[n];
    }
};
```

### Java Code
```java
class Solution {

    int cutRod(int[] price, int n) {
        // dp[l] stores max profit obtainable from a rod of length l
        int[] dp = new int[n + 1];
        
        // Iterate through all possible piece lengths (1 to n)
        for (int i = 0; i < n; i++) {
            int pieceLength = i + 1;
            int piecePrice = price[i];
            
            // Forward iteration from pieceLength to n allows infinite pieces
            for (int l = pieceLength; l <= n; l++) {
                dp[l] = Math.max(dp[l], piecePrice + dp[l - pieceLength]);
            }
        }
        
        return dp[n];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time ($N$ pieces $\times N$ lengths).
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary space (single vector of size $N + 1$).
- **Why this is optimal**: Forward 1D relaxation computes maximum cutting profit in minimal linear memory.

---

## 6. Dry Run

`price = [1, 5, 8, 9, 10, 17, 17, 20]`, $N = 8$

| Step | Action / State Change | Result |
|---|---|---|
| `Piece 1 (len=1, p=1)` | dp: `[0, 1, 2, 3, 4, 5, 6, 7, 8]` | All 1-inch cuts |
| `Piece 2 (len=2, p=5)` | dp[2]=5, dp[4]=10, dp[6]=15, dp[8]=20 | 2-inch cuts dominate |
| `Piece 3 (len=3, p=8)` | dp[3]=8, dp[6]=16, dp[8]=max(20, 8+dp[5]=8+13=21) | dp[8] = 21 |
| `Piece 6 (len=6, p=17)` | dp[8] = max(21, 17+dp[2]=17+5=22) | dp[8] = 22 |
| `Result` | Return dp[8] = 22 (Cut into length 2 and length 6: $5 + 17 = 22$) | Max Revenue = 22 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `price[0]`).
- Cutting not profitable (best price is uncut $N$-length piece).
- All pieces priced uniformly.

### Common Bugs to Avoid
- 1-based vs 0-based indexing mismatch (`pieceLength = i + 1` with `price[i]`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to reconstruct the exact piece lengths cut?**  
  **A**: Maintain an array `cutChoice[l]` storing the optimal `pieceLength` used for length $l$. Backtrack $l = N$: subtract `cutChoice[l]` until $l = 0$ in $\mathcal{O}(N)$ time.

- **Q2: How does Rod Cutting relate to Minimum Cost to Cut a Stick (Problem 50)?**  
  **A**: In Rod Cutting, cutting makes money and pieces have fixed prices (Unbounded Knapsack). In **Cut a Stick**, every cut COSTS money equal to the current stick length, which requires **Matrix Chain Multiplication / Partition DP**!

- **Q3: What is the time complexity if cuts can only be made from a restricted set of allowed lengths K?**  
  **A**: If only $|K|$ piece lengths are permitted, time complexity drops to $\mathcal{O}(|K| \times N)$ and space remains $\mathcal{O}(N)$.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Subsequences`, `Unbounded Knapsack`, `Rod Cutting`, `Medium`
- **Related problems to practice next**:
- **Unbounded Knapsack**: General framework.
- **Minimum Cost to Cut a Stick**: Partition DP variant.
