# Articulation Point in Graph (Tarjan's Algorithm for Cut Vertices) (Step 15.6 — Other Graph Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Articulation Point in Graph (Tarjan's Algorithm for Cut Vertices)](https://takeuforward.org/data-structure/articulation-point-in-graph-g-56/)
- **Difficulty**: Hard
- **Statement**: Given an undirected connected graph with $V$ vertices (0-indexed) and an adjacency list `adj`, find all Articulation Points (Cut Vertices) whose removal increases the number of connected components. Return the sorted list of articulation points, or `{-1}` if none exist.

---

## 1. Problem, Restated

Find all vertices $u$ whose removal disconnects the graph using Tarjan's low-link DFS with separate root and non-root articulation invariants in $\mathcal{O}(V + 2E)$ time.

- **Input**: `int V`, `vector<int> adj[]`.
- **Output**: `vector<int>` sorted list of articulation points (or `-1`).
- **Constraints**: $1 \le V \le 10^5$, $1 \le E \le 10^5$.

---

## 2. Intuition & Pattern

A vertex $u$ is an **Articulation Point** if removing $u$ separates its children from the rest of the graph: 
**1. Non-Root Vertex ($parent \ne -1$)**: If $u$ has a DFS child $v$ such that `low[v] >= tin[u]`, then subtree $v$ cannot reach any strict ancestor of $u$ without going through $u$. Removing $u$ isolates subtree $v$! (Note: `>=` because reaching $u$ itself is not enough once $u$ is deleted!). 
**2. Root Vertex ($parent == -1$)**: The root is an articulation point if and only if it has **more than 1 independent DFS children** (`child > 1`). 
**Algorithm**: 1) Maintain `tin`, `low`, and a boolean array `isArticulation(V, 0)`. 2) In DFS, for unvisited neighbor $v$: `child++`, `dfs(v, u)`, `low[u] = min(low[u], low[v])`. If `low[v] >= tin[u] && parent != -1`, mark `isArticulation[u] = 1`. 3) After loop: if `parent == -1 && child > 1`, mark `isArticulation[u] = 1`. 4) Collect all marked indices. Runs in $\mathcal{O}(V + 2E)$ time.

- **Underlying Pattern**: `Tarjan's Low-Link DFS Traversal (Cut Vertex Invariant)`.

---

## 3. Approach 1 — Naive / Vertex-Removal Graph Traversal

### Idea
For every vertex $i$: temporarily remove $i$ and all incident edges, run full BFS/DFS to count connected components, and restore $i$ in $\mathcal{O}(V \times (V + E))$ time.

### C++17 Code
```cpp
// O(V * (V + E)) vertex removal brute force
```

### Java Code
```java
// Java equivalent
// O(V * (V + E)) vertex removal brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V \times (V + E))$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's not good enough**: Quadratic graph scans for every single vertex removal.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Tarjan's Articulation Point Algorithm below directly achieves optimal $\mathcal{O}(V + 2E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Tarjan's Articulation Point Algorithm)

### Idea
Tarjan's Single-Pass Low-Link DFS with Articulation Marker in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V + 2E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    int timer = 1;
    
    void dfs(int node, int parent, vector<int> adj[], 
             vector<int>& vis, vector<int>& tin, vector<int>& low, 
             vector<int>& mark) {
        
        vis[node] = 1;
        tin[node] = low[node] = timer++;
        int childCount = 0;
        
        for (int neighbor : adj[node]) {
            if (neighbor == parent) continue;
            
            if (!vis[neighbor]) {
                childCount++;
                dfs(neighbor, node, adj, vis, tin, low, mark);
                
                // Propagate lowest reachable time from child subtree
                low[node] = min(low[node], low[neighbor]);
                
                // Case 1: Non-root vertex condition (>= comparison)
                if (low[neighbor] >= tin[node] && parent != -1) {
                    mark[node] = 1;
                }
            } else {
                // Back-edge to an ancestor
                low[node] = min(low[node], tin[neighbor]);
            }
        }
        
        // Case 2: Root vertex condition (more than 1 independent DFS branch)
        if (parent == -1 && childCount > 1) {
            mark[node] = 1;
        }
    }
    
public:
    vector<int> articulationPoints(int V, vector<int> adj[]) {
        vector<int> vis(V, 0);
        vector<int> tin(V, 0);
        vector<int> low(V, 0);
        vector<int> mark(V, 0); // Marks articulation points uniquely
        
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                dfs(i, -1, adj, vis, tin, low, mark);
            }
        }
        
        vector<int> ans;
        for (int i = 0; i < V; i++) {
            if (mark[i] == 1) {
                ans.push_back(i);
            }
        }
        
        if (ans.empty()) {
            return {-1};
        }
        
        return ans;
    }
};
```

