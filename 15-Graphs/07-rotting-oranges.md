# Rotting Oranges (Multi-source BFS) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Rotting Oranges (Multi-source BFS)](https://takeuforward.org/data-structure/rotton-oranges-min-time-to-rot-all-oranges-bfs/)
- **Difficulty**: Medium
- **Statement**: You are given an $m \times n$ grid where each cell has one of three values: 0 (empty cell), 1 (fresh orange), or 2 (rotten orange). Every minute, any fresh orange that is 4-directionally adjacent to a rotten orange becomes rotten. Return the minimum number of minutes that must elapse until no cell has a fresh orange. If this is impossible, return -1.

---

## 1. Problem, Restated

Multi-Source BFS: enqueue all initially rotten oranges at $t = 0$, expand radially in 4 directions, and verify if any fresh orange remains.

- **Input**: Parameters specified ($V$ vertices, grid $N \times M$, or adjacency matrix).
- **Output**: Value / count / modified matrix / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

All rotten oranges spread rot simultaneously in parallel! 1) Count total `freshCount`. Enqueue all cells with `grid[r][c] == 2` into `queue<pair<int, int>> q`. 2) If `freshCount == 0`, return 0 immediately. 3) While `!q.empty()` and `freshCount > 0`: process all nodes currently in queue (`int sz = q.size()`). For each node, inspect 4 neighbors: if `grid[nr][nc] == 1`, turn rotten `grid[nr][nc] = 2`, `freshCount--`, push to queue. Increment `minutes++`. 4) Return `freshCount == 0 ? minutes : -1`.

- **Underlying Pattern**: `Multi-Source FIFO Queue BFS Level Expansion`.

---

## 3. Approach 1 — Naive / Matrix Search

### Idea
Iterative simulation scanning entire matrix each minute until no new oranges rot in $\mathcal{O}((N \times M)^2)$ time.

### C++17 Code
```cpp
// O((N*M)^2) simulation scanning grid repeatedly
```

### Java Code
```java
// Java equivalent
// O((N*M)^2) simulation scanning grid repeatedly
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((N \times M)^2)$ time.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Quadratic grid rescans.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS/DFS / DSU algorithm below directly achieves optimal $\mathcal{O}(V + E)$ or $\mathcal{O}(N \times M)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Multi-Source BFS Level Expansion in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int orangesRotting(vector<vector<int>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        
        queue<pair<int, int>> q;
        int freshCount = 0;
        
        // 1. Enqueue all initially rotten oranges and count fresh oranges
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 2) {
                    q.push({i, j});
                } else if (grid[i][j] == 1) {
                    freshCount++;
                }
            }
        }
        
        if (freshCount == 0) return 0;
        
        int minutes = 0;
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        // 2. Multi-source BFS layer-by-layer
        while (!q.empty() && freshCount > 0) {
            int sz = q.size();
            minutes++;
            
            for (int i = 0; i < sz; i++) {
                auto [r, c] = q.front();
                q.pop();
                
                for (int d = 0; d < 4; d++) {
                    int nr = r + dRow[d];
                    int nc = c + dCol[d];
                    
                    if (nr >= 0 && nr < n && nc >= 0 && nc < m && grid[nr][nc] == 1) {
                        grid[nr][nc] = 2; // rot the orange
                        freshCount--;
                        q.push({nr, nc});
                    }
                }
            }
        }
        
        return (freshCount == 0) ? minutes : -1;
    }
};
```

### Java Code
```java
class Solution {

    int orangesRotting(int[][] grid) {
        int n = grid.length;
        int m = grid[0].size();
        
        queue<pair<int, int>> q;
        int freshCount = 0;
        
        // 1. Enqueue all initially rotten oranges and count fresh oranges
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == 2) {
                    q.push({i, j});
                } else if (grid[i][j] == 1) {
                    freshCount++;
                }
            }
        }
        
        if (freshCount == 0) return 0;
        
        int minutes = 0;
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        // 2. Multi-source BFS layer-by-layer
        while (!q.isEmpty() && freshCount > 0) {
            int sz = q.length;
            minutes++;
            
            for (int i = 0; i < sz; i++) {
                var [r, c] = q.peek();
                q.pop();
                
                for (int d = 0; d < 4; d++) {
                    int nr = r + dRow[d];
                    int nc = c + dCol[d];
                    
                    if (nr >= 0 && nr < n && nc >= 0 && nc < m && grid[nr][nc] == 1) {
                        grid[nr][nc] = 2; // rot the orange
                        freshCount--;
                        q.push({nr, nc});
                    }
                }
            }
        }
        
        return (freshCount == 0) ? minutes : -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each cell is enqueued and processed at most once).
- **Space Complexity**: $\mathcal{O}(N \times M)$ queue space.
- **Why this is optimal**: Simultaneous multi-source queuing models exact parallel time progression without redundant matrix scans.

---

## 6. Dry Run

`grid = [[2,1,1],[1,1,0],[0,1,1]]` ($N=3, M=3$)

| Step | Action / State Change | Result |
|---|---|---|
| `t = 0` | q: `[(0,0)]`, freshCount = 6 | init |
| `t = 1` | rot (0,1) and (1,0) -> freshCount = 4, q: `[(0,1),(1,0)]` | min = 1 |
| `t = 2` | rot (0,2) and (1,1) -> freshCount = 2, q: `[(0,2),(1,1)]` | min = 2 |
| `t = 3` | rot (2,1) -> freshCount = 1, q: `[(2,1)]` | min = 3 |
| `t = 4` | rot (2,2) -> freshCount = 0, q: `[(2,2)]` | min = 4 |
| `Result` | freshCount == 0 $\implies$ All oranges rotten in 4 mins | Minutes = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No fresh oranges at start (returns 0).
- Isolated fresh orange surrounded by 0s (returns -1).

### Common Bugs to Avoid
- Incrementing `minutes++` after queue becomes empty (causes off-by-one error when no fresh oranges were left).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Multi-Source BFS work for parallel infection problems?**  
  **A**: Because multi-source BFS enqueues ALL initial source nodes into the queue at time $t = 0$. The FIFO property guarantees all distance-1 nodes are processed before any distance-2 node, perfectly simulating simultaneous propagation!

- **Q2: Why CANNOT DFS be used to solve this problem in a single pass?**  
  **A**: DFS explores one path deeply, assigning timestamps that depend on the search order rather than the minimum arrival time from the closest rotten orange. DFS would require tracking minimum timestamps at every cell in $\mathcal{O}(N^2 M^2)$ time!

- **Q3: How to avoid modifying the input grid?**  
  **A**: Maintain a separate `vector<vector<int>> vis` initialized with 0s, marking `vis[r][c] = 2` upon enqueuing.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Multi-Source BFS`, `Matrix`, `LeetCode-994`, `Medium`
- **Related problems to practice next**:
- **01 Matrix**: Nearest 1 distance.
- **Shortest Path in Binary Matrix**: Grid BFS.
