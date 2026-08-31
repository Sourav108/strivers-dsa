# Swim in Rising Water (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Swim in Rising Water](https://takeuforward.org/data-structure/swim-in-rising-water/)
- **Difficulty**: Hard
- **Statement**: You are given an $n \times n$ integer matrix `grid` where `grid[i][j]` represents the elevation at `(i, j)`. At time $t$, water level is $t$. You can swim between adjacent cells if both elevations $\le t$. Return the minimum time $t$ required to swim from `(0, 0)` to `(n - 1, n - 1)`.

---

## 1. Problem, Restated

Find the minimum bottleneck path (minimax maximum elevation) from `(0, 0)` to `(n - 1, n - 1)` on an $n \times n$ grid using Modified Minimax Dijkstra or DSU in $\mathcal{O}(N^2 \log N)$ time.

- **Input**: `vector<vector<int>>& grid` of size $N \times N$.
- **Output**: Integer representing minimum time $t$ to reach `(n-1, n-1)`.
- **Constraints**: $1 \le n \le 50$, $0 \le \text{grid}[i][j] < n^2$, all values unique.

---

## 2. Intuition & Pattern

To reach $(n-1, n-1)$ with minimum water level $t$, we need to find a path whose maximum cell elevation is minimized! 
**Approach 1: Minimax Dijkstra (Optimal & Most Elegant)**: 
1) Min-Heap stores `{time, {r, c}}`. Push `{grid[0][0], {0, 0}}`. 
2) Maintain `dist[n][n]` initialized to infinity, with `dist[0][0] = grid[0][0]`. 
3) Pop `{currentTime, {r, c}}`. If `r == n - 1 && c == n - 1`, return `currentTime` immediately! 
4) For each 4-directional neighbor $(nr, nc)$: calculate next bottleneck time `nextTime = max(currentTime, grid[nr][nc])`. 
5) If `nextTime < dist[nr][nc]`, update `dist[nr][nc] = nextTime` and push `{nextTime, {nr, nc}}`. 
**Approach 2: Kruskal's DSU Connectivity**: Sort all cells/edges by elevation; unite adjacent cells at time $t$ and stop as soon as `dsu.isConnected(0, n * n - 1)`. Runs in $\mathcal{O}(N^2 \log N)$ time.

- **Underlying Pattern**: `Minimax Priority Queue Dijkstra / Kruskal's DSU Connectivity / Binary Search + BFS`.

---

## 3. Approach 1 — Naive / Exhaustive Path DFS

### Idea
DFS backtracking with all simple paths in $\mathcal{O}(4^{N^2})$ time.

### C++17 Code
```cpp
// O(4^(N^2)) naive DFS
```

### Java Code
```java
// Java equivalent
// O(4^(N^2)) naive DFS
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(4^{N^2})$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ stack.
- **Why it's not good enough**: Exponential path combinations on grids.

---

## 4. Approach 2 — Better (Binary Search on Answer with BFS)

### Idea
Binary Search on Answer [0, n*n - 1] with BFS Reachability in O(N^2 log(N^2)) time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class SolutionBinarySearch {
    bool canReach(int t, const vector<vector<int>>& grid, int n) {
        if (grid[0][0] > t) return false;
        vector<vector<int>> vis(n, vector<int>(n, 0));
        queue<pair<int, int>> q;
        q.push({0, 0}); vis[0][0] = 1;
        int dr[] = {-1, 0, 1, 0}, dc[] = {0, 1, 0, -1};
        while (!q.empty()) {
            auto [r, c] = q.front(); q.pop();
            if (r == n - 1 && c == n - 1) return true;
            for (int i = 0; i < 4; i++) {
                int nr = r + dr[i], nc = c + dc[i];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && !vis[nr][nc] && grid[nr][nc] <= t) {
                    vis[nr][nc] = 1; q.push({nr, nc});
                }
            }
        }
        return false;
    }
public:
    int swimInWater(vector<vector<int>>& grid) {
        int n = grid.size();
        int low = grid[0][0], high = n * n - 1, ans = high;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canReach(mid, grid, n)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
};
```

