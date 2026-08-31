# Shortest Path in Binary Matrix (Maze BFS) (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Shortest Path in Binary Matrix (Maze BFS)](https://takeuforward.org/data-structure/g-36-shortest-distance-in-a-binary-maze/)
- **Difficulty**: Medium
- **Statement**: Given an $n \times n$ binary matrix `grid`, return the length of the shortest clear path in the matrix from top-left $(0, 0)$ to bottom-right $(n-1, n-1)$. A clear path only visits cells containing 0 and connects 8-directionally (or 4-directionally in standard maze). If no clear path exists, return -1.

---

## 1. Problem, Restated

Find the shortest unweighted hop count from $(0, 0)$ to $(n-1, n-1)$ in a 2D matrix using 8-directional FIFO Queue BFS in $\mathcal{O}(N^2)$ time.

- **Input**: `vector<vector<int>>& grid` binary matrix of size $N \times N$.
- **Output**: Integer representing length of shortest clear path, or -1.
- **Constraints**: $1 \le n \le 100$, `grid[i][j]` is 0 or 1.

---

## 2. Intuition & Pattern

Since every valid move has equal step cost of 1, standard **FIFO Queue BFS guarantees finding the shortest path upon first reaching the target** (no Priority Queue needed!). 1) If `grid[0][0] != 0` or `grid[n-1][n-1] != 0`, return -1 immediately. 2) If $n = 1$, return 1. 3) Queue stores `{row, col, distance}`, initialized with `{0, 0, 1}`. 4) Mark visited cell immediately (`grid[0][0] = 1`). 5) Pop `{r, c, d}`. For each of 8 neighbor directions: if $(nr, nc)$ is in bounds and `grid[nr][nc] == 0`: if $(nr, nc) == (n-1, n-1)$, return $d + 1$! Mark `grid[nr][nc] = 1` and push `{nr, nc, d + 1}` to queue. 6) If queue becomes empty, return -1. Runs in $\mathcal{O}(N^2)$ time.

- **Underlying Pattern**: `8-Directional Unweighted Matrix BFS (Level-Order Expansion)`.

---

## 3. Approach 1 — Naive / DFS Backtracking

### Idea
DFS backtracking all 8-direction paths taking $\mathcal{O}(8^{N^2})$ exponential time.

### C++17 Code
```cpp
// O(8^(N^2)) DFS backtracking
```

