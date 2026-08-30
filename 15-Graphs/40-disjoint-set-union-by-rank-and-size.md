# Disjoint Set Union (DSU by Rank & Size with Path Compression) (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Disjoint Set Union (DSU by Rank & Size with Path Compression)](https://takeuforward.org/data-structure/disjoint-set-union-by-rank-union-by-size-path-compression-g-46/)
- **Difficulty**: Medium
- **Statement**: Implement the complete, interview-grade Disjoint Set Union (Union-Find) data structure in C++ supporting `findParent(u)`, `unionByRank(u, v)`, and `unionBySize(u, v)` in near $\mathcal{O}(1)$ amortized time using **Path Compression**.

---

## 1. Problem, Restated

Design and analyze the foundational DSU data structure supporting dynamic connectivity queries and merges in $\mathcal{O}(\alpha(N))$ amortized time.

- **Data Structure**: `DisjointSet` class in C++17.
- **Operations**: `findUPar(u)`, `unionBySize(u, v)`, `unionByRank(u, v)`, `isConnected(u, v)`.
- **Complexity**: $\mathcal{O}(\alpha(N))$ amortized per operation.

---

## 2. Intuition & Pattern

Standard DFS/BFS requires $\mathcal{O}(V + E)$ to answer whether two nodes belong to the same connected component. **Disjoint Set Union (DSU)** answers dynamic connectivity queries and merges components in near $\mathcal{O}(1)$ time ($\\alpha(N) \le 4$ for all practical universe sizes)! 1) **Find with Path Compression**: `find(u)` recursively finds the representative root and directly flattens the tree (`parent[u] = find(parent[u])`). 2) **Union by Rank**: Attach tree with smaller rank under root of higher rank. If ranks are equal, increment resulting root rank by 1. 3) **Union by Size (Preferred)**: Attach smaller tree under larger tree and add sizes (`size[rootU] += size[rootV]`). Runs in $\mathcal{O}(\alpha(N))$ amortized time per operation.

- **Underlying Pattern**: `Disjoint Set Union (Path Compression + Union by Rank / Size)`.

---

## 3. Approach 1 — Naive / Unbalanced Linear Tree

### Idea
Naive DSU without path compression and without union by rank/size (trees degenerate into linear linked lists of depth $N$, leading to $\mathcal{O}(N)$ per query).

### C++17 Code
```cpp
// O(N) naive chained DSU without optimizations
```

### Java Code
```java
// Java equivalent
// O(N) naive chained DSU without optimizations
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ per query (linear search).
- **Space Complexity**: $\mathcal{O}(N)$ array.
- **Why it's not good enough**: Tree depth degenerates into $\mathcal{O}(N)$ linked list chains.

---

## 4. Approach 2 — Better (DSU with Union by Rank)

### Idea
DSU with Union by Rank and Path Compression in O(alpha(N)) amortized time.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;

class DisjointSetRank {
    vector<int> rank, parent;
public:
    DisjointSetRank(int n) : rank(n + 1, 0), parent(n + 1) {
        iota(parent.begin(), parent.end(), 0);
    }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]); // Path compression
    }
    void unionByRank(int u, int v) {
        int ulp_u = findUPar(u), ulp_v = findUPar(v);
        if (ulp_u == ulp_v) return;
        if (rank[ulp_u] < rank[ulp_v]) parent[ulp_u] = ulp_v;
        else if (rank[ulp_v] < rank[ulp_u]) parent[ulp_v] = ulp_u;
        else { parent[ulp_v] = ulp_u; rank[ulp_u]++; }
    }
};
```

