# Bridges in Graph (Tarjan's Algorithm / Critical Connections in a Network) (Step 15.6 — Other Graph Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Bridges in Graph (Tarjan's Algorithm / Critical Connections in a Network)](https://takeuforward.org/data-structure/bridges-in-graph-using-tarjans-algorithm-g-55/)
- **Difficulty**: Hard
- **Statement**: There are $n$ servers connected by undirected server-to-server `connections` where `connections[i] = [u, v]`. A critical connection (bridge) is an edge that, when removed, increases the number of connected components in the network. Return all critical connections in the network in any order.

---

## 1. Problem, Restated

Find all bridge edges $(u, v)$ in an undirected connected graph in a single DFS pass using Tarjan's insertion timer `tin` and lowest reachable ancestor time `low` in $\mathcal{O}(V + 2E)$ time.

- **Input**: `int n`, `vector<vector<int>>& connections`.
- **Output**: `vector<vector<int>>` list of all critical bridge edges.
- **Constraints**: $2 \le n \le 10^5$, $n - 1 \le \text{connections.length} \le 10^5$, no duplicate edges.

---

## 2. Intuition & Pattern

An undirected edge $(u, v)$ is a **Bridge** if and only if there is NO alternative back-edge path connecting the subtree rooted at $v$ back to $u$ or any ancestor of $u$! 
**Tarjan's Low-Link Formulation**: 
1) Maintain global `timer = 1`. 
2) `tin[node]`: Time of first discovery (entry) during DFS. 
3) `low[node]`: Lowest `tin` reachable from `node` using at most ONE back-edge. 
4) In DFS traversal from $u$ to neighbor $v$ (ignoring parent `p`): 
   - **Case 1: $v$ is not visited**: 
     - Recursively run `dfs(v, u)`. 
     - On return, update $u$'s lowest reach: `low[u] = min(low[u], low[v])`. 
     - **Bridge Condition**: `if (low[v] > tin[u])`, then $v$ has NO back-edge reaching $u$ or above $\implies$ edge $(u, v)$ is a **Bridge**! 
   - **Case 2: $v$ is already visited ($v \ne p$)**: 
     - Back-edge discovered! Update `low[u] = min(low[u], tin[v])` (or `low[v]`). Runs in $\mathcal{O}(V + 2E)$ time.

- **Underlying Pattern**: `Tarjan's Bridge-Finding Algorithm (DFS Tree with Time of Entry & Low-Link Values)`.

---

## 3. Approach 1 — Naive / Edge-Removal Graph Traversal

### Idea
For every edge $e \in E$: temporarily remove $e$, run full BFS/DFS to check if the graph becomes disconnected, and restore $e$ in $\mathcal{O}(E \times (V + E))$ time.

### C++17 Code
```cpp
// O(E * (V + E)) edge removal brute force
```

### Java Code
```java
// Java equivalent
// O(E * (V + E)) edge removal brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E \times (V + E))$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's not good enough**: Running full graph traversals for every individual edge is quadratic.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Tarjan's Low-Link Bridge Algorithm below directly achieves optimal $\mathcal{O}(V + 2E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Tarjan's Bridge Algorithm)

### Idea
Tarjan's Single-Pass DFS with Time-of-Entry & Low-Link Arrays in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V + 2E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    int timer = 1;
    
    void dfs(int node, int parent, vector<vector<int>>& adj, 
             vector<int>& vis, vector<int>& tin, vector<int>& low, 
             vector<vector<int>>& bridges) {
        
        vis[node] = 1;
        tin[node] = low[node] = timer++;
        
        for (int neighbor : adj[node]) {
            // Ignore the direct parent edge in undirected graph
            if (neighbor == parent) continue;
            
            if (!vis[neighbor]) {
                // Forward tree edge
                dfs(neighbor, node, adj, vis, tin, low, bridges);
                
                // Propagate low-link value back to current node
                low[node] = min(low[node], low[neighbor]);
                
                // Bridge invariant: neighbor cannot reach node or any of its ancestors
                if (low[neighbor] > tin[node]) {
                    bridges.push_back({node, neighbor});
                }
            } else {
                // Back-edge to an already visited ancestor
                low[node] = min(low[node], tin[neighbor]);
            }
        }
    }
    
public:
    vector<vector<int>> criticalConnections(int n, vector<vector<int>>& connections) {
        // 1. Build undirected adjacency list
        vector<vector<int>> adj(n);
        for (const auto& edge : connections) {
            adj[edge[0]].push_back(edge[1]);
            adj[edge[1]].push_back(edge[0]);
        }
        
        vector<int> vis(n, 0);
        vector<int> tin(n, 0);
        vector<int> low(n, 0);
        vector<vector<int>> bridges;
        
        // 2. Perform DFS traversal (handles disconnected graph components)
        for (int i = 0; i < n; i++) {
            if (!vis[i]) {
                dfs(i, -1, adj, vis, tin, low, bridges);
            }
        }
        
        return bridges;
    }
};
```

