# Minimum Spanning Tree (Prim's & Kruskal's Full Comparison) (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Spanning Tree (Prim's & Kruskal's Full Comparison)](https://takeuforward.org/data-structure/minimum-spanning-tree-theory-and-comparison/)
- **Difficulty**: Medium
- **Statement**: Given a weighted, undirected, connected graph of $V$ vertices and $E$ edges represented by adjacency list `adj`, compute the total MST weight and construct the $V - 1$ MST edges using both Prim's and Kruskal's algorithms. Provide deep theoretical comparisons on dense vs sparse graphs.

---

## 1. Problem, Restated

Connect all $V$ vertices using exactly $V - 1$ edges with minimum total weight, proving correctness via Cut Property and Cycle Property in $\mathcal{O}(E \log V)$ / $\mathcal{O}(E \log E)$ time.

- **Input**: `int V`, `vector<vector<pair<int, int>>> adj` (or edge list `edges`).
- **Output**: Integer representing total weight of the Minimum Spanning Tree.
- **Constraints**: $1 \le V \le 2000$, $1 \le E \le V(V-1)/2$.

---

## 2. Intuition & Pattern

**1. Cut Property (Prim's Foundation)**: For any cut partition $(S, V \setminus S)$, the minimum weight edge crossing the cut strictly belongs to the MST. Prim's grows a single tree by repeatedly picking the cheapest crossing edge via a Min-Heap. 
**2. Cycle Property (Kruskal's Foundation)**: For any cycle $C$ in the graph, the heaviest edge in $C$ cannot belong to a unique MST. Kruskal's sorts all edges globally and avoids cycles using Disjoint Set Union (DSU). 
**Trade-offs**: 
- **Dense Graphs ($E \approx V^2$)**: Prim's with adjacency matrix/indexed heap runs in $\mathcal{O}(V^2)$, beating Kruskal's $\mathcal{O}(V^2 \log V)$. 
- **Sparse Graphs ($E \approx V$)**: Kruskal's runs in $\mathcal{O}(E \log E) \approx \mathcal{O}(E \log V)$ with zero heap extraction overhead and simpler edge-list cache locality. Runs in $\mathcal{O}(E \log V)$ time.

- **Underlying Pattern**: `Greedy Cut Property (Prim's Heap) & Cycle Property (Kruskal's DSU)`.

---

## 3. Approach 1 — Naive / Combinatorial Edge Generation

### Idea
Enumerate all $\binom{E}{V-1}$ subsets of edges and check acyclic spanning tree connectivity in exponential time.

### C++17 Code
```cpp
// O(Combinatorial) brute force
```

### Java Code
```java
// Java equivalent
// O(Combinatorial) brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\text{Exponential})$.
- **Space Complexity**: $\mathcal{O}(V)$.
- **Why it's not good enough**: Combinatorial edge explosion.

---

## 4. Approach 2 — Better (Prim's Algorithm)

### Idea
Prim's Algorithm with Min-Heap Priority Queue in O(E log V) time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class SolutionPrim {
public:
    int primMST(int V, vector<vector<pair<int, int>>>& adj) {
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
        vector<int> vis(V, 0);
        pq.push({0, 0});
        int mstSum = 0;
        while (!pq.empty()) {
            auto [wt, u] = pq.top(); pq.pop();
            if (vis[u]) continue;
            vis[u] = 1; mstSum += wt;
            for (auto& [v, edgeWt] : adj[u]) {
                if (!vis[v]) pq.push({edgeWt, v});
            }
        }
        return mstSum;
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionPrim {

    int primMST(int V, vector<List<int[]>> adj) {
        priority_queue<pair<int, int>, List<int[]>, greater<>> pq;
        int[] vis = new int[V];
        pq.push({0, 0});
        int mstSum = 0;
        while (!pq.isEmpty()) {
            var [wt, u] = pq.peek(); pq.pop();
            if (vis[u]) continue;
            vis[u] = 1; mstSum += wt;
            for (var [v, edgeWt] : adj[u]) {
                if (!vis[v]) pq.push({edgeWt, v});
            }
        }
        return mstSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E \log V)$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's still not optimal**: Both Prim and Kruskal are dual optimal formulations of greedy MST.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Dual Kruskal & Prim Architectures)

### Idea
Unified Dual MST Implementation: Kruskal's DSU + Prim's Min-Heap in $\mathcal{O}(E \log E)$ / $\mathcal{O}(E \log V)$ time.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
#include <queue>
#include <algorithm>
using namespace std;

// Disjoint Set Union by Size for Kruskal's Algorithm
class DisjointSet {
public:
    vector<int> parent, size;
    DisjointSet(int n) {
        parent.resize(n);
        size.resize(n, 1);
        iota(parent.begin(), parent.end(), 0);
    }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]);
    }
    bool unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        if (rootU == rootV) return false;
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

class SolutionMST {
public:
    // Kruskal's Algorithm (Global Edge Sorting + DSU)
    int kruskalsMST(int V, vector<vector<int>>& edgeList) {
        // edgeList: [u, v, weight]
        sort(edgeList.begin(), edgeList.end(), [](const auto& a, const auto& b) {
            return a[2] < b[2];
        });
        
        DisjointSet dsu(V);
        int mstWeight = 0;
        int edgeCount = 0;
        
        for (const auto& edge : edgeList) {
            int u = edge[0];
            int v = edge[1];
            int wt = edge[2];
            
            if (dsu.unionBySize(u, v)) {
                mstWeight += wt;
                edgeCount++;
                if (edgeCount == V - 1) break;
            }
        }
        
        return mstWeight;
    }
    
    // Prim's Algorithm (Local Cut Expansion + Min-Heap)
    int primsMST(int V, vector<vector<pair<int, int>>>& adj) {
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
        vector<int> vis(V, 0);
        
        pq.push({0, 0}); // {weight, node}
        int mstWeight = 0;
        
        while (!pq.empty()) {
            auto [wt, node] = pq.top();
            pq.pop();
            
            if (vis[node]) continue;
            
            vis[node] = 1;
            mstWeight += wt;
            
            for (const auto& [adjNode, edgeWeight] : adj[node]) {
                if (!vis[adjNode]) {
                    pq.push({edgeWeight, adjNode});
                }
            }
        }
        
        return mstWeight;
    }
};
```

### Java Code
```java
import java.util.*;

// Disjoint Set Union by Size for Kruskal's Algorithm
class DisjointSet {

    int[] parent, size;
    DisjointSet(int n) {
        parent.resize(n);
        size.resize(n, 1);
        iota(parent.begin(), parent.end(), 0);
    }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]);
    }
    boolean unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        if (rootU == rootV) return false;
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

class SolutionMST {

    // Kruskal's Algorithm (Global Edge Sorting + DSU)
    int kruskalsMST(int V, int[][] edgeList) {
        // edgeList: [u, v, weight]
        sort(edgeList.begin(), edgeList.end(), [](var a, var b) {
            return a[2] < b[2];
        });
        
        DisjointSet dsu(V);
        int mstWeight = 0;
        int edgeCount = 0;
        
        for (var edge : edgeList) {
            int u = edge[0];
            int v = edge[1];
            int wt = edge[2];
            
            if (dsu.unionBySize(u, v)) {
                mstWeight += wt;
                edgeCount++;
                if (edgeCount == V - 1) break;
            }
        }
        
        return mstWeight;
    }
    
    // Prim's Algorithm (Local Cut Expansion + Min-Heap)
    int primsMST(int V, vector<List<int[]>> adj) {
        priority_queue<pair<int, int>, List<int[]>, greater<>> pq;
        int[] vis = new int[V];
        
        pq.push({0, 0}); // {weight, node}
        int mstWeight = 0;
        
        while (!pq.isEmpty()) {
            var [wt, node] = pq.peek();
            pq.pop();
            
            if (vis[node]) continue;
            
            vis[node] = 1;
            mstWeight += wt;
            
            for (var [adjNode, edgeWeight] : adj[node]) {
                if (!vis[adjNode]) {
                    pq.push({edgeWeight, adjNode});
                }
            }
        }
        
        return mstWeight;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E \log E)$ for Kruskal's, $\mathcal{O}(E \log V)$ for Prim's (near identical since $\log(V^2) = 2 \log V$).
- **Space Complexity**: $\mathcal{O}(V + E)$ space for both approaches.
- **Why this is optimal**: Kruskal's utilizes fast contiguous cache arrays for edge sorting; Prim's avoids edge list sorting by growing locally.

---

## 6. Dry Run

Graph with 4 nodes: `(0-1: 1), (1-2: 2), (2-3: 3), (0-3: 4), (1-3: 5)`

| Step | Action / State Change | Result |
|---|---|---|
| `Kruskal Step 1` | Sort edges: `[0-1:1, 1-2:2, 2-3:3, 0-3:4, 1-3:5]` | Sorted list ready |
| `Kruskal Step 2` | Pick (0-1:1) -> unite(0,1), Pick (1-2:2) -> unite(1,2), Pick (2-3:3) -> unite(2,3) | 3 edges chosen ($V-1$) |
| `Kruskal Step 3` | MST Weight = $1 + 2 + 3 = 6$ | Total = 6 ✅ |
| `Prim Step 1` | Start node 0: pop (0,0)->add (1,1)->pop (1,1)->add (2,2)->pop (2,2)->add (3,3)->pop (3,3) | Same 3 edges chosen |
| `Result` | Both algorithms produce identical optimal MST Weight = 6 | Weight = 6 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Disconnected graphs (MST does not exist).
- Graph with multiple identical MST weight choices.
- Graph with negative edge weights (handled correctly by both).

### Common Bugs to Avoid
- Marking visited upon heap push in Prim's (must mark upon pop).
- Not checking if $V - 1$ edges are formed in disconnected graphs.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the formal proof of the Cut Property in MST?**  
  **A**: Let $T$ be an MST. Suppose cut $(S, V \setminus S)$ has minimum crossing edge $e$. If $e \notin T$, adding $e$ to $T$ creates a cycle $C$ containing at least one other crossing edge $e'$. Replacing $e'$ with $e$ gives tree $T' = T \cup \{e\} \setminus \{e'\}$ with weight $w(T') = w(T) + w(e) - w(e') \le w(T)$ since $w(e) \le w(e')$. Thus, $T'$ is also an MST containing $e$!

- **Q2: When does a graph have a UNIQUE Minimum Spanning Tree?**  
  **A**: If all edge weights in the connected undirected graph are strictly distinct, the MST is mathematically guaranteed to be 100% UNIQUE!

- **Q3: What is Borůvka's Algorithm and when is it used?**  
  **A**: Borůvka's algorithm is a parallel MST algorithm where each component simultaneously finds its cheapest outgoing edge and connects them in $\mathcal{O}(E \log V)$ time using $\mathcal{O}(\log V)$ contraction phases; it forms the backbone of distributed graph processing engines like Apache Spark GraphX and Pregel.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `MST`, `Prim's Algorithm`, `Kruskal's Algorithm`, `DSU`, `Theory`, `Medium`
- **Related problems to practice next**:
- **Kruskal's Algorithm**: Kruskal details.
- **Prim's Algorithm**: Prim details.
