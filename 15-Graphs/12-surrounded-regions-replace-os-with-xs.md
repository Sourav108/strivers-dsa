# Surrounded Regions (Replace O's with X's on Board) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Surrounded Regions (Replace O's with X's on Board)](https://takeuforward.org/data-structure/surrounded-regions-replace-os-with-xs/)
- **Difficulty**: Medium
- **Statement**: Given an $m \times n$ matrix `board` containing `'X'` and `'O'`, capture all regions that are 4-directionally surrounded by `'X'`. A region is captured by flipping all `'O'`s into `'X'`s in that surrounded region. An `'O'` is NOT surrounded if it is connected to any boundary cell.

---

## 1. Problem, Restated

Boundary-Connected Component Preservation: any `'O'` connected to the perimeter cannot be captured. Run DFS/BFS from all 4 boundaries to mark safe `'O'`s, then flip all remaining internal `'O'`s to `'X'` in $\mathcal{O}(N \times M)$ time.

- **Input**: `vector<vector<char>>& board` of size $M \times N$.
- **Output**: Modified `board` in-place with captured regions replaced by `'X'`.
- **Constraints**: $1 \le m, n \le 200$, `board[i][j]` is `'X'` or `'O'`.

---

## 2. Intuition & Pattern

Instead of checking every internal `'O'` component to see if it reaches a boundary, **invert the logic**: 1) Scan the 4 perimeter borders (top row, bottom row, left column, right column). 2) Whenever a boundary `'O'` is found, run DFS/BFS to mark it and all connected `'O'`s as safe (`vis[r][c] = 1` or mutate to `'#'`). 3) Scan entire matrix: any `'O'` that is NOT marked safe must be surrounded $\implies$ flip to `'X'`. Restore any marked safe `'#'` back to `'O'`. Runs in strictly linear $\mathcal{O}(N \times M)$ time!

- **Underlying Pattern**: `Boundary Flood Fill Inversion (Perimeter DFS/BFS + Matrix Sweep)`.

---

## 3. Approach 1 — Naive / Individual Cell Search

### Idea
For every 'O' cell, launch a search to test if it can reach the boundary, taking $\mathcal{O}((N \times M)^2)$ time.

### C++17 Code
```cpp
// O((N*M)^2) individual search per cell
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((N \times M)^2)$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$.
- **Why it's not good enough**: Repeatedly searches overlapping connected components.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Boundary DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N \times M)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Boundary DFS Flood Fill with In-Place Mutation in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ recursion stack space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    void dfs(int r, int c, vector<vector<char>>& board, vector<vector<int>>& vis) {
        vis[r][c] = 1;
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        int n = board.size();
        int m = board[0].size();
        
        for (int i = 0; i < 4; i++) {
            int nr = r + dRow[i];
            int nc = c + dCol[i];
            
            // Explore unvisited connected 'O' cells
            if (nr >= 0 && nr < n && nc >= 0 && nc < m && !vis[nr][nc] && board[nr][nc] == 'O') {
                dfs(nr, nc, board, vis);
            }
        }
    }

public:
    void solve(vector<vector<char>>& board) {
        int n = board.size();
        if (n == 0) return;
        int m = board[0].size();
        
        vector<vector<int>> vis(n, vector<int>(m, 0));
        
        // 1. Traverse 1st row and last row
        for (int j = 0; j < m; j++) {
            if (!vis[0][j] && board[0][j] == 'O') {
                dfs(0, j, board, vis);
            }
            if (!vis[n - 1][j] && board[n - 1][j] == 'O') {
                dfs(n - 1, j, board, vis);
            }
        }
        
        // 2. Traverse 1st column and last column
        for (int i = 0; i < n; i++) {
            if (!vis[i][0] && board[i][0] == 'O') {
                dfs(i, 0, board, vis);
            }
            if (!vis[i][m - 1] && board[i][m - 1] == 'O') {
                dfs(i, m - 1, board, vis);
            }
        }
        
        // 3. Flip all unvisited 'O's to 'X' (they are surrounded)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (!vis[i][j] && board[i][j] == 'O') {
                    board[i][j] = 'X';
                }
            }
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each cell visited at most twice: boundary DFS + final matrix sweep).
- **Space Complexity**: $\mathcal{O}(N \times M)$ recursion stack and visited array space.
- **Why this is optimal**: Inverting the search from boundaries avoids redundant inner component graph traversals.

---

## 6. Dry Run

`board = [["X","X","X","X"],["X","O","O","X"],["X","X","O","X"],["X","O","X","X"]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Boundary Scan` | Row 3, Col 1 has 'O' at (3, 1) -> Boundary 'O' found! Calls dfs(3, 1) | dfs(3,1) launched |
| `dfs(3,1)` | Neighbors (2,1)='X', (3,0)='X', (3,2)='X' -> no other 'O's connected -> marks vis[3][1]=1 | vis[3][1]=1 |
| `Final Sweep` | Internal 'O's at (1,1), (1,2), (2,2) have vis=0 -> flipped to 'X'! Boundary 'O' at (3,1) has vis=1 -> preserved as 'O' | Result updated |
| `Result` | `[["X","X","X","X"],["X","X","X","X"],["X","X","X","X"],["X","O","X","X"]]` | Surrounded Regions Captured ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N \le 2$ or $M \le 2$ (all cells are boundaries $\implies$ no internal 'O' can be surrounded).
- Board has only 'X's or only 'O's.

### Common Bugs to Avoid
- Forgetting column boundary checks (only checking top/bottom rows misses left/right boundary 'O' chains).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is it much faster to start DFS from the boundaries rather than the inside?**  
  **A**: There are at most $2(N + M)$ boundary cells compared to $N \times M$ internal cells. By flooding inwards from the boundary once, we immediately resolve all non-surrounded cells in a single pass without any backtracking!

- **Q2: How can we achieve O(1) auxiliary memory (excluding call stack)?**  
  **A**: Instead of a `vis` matrix, mutate boundary-connected `'O'` cells directly to a sentinel character `'#'`. After the final pass, flip `'O' \to 'X'` and restore `'#' \to 'O'`!

- **Q3: How is this problem related to Number of Enclaves (LeetCode 1020)?**  
  **A**: Number of Enclaves uses the exact same boundary DFS logic, but instead of flipping cells, it counts how many unvisited `'1'`s remain inside.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `BFS`, `Matrix`, `LeetCode-130`, `Medium`
- **Related problems to practice next**:
- **Number of Enclaves**: Enclave counting counterpart.
- **Flood Fill**: Component coloring.
