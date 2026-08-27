# Shortest Path in Directed Acyclic Graph (DAG) using Topo Sort (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Shortest Path in Directed Acyclic Graph (DAG) using Topo Sort](https://takeuforward.org/data-structure/shortest-path-in-directed-acyclic-graph-dag/)
- **Difficulty**: Medium
- **Statement**: Given a Directed Acyclic Graph (DAG) with $N$ vertices (0 to $N-1$) and $M$ weighted edges, find the shortest path from a given source vertex (typically 0) to all other vertices. If a vertex is unreachable, its distance should be -1.

---

## 1. Problem, Restated

Find single-source shortest path on a weighted DAG in linear $\mathcal{O}(V + E)$ time by relaxing edges in topological sort order.

- **Input**: `int N, int M`, `vector<vector<int>>& edges` (triplets `[u, v, wt]`).
- **Output**: `vector<int>` array of shortest distances from source (node 0) to all vertices.
- **Constraints**: $1 \le N \le 10^4$, $0 \le M \le \min(N(N-1)/2, 10^5)$, graph is a DAG.

---

## 2. Intuition & Pattern

In a general weighted graph, Dijkstra takes $\mathcal{O}((V + E) \log V)$. But in a **DAG**, we can achieve strictly linear $\mathcal{O}(V + E)$ time and handle **negative edge weights**! **Key Insight**: In topological order, when we reach vertex $u$, all possible paths leading into $u$ have ALREADY been processed and finalized! 1) Compute Topological Sort using DFS finish-time stack. 2) Initialize `dist(N, 1e9)` with `dist[src] = 0`. 3) Pop vertices from topological stack. If `dist[node] != 1e9`: relax all outgoing edges: if `dist[node] + wt < dist[v]`, update `dist[v] = dist[node] + wt`. 4) Unreachable nodes (`1e9`) become `-1`. Runs in $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `Topological Sort Directed Edge Relaxation (DAG Shortest Path)`.

---

## 3. Approach 1 — Naive / Dijkstra's Algorithm

### Idea
Standard Dijkstra Algorithm with Priority Queue in $\mathcal{O}((V + E) \log V)$ time (fails if negative edges exist).

### C++17 Code
```cpp
// O((V + E) log V) Dijkstra alternative
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((V + E) \log V)$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's not good enough**: Logarithmic priority queue overhead is slower than linear topological relaxation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DAG Topological Sort Edge Relaxation algorithm below directly achieves optimal $\mathcal{O}(V + E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Topological Sort Edge Relaxation in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;

class Solution {
private:
    void topoDFS(int node, const vector<vector<pair<int, int>>>& adj, vector<int>& vis, stack<int>& st) {
        vis[node] = 1;
        for (const auto& edge : adj[node]) {
            int v = edge.first;
            if (!vis[v]) {
                topoDFS(v, adj, vis, st);
            }
        }
        st.push(node);
    }

public:
    vector<int> shortestPath(int N, int M, vector<vector<int>>& edges) {
        // 1. Build weighted directed adjacency list
        vector<vector<pair<int, int>>> adj(N);
        for (int i = 0; i < M; i++) {
            int u = edges[i][0];
            int v = edges[i][1];
            int wt = edges[i][2];
            adj[u].push_back({v, wt});
        }
        
        // 2. Compute Topological Sort of the DAG
        vector<int> vis(N, 0);
        stack<int> st;
        for (int i = 0; i < N; i++) {
            if (!vis[i]) {
                topoDFS(i, adj, vis, st);
            }
        }
        
        // 3. Relax edges in Topological Order starting from source (0)
        vector<int> dist(N, 1e9);
        dist[0] = 0; // Source is node 0
        
        while (!st.empty()) {
            int node = st.top();
            st.pop();
            
            // Only relax outgoing edges if node itself is reachable
            if (dist[node] != 1e9) {
                for (const auto& edge : adj[node]) {
                    int v = edge.first;
                    int wt = edge.second;
                    
                    if (dist[node] + wt < dist[v]) {
                        dist[v] = dist[node] + wt;
                    }
                }
            }
        }
        
        // 4. Mark unreachable nodes as -1
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
- **Time Complexity**: $\mathcal{O}(V + E)$ time (Topological sort takes $\mathcal{O}(V + E)$, each vertex and outgoing edge relaxed exactly once).
- **Space Complexity**: $\mathcal{O}(V + E)$ adjacency list, visited array, stack, and distance vector space.
- **Why this is optimal**: DAG topological ordering eliminates the need for heap re-ordering and works even with negative edge weights!

---

## 6. Dry Run

$N = 6, M = 7$, `edges = [[0,1,2],[0,4,1],[4,5,4],[4,2,2],[1,2,3],[2,3,6],[5,3,1]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Topo Stack` | Stack order from top: `0, 4, 5, 1, 2, 3` | Topo ready |
| `Pop 0 (dist=0)` | Relax: $0 \to 1 (wt 2) \implies dist[1]=2$; $0 \to 4 (wt 1) \implies dist[4]=1$ | dist: `[0, 2, inf, inf, 1, inf]` |
| `Pop 4 (dist=1)` | Relax: $4 \to 5 (wt 4) \implies dist[5]=5$; $4 \to 2 (wt 2) \implies dist[2]=3$ | dist: `[0, 2, 3, inf, 1, 5]` |
| `Pop 5 (dist=5)` | Relax: $5 \to 3 (wt 1) \implies dist[3]=6$ | dist: `[0, 2, 3, 6, 1, 5]` |
| `Pop 1 (dist=2)` | Relax: $1 \to 2 (wt 3): 2+3=5 > 3$ (no change) | dist unchanged |
| `Pop 2 (dist=3)` | Relax: $2 \to 3 (wt 6): 3+6=9 > 6$ (no change) | dist unchanged |
| `Result` | `dist = [0, 2, 3, 6, 1, 5]` | Shortest Distances Found ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Negative edge weights (algorithm handles seamlessly!).
- Unreachable isolated components (remain -1).

### Common Bugs to Avoid
- Relaxing outgoing edges when `dist[node] == 1e9` (leads to integer overflow $10^9 + wt$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Topological Sort allow linear O(V + E) shortest paths even with NEGATIVE edge weights?**  
  **A**: Because a DAG has NO cycles! In topological order, a node $u$ is processed only after all possible ways to reach $u$ have been evaluated. Since no back-edges exist, `dist[u]` is mathematically guaranteed to be final upon popping!

- **Q2: How to solve LONGEST path in a DAG using this technique?**  
  **A**: Initialize `dist(N, -1e9)` with `dist[src] = 0`. In relaxation, simply change condition to `if (dist[node] + wt > dist[v]) dist[v] = dist[node] + wt`. Finding longest paths in general graphs is NP-hard, but in a DAG it is solvable in linear $\mathcal{O}(V + E)$ time (Critical Path Method)!

- **Q3: Why does Dijkstra fail on negative edges whereas DAG Topo Sort succeeds?**  
  **A**: Dijkstra greedily marks nodes visited assuming edge weights are non-negative. DAG Topo Sort relies on topological dependency order, not greedy distance values, so negative weights never invalidate topological invariants.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DAG`, `Topological Sort`, `Shortest Path`, `Dynamic Programming`, `Medium`
- **Related problems to practice next**:
- **Shortest Path Unit Weights**: Unweighted BFS.
- **Dijkstra's Algorithm**: General weighted shortest path.
