# Minimum Path Sum in Grid (Step 16.2 — 2D/3D DP and DP on Grids)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Path Sum in Grid](https://takeuforward.org/data-structure/minimum-path-sum-in-a-grid-dp-10/)
- **Difficulty**: Medium
- **Statement**: Given a $m \times n$ `grid` filled with non-negative numbers, find a path from top left to bottom right, which minimizes the sum of all numbers along its path. You can only move either down or right at any point in time.

---

## 1. Problem, Restated

Find the minimum weight path from $(0, 0)$ to $(m - 1, n - 1)$ moving only Right or Down in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ or $\mathcal{O}(1)$ auxiliary space.

---

## 2. Intuition & Pattern

To reach cell $(i, j)$ with minimum cost, the robot must transition from either the cell above $(i - 1, j)$ or the cell to the left $(i, j - 1)$ with minimum accumulated cost: 
$$\text{dp}[i][j] = \text{grid}[i][j] + \min(\text{dp}[i - 1][j], \text{dp}[i][j - 1])$$ 
**Base Cases**: 
- $\text{dp}[0][0] = \text{grid}[0][0]$ 
- Top boundary ($i = 0$): $\text{dp}[0][j] = \text{dp}[0][j - 1] + \text{grid}[0][j]$ (can only come from left) 
- Left boundary ($j = 0$): $\text{dp}[i][0] = \text{dp}[i - 1][0] + \text{grid}[i][0]$ (can only come from above) 
**Space Optimization**: A single 1D row array `prev(n)` stores the previous row costs. For each cell: `cur[j] = grid[i][j] + min(prev[j], cur[j - 1])`. Runs in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `Grid 2D DP / Min-Cost Arrival Transition`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all Down and Right paths in $\mathcal{O}(2^{M+N})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int j, const vector<vector<int>>& g) {
        if (i == 0 && j == 0) return g[0][0];
        if (i < 0 || j < 0) return 1e9;
        int up = g[i][j] + solve(i - 1, j, g);
        int left = g[i][j] + solve(i, j - 1, g);
        return min(up, left);
    }
public:
    int minPathSum(vector<vector<int>>& grid) {
        return solve(grid.size() - 1, grid[0].size() - 1, grid);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int j, int[][] g) {
        if (i == 0 && j == 0) return g[0][0];
        if (i < 0 || j < 0) return 1e9;
        int up = g[i][j] + solve(i - 1, j, g);
        int left = g[i][j] + solve(i, j - 1, g);
        return Math.min(up, left);
    }

    int minPathSum(int[][] grid) {
        return solve(grid.length - 1, grid[0].size() - 1, grid);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^{M+N})$ time.
- **Space Complexity**: $\mathcal{O}(M + N)$ recursion stack.
- **Why it's not good enough**: Exponential duplicate evaluation of cell prefixes.

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
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        vector<vector<int>> dp(m, vector<int>(n, 0));
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 && j == 0) dp[i][j] = grid[i][j];
                else {
                    int up = (i > 0) ? dp[i - 1][j] : 1e9;
                    int left = (j > 0) ? dp[i][j - 1] : 1e9;
                    dp[i][j] = grid[i][j] + min(up, left);
                }
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

### Java Code
```java
class Solution2D {

    int minPathSum(int[][] grid) {
        int m = grid.length, n = grid[0].size();
        int[][] dp = new int[m][n];
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 && j == 0) dp[i][j] = grid[i][j];
                else {
                    int up = (i > 0) ? dp[i - 1][j] : 1e9;
                    int left = (j > 0) ? dp[i][j - 1] : 1e9;
                    dp[i][j] = grid[i][j] + Math.min(up, left);
                }
            }
        }
        return dp[m - 1][n - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \times N)$ time.
- **Space Complexity**: $\mathcal{O}(M \times N)$ table space.
- **Why it's still not optimal**: Full 2D table uses redundant memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Space-Optimized 1D Row DP in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        int m = grid.size();
        int n = grid[0].size();
        
        // prev[j] stores minimum path sum to reach (previous_row, j)
        vector<int> prev(n, 0);
        
        for (int i = 0; i < m; i++) {
            vector<int> cur(n, 0);
            for (int j = 0; j < n; j++) {
                if (i == 0 && j == 0) {
                    cur[j] = grid[i][j];
                } else {
                    int up = (i > 0) ? prev[j] : 1e9;
                    int left = (j > 0) ? cur[j - 1] : 1e9;
                    cur[j] = grid[i][j] + min(up, left);
                }
            }
            prev = cur;
        }
        
        return prev[n - 1];
    }
};
```