### Java Code
```java
class Solution {

    int timer = 1;
    
    void dfs(int node, int parent, int[][] adj, 
             int[] vis, int[] tin, int[] low, 
             int[][] bridges) {
        
        vis[node] = 1;
        tin[node] = low[node] = timer++;
        
        for (int neighbor : adj[node]) {
            // Ignore the direct parent edge in undirected graph
            if (neighbor == parent) continue;
            
            if (!vis[neighbor]) {
                // Forward tree edge
                dfs(neighbor, node, adj, vis, tin, low, bridges);
                
                // Propagate low-link value back to current node
                low[node] = Math.min(low[node], low[neighbor]);
                
                // Bridge invariant: neighbor cannot reach node or any of its ancestors
                if (low[neighbor] > tin[node]) {
                    bridges.add({node, neighbor});
                }
            } else {
                // Back-edge to an already visited ancestor
                low[node] = Math.min(low[node], tin[neighbor]);
            }
        }
    }

    int[][] criticalConnections(int n, int[][] connections) {
        // 1. Build undirected adjacency list
        int[][] adj(n);
        for (var edge : connections) {
            adj[edge[0]].add(edge[1]);
            adj[edge[1]].add(edge[0]);
        }
        
        int[] vis = new int[n];
        int[] tin = new int[n];
        int[] low = new int[n];
        int[][] bridges;
        
        // 2. Perform DFS traversal (handles disconnected graph components)
        for (int i = 0; i < n; i++) {
            if (!vis[i]) {
                dfs(i, -1, adj, vis, tin, low, bridges);
            }
        }
        
        return bridges;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (single depth-first search visiting every vertex and traversing each undirected edge twice).
- **Space Complexity**: $\mathcal{O}(V + 2E)$ adjacency list, `vis`, `tin`, `low` arrays, and recursion stack memory.
- **Why this is optimal**: Tarjan's algorithm computes bridges for the entire graph in a single linear DFS sweep without modifying edges.

---

## 6. Dry Run

$n = 4, \text{connections} = [[0,1],[1,2],[2,0],[1,3]]$

| Step | Action / State Change | Result |
|---|---|---|
| `DFS 0 (parent -1)` | tin[0]=1, low[0]=1 | DFS to 1 |
| `DFS 1 (parent 0)` | tin[1]=2, low[1]=2 | DFS to 2 |
| `DFS 2 (parent 1)` | tin[2]=3, low[2]=3. Neighbor 0 visited! Back-edge $\implies low[2] = \min(3, tin[0]=1) = 1$ | low[2]=1 |
| `Return to 1` | low[1] = $\min(2, low[2]=1) = 1$. low[2] (1) $\ngtr$ tin[1] (2) $\implies (1,2)$ NOT bridge | DFS to 3 |
| `DFS 3 (parent 1)` | tin[3]=4, low[3]=4. No unvisited neighbors | Return to 1 |
| `Back to 1 from 3` | low[1] = $\min(1, 4) = 1$. $low[3] (4) > tin[1] (2) \implies$ BRIDGE FOUND: `[1, 3]`! | Bridge: `[1, 3]` |
| `Return to 0` | low[0] = $\min(1, 1) = 1$. $low[1] (1) \ngtr tin[0] (1) \implies (0,1)$ NOT bridge | Only bridge: `[1, 3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Graph has no bridges (simple cycle, returns `[]`).
- Graph is a tree (all $V - 1$ edges are bridges).
- Disconnected graph components.

### Common Bugs to Avoid
- Updating `low[node] = min(low[node], low[neighbor])` on back-edges instead of `tin[neighbor]` (conceptually flawed, though `tin` strictly preserves definition).
- Checking `neighbor == parent` incorrectly (treating parent edge as a back-edge).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the bridge condition strictly 'low[neighbor] > tin[node]' (STRICTLY greater)?**  
  **A**: If `low[neighbor] == tin[node]`, it means the neighbor (or its subtree) can reach `node` via an alternative path (a cycle containing `node`). If `low[neighbor] < tin[node]`, the neighbor can reach an ancestor of `node` above it! Only when `low[neighbor] > tin[node]` is there ZERO alternative path back to `node` or above, proving the edge is a single point of failure (Bridge)!

- **Q2: What is the difference between a Bridge and an Articulation Point?**  
  **A**: A **Bridge** is a critical *edge* whose removal disconnects the graph; an **Articulation Point** (Cut Vertex) is a critical *vertex* whose removal (along with all incident edges) disconnects the graph. An edge $(u, v)$ being a bridge does NOT necessarily mean both $u$ and $v$ are articulation points (e.g. leaf nodes).

- **Q3: How is Tarjan's Bridge algorithm used in Telecommunications and Cloud Infrastructure?**  
  **A**: SLA Reliability Analysis: Identifying single-point-of-failure fiber routes in AWS/GCP data-center interconnections to trigger automated dual-homing trunk provisioning.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Tarjan's Algorithm`, `DFS`, `Bridges`, `Critical Connections`, `LeetCode-1192`, `Hard`
- **Related problems to practice next**:
- **Articulation Point**: Cut vertices via Tarjan.
- **Strongly Connected Components**: Kosaraju / Tarjan SCC.
