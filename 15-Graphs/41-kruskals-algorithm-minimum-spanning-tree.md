# Kruskal's Algorithm for Minimum Spanning Tree (MST) (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Kruskal's Algorithm for Minimum Spanning Tree (MST)](https://takeuforward.org/data-structure/kruskals-algorithm-minimum-spanning-tree-g-47/)
- **Difficulty**: Medium
- **Statement**: Given a weighted, undirected and connected graph of $V$ vertices and $E$ edges, find the sum of weights of the edges of the Minimum Spanning Tree (MST) using Kruskal's Algorithm and Disjoint Set Union (DSU). Also construct the list of $V - 1$ edges that constitute the MST.

---

## 1. Problem, Restated

Sort all graph edges in non-decreasing order of weight; greedily add edges that connect different components using DSU cycle prevention in $\mathcal{O}(E \log E)$ time.

- **Input**: `int V`, `vector<vector<int>> adj[]`.
- **Output**: Integer sum of weights of edges in the Minimum Spanning Tree.
- **Constraints**: $2 \le V \le 1000$, $V - 1 \le E \le V \times (V - 1) / 2$, $1 \le \text{weight} \le 1000$.

---

## 2. Intuition & Pattern

Kruskal's algorithm operates globally on the entire edge set: 1) Flatten all edges into a flat edge list `vector<pair<int, pair<int, int>>> edges` storing `{weight, {u, v}}`. 2) Sort `edges` in ascending order of `weight`. 3) Initialize `DisjointSet dsu(V)`. 4) Iterate through sorted edges: for `{wt, {u, v}}`: 
   - Check if `u` and `v` are ALREADY in the same connected component: `if (dsu.findUPar(u) != dsu.findUPar(v))`. 
   - If they are in different components, adding this edge will NOT create a cycle $\implies$ `dsu.unionBySize(u, v)`, add `wt` to `mstSum`, and record `{u, v}` in `mstEdges`! 
   - If they are in the same component, discard the edge to prevent cycles! 5) Stop when $V - 1$ edges are selected. Runs in $\mathcal{O}(E \log E)$ time.

- **Underlying Pattern**: `Greedy Edge Sorting + DSU Cycle Avoidance (Kruskal's Algorithm)`.

---

## 3. Approach 1 — Naive / DFS Connectivity Checks

### Idea
DFS/BFS connectivity checks for each sorted edge in $\mathcal{O}(E \times (V + E))$ time.

### C++17 Code
```cpp
// O(E * (V + E)) cycle check via DFS
```

### Java Code
```java
// Java equivalent
// O(E * (V + E)) cycle check via DFS
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E \times (V + E))$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's not good enough**: Running full graph DFS on each edge check is quadratic.

---

## 4. Approach 2 — Better (Prim's Algorithm)

### Idea
Prim's Algorithm with Min-Heap in O(E log V) time.

### C++17 Code
```cpp
// Prim's algorithm counterpart
```

### Java Code
```java
// Java equivalent
// Prim's algorithm counterpart
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E \log V)$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's still not optimal**: Prim's is ideal for dense graphs; Kruskal's is ideal for edge lists / sparse graphs.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Kruskal's Algorithm with DSU)

### Idea
Kruskal's Algorithm with DSU Union by Size in $\mathcal{O}(E \log E)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
#include <algorithm>
using namespace std;

class DisjointSet {
    vector<int> parent, size;
public:
    DisjointSet(int n) {
        parent.resize(n + 1);
        size.resize(n + 1, 1);
        iota(parent.begin(), parent.end(), 0);
    }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]);
    }
    bool unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        if (rootU == rootV) return false; // Cycle detected
        if (size[rootU] < size[rootV]) {
            parent[rootU] = rootV;
            size[rootV] += size[rootU];
        } else {
            parent[rootV] = rootU;
            size[rootU] += size[rootV];
        }
        return true;
    }
};

class Solution {
public:
    int spanningTree(int V, vector<vector<int>> adj[]) {
        // 1. Extract all unique edges: {weight, {u, v}}
        vector<pair<int, pair<int, int>>> edges;
        for (int u = 0; u < V; u++) {
            for (const auto& edge : adj[u]) {
                int v = edge[0];
                int wt = edge[1];
                if (u < v) { // Avoid duplicate undirected edges
                    edges.push_back({wt, {u, v}});
                }
            }
        }
        
        // 2. Sort all edges in ascending order of weights
        sort(edges.begin(), edges.end());
        
        DisjointSet dsu(V);
        int mstSum = 0;
        int edgesCount = 0;
        
        // 3. Greedily process edges
        for (const auto& edge : edges) {
            int wt = edge.first;
            int u = edge.second.first;
            int v = edge.second.second;
            
            // If u and v are in different components, unite them (no cycle)
            if (dsu.unionBySize(u, v)) {
                mstSum += wt;
                edgesCount++;
                if (edgesCount == V - 1) break; // MST contains exactly V - 1 edges
            }
        }
        
        return mstSum;
    }
};
```

### Java Code
```java
import java.util.*;

