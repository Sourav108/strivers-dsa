# 0/1 Matrix (Distance of Nearest Cell having 1 / 0) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [0/1 Matrix (Distance of Nearest Cell having 1 / 0)](https://takeuforward.org/data-structure/distance-of-nearest-cell-having-1/)
- **Difficulty**: Medium
- **Statement**: Given an $m \times n$ binary matrix `mat`, return the distance of the nearest 0 (or nearest 1 in GFG variant) for each cell. The distance between two adjacent cells is 1.

---

## 1. Problem, Restated

Multi-Source BFS: enqueue all target cells (distance 0) at start and expand outward layer-by-layer to populate distance matrix in $\mathcal{O}(N \times M)$ time.

- **Input**: `vector<vector<int>>& mat` binary grid of size $M \times N$.
- **Output**: `vector<vector<int>>` matrix of shortest distances.
- **Constraints**: $1 \le m, n \le 10^4$, $1 \le m \times n \le 10^4$.

---

## 2. Intuition & Pattern

If we start a BFS from every non-target cell to find the nearest target, it takes $\mathcal{O}((N \times M)^2)$ time (quadratic!). **Invert the problem**: Start a simultaneous Multi-Source BFS from ALL target cells at once with distance 0. As the BFS expands level-by-level, the first time any cell is visited, its distance from the closest source is guaranteed to be optimal! Runs in strictly linear $\mathcal{O}(N \times M)$ time.

- **Underlying Pattern**: `Multi-Source BFS Distance Propagation on 2D Grid`.

---

## 3. Approach 1 — Naive / Individual BFS

### Idea
Launch a separate BFS/DFS from every cell in $\mathcal{O}((N \times M)^2)$ time.

### C++17 Code
```cpp
// O((N*M)^2) individual BFS per cell
```

### Java Code
```java
// Java equivalent
// O((N*M)^2) individual BFS per cell
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((N \times M)^2)$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$ queue.
- **Why it's not good enough**: Redundant explorations from each starting cell.

---

## 4. Approach 2 — Better

### Idea
2-Pass Dynamic Programming (Top-Left to Bottom-Right, then Bottom-Right to Top-Left) in O(N x M) time and O(1) extra space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionDP {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        int n = mat.size(), m = mat[0].size();
        int INF = n + m;
        vector<vector<int>> dist(n, vector<int>(m, INF));
        
        // Pass 1: Top-Left to Bottom-Right (check Top and Left)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (mat[i][j] == 0) dist[i][j] = 0;
                else {
                    if (i > 0) dist[i][j] = min(dist[i][j], dist[i - 1][j] + 1);
                    if (j > 0) dist[i][j] = min(dist[i][j], dist[i][j - 1] + 1);
                }
            }
        }
        
        // Pass 2: Bottom-Right to Top-Left (check Bottom and Right)
        for (int i = n - 1; i >= 0; i--) {
            for (int j = m - 1; j >= 0; j--) {
                if (i < n - 1) dist[i][j] = min(dist[i][j], dist[i + 1][j] + 1);
                if (j < m - 1) dist[i][j] = min(dist[i][j], dist[i][j + 1] + 1);
            }
        }
        
        return dist;
    }
};
```

