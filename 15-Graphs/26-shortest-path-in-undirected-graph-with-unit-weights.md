# Shortest Path in Undirected Graph with Unit Weights (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Shortest Path in Undirected Graph with Unit Weights](https://takeuforward.org/data-structure/shortest-path-in-undirected-graph-with-unit-distance/)
- **Difficulty**: Medium
- **Statement**: You are given an Undirected Graph having unit weight (1) for each edge with $N$ vertices (0-indexed) and $M$ edges. Find the shortest path distance from a given source vertex `src` to all other vertices. If a vertex is unreachable, return -1 for that vertex.

---

## 1. Problem, Restated

Compute single-source shortest path in an unweighted graph using standard FIFO Queue BFS in $\mathcal{O}(V + 2E)$ time.

- **Input**: `int N, int M, int src`, `vector<vector<int>>& edges`.
- **Output**: `vector<int>` array of shortest distances from `src` to all nodes.
- **Constraints**: $1 \le N \le 10^5$, $0 \le M \le 2 \times 10^5$, $0 \le \text{src} < N$.

---

## 2. Intuition & Pattern

When all edge weights are equal (unit weight $= 1$), Dijkstra's priority queue is completely unnecessary! Standard FIFO Queue BFS naturally processes nodes in strictly non-decreasing order of edge distance (layer $0, 1, 2, \dots$). 1) Initialize `vector<int> dist(N, 1e9)` with `dist[src] = 0`. 2) Push `src` into `queue<int> q`. 3) Pop `curr = q.front()`. For each neighbor $v$ of `curr`: if `dist[curr] + 1 < dist[v]`, update `dist[v] = dist[curr] + 1` and push $v$ into queue. 4) Convert remaining unreachable `1e9` distances to `-1`. Runs in $\mathcal{O}(V + 2E)$ time.

- **Underlying Pattern**: `Single-Source BFS Radial Distance Relaxation`.

---

## 3. Approach 1 — Naive / Dijkstra Algorithm

### Idea
Dijkstra's Algorithm with `priority_queue` in $\mathcal{O}((V + E) \log V)$ time.

### C++17 Code
```cpp
// O((V + E) log V) Dijkstra
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((V + E) \log V)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ priority queue.
- **Why it's not good enough**: Logarithmic heap overhead is redundant when all edge weights are identical (1).

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS Algorithm below directly achieves optimal $\mathcal{O}(V + 2E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
FIFO Queue BFS in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    vector<int> shortestPath(vector<vector<int>>& edges, int N, int M, int src) {
        // 1. Build adjacency list for undirected graph
        vector<vector<int>> adj(N);
        for (int i = 0; i < M; i++) {
            int u = edges[i][0];
            int v = edges[i][1];
            adj[u].push_back(v);
            adj[v].push_back(u);
        }
        
        // 2. Initialize distance array with infinity (1e9)
        vector<int> dist(N, 1e9);
        dist[src] = 0;
        
        // 3. FIFO Queue for level-order radial exploration
        queue<int> q;
        q.push(src);
        
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            
            for (int neighbor : adj[node]) {
                // Relaxation step
                if (dist[node] + 1 < dist[neighbor]) {
                    dist[neighbor] = dist[node] + 1;
                    q.push(neighbor);
                }
            }
        }
        
        // 4. Mark unreachable nodes with -1
        for (int i = 0; i < N; i++) {
            if (dist[i] == 1e9) {
                dist[i] = -1;
            }
        }
        
        return dist;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (each node entered into queue at most once, each undirected edge relaxed twice).
- **Space Complexity**: $\mathcal{O}(V + 2E)$ adjacency list and distance array space.
- **Why this is optimal**: FIFO queue properties guarantee optimal shortest distance upon first discovery in unweighted graphs.

---

## 6. Dry Run

$N = 9$, $M = 10$, `edges = [[0,1],[0,3],[3,4],[4,5],[5,6],[1,2],[2,6],[6,7],[7,8],[6,8]]`, `src = 0`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist: `[0, inf, inf, inf, inf, inf, inf, inf, inf]` | q: `[0]` |
| `Pop 0` | dist[1]=1, dist[3]=1 -> pushes 1, 3 | q: `[1, 3]` |
| `Pop 1, 3` | from 1: dist[2]=2; from 3: dist[4]=2 -> pushes 2, 4 | q: `[2, 4]` |
| `Pop 2, 4` | from 2: dist[6]=3 (via path 0-1-2-6); from 4: dist[5]=3 | q: `[6, 5]` |
| `Pop 6` | dist[7]=4, dist[8]=4 | q: `[5, 7, 8]` |
| `Result` | `dist = [0, 1, 2, 1, 2, 3, 3, 4, 4]` | All Shortest Distances Computed ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Disconnected nodes (distance remains -1).
- Source node isolated with degree 0.
- $N = 1$.

### Common Bugs to Avoid
- Setting `dist[src] = 1` instead of `0`.
- Not checking `dist[node] + 1 < dist[neighbor]` (causes infinite queuing).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does BFS find the shortest path in unweighted graphs but fail on weighted graphs?**  
  **A**: Because BFS explores vertices in order of hop count. When all edges have weight 1, hop count is equal to total distance. When edge weights differ, a path with 2 hops could have total weight 10 while a path with 3 hops could have total weight 3!

- **Q2: How to adapt this for edge weights that are either 0 or 1 (0-1 BFS)?**  
  **A**: Use `std::deque<int>`. For 0-weight edges, push to the FRONT of deque (`push_front`). For 1-weight edges, push to the BACK (`push_back`). Runs in $\mathcal{O}(V + E)$ time without priority queues!

- **Q3: How to reconstruct the actual shortest path to any node?**  
  **A**: Maintain a `parent` array initialized to `-1`. When relaxing `dist[neighbor] = dist[node] + 1`, record `parent[neighbor] = node`. Backtrack from target to source to recover the path.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Shortest Path`, `Unweighted Graph`, `Medium`
- **Related problems to practice next**:
- **Shortest Path in DAG**: DAG shortest path.
- **Dijkstra's Algorithm**: General weighted shortest path.
