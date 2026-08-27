# Print Shortest Path in Weighted Undirected Graph (Dijkstra Parent array) (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Print Shortest Path in Weighted Undirected Graph (Dijkstra Parent array)](https://takeuforward.org/data-structure/g-35-print-shortest-path-dijkstras-algorithm/)
- **Difficulty**: Medium
- **Statement**: You are given a weighted undirected graph having $n$ vertices numbered from 1 to $n$ and $m$ edges along with their weights. Find the shortest path from vertex 1 to vertex $n$. Return a list of integers containing the path sequence starting from 1 and ending at $n$ (along with total weight prepended in recent GFG versions). If no path exists, return `{-1}`.

---

## 1. Problem, Restated

Run Dijkstra's algorithm from source 1 while recording predecessor nodes in `parent[adjNode] = node`; backtrack from node $n$ to 1 to reconstruct the shortest path in $\mathcal{O}((V + E) \log V)$ time.

- **Input**: `int n, int m`, `vector<vector<int>>& edges` (triplets `[u, v, wt]`).
- **Output**: `vector<int>` sequence of nodes representing the shortest path from 1 to $n$ (or `-1` if unreachable).
- **Constraints**: $2 \le n \le 10^5$, $0 \le m \le 2 \times 10^5$, $1 \le \text{weight} \le 10^6$.

---

## 2. Intuition & Pattern

Tracking optimal distances alone is insufficient to reconstruct the sequence of vertices. 1) Maintain `vector<int> parent(n + 1)` initialized such that `parent[i] = i`. 2) Run standard Dijkstra with min-heap from source 1. Whenever an edge $u \to v$ relaxes distance (`dist[u] + wt < dist[v]`), update `dist[v] = dist[u] + wt` AND record `parent[v] = u`. 3) If `dist[n] == 1e9`, node $n$ is unreachable $\implies$ return `{-1}`. 4) Backtrack: start at `curr = n`, repeatedly push `curr` and jump `curr = parent[curr]` until `curr == parent[curr]` (source 1 reached). Reverse the path vector! Runs in $\mathcal{O}((V + E) \log V)$ time.

- **Underlying Pattern**: `Dijkstra Min-Heap Shortest Path with Predecessor Parent Pointer Backtracking`.

---

## 3. Approach 1 — Naive / All-Paths DFS Backtracking

### Idea
DFS backtracking all possible simple paths from 1 to $n$ in $\mathcal{O}(V!)$ time.

### C++17 Code
```cpp
// O(V!) DFS all paths
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V!)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ stack.
- **Why it's not good enough**: Factorial search time across all paths.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Dijkstra with Parent Backtracking below directly achieves optimal $\mathcal{O}((V + E) \log V)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Dijkstra with Parent Array Backtracking in $\mathcal{O}((V + E) \log V)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<int> shortestPath(int n, int m, vector<vector<int>>& edges) {
        // 1. 1-indexed adjacency list: {neighbor, weight}
        vector<vector<pair<int, int>>> adj(n + 1);
        for (int i = 0; i < m; i++) {
            int u = edges[i][0];
            int v = edges[i][1];
            int wt = edges[i][2];
            adj[u].push_back({v, wt});
            adj[v].push_back({u, wt});
        }
        
        // Min-heap stores {distance, node}
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
        
        vector<int> dist(n + 1, 1e9);
        vector<int> parent(n + 1);
        for (int i = 1; i <= n; i++) parent[i] = i;
        
        dist[1] = 0;
        pq.push({0, 1});
        
        // 2. Dijkstra's Algorithm
        while (!pq.empty()) {
            auto [d, node] = pq.top();
            pq.pop();
            
            if (d > dist[node]) continue;
            if (node == n) break; // Reached destination with optimal distance
            
            for (const auto& edge : adj[node]) {
                int adjNode = edge.first;
                int weight = edge.second;
                
                if (d + weight < dist[adjNode]) {
                    dist[adjNode] = d + weight;
                    pq.push({dist[adjNode], adjNode});
                    parent[adjNode] = node; // Record optimal predecessor
                }
            }
        }
        
        // 3. Unreachable target check
        if (dist[n] == 1e9) return {-1};
        
        // 4. Backtrack from target n to source 1
        vector<int> path;
        int node = n;
        while (parent[node] != node) {
            path.push_back(node);
            node = parent[node];
        }
        path.push_back(1); // Push source
        
        reverse(path.begin(), path.end());
        
        // Prepend total weight (for standard GFG format with weight)
        // vector<int> result = {dist[n]};
        // result.insert(result.end(), path.begin(), path.end());
        // return result;
        
        return path;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((V + E) \log V)$ time (Dijkstra) + $\mathcal{O}(V)$ path reconstruction.
- **Space Complexity**: $\mathcal{O}(V + E)$ adjacency list, distance, parent array, and priority queue space.
- **Why this is optimal**: Parent pointers capture the exact shortest-path DAG tree in linear extra space.

---

## 6. Dry Run

$n = 5, m = 6$, `edges = [[1,2,2],[2,5,5],[2,3,4],[1,4,1],[4,3,3],[3,5,1]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist[1]=0, parent: `[1,2,3,4,5]` | pq: `[{0, 1}]` |
| `Pop {0, 1}` | dist[2]=2 (parent[2]=1), dist[4]=1 (parent[4]=1) | pq: `[{1, 4}, {2, 2}]` |
| `Pop {1, 4}` | dist[3]=1+3=4 (parent[3]=4) | pq: `[{2, 2}, {4, 3}]` |
| `Pop {2, 2}` | dist[3]=2+4=6 > 4 (ignore), dist[5]=2+5=7 (parent[5]=2) | pq: `[{4, 3}, {7, 5}]` |
| `Pop {4, 3}` | dist[5]=4+1=5 < 7 $\implies$ dist[5]=5 (parent[5]=3!) | pq: `[{5, 5}, {7, 5}]` |
| `Pop {5, 5}` | Target reached with dist 5! | Backtrack from 5 |
| `Backtrack` | $5 \to parent[5]=3 \to parent[3]=4 \to parent[4]=1$. Reverse: `[1, 4, 3, 5]` | Path: `1 -> 4 -> 3 -> 5` (Weight 5) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target $n$ unreachable from 1 (returns `{-1}`).
- Graph consists of a single node $n = 1$ (returns `[1]`).

### Common Bugs to Avoid
- Setting `parent[i] = -1` without handling source termination properly (causes infinite loop or out-of-bounds index).
- Not checking `dist[n] == 1e9` before backtracking.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does the parent array work for path reconstruction in graphs?**  
  **A**: Whenever a shorter path to neighbor $v$ is discovered through vertex $u$, we assign `parent[v] = u`. This builds a Shortest Path Tree (SPT) where tracing `parent` backward from target $n$ yields the exact unique optimal predecessor sequence!

- **Q2: How to handle printing ALL shortest paths if multiple exist?**  
  **A**: Instead of storing a single `int parent`, store `vector<int> parent[V]`. When `d + wt == dist[v]`, push $u$ into `parent[v]`. Reconstruct all paths using backtracking DFS (similar to Word Ladder II).

- **Q3: Can this technique be used with Bellman-Ford or Floyd-Warshall?**  
  **A**: Yes! In Bellman-Ford, maintain `parent[v] = u` on relaxation. In Floyd-Warshall, maintain `next[i][j]` tracking intermediate pivot hops.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Dijkstra`, `Shortest Path`, `Backtracking`, `Medium`
- **Related problems to practice next**:
- **Dijkstra Algorithm**: Base distance algorithm.
- **Cheapest Flights Within K Stops**: Constrained shortest path.
