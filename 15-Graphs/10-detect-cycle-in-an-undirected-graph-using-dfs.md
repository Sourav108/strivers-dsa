# Detect Cycle in an Undirected Graph using DFS (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Detect Cycle in an Undirected Graph using DFS](https://takeuforward.org/data-structure/detect-cycle-in-an-undirected-graph-using-dfs/)
- **Difficulty**: Medium
- **Statement**: Given an undirected graph with $V$ vertices and $E$ edges represented by an adjacency list `adj`, determine whether the graph contains a cycle using Depth First Search (DFS).

---

## 1. Problem, Restated

Recursive DFS cycle detection: pass `parent` parameter; if an adjacent neighbor is already visited and `neighbor != parent`, cycle exists.

- **Input**: Parameters specified ($V$ vertices, grid $N \times M$, or adjacency matrix).
- **Output**: Value / count / modified matrix / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Call `dfs(node, parent)`. Mark `vis[node] = 1`. For each neighbor $v$ of `node`: 1) If `!vis[v]`: recursively call `dfs(v, node)`. If recursion returns `true`, propagate `true` upwards immediately! 2) Else if `v != parent`: $v$ was visited earlier on this path $\implies$ Back edge exists $\implies$ return `true`. Wrap in outer loop for disconnected graphs.

- **Underlying Pattern**: `Recursive DFS with Parent Parameter (`vis[neighbor] && neighbor != parent` $\implies$ Back Edge Cycle)`.

---

## 3. Approach 1 — Naive / Matrix Search

### Idea
BFS queue-based cycle detection with parent pairs.

### C++17 Code
```cpp
// BFS alternative
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$.
- **Space Complexity**: $\mathcal{O}(V)$.
- **Why it's not good enough**: BFS and DFS achieve identical bounds.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS/DFS / DSU algorithm below directly achieves optimal $\mathcal{O}(V + E)$ or $\mathcal{O}(N \times M)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive DFS with Parent Parameter in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    bool dfs(int node, int parent, const vector<vector<int>>& adj, vector<int>& vis) {
        vis[node] = 1;
        
        for (int neighbor : adj[node]) {
            if (!vis[neighbor]) {
                if (dfs(neighbor, node, adj, vis)) {
                    return true; // Cycle found down the branch -> propagate true
                }
            }
            // Neighbor is already visited and is NOT the direct parent -> CYCLE!
            else if (neighbor != parent) {
                return true;
            }
        }
        
        return false;
    }

public:
    bool isCycle(int V, vector<vector<int>>& adj) {
        vector<int> vis(V, 0);
        
        // Multi-component loop for disconnected graphs
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                if (dfs(i, -1, adj, vis)) {
                    return true;
                }
            }
        }
        
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (each node visited once, each edge checked twice).
- **Space Complexity**: $\mathcal{O}(V)$ recursion call stack and visited array space.
- **Why this is optimal**: Clean recursive back-edge detection with immediate boolean short-circuiting.

---

## 6. Dry Run

Graph $V = 3$, Edges: `0-1, 1-2, 2-0` (Triangle Cycle)

| Step | Action / State Change | Result |
|---|---|---|
| `Call dfs(0, -1)` | vis[0]=1 -> neighbor 1 unvisited -> call dfs(1, 0) | dfs(1, 0) |
| `Call dfs(1, 0)` | vis[1]=1 -> neighbor 0 is parent; neighbor 2 unvisited -> call dfs(2, 1) | dfs(2, 1) |
| `Call dfs(2, 1)` | vis[2]=1 -> neighbor 1 is parent; neighbor 0 is visited and $0 \ne 1$ $\implies$ CYCLE! | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Self loops $u-u$ (cycle).
- Graph with no cycles (Tree).
- Disconnected forest.

### Common Bugs to Avoid
- Not returning immediately when `dfs(neighbor, node, ...)` returns `true` (overwrites true with false from sibling branches).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the formal graph theory definition of a Back Edge in DFS?**  
  **A**: A **Back Edge** is an edge $(u, v)$ connecting vertex $u$ to an ancestor $v$ in the DFS tree ($v \ne \text{parent}(u)$). In an undirected graph, a graph has a cycle IF AND ONLY IF its DFS traversal contains at least one back edge!

- **Q2: Why is DFS generally faster in practice than BFS for cycle detection?**  
  **A**: Because DFS dives deeply down a single path, often encountering the back edge of a cycle much earlier than BFS (which must explore all shallow layers first).

- **Q3: How to return the actual cycle path nodes?**  
  **A**: Maintain a `parent` array. When cycle edge $(u, v)$ is found, backtrack from $u$ up to $v$ using parent pointers to extract all vertices forming the cycle loop.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `Cycle Detection`, `Recursion`, `Medium`
- **Related problems to practice next**:
- **Detect Cycle in Undirected Graph BFS**: BFS counterpart.
- **Detect Cycle in Directed Graph**: Directed graph.