### Java Code
```java
// Java equivalent
// O(8^(N^2)) DFS backtracking
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(8^{N^2})$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ recursion stack.
- **Why it's not good enough**: DFS explores sub-optimal exponential paths before finding shortest.

---

## 4. Approach 2 — Better (Dijkstra on Grid)

### Idea
Dijkstra Algorithm with Priority Queue on Grid in O(N^2 log(N^2)) time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class SolutionDijkstraGrid {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int n = grid.size();
        if (grid[0][0] != 0 || grid[n-1][n-1] != 0) return -1;
        priority_queue<pair<int, pair<int, int>>, vector<pair<int, pair<int, int>>>, greater<>> pq;
        vector<vector<int>> dist(n, vector<int>(n, 1e9));
        dist[0][0] = 1;
        pq.push({1, {0, 0}});
        while (!pq.empty()) {
            auto [d, cell] = pq.top(); pq.pop();
            int r = cell.first, c = cell.second;
            if (r == n - 1 && c == n - 1) return d;
            for (int dr = -1; dr <= 1; dr++) {
                for (int dc = -1; dc <= 1; dc++) {
                    if (dr == 0 && dc == 0) continue;
                    int nr = r + dr, nc = c + dc;
                    if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 0 && d + 1 < dist[nr][nc]) {
                        dist[nr][nc] = d + 1;
                        pq.push({d + 1, {nr, nc}});
                    }
                }
            }
        }
        return -1;
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionDijkstraGrid {

    int shortestPathBinaryMatrix(int[][] grid) {
        int n = grid.length;
        if (grid[0][0] != 0 || grid[n-1][n-1] != 0) return -1;
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) . Integer.compare(a[0], b[0]));
        int[][] dist = new int[n][n];
        dist[0][0] = 1;
        pq.push({1, {0, 0}});
        while (!pq.isEmpty()) {
            var [d, cell] = pq.peek(); pq.pop();
            int r = cell.first, c = cell.second;
            if (r == n - 1 && c == n - 1) return d;
            for (int dr = -1; dr <= 1; dr++) {
                for (int dc = -1; dc <= 1; dc++) {
                    if (dr == 0 && dc == 0) continue;
                    int nr = r + dr, nc = c + dc;
                    if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 0 && d + 1 < dist[nr][nc]) {
                        dist[nr][nc] = d + 1;
                        pq.push({d + 1, {nr, nc}});
                    }
                }
            }
        }
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \log(N^2))$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ space.
- **Why it's still not optimal**: Priority queue adds logarithmic overhead when all weights are 1.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (8-Directional FIFO BFS)

### Idea
8-Directional FIFO Queue BFS with In-Place Visited Mutation in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N^2)$ queue space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int shortestPathBinaryMatrix(vector<vector<int>>& grid) {
        int n = grid.size();
        
        // Edge cases: start or target blocked
        if (grid[0][0] != 0 || grid[n - 1][n - 1] != 0) {
            return -1;
        }
        
        // Single cell matrix
        if (n == 1) {
            return 1;
        }
        
        // Queue stores {row, col, distance}
        queue<pair<pair<int, int>, int>> q;
        q.push({{0, 0}, 1});
        grid[0][0] = 1; // Mark visited in-place
        
        // 8 directional movements (horizontal, vertical, diagonal)
        int dRow[] = {-1, -1, -1, 0, 0, 1, 1, 1};
        int dCol[] = {-1, 0, 1, -1, 1, -1, 0, 1};
        
        while (!q.empty()) {
            auto front = q.front();
            q.pop();
            
            int r = front.first.first;
            int c = front.first.second;
            int d = front.second;
            
            for (int i = 0; i < 8; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 0) {
                    // Early exit as soon as target is reached!
                    if (nr == n - 1 && nc == n - 1) {
                        return d + 1;
                    }
                    
                    grid[nr][nc] = 1; // Mark visited immediately to prevent duplicate enqueues
                    q.push({{nr, nc}, d + 1});
                }
            }
        }
        
        return -1; // Unreachable
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int shortestPathBinaryMatrix(int[][] grid) {
        int n = grid.length;
        
        // Edge cases: start or target blocked
        if (grid[0][0] != 0 || grid[n - 1][n - 1] != 0) {
            return -1;
        }
        
        // Single cell matrix
        if (n == 1) {
            return 1;
        }
        
        // Queue stores {row, col, distance}
        queue<pair<pair<int, int>, int>> q;
        q.push({{0, 0}, 1});
        grid[0][0] = 1; // Mark visited in-place
        
        // 8 directional movements (horizontal, vertical, diagonal)
        int dRow[] = {-1, -1, -1, 0, 0, 1, 1, 1};
        int dCol[] = {-1, 0, 1, -1, 1, -1, 0, 1};
        
        while (!q.isEmpty()) {
            var front = q.peek();
            q.pop();
            
            int r = front.first.first;
            int c = front.first.second;
            int d = front.second;
            
            for (int i = 0; i < 8; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 0) {
                    // Early exit as soon as target is reached!
                    if (nr == n - 1 && nc == n - 1) {
                        return d + 1;
                    }
                    
                    grid[nr][nc] = 1; // Mark visited immediately to prevent duplicate enqueues
                    q.push({{nr, nc}, d + 1});
                }
            }
        }
        
        return -1; // Unreachable
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (each cell visited and marked at most once).
- **Space Complexity**: $\mathcal{O}(N^2)$ queue memory in the worst case; $\mathcal{O}(1)$ auxiliary memory using in-place matrix mutation.
- **Why this is optimal**: Standard FIFO queue guarantees minimum distance on unweighted grids and avoids priority queue log factor.

---

## 6. Dry Run

`grid = [[0,0,0],[1,1,0],[1,1,0]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | grid[0][0]=0 -> mark grid[0][0]=1 | q: `[{(0,0), 1}]` |
| `Pop (0,0), d=1` | Neighbors: (0,1)=0 -> d=2; (1,2)=0 -> d=2 (via diagonal!) | q: `[{(0,1), 2}, {(1,2), 2}]` |
| `Pop (0,1), d=2` | Neighbor (0,2)=0 -> d=3 | q: `[{(1,2), 2}, {(0,2), 3}]` |
| `Pop (1,2), d=2` | Neighbor (2,2) is target! $\implies$ Return $d + 1 = 2 + 1 = 4$ | Shortest Path = 4 ✅ (`(0,0)->(1,2)->(2,2)`) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `grid[0][0] == 1` or `grid[n-1][n-1] == 1` (returns -1).
- $n = 1$ with `grid[0][0] == 0` (returns 1).
- No valid path available.

### Common Bugs to Avoid
- Marking cell visited upon popping rather than upon enqueuing (causes exponential duplicate queue insertions and TLE/MLE!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST we mark cells visited IMMEDIATELY when pushing to the queue?**  
  **A**: If a cell is only marked visited when popped, multiple neighboring cells in the same level can all push that same cell into the queue before it gets processed. This causes the queue size to explode exponentially, leading to severe Memory Limit Exceeded (MLE)!

- **Q2: How does the A* (A-Star) search algorithm optimize this problem?**  
  **A**: A* uses a heuristic function $h(r, c) = \max(|r - (n-1)|, |c - (n-1)|)$ (Chebyshev distance for 8 directions). It prioritizes exploring cells closer to the target, reducing states visited in large open grids!

- **Q3: How does this problem differ from Path with Minimum Effort (LeetCode 1631)?**  
  **A**: In Shortest Path Binary Matrix, all edges have weight 1 (solved by BFS). In Path with Minimum Effort, edges have variable elevation difference weights, requiring Dijkstra's algorithm.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Matrix`, `Shortest Path`, `LeetCode-1091`, `Medium`
- **Related problems to practice next**:
- **Path with Minimum Effort**: Dijkstra on matrix.
- **Shortest Path Unit Weights**: Base unweighted BFS.