### Java Code
```java
class Solution {

    int timer = 1;
    
    void dfs(int node, int parent, int[] adj[], 
             int[] vis, int[] tin, int[] low, 
             int[] mark) {
        
        vis[node] = 1;
        tin[node] = low[node] = timer++;
        int childCount = 0;
        
        for (int neighbor : adj[node]) {
            if (neighbor == parent) continue;
            
            if (!vis[neighbor]) {
                childCount++;
                dfs(neighbor, node, adj, vis, tin, low, mark);
                
                // Propagate lowest reachable time from child subtree
                low[node] = Math.min(low[node], low[neighbor]);
                
                // Case 1: Non-root vertex condition (>= comparison)
                if (low[neighbor] >= tin[node] && parent != -1) {
                    mark[node] = 1;
                }
            } else {
                // Back-edge to an ancestor
                low[node] = Math.min(low[node], tin[neighbor]);
            }
        }
        
        // Case 2: Root vertex condition (more than 1 independent DFS branch)
        if (parent == -1 && childCount > 1) {
            mark[node] = 1;
        }
    }

    int[] articulationPoints(int V, int[] adj[]) {
        int[] vis = new int[V];
        int[] tin = new int[V];
        int[] low = new int[V];
        int[] mark = new int[V]; // Marks articulation points uniquely
        
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                dfs(i, -1, adj, vis, tin, low, mark);
            }
        }
        
        int[] ans;
        for (int i = 0; i < V; i++) {
            if (mark[i] == 1) {
                ans.add(i);
            }
        }
        
        if (ans.isEmpty()) {
            return {-1};
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (single DFS pass through all vertices and edges).
- **Space Complexity**: $\mathcal{O}(V + 2E)$ adjacency list, `vis`, `tin`, `low`, `mark` arrays, and recursion stack memory.
- **Why this is optimal**: Tarjan's algorithm identifies all cut vertices in a single linear DFS sweep without modifying graph structure.

---

## 6. Dry Run

Graph $V = 5$, `adj = 0: [1,2,3], 1: [0,2], 2: [0,1], 3: [0,4], 4: [3]`

| Step | Action / State Change | Result |
|---|---|---|
| `DFS 0 (root)` | tin[0]=1, low[0]=1. Child 1: dfs(1)->vis[2]=1. Child 3: dfs(3)->dfs(4) | childCount = 2 (nodes 1 and 3) |
| `Subtree 4` | tin[4]=5, low[4]=5. low[4] (5) $\ge$ tin[3] (4) $\implies$ Node 3 marked! | mark[3] = 1 |
| `Subtree 3` | low[3] = $\min(4, 5) = 4$. low[3] (4) $\ge$ tin[0] (1) | Node 0 candidate |
| `Root 0 Check` | parent == -1 and childCount = 2 > 1 $\implies$ Root 0 marked! | mark[0] = 1 |
| `Result` | Articulation Points: `{0, 3}` | Ans = `[0, 3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No articulation points (returns `{-1}`).
- Graph is a linear chain $0-1-2-3-4$ (all internal vertices $\{1, 2, 3\}$ are articulation points).
- Root with only 1 child.

### Common Bugs to Avoid
- Using `>` instead of `>=` for non-root condition (bridges use `>`, articulation points MUST use `>=`).
- Adding a node directly to `ans` inside DFS (leads to duplicate entries if a node satisfies the condition for multiple children; must use `mark` array!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Articulation Point use 'low[neighbor] >= tin[node]' while Bridges use 'low[neighbor] > tin[node]'?**  
  **A**: For **Bridges**, if `low[v] == tin[u]`, $v$ can reach $u$ via an alternative path without edge $(u, v)$, so the edge is NOT a bridge. But for **Articulation Points**, the vertex $u$ ITSELF is being deleted! If $v$'s best back-edge only reaches $u$ (`low[v] == tin[u]`), once $u$ is removed, $v$ cannot reach any higher ancestor! Hence $u$ IS an articulation point!

- **Q2: Why does the root vertex have a special condition 'childCount > 1'?**  
  **A**: Since root has `parent == -1`, it has no ancestors above it. Any DFS tree root has `tin[root] = 1`, so `low[v] >= tin[root]` is ALWAYS trivially true for all children! But root only disconnects the graph if it connects TWO OR MORE independent DFS branches (`childCount > 1`).

- **Q3: What is Biconnected Component (BCC) decomposition?**  
  **A**: A maximal biconnected subgraph contains no articulation points (every pair of vertices lies on a simple cycle). Articulation points are the exact pivot vertices connecting different Biconnected Components.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Tarjan's Algorithm`, `DFS`, `Articulation Point`, `Cut Vertex`, `Hard`
- **Related problems to practice next**:
- **Bridges in Graph**: Bridge edges via Tarjan.
- **Strongly Connected Components**: Kosaraju's Algorithm.
