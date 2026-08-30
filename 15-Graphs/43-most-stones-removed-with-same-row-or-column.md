# Most Stones Removed with Same Row or Column (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Most Stones Removed with Same Row or Column](https://takeuforward.org/data-structure/most-stones-removed-with-same-row-or-column-dsu-g-53/)
- **Difficulty**: Medium
- **Statement**: On a 2D plane, $n$ stones are placed at integer coordinates `stones[i] = [r, c]`. A stone can be removed if it shares either the same row or the same column with another stone that has not been removed. Return the maximum possible number of stones that can be removed.

---

## 1. Problem, Restated

In every connected component of stones (sharing row or column), all stones except ONE can be sequentially removed. Total stones removed is strictly $N - C$ (where $N = \text{total stones}$, $C = \text{number of connected components}$).

- **Input**: `vector<vector<int>>& stones` array of coordinates `[r, c]`.
- **Output**: Integer representing maximum number of stones that can be removed.
- **Constraints**: $1 \le \text{stones.length} \le 1000$, $0 \le r_i, c_i \le 10^4$.

---

## 2. Intuition & Pattern

**Connected Component Theorem**: For any connected cluster of $K$ stones sharing rows or columns, we can always choose a spanning tree of removals, removing $K - 1$ stones and leaving exactly 1 representative stone intact! Across all $C$ disconnected components: $\text{Max Removals} = \sum (K_i - 1) = \sum K_i - C = N - C$! 
**Clever DSU Modeling**: Treat each Row $r$ and Column $c$ as graph nodes! Since row $r \in [0, \text{maxRow}]$ and col $c \in [0, \text{maxCol}]$, shift column index by `colNode = c + maxRow + 1` to prevent collisions! 1) For each stone `[r, c]`: unite row node $r$ and column node `c + maxRow + 1`. Record both nodes in `unordered_set<int> stoneNodes`. 2) Count distinct component roots: `for (node : stoneNodes) if (dsu.find(node) == node) C++`. 3) Return $N - C$ in $\mathcal{O}(N \cdot \alpha(R + C))$ time.

- **Underlying Pattern**: `Coordinate Mapping DSU (Row & Column Fusion) / Connected Component Invariant`.

---

## 3. Approach 1 — Naive / Pairwise Stone Graph

### Idea
Build $N \times N$ graph connecting any pair of stones sharing row or column in $\mathcal{O}(N^2)$ time, then run DFS.

### C++17 Code
```cpp
// O(N^2) pairwise stone graph construction + DFS
```

