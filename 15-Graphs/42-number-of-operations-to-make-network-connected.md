# Number of Operations to Make Network Connected (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Number of Operations to Make Network Connected](https://takeuforward.org/data-structure/number-of-operations-to-make-network-connected-dsu-g-49/)
- **Difficulty**: Medium
- **Statement**: There are $n$ computers numbered from 0 to $n - 1$ connected by ethernet cables `connections` where `connections[i] = [a, b]`. You can extract existing redundant cables and connect them between any disconnected computers. Return the minimum number of cable relocations to connect all $n$ computers into a single network. If impossible, return -1.

---

## 1. Problem, Restated

Connecting $C$ disjoint components into 1 single network requires at least $C - 1$ bridge cables. If total available cables is at least $n - 1$, the answer is always $C - 1$; else return -1 in $\mathcal{O}(V + E \cdot \alpha(V))$ time.

- **Input**: `int n`, `vector<vector<int>>& connections`.
- **Output**: Integer representing minimum cable relocations to connect all computers (or -1).
- **Constraints**: $1 \le n \le 10^5$, $1 \le \text{connections.length} \le \min(n(n-1)/2, 10^5)$.

---

## 2. Intuition & Pattern

**Fundamental Tree Invariant**: To connect $n$ vertices, we strictly need at least $n - 1$ total edges. If `connections.size() < n - 1`, connecting all computers is mathematically impossible $\implies$ return -1 immediately! 
If `connections.size() >= n - 1`, we are guaranteed to have enough redundant cables. 1) Initialize `DisjointSet dsu(n)`. 2) For each connection `[u, v]`: call `dsu.unionBySize(u, v)`. 3) Count number of distinct connected components $C$ (nodes where `parent[i] == i`). 4) To connect $C$ components together, we need exactly $C - 1$ edge moves $\implies$ return $C - 1$! Runs in $\mathcal{O}(n + E \cdot \alpha(n))$ time.

- **Underlying Pattern**: `Spanning Tree Edge Invariant + DSU Component & Redundant Edge Counting`.

---

## 3. Approach 1 — Naive / Adjacency Graph DFS

### Idea
Standard DFS/BFS connected component counting after checking `edges.size() >= n - 1`.

### C++17 Code
```cpp
// DFS component counting alternative
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's not good enough**: DFS requires building an adjacency list, while DSU operates directly on the edge list.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DSU Component Counting algorithm below directly achieves optimal $\mathcal{O}(V + E \cdot \alpha(V))$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (DSU Component Counting)

### Idea
Disjoint Set Union (DSU) Component Counting in $\mathcal{O}(V + E \cdot \alpha(V))$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;

class DisjointSet {
public:
    vector<int> parent, size;
    int components;
    
    DisjointSet(int n) {
        parent.resize(n);
        size.resize(n, 1);
        components = n;
        iota(parent.begin(), parent.end(), 0);
    }
    
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]);
    }
    
    bool unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        
        if (rootU == rootV) return false; // Redundant extra edge!
        
        if (size[rootU] < size[rootV]) {
            parent[rootU] = rootV;
            size[rootV] += size[rootU];
        } else {
            parent[rootV] = rootU;
            size[rootU] += size[rootV];
        }
        
        components--;
        return true;
    }
};

class Solution {
public:
    int makeConnected(int n, vector<vector<int>>& connections) {
        // Condition for tree spanning: at least n - 1 total edges required
        if ((int)connections.size() < n - 1) {
            return -1;
        }
        
        DisjointSet dsu(n);
        int extraEdges = 0;
        
        for (const auto& conn : connections) {
            int u = conn[0];
            int v = conn[1];
            
            // If union fails, this edge is redundant inside an already-connected component
            if (!dsu.unionBySize(u, v)) {
                extraEdges++;
            }
        }
        
        int requiredCables = dsu.components - 1;
        
        // If extra cables can bridge all separate components
        if (extraEdges >= requiredCables) {
            return requiredCables;
        }
        
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E \cdot \alpha(V))$ time (where $V = n, E = \text{connections.size()}$).
- **Space Complexity**: $\mathcal{O}(V)$ DSU parent and size array memory.
- **Why this is optimal**: DSU counts components and redundant edges online without allocating adjacency lists.

---

## 6. Dry Run

$n = 6$, `connections = [[0,1],[0,2],[0,3],[1,2],[1,3]]` ($E = 5 \ge 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `Edges 1,2,3` | unite(0,1), unite(0,2), unite(0,3) $\implies$ component {0,1,2,3} | components = 3 (nodes {0,1,2,3}, {4}, {5}) |
| `Edge 4: [1,2]` | find(1)=0, find(2)=0 $\implies$ Redundant edge 1! | extraEdges = 1 |
| `Edge 5: [1,3]` | find(1)=0, find(3)=0 $\implies$ Redundant edge 2! | extraEdges = 2 |
| `Result` | dsu.components = 3, required = $3 - 1 = 2$. extraEdges = 2 $\ge 2$ | Return 2 moves ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `connections.size() < n - 1` (returns -1 immediately).
- All computers already connected (returns 0).
- All computers isolated (no edges, returns -1).

### Common Bugs to Avoid
- Not checking `connections.size() < n - 1` initially (can lead to incorrect edge counts).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is 'totalEdges >= n - 1' a NECESSARY and SUFFICIENT condition when extraEdges >= components - 1?**  
  **A**: Because mathematically, $\text{totalEdges} = \text{spanningEdges} + \text{extraEdges} = (n - \text{components}) + \text{extraEdges}$. Therefore, $\text{totalEdges} \ge n - 1 \iff (n - \text{components}) + \text{extraEdges} \ge n - 1 \iff \text{extraEdges} \ge \text{components} - 1$! Thus checking `totalEdges >= n - 1` alone guarantees that enough extra edges exist!

- **Q2: Can this be solved using DFS instead of DSU?**  
  **A**: Yes! Check `if (connections.size() < n - 1) return -1;` then run DFS to count connected components $C$. Return $C - 1$.

- **Q3: What is the real-world application in Network Topology Design?**  
  **A**: Network redundancy optimization: relocating standby / redundant fiber links in ring topologies to span dark disconnected data centers with zero added hardware cost.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DSU`, `Disjoint Set Union`, `Connected Components`, `LeetCode-1319`, `Medium`
- **Related problems to practice next**:
- **Number of Provinces**: Component counting.
- **Kruskal's Algorithm**: MST with DSU.
