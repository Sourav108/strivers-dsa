# Check if Graph is Bipartite (2-Coloring via BFS/DFS) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check if Graph is Bipartite (2-Coloring via BFS/DFS)](https://takeuforward.org/data-structure/bipartite-graph/)
- **Difficulty**: Medium
- **Statement**: Given an undirected graph with $n$ nodes represented as an adjacency list `graph`, return `true` if and only if the graph is bipartite. A graph is bipartite if its vertices can be divided into two disjoint sets $U$ and $V$ such that every edge connects a vertex in $U$ to one in $V$ (no two adjacent vertices share the same color).

---

## 1. Problem, Restated

2-Coloring Algorithm: assign alternating colors $(0, 1)$ to adjacent nodes; if any adjacent neighbor has the same color as the current node, the graph contains an odd-length cycle and is NOT bipartite.

- **Input**: `vector<vector<int>>& graph` adjacency list of $N$ nodes.
- **Output**: Boolean `true` if bipartite, `false` otherwise.
- **Constraints**: $1 \le n \le 100$, $0 \le \text{edges} \le 10^4$.

---

## 2. Intuition & Pattern

**Fundamental Bipartite Theorem**: A graph is Bipartite IF AND ONLY IF it contains **NO ODD-LENGTH CYCLES**! (Any graph with even-length cycles or linear trees is always bipartite). 1) Maintain `vector<int> color(V, -1)`. 2) Loop `for (int i = 0; i < V; ++i)`: if `color[i] == -1`, color `color[i] = 0` and launch BFS/DFS. 3) For each neighbor $v$ of $u$: if `color[v] == -1`, set `color[v] = !color[u]` (invert color) and enqueue/recurse. If `color[v] == color[u]`, an adjacent conflict is detected $\implies$ graph is NOT bipartite $\implies$ return `false`! Runs in $\mathcal{O}(V + 2E)$ time.

- **Underlying Pattern**: `2-Coloring BFS / DFS (Odd-Length Cycle Detection Invariant)`.

---

## 3. Approach 1 — Naive / Combinatorial 2-Partition

### Idea
Try all $2^V$ possible color assignments taking $\mathcal{O}(2^V \cdot E)$ time.

### C++17 Code
```cpp
// O(2^V * E) brute force partitioning
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^V \cdot E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$.
- **Why it's not good enough**: Exponential time complexity.

---

## 4. Approach 2 — Better

### Idea
DFS 2-Coloring in O(V + 2E) time and O(V) call stack space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class SolutionDFS {
    bool dfs(int node, int col, const vector<vector<int>>& graph, vector<int>& color) {
        color[node] = col;
        for (int neighbor : graph[node]) {
            if (color[neighbor] == -1) {
                if (!dfs(neighbor, !col, graph, color)) return false;
            } else if (color[neighbor] == col) {
                return false; // Adjacent same color conflict!
            }
        }
        return true;
    }
public:
    bool isBipartite(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<int> color(n, -1);
        for (int i = 0; i < n; i++)
            if (color[i] == -1)
                if (!dfs(i, 0, graph, color)) return false;
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ call stack space.
- **Why it's still not optimal**: DFS and BFS have identical optimal asymptotic bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
FIFO Queue BFS 2-Coloring in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
private:
    bool checkBFS(int start, const vector<vector<int>>& graph, vector<int>& color) {
        queue<int> q;
        q.push(start);
        color[start] = 0; // Assign first color
        
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            
            for (int neighbor : graph[node]) {
                // If the adjacent node is yet not colored
                if (color[neighbor] == -1) {
                    color[neighbor] = !color[node]; // Assign opposite color
                    q.push(neighbor);
                }
                // If the adjacent node has the SAME color as current node -> NOT BIPARTITE!
                else if (color[neighbor] == color[node]) {
                    return false;
                }
            }
        }
        
        return true;
    }

public:
    bool isBipartite(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<int> color(n, -1); // -1 denotes unvisited/uncolored
        
        // Check for all disconnected components
        for (int i = 0; i < n; i++) {
            if (color[i] == -1) {
                if (!checkBFS(i, graph, color)) {
                    return false;
                }
            }
        }
        
        return true;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (each vertex colored once, each edge inspected twice).
- **Space Complexity**: $\mathcal{O}(V)$ queue and color array space.
- **Why this is optimal**: Queue-based level alternation tests odd-length cycle presence without recursion stack overhead.

---

## 6. Dry Run

Graph 1: `[[1,2,3],[0,2],[0,1,3],[0,2]]` (Odd cycle 0-1-2-0) vs Graph 2: `[[1,3],[0,2],[1,3],[0,2]]` (Even cycle 0-1-2-3-0)

| Step | Action / State Change | Result |
|---|---|---|
| `Graph 1 (Odd Cycle)` | color[0]=0 -> q pushes 1,2,3 with color 1. At 1: sees neighbor 2 has color 1! Conflict: $color[1] == color[2]$ | Return FALSE ❌ (Odd cycle of length 3) |
| `Graph 2 (Even Cycle)` | color[0]=0, color[1]=1, color[2]=0, color[3]=1 -> all neighbors have opposite colors | Return TRUE ✅ (Bipartite) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Disconnected components.
- Graph with no edges (trivially bipartite).
- Trees (always bipartite).

### Common Bugs to Avoid
- Setting `color[neighbor] = 1 - color[node]` after popping from queue instead of upon enqueuing.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does a graph with an ODD-LENGTH cycle fail to be bipartite?**  
  **A**: Suppose a cycle has odd length $2k + 1$. Coloring nodes alternately around the cycle: node 1 gets color 0, node 2 gets color 1, ..., node $2k+1$ gets color 0. But node $2k+1$ is directly connected back to node 1 (which also has color 0)! This creates an unavoidable color clash between adjacent vertices!

- **Q2: Why is EVERY tree always bipartite?**  
  **A**: Because trees are acyclic (contain 0 cycles). Level-order coloring guarantees parents and children are strictly in alternating color sets!

- **Q3: Can Disjoint Set Union (DSU) be used to check bipartite graphs?**  
  **A**: Yes! For each node $u$, maintain two virtual nodes $u$ and $u + V$ (representing its two color partitions). For edge $(u, v)$, unite $(u, v + V)$ and $(v, u + V)$. If `find(u) == find(v)`, the graph is NOT bipartite!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `DFS`, `Bipartite`, `2-Coloring`, `LeetCode-785`, `Medium`
- **Related problems to practice next**:
- **Detect Cycle in Undirected Graph**: Cycle detection.
- **Possible Bipartition**: LeetCode 886.
