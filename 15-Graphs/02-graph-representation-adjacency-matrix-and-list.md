# Graph Representation in C++ (Adjacency Matrix & Adjacency List) (Step 15.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Graph Representation in C++ (Adjacency Matrix & Adjacency List)](https://takeuforward.org/graph/graph-representation-in-c/)
- **Difficulty**: Easy
- **Statement**: Given $N$ vertices and $M$ edges of an undirected/directed graph, implement graph storage using both an Adjacency Matrix (`vector<vector<int>>`) and an Adjacency List (`vector<vector<int>>` or `vector<vector<pair<int, int>>>` for weighted graphs). Explain why Adjacency List is optimal for sparse graphs ($E \ll V^2$).

---

## 1. Problem, Restated

Compare matrix storage $\mathcal{O}(V^2)$ with list storage $\mathcal{O}(V + 2E)$ space and neighbor iteration speed.

- **Input**: Parameters specified ($V$ vertices, $E$ edges / adjacency lists).
- **Output**: Traversal vector / count / data structures.
- **Constraints**: Standard competitive programming limits ($V \le 10^5, E \le 2 \times 10^5$).

---

## 2. Intuition & Pattern

1) **Adjacency Matrix `adj[V][V]`**: `adj[u][v] = 1` if edge exists. Space $\mathcal{O}(V^2)$. For $V = 10^5$, matrix requires $10^{10}$ integers (40 GB RAM $\implies$ Memory Limit Exceeded!). 2) **Adjacency List `vector<int> adj[V]`**: `adj[u].push_back(v)` stores only existing edges. Space $\mathcal{O}(V + 2E)$ for undirected, $\mathcal{O}(V + E)$ for directed. Consumes only few megabytes, ideal for competitive programming.

- **Underlying Pattern**: `Graph Memory Models (Dense $\mathcal{O}(V^2)$ vs Sparse $\mathcal{O}(V + E)$ Adjacency List)`.

---

## 3. Approach 1 — Naive / Adjacency Matrix

### Idea
Adjacency Matrix storage consuming $\mathcal{O}(V^2)$ space and $\mathcal{O}(V)$ neighbor iteration.

### C++17 Code
```cpp
#include <vector>
using namespace std;
// O(V^2) Matrix Representation
vector<vector<int>> buildMatrix(int V, const vector<pair<int, int>>& edges) {
    vector<vector<int>> adj(V + 1, vector<int>(V + 1, 0));
    for (auto& edge : edges) {
        adj[edge.first][edge.second] = 1;
        adj[edge.second][edge.first] = 1; // undirected
    }
    return adj;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    // O(V^2) Matrix Representation
    int[][] buildMatrix(int V, List<int[]> edges) {
        int[][] adj = new int[V + 1][V + 1];
        for (var edge : edges) {
            adj[edge.first][edge.second] = 1;
            adj[edge.second][edge.first] = 1; // undirected
        }
        return adj;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2)$ initialization time.
- **Space Complexity**: $\mathcal{O}(V^2)$ memory.
- **Why it's not good enough**: Fails on $V > 10^4$ due to MLE.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Adjacency List / BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(V + E)$ or $\mathcal{O}(V + 2E)$ time and space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Adjacency List in $\mathcal{O}(V + E)$ space and $\mathcal{O}(\text{deg}(u))$ neighbor lookup.

### C++17 Code
```cpp
#include <vector>
using namespace std;

// Unweighted Undirected Graph
vector<vector<int>> buildAdjList(int V, const vector<pair<int, int>>& edges) {
    vector<vector<int>> adj(V);
    for (const auto& [u, v] : edges) {
        adj[u].push_back(v);
        adj[v].push_back(u); // Remove this line for Directed Graph
    }
    return adj;
}

// Weighted Graph: stores pair<neighbor, weight>
vector<vector<pair<int, int>>> buildWeightedAdjList(int V, const vector<tuple<int, int, int>>& edges) {
    vector<vector<pair<int, int>>> adj(V);
    for (const auto& [u, v, w] : edges) {
        adj[u].push_back({v, w});
        adj[v].push_back({u, w}); // undirected
    }
    return adj;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    // Unweighted Undirected Graph
    int[][] buildAdjList(int V, List<int[]> edges) {
        int[][] adj(V);
        for (var [u, v] : edges) {
            adj[u].add(v);
            adj[v].add(u); // Remove this line for Directed Graph
        }
        return adj;
    }
    
    // Weighted Graph: stores pair<neighbor, weight>
    vector<List<int[]>> buildWeightedAdjList(int V, vector<tuple<int, int, int>> edges) {
        vector<List<int[]>> adj(V);
        for (var [u, v, w] : edges) {
            adj[u].add({v, w});
            adj[v].add({u, w}); // undirected
        }
        return adj;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ graph construction time.
- **Space Complexity**: $\mathcal{O}(V + 2E)$ space for undirected graph ($\\mathcal{O}(V + E)$ for directed).
- **Why this is optimal**: Sparse vector allocations prevent quadratic memory blowup.

---

## 6. Dry Run

$V = 4$, Edges: `(0, 1), (0, 2), (1, 2), (2, 3)`

| Step | Action / State Change | Result |
|---|---|---|
| `adj[0]` | Connected to 1, 2 | `[1, 2]` |
| `adj[1]` | Connected to 0, 2 | `[0, 2]` |
| `adj[2]` | Connected to 0, 1, 3 | `[0, 1, 3]` |
| `adj[3]` | Connected to 2 | `[2]` |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- 1-based vs 0-based indexing (allocate `vector<vector<int>> adj(V + 1)` for 1-based).
- Multiple parallel edges between same pair of nodes.

### Common Bugs to Avoid
- Forgetting `adj[v].push_back(u)` on undirected graphs (turns undirected graph into directed graph!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: When is an Adjacency Matrix preferred over an Adjacency List?**  
  **A**: When the graph is **dense** ($E \approx V^2$) or when constant time $\mathcal{O}(1)$ edge existence queries (`hasEdge(u, v)`) are required (such as in Floyd-Warshall All-Pairs Shortest Path $\mathcal{O}(V^3)$).

- **Q2: How to handle multi-threaded graph traversal in C++?**  
  **A**: Adjacency lists can be read concurrently by multiple worker threads without locking once construction is completed (read-only state).

- **Q3: How to represent a graph in flat memory without pointer indirection (CSR format)?**  
  **A**: Compressed Sparse Row (CSR) uses two flat arrays: `row_ptr[V + 1]` storing prefix offsets and `col_idx[2E]` storing neighbors, maximizing CPU L1 cache locality.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Representation`, `Data Structure`, `Easy`
- **Related problems to practice next**:
- **BFS Traversal**: Graph traversal.
- **DFS Traversal**: Graph traversal.