### Java Code
```java
class SolutionBinarySearch {
    boolean canReach(int t, int[][] grid, int n) {
        if (grid[0][0] > t) return false;
        int[][] vis = new int[n][n];
        queue<pair<int, int>> q;
        q.push({0, 0}); vis[0][0] = 1;
        int dr[] = {-1, 0, 1, 0}, dc[] = {0, 1, 0, -1};
        while (!q.isEmpty()) {
            var [r, c] = q.peek(); q.pop();
            if (r == n - 1 && c == n - 1) return true;
            for (int i = 0; i < 4; i++) {
                int nr = r + dr[i], nc = c + dc[i];
                if (nr >= 0 && nr < n && nc >= 0 && nc < n && !vis[nr][nc] && grid[nr][nc] <= t) {
                    vis[nr][nc] = 1; q.push({nr, nc});
                }
            }
        }
        return false;
    }

    int swimInWater(int[][] grid) {
        int n = grid.length;
        int low = grid[0][0], high = n * n - 1, ans = high;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canReach(mid, grid, n)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \log(N^2)) = \mathcal{O}(N^2 \log N)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ queue and visited grid.
- **Why it's still not optimal**: Binary Search is excellent, but Minimax Dijkstra explores the minimal search frontier without multiple BFS passes.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Minimax Dijkstra)

### Idea
Minimax Priority Queue Dijkstra in $\mathcal{O}(N^2 \log N)$ time and $\mathcal{O}(N^2)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

class Solution {
public:
    int swimInWater(vector<vector<int>>& grid) {
        int n = grid.size();
        
        // Min-heap stores {time, {row, col}}
        priority_queue<pair<int, pair<int, int>>,
                       vector<pair<int, pair<int, int>>>,
                       greater<pair<int, pair<int, int>>>> pq;
        
        vector<vector<int>> dist(n, vector<int>(n, 1e9));
        
        dist[0][0] = grid[0][0];
        pq.push({grid[0][0], {0, 0}});
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        while (!pq.empty()) {
            auto top = pq.top();
            pq.pop();
            
            int time = top.first;
            int r = top.second.first;
            int c = top.second.second;
            
            // Early exit: First extraction of destination is guaranteed optimal
            if (r == n - 1 && c == n - 1) {
                return time;
            }
            
            if (time > dist[r][c]) continue;
            
            for (int i = 0; i < 4; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < n) {
                    // Bottleneck elevation is maximum along the path
                    int nextTime = max(time, grid[nr][nc]);
                    
                    if (nextTime < dist[nr][nc]) {
                        dist[nr][nc] = nextTime;
                        pq.push({nextTime, {nr, nc}});
                    }
                }
            }
        }
        
        return 0;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int swimInWater(int[][] grid) {
        int n = grid.length;
        
        // Min-heap stores {time, {row, col}}
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) . Integer.compare(a[0], b[0]));
        
        int[][] dist = new int[n][n];
        
        dist[0][0] = grid[0][0];
        pq.push({grid[0][0], {0, 0}});
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        while (!pq.isEmpty()) {
            var top = pq.peek();
            pq.pop();
            
            int time = top.first;
            int r = top.second.first;
            int c = top.second.second;
            
            // Early exit: First extraction of destination is guaranteed optimal
            if (r == n - 1 && c == n - 1) {
                return time;
            }
            
            if (time > dist[r][c]) continue;
            
            for (int i = 0; i < 4; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < n) {
                    // Bottleneck elevation is maximum along the path
                    int nextTime = Math.max(time, grid[nr][nc]);
                    
                    if (nextTime < dist[nr][nc]) {
                        dist[nr][nc] = nextTime;
                        pq.push({nextTime, {nr, nc}});
                    }
                }
            }
        }
        
        return 0;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2 \log N)$ time (at most $N^2$ elements in the binary min-heap).
- **Space Complexity**: $\mathcal{O}(N^2)$ distance grid and priority queue memory.
- **Why this is optimal**: Greedy Minimax relaxation guarantees that the first time $(n-1, n-1)$ is popped from the heap, the water level is minimized.

---

## 6. Dry Run

`grid = [[0, 2], [1, 3]]` ($N = 2$)

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist[0][0]=0 | pq: `[{0, {0,0}}]` |
| `Pop {0, {0,0}}` | Neighbors: `(0,1)` (wt: $\max(0,2)=2$), `(1,0)` (wt: $\max(0,1)=1$) | pq: `[{1, {1,0}}, {2, {0,1}}]` |
| `Pop {1, {1,0}}` | Neighbor: `(1,1)` (wt: $\max(1,3)=3$) | pq: `[{2, {0,1}}, {3, {1,1}}]` |
| `Pop {2, {0,1}}` | Neighbor: `(1,1)` (wt: $\max(2,3)=3$ - not smaller) | pq: `[{3, {1,1}}]` |
| `Pop {3, {1,1}}` | Destination reached! $\implies$ return time = 3 | Result = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `grid[0][0]`).
- Matrix already strictly sorted.
- Source has maximum elevation in the entire grid.

### Common Bugs to Avoid
- Setting `dist[0][0] = 0` when `grid[0][0] > 0` (you MUST wait until $t = \text{grid}[0][0]$ before you can even stand at the starting cell!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does the Kruskal's DSU alternative solve this problem?**  
  **A**: 1) Create an array of all cells sorted by elevation $0, 1, 2, \dots, N^2 - 1$. 2) For time $t = 0 \dots N^2 - 1$: activate cell at elevation $t$ and unite with all already-active neighbors using `dsu.unionBySize()`. 3) Check `if (dsu.isConnected(0, n * n - 1))` $\implies$ return $t$! Runs in $\mathcal{O}(N^2 \log(N^2))$ time!

- **Q2: Why does Dijkstra work here even though edge costs are NOT additive sums?**  
  **A**: Because the bottleneck function $f(u, v) = \max(d(u), \text{elevation}(v))$ is **monotonic** ($f(u, v) \ge d(u)$). Dijkstra's greedy correctness relies only on the non-decreasing monotonicity of path costs, which holds for both summation and maximum bottleneck operations!

- **Q3: How does this compare to Path with Minimum Effort (Problem 31)?**  
  **A**: In *Path with Minimum Effort*, edge weights are absolute differences $|h_1 - h_2|$; in *Swim in Rising Water*, vertex weights are absolute elevations $\text{grid}[r][c]$, and the starting cost is $\text{grid}[0][0]$ instead of 0.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Dijkstra`, `Minimax`, `DSU`, `Binary Search`, `LeetCode-778`, `Hard`
- **Related problems to practice next**:
- **Path with Minimum Effort**: Minimax Dijkstra on absolute diffs.
- **Shortest Path in Binary Matrix**: Grid BFS.
