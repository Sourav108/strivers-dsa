# Grid Unique Paths II (With Obstacles) (Step 16.2 — 2D/3D DP and DP on Grids)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Grid Unique Paths II (With Obstacles)](https://takeuforward.org/data-structure/grid-unique-paths-2-dp-on-grids-dp9/)
- **Difficulty**: Medium
- **Statement**: You are given an $m \times n$ integer array `obstacleGrid`. There is a robot initially located at the top-left corner `(0, 0)`. The robot tries to move to the bottom-right corner `(m - 1, n - 1)`. The robot can only move either down or right at any point in time. An obstacle and space are marked as `1` or `0` respectively in `obstacleGrid`. A path that the robot takes cannot include any square that is an obstacle. Return the number of possible unique paths that the robot can take to reach the bottom-right corner.

---

## 1. Problem, Restated

Compute unique paths from $(0, 0)$ to $(m - 1, n - 1)$ around obstacles (`grid[i][j] == 1`) in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ or $\mathcal{O}(1)$ auxiliary space.

---

## 2. Intuition & Pattern

When obstacles are present, combinatorics no longer applies, but Dynamic Programming handles it naturally! 
**DP Rule**: 
1) If `obstacleGrid[i][j] == 1` (obstacle), the robot can NEVER enter this cell $\implies \text{dp}[i][j] = 0$. 
2) If `i == 0 && j == 0`: $\text{dp}[0][0] = 1$ (provided `obstacleGrid[0][0] == 0`). 
3) Otherwise: $\text{dp}[i][j] = \text{dp}[i - 1][j] + \text{dp}[i][j - 1]$. 
**Space Optimization**: Maintain a 1D vector `prev(n, 0)` representing the previous row. Update `prev[j]` in-place as `prev[j] = prev[j] + prev[j - 1]` when `obstacleGrid[i][j] == 0`, and reset `prev[j] = 0` when `obstacleGrid[i][j] == 1`. Runs in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `Grid 2D DP with Obstacle Invalidation (`dp[i][j] = 0` on obstacles)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
DFS backtracking searching all paths and terminating on obstacles in $\mathcal{O}(2^{M+N})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int dfs(int i, int j, int m, int n, const vector<vector<int>>& g) {
        if (i >= m || j >= n || g[i][j] == 1) return 0;
        if (i == m - 1 && j == n - 1) return 1;
        return dfs(i + 1, j, m, n, g) + dfs(i, j + 1, m, n, g);
    }
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        return dfs(0, 0, obstacleGrid.size(), obstacleGrid[0].size(), obstacleGrid);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^{M+N})$ time.
- **Space Complexity**: $\mathcal{O}(M + N)$ recursion stack.
- **Why it's not good enough**: Exponential duplicate path traversals.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(M x N) time and O(M x N) space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution2D {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        if (obstacleGrid[0][0] == 1 || obstacleGrid[m - 1][n - 1] == 1) return 0;
        
        vector<vector<long long>> dp(m, vector<long long>(n, 0));
        dp[0][0] = 1;
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (obstacleGrid[i][j] == 1) {
                    dp[i][j] = 0;
                } else {
                    if (i > 0) dp[i][j] += dp[i - 1][j];
                    if (j > 0) dp[i][j] += dp[i][j - 1];
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
- **Why it's still not optimal**: Storing all $M$ rows is redundant when only the immediately preceding row is accessed.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Row Space-Optimized DP in $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int uniquePathsWithObstacles(vector<vector<int>>& obstacleGrid) {
        int m = obstacleGrid.size();
        int n = obstacleGrid[0].size();
        
        // Base edge case: If start or destination is blocked, 0 paths exist
        if (obstacleGrid[0][0] == 1 || obstacleGrid[m - 1][n - 1] == 1) {
            return 0;
        }
        
        // 1D array representing current row paths
        vector<long long> prev(n, 0);
        prev[0] = 1; // Start cell
        
        for (int i = 0; i < m; i++) {
            vector<long long> cur(n, 0);
            for (int j = 0; j < n; j++) {
                // If obstacle, no paths can pass through this cell
                if (obstacleGrid[i][j] == 1) {
                    cur[j] = 0;
                } else if (i == 0 && j == 0) {
                    cur[j] = 1;
                } else {
                    long long up = (i > 0) ? prev[j] : 0;
                    long long left = (j > 0) ? cur[j - 1] : 0;
                    cur[j] = up + left;
                }
            }
            prev = cur;
        }
        
        return prev[n - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \times N)$ time (every cell evaluated once).
- **Space Complexity**: $\mathcal{O}(N)$ space (single row array of size $N$).
- **Why this is optimal**: Compressing state to a single row provides optimal memory efficiency and high cache line utilization.

---

## 6. Dry Run

`obstacleGrid = [[0,0,0],[0,1,0],[0,0,0]]` ($M = 3, N = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Row 0` | prev: `[1, 1, 1]` | All 3 cells open |
| `Row 1 (j=1 blocked)` | j=0: 1; j=1 (obs): 0; j=2: up(1)+left(0)=1 $\implies$ cur: `[1, 0, 1]` | prev = `[1, 0, 1]` |
| `Row 2` | j=0: 1; j=1: up(0)+left(1)=1; j=2: up(1)+left(1)=2 $\implies$ cur: `[1, 1, 2]` | prev = `[1, 1, 2]` |
| `Result` | Return prev[2] = 2 (Two paths bypassing the center obstacle: Right->Right->Down->Down and Down->Down->Right->Right) | Unique Paths = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `obstacleGrid[0][0] == 1` (start blocked $\implies$ returns 0).
- `obstacleGrid[m-1][n-1] == 1` (destination blocked $\implies$ returns 0).
- $1 \times 1$ grid.

### Common Bugs to Avoid
- Using 32-bit `int` instead of 64-bit `long long` for intermediate grid sums (intermediate paths can exceed `INT_MAX` before final result).
- Not checking if start cell has an obstacle.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is 64-bit 'long long' used for intermediate path sums in C++?**  
  **A**: Even though the final answer might fit in a 32-bit integer, intermediate subproblem cell paths inside the grid can temporarily exceed $2^{31} - 1$, triggering signed integer overflow undefined behavior in C++!

- **Q2: What if we could modify the input grid in-place to achieve O(1) extra space?**  
  **A**: We can overwrite `obstacleGrid[i][j]` directly: set obstacles to 0, start to 1, and accumulate `grid[i][j] = (grid[i-1][j] + grid[i][j-1])`. However, this mutates caller data and requires casting types to avoid 32-bit overflow.

- **Q3: How to find the shortest path avoiding obstacles with positive costs?**  
  **A**: When edge weights/costs are non-uniform or 4-directional moves are allowed, use **Dijkstra / A* Search / 0-1 BFS** instead of DAG DP.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Grid DP`, `Space Optimization`, `LeetCode-63`, `Medium`
- **Related problems to practice next**:
- **Grid Unique Paths**: Unconstrained grid.
- **Minimum Path Sum**: Cost-minimization grid.