### Java Code
```java
class Solution {

    int minPathSum(int[][] grid) {
        int m = grid.length;
        int n = grid[0].size();
        
        // prev[j] stores minimum path sum to reach (previous_row, j)
        int[] prev = new int[n];
        
        for (int i = 0; i < m; i++) {
            int[] cur = new int[n];
            for (int j = 0; j < n; j++) {
                if (i == 0 && j == 0) {
                    cur[j] = grid[i][j];
                } else {
                    int up = (i > 0) ? prev[j] : 1e9;
                    int left = (j > 0) ? cur[j - 1] : 1e9;
                    cur[j] = grid[i][j] + Math.min(up, left);
                }
            }
            prev = cur;
        }
        
        return prev[n - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \times N)$ time (each cell processed in $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(N)$ space (single row array of size $N$).
- **Why this is optimal**: Min-cost selection requires only the current row's left neighbor and previous row's upper neighbor.

---

## 6. Dry Run

`grid = [[1, 3, 1], [1, 5, 1], [4, 2, 1]]` ($M = 3, N = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Row 0` | cur: `[1, 1+3=4, 4+1=5]` $\implies prev = [1, 4, 5]$ | Row 0 done |
| `Row 1` | j=0: 1+1=2; j=1: 5+\min(4,2)=7; j=2: 1+\min(5,7)=6 $\implies prev = [2, 7, 6]$ | Row 1 done |
| `Row 2` | j=0: 4+2=6; j=1: 2+\min(7,6)=8; j=2: 1+\min(6,8)=7 $\implies prev = [6, 8, 7]$ | Row 2 done |
| `Result` | Return prev[2] = 7 (Path: $1 \to 3 \to 1 \to 1 \to 1 = 7$) | Min Path Sum = 7 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $M = 1$ (single row, sum of all cells).
- $N = 1$ (single column, sum of all cells).
- Cells with 0 values.

### Common Bugs to Avoid
- Using 0 instead of `1e9` for invalid out-of-bounds transitions (can cause `min(up, left)` to incorrectly pick 0 instead of the real valid direction).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to print the actual minimum path (sequence of cell coordinates)?**  
  **A**: Maintain a 2D direction matrix `dir[i][j]` (0 for Up, 1 for Left). Backtrack from $(m-1, n-1)$ to $(0,0)$ to reconstruct the path in $\mathcal{O}(M + N)$ time.

- **Q2: What if we could move in ALL 4 directions (Up, Down, Left, Right)?**  
  **A**: Moving in 4 directions introduces cycles! DP is strictly for Directed Acyclic Graphs (DAGs). With 4 directions, this becomes a shortest path problem solved with **Dijkstra's Algorithm** in $\mathcal{O}(M \times N \log(MN))$ time!

- **Q3: How does Minimum Path Sum extend to Triangular Grids (Problem 11)?**  
  **A**: In Triangle DP, we move from row $i$ to $(i+1, j)$ or $(i+1, j+1)$. Working bottom-up from the base of the triangle simplifies transitions to $\mathcal{O}(1)$ space without boundary checks!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Grid DP`, `Min Path`, `LeetCode-64`, `Medium`
- **Related problems to practice next**:
- **Minimum Path Sum in Triangular Grid**: Triangle grid DP.
- **Grid Unique Paths**: Path counting counterpart.
