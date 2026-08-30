# Number of Enclaves (Count unreachable land cells) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Number of Enclaves (Count unreachable land cells)](https://takeuforward.org/data-structure/number-of-enclaves/)
- **Difficulty**: Medium
- **Statement**: You are given an $m \times n$ binary matrix `grid`, where 0 represents a sea cell and 1 represents a land cell. A move consists of walking from one land cell to another adjacent (4-directionally) land cell or walking off the boundary of the grid. Return the number of land cells in `grid` for which we cannot walk off the boundary of the grid in any number of moves.

---

## 1. Problem, Restated

Count internal land cells (`1`s) that CANNOT reach any of the 4 boundaries using BFS/DFS in $\mathcal{O}(N \times M)$ time.

- **Input**: `vector<vector<int>>& grid` binary matrix of size $M \times N$.
- **Output**: Integer count of enclave land cells.
- **Constraints**: $1 \le m, n \le 500$, `grid[i][j]` is 0 or 1.

---

## 2. Intuition & Pattern

Any land cell `1` connected to the perimeter allows walking off the grid. 1) Enqueue all boundary `1` cells (or launch DFS from all 4 borders) and mark them as visited (`vis[r][c] = 1`). 2) Expand BFS to mark all land cells reachable from the boundary. 3) Count remaining unvisited `1` cells in the matrix (`if (grid[i][j] == 1 && !vis[i][j]) enclaves++`). These are strictly trapped enclaves! Runs in $\mathcal{O}(N \times M)$ time.

- **Underlying Pattern**: `Boundary Multi-Source BFS / DFS Land Eradication`.

---

## 3. Approach 1 — Naive / Individual Cell Search

### Idea
Launch individual BFS from every internal 1-cell to test boundary reachability in $\mathcal{O}((N \times M)^2)$ time.

### C++17 Code
```cpp
// O((N*M)^2) individual search
```

### Java Code
```java
// Java equivalent
// O((N*M)^2) individual search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((N \times M)^2)$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$.
- **Why it's not good enough**: Repeats traversals across overlapping connected land components.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Boundary BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(N \times M)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Multi-Source Boundary Queue BFS in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int numEnclaves(vector<vector<int>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        
        vector<vector<int>> vis(n, vector<int>(m, 0));
        queue<pair<int, int>> q;
        
        // 1. Enqueue all boundary land cells (row 0, row n-1, col 0, col m-1)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (i == 0 || i == n - 1 || j == 0 || j == m - 1) {
                    if (grid[i][j] == 1) {
                        q.push({i, j});
                        vis[i][j] = 1;
                    }
                }
            }
        }
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        // 2. Multi-source BFS to mark all boundary-connected land
        while (!q.empty()) {
            auto [r, c] = q.front();
            q.pop();
            
            for (int d = 0; d < 4; d++) {
                int nr = r + dRow[d];
                int nc = c + dCol[d];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < m && !vis[nr][nc] && grid[nr][nc] == 1) {
                    vis[nr][nc] = 1;
                    q.push({nr, nc});
                }
            }
        }
        
        // 3. Count remaining unvisited 1s (enclaves)
        int enclaves = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1 && !vis[i][j]) {
                    enclaves++;
                }
            }
        }
        
        return enclaves;
    }
};
```

### Java Code
```java
class Solution {

    int numEnclaves(int[][] grid) {
        int n = grid.length;
        int m = grid[0].size();
        
        int[][] vis = new int[n][m];
        queue<pair<int, int>> q;
        
        // 1. Enqueue all boundary land cells (row 0, row n-1, col 0, col m-1)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (i == 0 || i == n - 1 || j == 0 || j == m - 1) {
                    if (grid[i][j] == 1) {
                        q.push({i, j});
                        vis[i][j] = 1;
                    }
                }
            }
        }
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        // 2. Multi-source BFS to mark all boundary-connected land
        while (!q.isEmpty()) {
            var [r, c] = q.peek();
            q.pop();
            
            for (int d = 0; d < 4; d++) {
                int nr = r + dRow[d];
                int nc = c + dCol[d];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < m && !vis[nr][nc] && grid[nr][nc] == 1) {
                    vis[nr][nc] = 1;
                    q.push({nr, nc});
                }
            }
        }
        
        // 3. Count remaining unvisited 1s (enclaves)
        int enclaves = 0;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 1 && !vis[i][j]) {
                    enclaves++;
                }
            }
        }
        
        return enclaves;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each cell visited at most twice).
- **Space Complexity**: $\mathcal{O}(N \times M)$ queue and visited array space.
- **Why this is optimal**: Queue-based perimeter flooding visits each land component in linear time.

---

## 6. Dry Run

`grid = [[0,0,0,0],[1,0,1,0],[0,1,1,0],[0,0,0,0]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Boundary Land` | Cell (1,0)=1 is on left boundary -> q: `[(1,0)]`, vis[1][0]=1 | boundary queue |
| `BFS Expansion` | From (1,0): neighbors are (0,0)=0, (2,0)=0, (1,1)=0 -> no internal land connected! | q empty |
| `Matrix Count` | Cells (1,2), (2,1), (2,2) are 1s and unvisited (vis=0) -> enclaves = 3 | 3 enclave cells |
| `Result` | Total Enclaves = 3 | Enclaves = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No land cells in grid (returns 0).
- All land cells connected to boundary (returns 0).

### Common Bugs to Avoid
- Forgetting to check boundary condition in corner cells twice (e.g. `(0, 0)`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does this problem compare to Surrounded Regions (LeetCode 130)?**  
  **A**: The underlying graph pattern is IDENTICAL. In Surrounded Regions, boundary-unconnected `'O'`s are flipped to `'X'`. In Number of Enclaves, boundary-unconnected `'1'`s are counted and returned!

- **Q2: Can we achieve O(1) auxiliary space without a vis matrix?**  
  **A**: Yes! Mutate boundary-connected land cells directly: `grid[r][c] = 0` during BFS. In the final loop, simply count remaining `grid[i][j] == 1` cells in $\mathcal{O}(1)$ extra memory!

- **Q3: Can Disjoint Set Union (DSU) solve this problem?**  
  **A**: Yes! Create a dummy 'Border' node $N \times M$. Unite all boundary land cells to the Border node. Connect adjacent land cells together. In the final count, count land cells whose `find(u) != find(Border)`.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `DFS`, `Matrix`, `LeetCode-1020`, `Medium`
- **Related problems to practice next**:
- **Surrounded Regions**: Boundary flipping counterpart.
- **Number of Islands**: Component counting.