### Java Code
```java
import java.util.*;

class DisjointSetRank {
    int[] rank, parent;

    public DisjointSetRank(int n) { /* initialized: rank(n + 1, 0), parent(n + 1)  */ 
        iota(parent.begin(), parent.end(), 0);
     }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]); // Path compression
    }
    void unionByRank(int u, int v) {
        int ulp_u = findUPar(u), ulp_v = findUPar(v);
        if (ulp_u == ulp_v) return;
        if (rank[ulp_u] < rank[ulp_v]) parent[ulp_u] = ulp_v;
        else if (rank[ulp_v] < rank[ulp_u]) parent[ulp_v] = ulp_u;
        else { parent[ulp_v] = ulp_u; rank[ulp_u]++; }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\alpha(N))$ amortized per operation.
- **Space Complexity**: $\mathcal{O}(N)$ memory.
- **Why it's still not optimal**: Union by Rank and Union by Size have identical theoretical performance, but Union by Size keeps track of actual component sizes directly.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Production DSU with Union by Size)

### Idea
Production-Grade DSU Class with Union by Size & Path Compression in $\mathcal{O}(\alpha(N))$ amortized time.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;

class DisjointSet {
public:
    vector<int> parent, size, rank;
    int numComponents;
    
    // 1-indexed constructor supporting up to n elements
    DisjointSet(int n) {
        parent.resize(n + 1);
        size.resize(n + 1, 1);
        rank.resize(n + 1, 0);
        numComponents = n;
        
        // Initially each element is its own parent
        iota(parent.begin(), parent.end(), 0);
    }
    
    // Find representative root with recursive Path Compression
    int findUPar(int node) {
        if (node == parent[node]) {
            return node;
        }
        // Path compression: points node directly to ultimate parent
        return parent[node] = findUPar(parent[node]);
    }
    
    // Union by Size (Attaches smaller tree under larger tree root)
    bool unionBySize(int u, int v) {
        int ulp_u = findUPar(u);
        int ulp_v = findUPar(v);
        
        if (ulp_u == ulp_v) return false; // Already in the same component
        
        if (size[ulp_u] < size[ulp_v]) {
            parent[ulp_u] = ulp_v;
            size[ulp_v] += size[ulp_u];
        } else {
            parent[ulp_v] = ulp_u;
            size[ulp_u] += size[ulp_v];
        }
        
        numComponents--;
        return true;
    }
    
    // Union by Rank (Attaches smaller rank tree under larger rank tree root)
    bool unionByRank(int u, int v) {
        int ulp_u = findUPar(u);
        int ulp_v = findUPar(v);
        
        if (ulp_u == ulp_v) return false;
        
        if (rank[ulp_u] < rank[ulp_v]) {
            parent[ulp_u] = ulp_v;
        } else if (rank[ulp_v] < rank[ulp_u]) {
            parent[ulp_v] = ulp_u;
        } else {
            parent[ulp_v] = ulp_u;
            rank[ulp_u]++;
        }
        
        numComponents--;
        return true;
    }
    
    // Returns the size of the connected component containing node
    int getComponentSize(int node) {
        return size[findUPar(node)];
    }
    
    // Checks if two nodes belong to the same component
    bool isConnected(int u, int v) {
        return findUPar(u) == findUPar(v);
    }
};
```

