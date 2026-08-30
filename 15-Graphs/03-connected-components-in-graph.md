# Connected Components in Graph (Step 15.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Connected Components in Graph](https://takeuforward.org/graph/connected-components-in-graph/)
- **Difficulty**: Easy
- **Statement**: Given an undirected graph with $V$ vertices and $E$ edges, count the number of connected components in the graph. Also explain the multi-component outer loop pattern (`for (int i = 0; i < V; i++) if (!vis[i]) { count++; bfs/dfs(i); }`).

---

## 1. Problem, Restated

Traverse all unvisited nodes using an outer loop to discover and count disjoint connected components.

- **Input**: Parameters specified ($V$ vertices, $E$ edges / adjacency lists).
- **Output**: Traversal vector / count / data structures.
- **Constraints**: Standard competitive programming limits ($V \le 10^5, E \le 2 \times 10^5$).

---

## 2. Intuition & Pattern

A graph may not be fully connected; it can consist of multiple disjoint subgraphs (islands). If we run BFS/DFS only from node 0, disconnected components will never be visited! Solution: Maintain a global `vector<int> vis(V, 0)`. Loop `for (int i = 0; i < V; ++i)`: whenever `!vis[i]`, we have encountered a NEW connected component $\implies$ increment `componentCount++` and launch BFS/DFS from $i$ to mark all reachable nodes in that component.

- **Underlying Pattern**: `Multi-Component Outer Loop Driver (`for (int i = 0; i < V; ++i) if (!vis[i])`)`.

---

## 3. Approach 1 — Naive / Adjacency Matrix

### Idea
Floyd-Warshall transitive closure matrix in $\mathcal{O}(V^3)$ time.

### C++17 Code
```cpp
// O(V^3) Floyd Warshall connectivity
```

### Java Code
```java
// Java equivalent
// O(V^3) Floyd Warshall connectivity
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^3)$ time.
- **Space Complexity**: $\mathcal{O}(V^2)$.
- **Why it's not good enough**: Cubic time complexity.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Adjacency List / BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(V + E)$ or $\mathcal{O}(V + 2E)$ time and space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Outer Loop with BFS/DFS Marking in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
private:
    void bfs(int start, const vector<vector<int>>& adj, vector<int>& vis) {
        vis[start] = 1;
        queue<int> q;
        q.push(start);
        
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            
            for (int neighbor : adj[node]) {
                if (!vis[neighbor]) {
                    vis[neighbor] = 1;
                    q.push(neighbor);
                }
            }
        }
    }

public:
    int countConnectedComponents(int V, const vector<vector<int>>& adj) {
        vector<int> vis(V, 0);
        int components = 0;
        
        // Loop over all vertices to handle disconnected subgraphs
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                components++;
                bfs(i, adj, vis);
            }
        }
        
        return components;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void bfs(int start, int[][] adj, int[] vis) {
        vis[start] = 1;
        Queue<Integer> q = new LinkedList<>();
        q.push(start);
        
        while (!q.isEmpty()) {
            int node = q.peek();
            q.pop();
            
            for (int neighbor : adj[node]) {
                if (!vis[neighbor]) {
                    vis[neighbor] = 1;
                    q.push(neighbor);
                }
            }
        }
    }

    int countConnectedComponents(int V, int[][] adj) {
        int[] vis = new int[V];
        int components = 0;
        
        // Loop over all vertices to handle disconnected subgraphs
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                components++;
                bfs(i, adj, vis);
            }
        }
        
        return components;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (every vertex is visited once, every edge traversed twice).
- **Space Complexity**: $\mathcal{O}(V)$ visited array and queue space.
- **Why this is optimal**: Linear sweep guarantees visiting every component without re-visiting already marked nodes.

---

## 6. Dry Run

Graph with $V = 6$, Component 1: `(0, 1), (1, 2)`; Component 2: `(3, 4)`; Component 3: `5` (isolated)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0` | vis[0]=0 -> components=1 -> BFS visits {0, 1, 2} | vis: `[1, 1, 1, 0, 0, 0]` |
| `i = 1, 2` | vis[1]=1, vis[2]=1 -> skipped | skipped |
| `i = 3` | vis[3]=0 -> components=2 -> BFS visits {3, 4} | vis: `[1, 1, 1, 1, 1, 0]` |
| `i = 4` | vis[4]=1 -> skipped | skipped |
| `i = 5` | vis[5]=0 -> components=3 -> BFS visits {5} | vis: `[1, 1, 1, 1, 1, 1]` |
| `Result` | Total Connected Components = 3 | Result = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $E = 0$ (graph has no edges $\implies V$ isolated components).
- Complete graph $K_V$ (1 single component).

### Common Bugs to Avoid
- Calling BFS/DFS without the outer loop `for (int i = 0; i < V; ++i)` (only visits the component containing node 0!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the outer loop pattern mandatory for almost all graph interview questions?**  
  **A**: Because real-world graphs (e.g. social networks, distributed clusters) are often disconnected. Without the outer loop, algorithms fail on disconnected components!

- **Q2: Can Connected Components be counted using Disjoint Set Union (DSU)?**  
  **A**: Yes! Initialize `count = V`. For each edge $(u, v)$, if `findPar(u) != findPar(v)`, call `union(u, v)` and decrement `count--`. Final `count` is the number of connected components in $\mathcal{O}(E \alpha(V))$ time!

- **Q3: How does this apply to grid problems (like Number of Islands)?**  
  **A**: Every cell $(r, c)$ in the matrix represents a vertex; 4-directional adjacent land cells represent edges. The same outer loop counts connected island components.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `DFS`, `Connected Components`, `Easy`
- **Related problems to practice next**:
- **Number of Provinces**: LeetCode 547.
- **Number of Islands**: Grid components.