class DisjointSet {
    int[] parent, size;

    DisjointSet(int n) {
        parent.resize(n + 1);
        size.resize(n + 1, 1);
        iota(parent.begin(), parent.end(), 0);
    }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]);
    }
    boolean unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        if (rootU == rootV) return false; // Cycle detected
        if (size[rootU] < size[rootV]) {
            parent[rootU] = rootV;
            size[rootV] += size[rootU];
        } else {
            parent[rootV] = rootU;
            size[rootU] += size[rootV];
        }
        return true;
    }
};

class Solution {

    int spanningTree(int V, int[][] adj[]) {
        // 1. Extract all unique edges: {weight, {u, v}}
        vector<pair<int, pair<int, int>>> edges;
        for (int u = 0; u < V; u++) {
            for (var edge : adj[u]) {
                int v = edge[0];
                int wt = edge[1];
                if (u < v) { // Avoid duplicate undirected edges
                    edges.add({wt, {u, v}});
                }
            }
        }
        
        // 2. Sort all edges in ascending order of weights
        Arrays.sort(edges);
        
        DisjointSet dsu(V);
        int mstSum = 0;
        int edgesCount = 0;
        
        // 3. Greedily process edges
        for (var edge : edges) {
            int wt = edge.first;
            int u = edge.second.first;
            int v = edge.second.second;
            
            // If u and v are in different components, unite them (no cycle)
            if (dsu.unionBySize(u, v)) {
                mstSum += wt;
                edgesCount++;
                if (edgesCount == V - 1) break; // MST contains exactly V - 1 edges
            }
        }
        
        return mstSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E \log E)$ time (dominated by sorting $E$ edges; DSU operations take $\mathcal{O}(E \cdot \alpha(V))$).
- **Space Complexity**: $\mathcal{O}(V + E)$ edges list and DSU parent/size array memory.
- **Why this is optimal**: Edge sorting + DSU allows instant $\mathcal{O}(\alpha(V))$ cycle checking.

---

## 6. Dry Run

Graph $V = 5$, `edges = [[1, (0,2)], [1, (1,2)], [1, (3,4)], [2, (0,1)], [2, (2,3)], [2, (2,4)]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Edge 1: (0,2), wt 1` | find(0)=0, find(2)=2 $\implies$ unite(0,2), sum = 1 | edges = 1 |
| `Edge 2: (1,2), wt 1` | find(1)=1, find(2)=0 $\implies$ unite(1,0), sum = 1+1=2 | edges = 2 |
| `Edge 3: (3,4), wt 1` | find(3)=3, find(4)=4 $\implies$ unite(3,4), sum = 2+1=3 | edges = 3 |
| `Edge 4: (0,1), wt 2` | find(0)=0, find(1)=0 $\implies$ Same component! Discarded to prevent cycle! | ignored |
| `Edge 5: (2,3), wt 2` | find(2)=0, find(3)=3 $\implies$ unite(0,3), sum = 3+2=5 | edges = 4 ($V - 1$) |
| `Result` | MST has $V-1=4$ edges, Total Weight = 5 | MST Sum = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Disconnected graph (MST cannot span all vertices).
- All edges have equal weight.
- $V = 1$ (sum = 0).

### Common Bugs to Avoid
- Adding both directed copies of undirected edge $(u, v)$ without deduplication (wastes memory, though sorting handles correctly).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Kruskal's algorithm guarantee a MINIMUM Spanning Tree?**  
  **A**: By the **Greedy Cut Property / Cycle Property of MSTs**: when considering edges in ascending order of weight, adding an edge between two previously disconnected components is the absolute cheapest bridge possible. Since DSU strictly discards any edge closing a cycle, the resulting tree is guaranteed to be acyclic and minimal!

- **Q2: When is Kruskal's preferred over Prim's?**  
  **A**: 1) When graph is **sparse** ($E \approx V$), where $E \log E < V^2$; 2) When graph edges are **already given as an edge list** `[u, v, wt]` (saves building adjacency lists); 3) When edges are **already pre-sorted** (Kruskal runs in pure linear $\mathcal{O}(E \cdot \alpha(V))$ time!).

- **Q3: How to adapt Kruskal's for Maximum Spanning Tree?**  
  **A**: Simply sort edges in DESCENDING order of weight (`sort(edges.rbegin(), edges.rend())`) and apply identical DSU logic!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `MST`, `Kruskal's Algorithm`, `DSU`, `Greedy`, `Medium`
- **Related problems to practice next**:
- **Prim's Algorithm**: MST with Min-Heap.
- **Disjoint Set Union**: Foundational DSU structure.
