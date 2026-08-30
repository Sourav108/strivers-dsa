# Count Square Submatrices with All Ones (Step 16.7 — Matrix Chain Multiplication / Partition DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Count Square Submatrices with All Ones](https://takeuforward.org/data-structure/maximum-rectangle-area-with-all-1s-dp-on-rectangles-dp-55/)
- **Difficulty**: Medium
- **Statement**: Given a $m \times n$ matrix of ones and zeros, return how many **square submatrices** have all ones.

---

## 1. Problem, Restated

Count all valid square submatrices filled entirely with 1s using 2D Square Corner Expansion DP in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Value / Count result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Bottom-Right Corner Invariant**: 
Let $\text{dp}[i][j]$ denote the **size of the largest square submatrix whose bottom-right corner is at `(i, j)`**. 
- If `matrix[i][j] == 0`, no square can end at `(i, j)` $\implies \text{dp}[i][j] = 0$. 
- If `matrix[i][j] == 1`: 
  A square of size $S$ ending at $(i, j)$ requires squares of size at least $S - 1$ ending at: 
  1) The cell above $(i - 1, j)$ 
  2) The cell to the left $(i, j - 1)$ 
  3) The diagonal cell $(i - 1, j - 1)$ 
$$\text{dp}[i][j] = 1 + \min(\text{dp}[i - 1][j], \text{dp}[i][j - 1], \text{dp}[i - 1][j - 1])$$ 
**Counting Principle**: 
If the largest square ending at $(i, j)$ has size $S$, then there are EXACTLY $S$ squares of sizes $1, 2, \dots, S$ ending at $(i, j)$! 
$$\text{Total Squares} = \sum_{i=0}^{m-1} \sum_{j=0}^{n-1} \text{dp}[i][j]$$ 
**Space Optimization**: A single 1D row array `dp[j]` in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `2D Grid DP / Square Bottom-Right Corner Expansion $\min(\text{top}, \text{left}, \text{diag}) + 1$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
For every cell $(i, j)$ and every possible square size $S$, check if all $S^2$ cells are 1 in $\mathcal{O}(M \cdot N \cdot \min(M, N)^3)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(M * N * min(M,N)^3) brute force
};
```

### Java Code
```java
class SolutionNaive {
    // O(M * N * Math.min(M,N)^3) brute force
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \cdot N \cdot \min(M, N)^3)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Polynomial brute force checking all submatrices.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(M x N) time and O(M x N) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int countSquares(vector<vector<int>>& matrix) {
        int m = matrix.size(), n = matrix[0].size();
        vector<vector<int>> dp(m, vector<int>(n, 0));
        int totalSquares = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 1) {
                    if (i == 0 || j == 0) dp[i][j] = 1;
                    else dp[i][j] = 1 + min({dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1]});
                    totalSquares += dp[i][j];
                }
            }
        }
        return totalSquares;
    }
};
```

### Java Code
```java
class Solution2D {

    int countSquares(int[][] matrix) {
        int m = matrix.length, n = matrix[0].size();
        int[][] dp = new int[m][n];
        int totalSquares = 0;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == 1) {
                    if (i == 0 || j == 0) dp[i][j] = 1;
                    else dp[i][j] = 1 + Math.min({dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1]});
                    totalSquares += dp[i][j];
                }
            }
        }
        return totalSquares;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \times N)$ time.
- **Space Complexity**: $\mathcal{O}(M \times N)$ space.
- **Why it's still not optimal**: Full 2D table uses redundant memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-Place / 1D Space-Optimized DP in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int countSquares(vector<vector<int>>& matrix) {
        int m = matrix.size();
        int n = matrix[0].size();
        
        int totalSquares = 0;
        vector<int> dp(n, 0);
        int diag = 0; // Stores dp[i - 1][j - 1]
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int temp = dp[j]; // Save before overwriting (becomes diag for next column)
                
                if (matrix[i][j] == 1) {
                    if (i == 0 || j == 0) {
                        dp[j] = 1;
                    } else {
                        dp[j] = 1 + min({dp[j], dp[j - 1], diag});
                    }
                    totalSquares += dp[j];
                } else {
                    dp[j] = 0;
                }
                
                diag = temp;
            }
        }
        
        return totalSquares;
    }
};
```

### Java Code
```java
class Solution {

    int countSquares(int[][] matrix) {
        int m = matrix.length;
        int n = matrix[0].size();
        
        int totalSquares = 0;
        int[] dp = new int[n];
        int diag = 0; // Stores dp[i - 1][j - 1]
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                int temp = dp[j]; // Save before overwriting (becomes diag for next column)
                
                if (matrix[i][j] == 1) {
                    if (i == 0 || j == 0) {
                        dp[j] = 1;
                    } else {
                        dp[j] = 1 + Math.min({dp[j], dp[j - 1], diag});
                    }
                    totalSquares += dp[j];
                } else {
                    dp[j] = 0;
                }
                
                diag = temp;
            }
        }
        
        return totalSquares;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \times N)$ time (each cell evaluated in $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary space (single 1D vector of size $N$).
- **Why this is optimal**: Tracks 3 neighbor corners using 1D row array and a single scalar `diag` variable.

---

## 6. Dry Run

`matrix = [[0,1,1,1],[1,1,1,1],[0,1,1,1]]` ($M = 3, N = 4$)

| Step | Action / State Change | Result |
|---|---|---|
| `Row 0` | dp: `[0, 1, 1, 1]`, total = 3 | Row 0 done |
| `Row 1` | j=0: 1; j=1: 1+min(1,1,0)=1; j=2: 1+min(1,1,1)=2; j=3: 1+min(1,2,1)=2 $\implies dp = [1, 1, 2, 2]$, total += 6 $\implies 9$ | Row 1 done |
| `Row 2` | j=0: 0; j=1: 1; j=2: 1+min(2,1,1)=2; j=3: 1+min(2,2,2)=3 $\implies dp = [0, 1, 2, 3]$, total += 6 $\implies 15$ | Row 2 done |
| `Result` | Return totalSquares = 15 | Total Squares = 15 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All zeroes matrix (returns 0).
- All ones matrix of size $M \times N$.
- $1 \times 1$ matrix.

### Common Bugs to Avoid
- Double-counting boundary cells.
- Not resetting `dp[j] = 0` when `matrix[i][j] == 0`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the sum of dp[i][j] give the EXACT total count of square submatrices?**  
  **A**: If a cell has $\text{dp}[i][j] = 3$, it means $(i, j)$ is the bottom-right corner of a $3 \times 3$ square, a $2 \times 2$ square, and a $1 \times 1$ square (exactly 3 distinct squares). Summing $\text{dp}[i][j]$ over all cells counts every unique square in the entire matrix without duplicates!

- **Q2: How does this problem relate to Maximal Square (LeetCode 221)?**  
  **A**: In Maximal Square, we return the AREA of the largest square: $(\max_{i, j} \text{dp}[i][j])^2$. In Count Squares, we return the SUM of all values: $\sum \text{dp}[i][j]$!

- **Q3: How to find Maximal RECTANGLE with all ones (LeetCode 85)?**  
  **A**: In a general rectangle, width and height can differ. We convert each row into a Histogram of heights and use the **Monotonic Stack (Largest Rectangle in Histogram)** algorithm in $\mathcal{O}(M \times N)$ time!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Grid DP`, `Square Submatrices`, `Space Optimization`, `LeetCode-1277`, `Medium`
- **Related problems to practice next**:
- **Maximal Square**: Area maximization.
- **Maximal Rectangle**: Monotonic stack histogram.
