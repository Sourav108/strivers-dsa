# Path with Minimum Effort (Dijkstra on 2D Matrix) (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Path with Minimum Effort (Dijkstra on 2D Matrix)](https://takeuforward.org/data-structure/g-37-path-with-minimum-effort/)
- **Difficulty**: Medium
- **Statement**: You are given a 2D array `heights` of size $R \times C$. A route's effort is the maximum absolute difference in heights between two consecutive cells of the route. Return the minimum effort required to travel from the top-left cell $(0, 0)$ to the bottom-right cell $(R-1, C-1)$.

---

## 1. Problem, Restated

Find the path minimizing the maximum edge weight (minimax path) from $(0, 0)$ to $(R-1, C-1)$ using Dijkstra's algorithm in $\mathcal{O}(R \times C \log(R \times C))$ time.

- **Input**: `vector<vector<int>>& heights` matrix of size $R \times C$.
- **Output**: Integer representing minimum path effort.
- **Constraints**: $1 \le R, C \le 100$, $1 \le \text{heights}[i][j] \le 10^6$.

---

## 2. Intuition & Pattern

Instead of summing edge weights ($d + wt$), the path cost function is the **bottleneck maximum**: $\max(\text{effort}, |h_1 - h_2|)$! 1) Initialize `dist(R, vector<int>(C, 1e9))` with `dist[0][0] = 0`. 2) Min-Heap stores `{effort, {r, c}}`. Push `{0, {0, 0}}`. 3) Pop `{diff, {r, c}}`. If $(r, c) == (R-1, C-1)$, return `diff` immediately (first pop is globally optimal!). 4) If `diff > dist[r][c]`, continue (lazy deletion). 5) For each 4-direction neighbor $(nr, nc)$: calculate step effort `newEffort = max(diff, abs(heights[nr][nc] - heights[r][c]))`. If `newEffort < dist[nr][nc]`, update `dist[nr][nc] = newEffort` and push to min-heap. Runs in $\mathcal{O}(R \times C \log(R \times C))$ time.

- **Underlying Pattern**: `Minimax Path Relaxation via Dijkstra / Binary Search on Answer + BFS`.

---

## 3. Approach 1 — Naive / All-Paths DFS Backtracking

### Idea
DFS backtracking all simple paths in $\mathcal{O}(4^{R \times C})$ exponential time.

### C++17 Code
```cpp
// O(4^(R*C)) DFS backtracking
```

### Java Code
```java
// Java equivalent
// O(4^(R*C)) DFS backtracking
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(4^{R \times C})$ time.
- **Space Complexity**: $\mathcal{O}(R \times C)$ call stack.
- **Why it's not good enough**: Exponential search without greedy path pruning.

---

## 4. Approach 2 — Better (Binary Search on Answer + BFS)

### Idea
Binary Search on Answer (Effort Range [0, 10^6]) + BFS Reachability in O(R x C x log(maxHeight)) time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
#include <cmath>
using namespace std;

class SolutionBinarySearch {
    bool canReach(int mid, const vector<vector<int>>& heights) {
        int r = heights.size(), c = heights[0].size();
        vector<vector<int>> vis(r, vector<int>(c, 0));
        queue<pair<int, int>> q;
        q.push({0, 0});
        vis[0][0] = 1;
        int dRow[] = {-1, 0, 1, 0}, dCol[] = {0, 1, 0, -1};
        while (!q.empty()) {
            auto [cr, cc] = q.front(); q.pop();
            if (cr == r - 1 && cc == c - 1) return true;
            for (int d = 0; d < 4; d++) {
                int nr = cr + dRow[d], nc = cc + dCol[d];
                if (nr >= 0 && nr < r && nc >= 0 && nc < c && !vis[nr][nc]) {
                    if (abs(heights[nr][nc] - heights[cr][cc]) <= mid) {
                        vis[nr][nc] = 1;
                        q.push({nr, nc});
                    }
                }
            }
        }
        return false;
    }
public:
    int minimumEffortPath(vector<vector<int>>& heights) {
        int low = 0, high = 1e6, ans = 1e6;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canReach(mid, heights)) {
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
    boolean canReach(int mid, int[][] heights) {
        int r = heights.length, c = heights[0].size();
        int[][] vis = new int[r][c];
        queue<pair<int, int>> q;
        q.push({0, 0});
        vis[0][0] = 1;
        int dRow[] = {-1, 0, 1, 0}, dCol[] = {0, 1, 0, -1};
        while (!q.isEmpty()) {
            var [cr, cc] = q.peek(); q.pop();
            if (cr == r - 1 && cc == c - 1) return true;
            for (int d = 0; d < 4; d++) {
                int nr = cr + dRow[d], nc = cc + dCol[d];
                if (nr >= 0 && nr < r && nc >= 0 && nc < c && !vis[nr][nc]) {
                    if (Math.abs(heights[nr][nc] - heights[cr][cc]) <= mid) {
                        vis[nr][nc] = 1;
                        q.push({nr, nc});
                    }
                }
            }
        }
        return false;
    }

    int minimumEffortPath(int[][] heights) {
        int low = 0, high = 1e6, ans = 1e6;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (canReach(mid, heights)) {
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
- **Time Complexity**: $\mathcal{O}(R \times C \cdot \log(10^6))$ time.
- **Space Complexity**: $\mathcal{O}(R \times C)$ queue space.
- **Why it's still not optimal**: Both BS + BFS and Dijkstra are competitive, with Dijkstra adapting dynamically to graph topology.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Minimax Dijkstra)

### Idea
Minimax Dijkstra Priority Queue Relaxation in $\mathcal{O}(R \times C \log(R \times C))$ time and $\mathcal{O}(R \times C)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
#include <cmath>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minimumEffortPath(vector<vector<int>>& heights) {
        int n = heights.size();
        int m = heights[0].size();
        
        // Min-heap stores {effort, {row, col}}
        priority_queue<pair<int, pair<int, int>>,
                       vector<pair<int, pair<int, int>>>,
                       greater<pair<int, pair<int, int>>>> pq;
        
        vector<vector<int>> dist(n, vector<int>(m, 1e9));
        dist[0][0] = 0;
        pq.push({0, {0, 0}});
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        while (!pq.empty()) {
            auto top = pq.top();
            pq.pop();
            
            int diff = top.first;
            int r = top.second.first;
            int c = top.second.second;
            
            // Early exit: First time bottom-right cell is popped -> guaranteed minimum effort!
            if (r == n - 1 && c == m - 1) {
                return diff;
            }
            
            if (diff > dist[r][c]) continue;
            
            for (int i = 0; i < 4; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < m) {
                    // Maximum height jump along the path to (nr, nc)
                    int newEffort = max(diff, abs(heights[nr][nc] - heights[r][c]));
                    
                    if (newEffort < dist[nr][nc]) {
                        dist[nr][nc] = newEffort;
                        pq.push({newEffort, {nr, nc}});
                    }
                }
            }
        }
        
        return 0; // Fallback for 1x1 matrix
    }
};
```