### Java Code
```java
class SolutionDP {

    int[][] updateMatrix(int[][] mat) {
        int n = mat.length, m = mat[0].size();
        int INF = n + m;
        int[][] dist = new int[n][m];
        
        // Pass 1: Top-Left to Bottom-Right (check Top and Left)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (mat[i][j] == 0) dist[i][j] = 0;
                else {
                    if (i > 0) dist[i][j] = Math.min(dist[i][j], dist[i - 1][j] + 1);
                    if (j > 0) dist[i][j] = Math.min(dist[i][j], dist[i][j - 1] + 1);
                }
            }
        }
        
        // Pass 2: Bottom-Right to Top-Left (check Bottom and Right)
        for (int i = n - 1; i >= 0; i--) {
            for (int j = m - 1; j >= 0; j--) {
                if (i < n - 1) dist[i][j] = Math.min(dist[i][j], dist[i + 1][j] + 1);
                if (j < m - 1) dist[i][j] = Math.min(dist[i][j], dist[i][j + 1] + 1);
            }
        }
        
        return dist;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (excluding output matrix).
- **Why it's still not optimal**: Dynamic Programming works only on Manhattan grid metrics without obstacles.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Multi-Source FIFO Queue BFS in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    vector<vector<int>> updateMatrix(vector<vector<int>>& mat) {
        int n = mat.size();
        int m = mat[0].size();
        
        vector<vector<int>> dist(n, vector<int>(m, 0));
        vector<vector<int>> vis(n, vector<int>(m, 0));
        
        // Queue stores tuple: {{row, col}, distance}
        queue<pair<pair<int, int>, int>> q;
        
        // 1. Enqueue all 0s with initial distance 0 (Sources)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (mat[i][j] == 0) {
                    q.push({{i, j}, 0});
                    vis[i][j] = 1;
                }
            }
        }
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        // 2. Multi-source BFS expansion
        while (!q.empty()) {
            auto curr = q.front();
            q.pop();
            
            int r = curr.first.first;
            int c = curr.first.second;
            int d = curr.second;
            
            dist[r][c] = d;
            
            for (int i = 0; i < 4; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                // If within bounds and not yet visited by an earlier/closer source
                if (nr >= 0 && nr < n && nc >= 0 && nc < m && !vis[nr][nc]) {
                    vis[nr][nc] = 1;
                    q.push({{nr, nc}, d + 1});
                }
            }
        }
        
        return dist;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[][] updateMatrix(int[][] mat) {
        int n = mat.length;
        int m = mat[0].size();
        
        int[][] dist = new int[n][m];
        int[][] vis = new int[n][m];
        
        // Queue stores tuple: {{row, col}, distance}
        queue<pair<pair<int, int>, int>> q;
        
        // 1. Enqueue all 0s with initial distance 0 (Sources)
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (mat[i][j] == 0) {
                    q.push({{i, j}, 0});
                    vis[i][j] = 1;
                }
            }
        }
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        // 2. Multi-source BFS expansion
        while (!q.isEmpty()) {
            var curr = q.peek();
            q.pop();
            
            int r = curr.first.first;
            int c = curr.first.second;
            int d = curr.second;
            
            dist[r][c] = d;
            
            for (int i = 0; i < 4; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                // If within bounds and not yet visited by an earlier/closer source
                if (nr >= 0 && nr < n && nc >= 0 && nc < m && !vis[nr][nc]) {
                    vis[nr][nc] = 1;
                    q.push({{nr, nc}, d + 1});
                }
            }
        }
        
        return dist;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each cell entered into queue and processed exactly once).
- **Space Complexity**: $\mathcal{O}(N \times M)$ queue and visited matrix space.
- **Why this is optimal**: Multi-source initialization guarantees shortest path assignment upon first visit.

---

## 6. Dry Run

`mat = [[0,0,0],[0,1,0],[1,1,1]]` ($3 \times 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Init (d=0)` | Push all five 0-cells: (0,0), (0,1), (0,2), (1,0), (1,2) with d=0 | dist initialized with 0s |
| `Expand d=1` | From (1,0) and (1,2): visit (1,1) -> dist[1][1] = 1; visit (2,0) and (2,2) -> dist[2][0]=1, dist[2][2]=1 | q: `[(1,1, 1), (2,0, 1), (2,2, 1)]` |
| `Expand d=2` | From (2,0)/(2,2): visit (2,1) -> dist[2][1] = 2 | q: `[(2,1, 2)]` |
| `Result` | `[[0,0,0],[0,1,0],[1,2,1]]` | Optimal Distances Reached ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All cells are 0 (all distances 0).
- Only one 0 at corner.

### Common Bugs to Avoid
- Setting `vis[nr][nc] = 1` after popping rather than when pushing to queue (leads to duplicate queue entries).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Multi-Source BFS guarantee the minimum distance?**  
  **A**: Because all source cells (distance 0) are added to the queue first. BFS explores nodes in strictly non-decreasing order of distance. The first time a node is reached, it is mathematically guaranteed to be via the shortest possible distance!

- **Q2: Can we do this without an explicit vis matrix?**  
  **A**: Yes! Initialize `dist` matrix with `-1`. When enqueuing sources, set `dist[i][j] = 0`. An unvisited cell is identified by `dist[nr][nc] == -1`.

- **Q3: How does the 2-Pass DP approach work in O(1) extra space?**  
  **A**: Any shortest Manhattan path moves either (Down/Right) or (Up/Left). Pass 1 computes shortest paths using only Top and Left neighbors; Pass 2 relaxes distances using Bottom and Right neighbors.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Multi-Source BFS`, `Matrix`, `Dynamic Programming`, `LeetCode-542`, `Medium`
- **Related problems to practice next**:
- **Rotting Oranges**: Multi-source BFS counterpart.
- **Shortest Path in Binary Matrix**: Grid BFS.
