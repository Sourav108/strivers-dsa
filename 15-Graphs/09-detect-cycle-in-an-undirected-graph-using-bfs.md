# Detect Cycle in an Undirected Graph using BFS (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Detect Cycle in an Undirected Graph using BFS](https://takeuforward.org/data-structure/detect-cycle-in-an-undirected-graph-using-bfs/)
- **Difficulty**: Medium
- **Statement**: Given an undirected graph with $V$ vertices and $E$ edges represented by an adjacency list `adj`, determine whether the graph contains a cycle using Breadth First Search (BFS).

---

## 1. Problem, Restated

Detect cycle in undirected graph with BFS: queue stores `{node, parent}` pairs; if neighbor is visited and `neighbor != parent`, cycle exists!

- **Input**: Parameters specified ($V$ vertices, grid $N \times M$, or adjacency matrix).
- **Output**: Value / count / modified matrix / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

In an undirected graph, an edge connects $u$ and $v$ in both directions. When visiting $v$ from $u$, $v$ will see $u$ as a visited neighbor (which is normal parent back-tracking). But if $v$ sees a visited neighbor $w$ that is **NOT its parent** ($w \ne \text{parent}$), it means $w$ was visited from a DIFFERENT path $\implies$ a CYCLE exists! Store `{node, parent}` in BFS queue. Wrap in outer loop for disconnected components.

- **Underlying Pattern**: `BFS with Parent Tracking (`vis[neighbor] && neighbor != parent` $\implies$ Cycle)`.

---

## 3. Approach 1 — Naive / Matrix Search

### Idea
DFS cycle detection with recursion stack.

### C++17 Code
```cpp
// DFS alternative
```

### Java Code
```java
// Java equivalent
// DFS alternative
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
BFS Queue with `{node, parent}` Tracking in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
private:
    bool checkForCycleBFS(int src, const vector<vector<int>>& adj, vector<int>& vis) {
        vis[src] = 1;
        // Queue stores pair of {currentNode, parentNode}
        queue<pair<int, int>> q;
        q.push({src, -1});
        
        while (!q.empty()) {
            auto [node, parent] = q.front();
            q.pop();
            
            for (int neighbor : adj[node]) {
                if (!vis[neighbor]) {
                    vis[neighbor] = 1;
                    q.push({neighbor, node});
                }
                // If neighbor is visited and is NOT the parent who called this node -> CYCLE!
                else if (neighbor != parent) {
                    return true;
                }
            }
        }
        
        return false;
    }

public:
    bool isCycle(int V, vector<vector<int>>& adj) {
        vector<int> vis(V, 0);
        
        // Check for cycle across all disconnected components
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                if (checkForCycleBFS(i, adj, vis)) {
                    return true;
                }
            }
        }
        
        return false;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    boolean checkForCycleBFS(int src, int[][] adj, int[] vis) {
        vis[src] = 1;
        // Queue stores pair of {currentNode, parentNode}
        queue<pair<int, int>> q;
        q.push({src, -1});
        
        while (!q.isEmpty()) {
            var [node, parent] = q.peek();
            q.pop();
            
            for (int neighbor : adj[node]) {
                if (!vis[neighbor]) {
                    vis[neighbor] = 1;
                    q.push({neighbor, node});
                }
                // If neighbor is visited and is NOT the parent who called this node . CYCLE!
                else if (neighbor != parent) {
                    return true;
                }
            }
        }
        
        return false;
    }

    boolean isCycle(int V, int[][] adj) {
        int[] vis = new int[V];
        
        // Check for cycle across all disconnected components
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                if (checkForCycleBFS(i, adj, vis)) {
                    return true;
                }
            }
        }
        
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (all vertices and edges inspected at most once).
- **Space Complexity**: $\mathcal{O}(V)$ queue and visited array space.
- **Why this is optimal**: Queue-based level traversal detects cross-edge convergence without recursion.

---

## 6. Dry Run

Graph $V = 4$, Edges: `1-2, 2-3, 3-4, 4-1` (Cycle $1-2-3-4-1$)

| Step | Action / State Change | Result |
|---|---|---|
| `Start 1` | q: `[{1, -1}]`, vis[1]=1 | start |
| `Pop {1, -1}` | push {2, 1}, {4, 1} | q: `[{2, 1}, {4, 1}]` |
| `Pop {2, 1}` | neighbor 3 unvisited -> push {3, 2} | q: `[{4, 1}, {3, 2}]` |
| `Pop {4, 1}` | neighbor 3 is visited (vis[3]=1) and $3 \ne 1$ (not parent!) $\implies$ CYCLE DETECTED! | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Linear acyclic chain $1-2-3-4$ (returns `false`).
- Disconnected graph with a cycle in 2nd component.
- Single vertex $V = 1$.

### Common Bugs to Avoid
- Forgetting `neighbor != parent` condition (falsely flags every bidirectional edge as a cycle!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is parent tracking necessary in an undirected graph?**  
  **A**: Because every undirected edge $(u, v)$ exists in both adjacency lists (`adj[u]` contains $v$ and `adj[v]` contains $u$). When exploring from $v$, $u$ is already marked visited. We must distinguish between the trivial return edge to $u$ (parent) vs a genuine cycle!

- **Q2: Can DSU be used for cycle detection in undirected graphs?**  
  **A**: YES! For each edge $(u, v)$: if `find(u) == find(v)`, the two nodes are ALREADY connected $\implies$ adding edge $(u, v)$ creates a cycle in $\mathcal{O}(E \alpha(V))$ time!

- **Q3: Does this BFS cycle detection work for DIRECTED graphs?**  
  **A**: NO! In a directed graph, seeing a visited node does NOT necessarily imply a cycle (it could be a cross-edge). Directed graph cycle detection requires tracking the recursion stack or using Kahn's algorithm (topological sort).


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Cycle Detection`, `Medium`
- **Related problems to practice next**:
- **Detect Cycle in Undirected Graph DFS**: DFS counterpart.
- **Detect Cycle in Directed Graph**: Directed graph.