### Java Code
```java
// Java equivalent
// O(N^2) pairwise stone graph construction + DFS
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ graph space.
- **Why it's not good enough**: Quadratic pair comparisons for $N$ stones.

---

## 4. Approach 2 — Better (Row-Col Hash Maps with DFS)

### Idea
Hash Map Adjacency Lists grouping stones by row and column with DFS in O(N) time.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
using namespace std;

class SolutionDFS {
    void dfs(int u, const vector<vector<int>>& adj, vector<int>& vis) {
        vis[u] = 1;
        for (int v : adj[u]) if (!vis[v]) dfs(v, adj, vis);
    }
public:
    int removeStones(vector<vector<int>>& stones) {
        int n = stones.size();
        unordered_map<int, vector<int>> rowMap, colMap;
        for (int i = 0; i < n; i++) {
            rowMap[stones[i][0]].push_back(i);
            colMap[stones[i][1]].push_back(i);
        }
        vector<vector<int>> adj(n);
        for (auto& [r, list] : rowMap)
            for (int i = 1; i < (int)list.size(); i++) {
                adj[list[0]].push_back(list[i]);
                adj[list[i]].push_back(list[0]);
            }
        for (auto& [c, list] : colMap)
            for (int i = 1; i < (int)list.size(); i++) {
                adj[list[0]].push_back(list[i]);
                adj[list[i]].push_back(list[0]);
            }
        vector<int> vis(n, 0);
        int components = 0;
        for (int i = 0; i < n; i++)
            if (!vis[i]) { components++; dfs(i, adj, vis); }
        return n - components;
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionDFS {
    void dfs(int u, int[][] adj, int[] vis) {
        vis[u] = 1;
        for (int v : adj[u]) if (!vis[v]) dfs(v, adj, vis);
    }

    int removeStones(int[][] stones) {
        int n = stones.length;
        unordered_map<int, int[]> rowMap, colMap;
        for (int i = 0; i < n; i++) {
            rowMap[stones[i][0]].add(i);
            colMap[stones[i][1]].add(i);
        }
        int[][] adj(n);
        for (var [r, list] : rowMap)
            for (int i = 1; i < list.length; i++) {
                adj[list[0]].add(list[i]);
                adj[list[i]].add(list[0]);
            }
        for (var [c, list] : colMap)
            for (int i = 1; i < list.length; i++) {
                adj[list[0]].add(list[i]);
                adj[list[i]].add(list[0]);
            }
        int[] vis = new int[n];
        int components = 0;
        for (int i = 0; i < n; i++)
            if (!vis[i]) { components++; dfs(i, adj, vis); }
        return n - components;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: DFS requires building adjacency lists and recursion stacks.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Row-Column DSU Node Fusion)

### Idea
Row-Column DSU Node Fusion in $\mathcal{O}(N \cdot \alpha(R + C))$ time and $\mathcal{O}(R + C)$ space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
#include <unordered_set>
#include <algorithm>
using namespace std;

class DisjointSet {
public:
    vector<int> parent, size;
    DisjointSet(int n) {
        parent.resize(n + 1);
        size.resize(n + 1, 1);
        iota(parent.begin(), parent.end(), 0);
    }
    int findUPar(int node) {
        if (node == parent[node]) return node;
        return parent[node] = findUPar(parent[node]);
    }
    void unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        if (rootU == rootV) return;
        if (size[rootU] < size[rootV]) {
            parent[rootU] = rootV;
            size[rootV] += size[rootU];
        } else {
            parent[rootV] = rootU;
            size[rootU] += size[rootV];
        }
    }
};

class Solution {
public:
    int removeStones(vector<vector<int>>& stones) {
        int n = stones.size();
        int maxRow = 0, maxCol = 0;
        
        for (const auto& stone : stones) {
            maxRow = max(maxRow, stone[0]);
            maxCol = max(maxCol, stone[1]);
        }
        
        // Total nodes: (maxRow + 1) rows + (maxCol + 1) columns
        DisjointSet dsu(maxRow + maxCol + 1);
        unordered_set<int> stoneNodes;
        
        for (const auto& stone : stones) {
            int nodeRow = stone[0];
            int nodeCol = stone[1] + maxRow + 1; // Shift column index
            
            dsu.unionBySize(nodeRow, nodeCol);
            stoneNodes.insert(nodeRow);
            stoneNodes.insert(nodeCol);
        }
        
        // Count number of connected components
        int components = 0;
        for (int node : stoneNodes) {
            if (dsu.findUPar(node) == node) {
                components++;
            }
        }
        
        // Answer = Total Stones - Connected Components
        return n - components;
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
    void unionBySize(int u, int v) {
        int rootU = findUPar(u);
        int rootV = findUPar(v);
        if (rootU == rootV) return;
        if (size[rootU] < size[rootV]) {
            parent[rootU] = rootV;
            size[rootV] += size[rootU];
        } else {
            parent[rootV] = rootU;
            size[rootU] += size[rootV];
        }
    }
};

class Solution {

    int removeStones(int[][] stones) {
        int n = stones.length;
        int maxRow = 0, maxCol = 0;
        
        for (var stone : stones) {
            maxRow = Math.max(maxRow, stone[0]);
            maxCol = Math.max(maxCol, stone[1]);
        }
        
        // Total nodes: (maxRow + 1) rows + (maxCol + 1) columns
        DisjointSet dsu(maxRow + maxCol + 1);
        Set<Integer> stoneNodes = new HashSet<>();
        
        for (var stone : stones) {
            int nodeRow = stone[0];
            int nodeCol = stone[1] + maxRow + 1; // Shift column index
            
            dsu.unionBySize(nodeRow, nodeCol);
            stoneNodes.add(nodeRow);
            stoneNodes.add(nodeCol);
        }
        
        // Count number of connected components
        int components = 0;
        for (int node : stoneNodes) {
            if (dsu.findUPar(node) == node) {
                components++;
            }
        }
        
        // Answer = Total Stones - Connected Components
        return n - components;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot \alpha(R + C))$ time (near linear $\mathcal{O}(N)$).
- **Space Complexity**: $\mathcal{O}(R + C)$ DSU parent array and hash set memory.
- **Why this is optimal**: Fusing row and column coordinates directly models bipartite grid connectivity in optimal $\mathcal{O}(1)$ amortized operations.

---

## 6. Dry Run

`stones = [[0,0],[0,1],[1,0],[1,2],[2,1],[2,2]]` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `maxRow=2, maxCol=2` | Col offset = $2 + 1 = 3$. Columns mapped to: col0->3, col1->4, col2->5 | Offset = 3 |
| `Unions` | unite(0,3), unite(0,4), unite(1,3), unite(1,5), unite(2,4), unite(2,5) | All rows/cols merged! |
| `Component Count` | All nodes $\{0,1,2,3,4,5\}$ share the same root $\implies$ Components $C = 1$ | $C = 1$ |
| `Result` | Removals = $N - C = 6 - 1 = 5$ | Removed = 5 ✅ (Leaves 1 stone) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 0).
- All stones in separate rows and cols (returns 0).
- All stones in same row.

### Common Bugs to Avoid
- Counting non-existent empty rows/cols as components (fixed by iterating strictly over `stoneNodes` set).
- Not shifting column indices (causes row $x$ to incorrectly collide with column $x$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the formula 'Answer = Total Stones - Number of Components' hold universally?**  
  **A**: In any connected graph of $K$ vertices, we can construct a spanning tree. Leaf nodes can always be removed one by one without disconnecting the rest of the tree until only the root node remains (1 stone left, $K - 1$ removed). Across $C$ components, $\sum (K_i - 1) = N - C$!

- **Q2: Why do we treat rows and columns as vertices instead of individual stones?**  
  **A**: If we treat stones as vertices, we must compare all $N^2$ pairs. By treating rows $r$ and columns $c$ as vertices and each stone $(r, c)$ as a directed/undirected edge between $r$ and $c$, we directly unite components in $\mathcal{O}(N)$ without pairwise comparisons!

- **Q3: How to handle sparse 64-bit coordinates up to 10^9?**  
  **A**: Use `unordered_map<int, int> parent` inside DSU instead of a flat vector to support coordinates up to $10^9$ with $\mathcal{O}(N)$ memory!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DSU`, `Disjoint Set Union`, `Connected Components`, `LeetCode-947`, `Medium`
- **Related problems to practice next**:
- **Number of Operations Network**: Spanning tree DSU.
- **Number of Provinces**: Component counting.