### Java Code
```java
import java.util.*;

class DisjointSet {

    int[] parent, size, rank;
    int numComponents;
    
    // 1-indexed constructor supporting up to n elements
    DisjointSet(int n) {
        parent.resize(n + 1);
        size.resize(n + 1, 1);
        rank.resize(n + 1, 0);
        numComponents = n;
        
        // Initially each element is its own parent
        iota(parent.begin(), parent.end(), 0);
    }
    
    // Find representative root with recursive Path Compression
    int findUPar(int node) {
        if (node == parent[node]) {
            return node;
        }
        // Path compression: points node directly to ultimate parent
        return parent[node] = findUPar(parent[node]);
    }
    
    // Union by Size (Attaches smaller tree under larger tree root)
    boolean unionBySize(int u, int v) {
        int ulp_u = findUPar(u);
        int ulp_v = findUPar(v);
        
        if (ulp_u == ulp_v) return false; // Already in the same component
        
        if (size[ulp_u] < size[ulp_v]) {
            parent[ulp_u] = ulp_v;
            size[ulp_v] += size[ulp_u];
        } else {
            parent[ulp_v] = ulp_u;
            size[ulp_u] += size[ulp_v];
        }
        
        numComponents--;
        return true;
    }
    
    // Union by Rank (Attaches smaller rank tree under larger rank tree root)
    boolean unionByRank(int u, int v) {
        int ulp_u = findUPar(u);
        int ulp_v = findUPar(v);
        
        if (ulp_u == ulp_v) return false;
        
        if (rank[ulp_u] < rank[ulp_v]) {
            parent[ulp_u] = ulp_v;
        } else if (rank[ulp_v] < rank[ulp_u]) {
            parent[ulp_v] = ulp_u;
        } else {
            parent[ulp_v] = ulp_u;
            rank[ulp_u]++;
        }
        
        numComponents--;
        return true;
    }
    
    // Returns the size of the connected component containing node
    int getComponentSize(int node) {
        return size[findUPar(node)];
    }
    
    // Checks if two nodes belong to the same component
    boolean isConnected(int u, int v) {
        return findUPar(u) == findUPar(v);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\alpha(N))$ amortized per `find` and `union` operation (where $\alpha$ is the Inverse Ackermann function; $\alpha(N) \le 4$ for all $N < 10^{600}$).
- **Space Complexity**: $\mathcal{O}(N)$ parent, size, and rank arrays.
- **Why this is optimal**: Path compression flattens trees on the fly while union-by-size prevents trees from ever exceeding depth $\log_2 N$.

---

## 6. Dry Run

Operations on $N = 7$: `union(1,2), union(2,3), union(4,5), union(6,7), union(5,6), union(3,7)`

| Step | Action / State Change | Result |
|---|---|---|
| `union(1,2)` | size[1]=1, size[2]=1 $\implies$ parent[2]=1, size[1]=2 | Component {1,2} |
| `union(2,3)` | find(2)=1 (size 2), find(3)=3 (size 1) $\implies$ parent[3]=1, size[1]=3 | Component {1,2,3} |
| `union(4,5)` | parent[5]=4, size[4]=2 | Component {4,5} |
| `union(6,7)` | parent[7]=6, size[6]=2 | Component {6,7} |
| `union(5,6)` | find(5)=4 (size 2), find(6)=6 (size 2) $\implies$ parent[6]=4, size[4]=4 | Component {4,5,6,7} |
| `union(3,7)` | find(3)=1 (size 3), find(7)=4 (size 4) $\implies$ parent[1]=4, size[4]=7 | Merged all into {4}: size 7 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $1$-indexed vs $0$-indexed node numbering (constructor size $N + 1$ handles both).
- Self-union `union(u, u)` (early returns false).

### Common Bugs to Avoid
- Forgetting path compression assignment `return parent[node] = find(parent[node])` (degrades to $\mathcal{O}(\log N)$ without compression).
- Mutating `rank` or `size` for non-root elements.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the Inverse Ackermann Function $\alpha(N)$ and why is it considered practically constant $\mathcal{O}(1)$?**  
  **A**: The Ackermann function $A(m, n)$ grows extremely rapidly ($A(4, 2) = 2^{65536}$, which exceeds the number of atoms in the observable universe!). Its inverse $\alpha(N)$ grows so slowly that for any conceivable physical number $N \le 10^{80}$, $\alpha(N) \le 4$. Therefore, amortized time is practically $\mathcal{O}(1)$!

- **Q2: Why is Union by Size generally preferred over Union by Rank in interview coding?**  
  **A**: Because many graph problems require querying the actual size of a connected component dynamically (e.g. Max Area of Island, Making A Large Island). `size[find(u)]` provides the component size in $\mathcal{O}(1)$ without extra state!

- **Q3: What happens if you use Path Compression WITHOUT Union by Rank/Size?**  
  **A**: Path compression alone yields $\mathcal{O}(N \log N)$ over $N$ operations. Union by rank alone yields $\mathcal{O}(\log N)$ per operation. COMBINING both yields optimal $\mathcal{O}(\alpha(N))$!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DSU`, `Disjoint Set Union`, `Path Compression`, `Data Structures`, `Medium`
- **Related problems to practice next**:
- **Kruskal's Algorithm**: MST with DSU.
- **Number of Provinces**: Connected components with DSU.