### Java Code
```java
class Solution {

    int minimumEffortPath(int[][] heights) {
        int n = heights.length;
        int m = heights[0].size();
        
        // Min-heap stores {effort, {row, col}}
        priority_queue<pair<int, pair<int, int>>,
                       vector<pair<int, pair<int, int>>>,
                       greater<pair<int, pair<int, int>>>> pq;
        
        int[][] dist = new int[n][m];
        dist[0][0] = 0;
        pq.push({0, {0, 0}});
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        while (!pq.isEmpty()) {
            var top = pq.peek();
            pq.pop();
            
            int diff = top.first;
            int r = top.second.first;
            int c = top.second.second;
            
            // Early exit: First time bottom-right cell is popped . guaranteed minimum effort!
            if (r == n - 1 && c == m - 1) {
                return diff;
            }
            
            if (diff > dist[r][c]) continue;
            
            for (int i = 0; i < 4; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < m) {
                    // Maximum height jump along the path to (nr, nc)
                    int newEffort = Math.max(diff, Math.abs(heights[nr][nc] - heights[r][c]));
                    
                    if (newEffort < dist[nr][nc]) {
                        dist[nr][nc] = newEffort;
                        pq.push({newEffort, {nr, nc}});
                    }
                }
            }
        }
        
        return 0; // Fallback for 1x1 matrix
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R \times C \log(R \times C))$ time (where $V = R \times C$, each 4-directional edge relaxed once).
- **Space Complexity**: $\mathcal{O}(R \times C)$ priority queue and distance matrix memory.
- **Why this is optimal**: Min-heap guarantees optimal minimax path upon popping target without inspecting all matrix cells.

---

## 6. Dry Run

`heights = [[1,2,2],[3,8,2],[5,3,5]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist[0][0]=0 | pq: `[{0, (0,0)}]` |
| `Pop (0,0), diff=0` | Right (0,1): $\max(0, |2-1|)=1$; Down (1,0): $\max(0, |3-1|)=2$ | pq: `[{1, (0,1)}, {2, (1,0)}]` |
| `Pop (0,1), diff=1` | Right (0,2): $\max(1, |2-2|)=1$; Down (1,1): $\max(1, |8-2|)=6$ | pq: `[{1, (0,2)}, {2, (1,0)}, {6, (1,1)}]` |
| `Pop (0,2), diff=1` | Down (1,2): $\max(1, |2-2|)=1$ | pq: `[{1, (1,2)}, {2, (1,0)}, {6, (1,1)}]` |
| `Pop (1,2), diff=1` | Down (2,2): $\max(1, |5-2|)=2$ | pq: `[{2, (1,0)}, {2, (2,2)}, {6, (1,1)}]` |
| `Pop (2,2), diff=2` | Target cell reached! Effort = 2 | Minimum Effort = 2 ✅ (`[1,2,2] -> [2] -> [5]`) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $1 \times 1$ matrix `[[1]]` (returns 0).
- All cells identical height (returns 0).
- Single row or single column.

### Common Bugs to Avoid
- Summing edge differences `diff + abs(...)` instead of taking bottleneck maximum `max(diff, abs(...))`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Dijkstra's algorithm work with the max() relaxation operator?**  
  **A**: Dijkstra works for any path metric that is monotonically non-decreasing along paths. Since $\max(\text{pathEffort}, \text{newEdge}) \ge \text{pathEffort}$, the greedy property holds and the first time a cell is extracted from the min-heap, its effort is guaranteed to be minimal!

- **Q2: Can Disjoint Set Union (Kruskal's Algorithm) solve this problem?**  
  **A**: Yes! Treat all $4 \times R \times C$ grid edges as weighted edges $(u, v, |h_u - h_v|)$. Sort all edges by weight. Add edges one by one using DSU until cell $(0, 0)$ is connected to $(R-1, C-1)$. The weight of the connecting edge is the exact answer!

- **Q3: What are the real-world applications of Minimax Shortest Paths?**  
  **A**: Network routing for maximum bandwidth / minimum latency bottlenecks, road routing for vehicles with maximum height / weight bridge clearances, and mountain hiking trails.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Dijkstra`, `Binary Search`, `Matrix`, `LeetCode-1631`, `Medium`
- **Related problems to practice next**:
- **Swim in Rising Water**: Minimax water level.
- **Cheapest Flights Within K Stops**: Constrained shortest path.
