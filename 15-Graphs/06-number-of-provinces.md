# Number of Provinces (Connected Components in Disjoint Graph) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Number of Provinces (Connected Components in Disjoint Graph)](https://takeuforward.org/data-structure/number-of-provinces/)
- **Difficulty**: Medium
- **Statement**: There are $n$ cities. Some of them are connected, while some are not. If city `a` is connected directly with city `b`, and city `b` is connected directly with city `c`, then city `a` is connected indirectly with city `c`. A province is a group of directly or indirectly connected cities and no other cities outside of the group. Given an $n \times n$ matrix `isConnected` where `isConnected[i][j] = 1` if the $i^{\text{th}}$ city and $j^{\text{th}}$ city are directly connected, and `isConnected[i][j] = 0` otherwise, return the total number of provinces.

---

## 1. Problem, Restated

Count connected components in an undirected graph given as an adjacency matrix in $\mathcal{O}(V^2)$ time.

- **Input**: Parameters specified ($V$ vertices, grid $N \times M$, or adjacency matrix).
- **Output**: Value / count / modified matrix / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Each province is a connected component. 1) Maintain `vector<int> vis(n, 0)`. 2) Loop `for (int i = 0; i < n; i++)`: if `!vis[i]`, increment `provinces++` and call `dfs(i)`. 3) In `dfs(u)`: mark `vis[u] = 1`. For every city $v$ from 0 to $n-1$: if `isConnected[u][v] == 1 && !vis[v]`, recursively call `dfs(v)`. Runs in $\mathcal{O}(V^2)$ time.

- **Underlying Pattern**: `Connected Components Counting with Matrix DFS Traversal / Disjoint Set Union`.

---

## 3. Approach 1 — Naive / Matrix Search

### Idea
Build explicit adjacency list first, then run DFS.

### C++17 Code
```cpp
// List conversion + DFS
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2)$.
- **Space Complexity**: $\mathcal{O}(V^2)$.
- **Why it's not good enough**: Allocates extra memory for list.

---

## 4. Approach 2 — Better

### Idea
Disjoint Set Union (DSU) uniting cities with isConnected[i][j] == 1 in O(V^2 alpha(V)) time and O(V) space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;
class DSU {
    vector<int> parent;
    int count;
public:
    DSU(int n) : parent(n), count(n) { iota(parent.begin(), parent.end(), 0); }
    int find(int i) { return parent[i] == i ? i : parent[i] = find(parent[i]); }
    void unite(int i, int j) {
        int rootI = find(i), rootJ = find(j);
        if (rootI != rootJ) { parent[rootI] = rootJ; count--; }
    }
    int getCount() const { return count; }
};
int findCircleNumDSU(vector<vector<int>>& isConnected) {
    int n = isConnected.size();
    DSU dsu(n);
    for (int i = 0; i < n; i++)
        for (int j = i + 1; j < n; j++)
            if (isConnected[i][j]) dsu.unite(i, j);
    return dsu.getCount();
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2 \cdot \alpha(V))$ time.
- **Space Complexity**: $\mathcal{O}(V)$ DSU parent array.
- **Why it's still not optimal**: DSU is optimal for dynamic edge additions.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Direct Adjacency Matrix DFS in $\mathcal{O}(V^2)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    void dfs(int node, const vector<vector<int>>& isConnected, vector<int>& vis) {
        vis[node] = 1;
        
        for (int neighbor = 0; neighbor < (int)isConnected.size(); neighbor++) {
            if (isConnected[node][neighbor] == 1 && !vis[neighbor]) {
                dfs(neighbor, isConnected, vis);
            }
        }
    }

public:
    int findCircleNum(vector<vector<int>>& isConnected) {
        int n = isConnected.size();
        vector<int> vis(n, 0);
        int provinces = 0;
        
        for (int i = 0; i < n; i++) {
            if (!vis[i]) {
                provinces++;
                dfs(i, isConnected, vis);
            }
        }
        
        return provinces;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2)$ time (each cell of the $N \times N$ matrix inspected once).
- **Space Complexity**: $\mathcal{O}(V)$ recursion call stack and visited array.
- **Why this is optimal**: Traverses matrix directly without redundant auxiliary graph structure allocations.

---

## 6. Dry Run

`isConnected = [[1,1,0],[1,1,0],[0,0,1]]` ($N = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `i = 0` | vis[0]=0 -> provinces=1 -> dfs(0) visits 1 -> vis: `[1, 1, 0]` | Province 1: {0, 1} |
| `i = 1` | vis[1]=1 -> already visited, skip | skipped |
| `i = 2` | vis[2]=0 -> provinces=2 -> dfs(2) visits 2 -> vis: `[1, 1, 1]` | Province 2: {2} |
| `Result` | Total Provinces = 2 | Provinces = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 1).
- Identity matrix (all cities disconnected $\implies N$ provinces).

### Common Bugs to Avoid
- Setting loop bound `neighbor = node + 1` in DFS (misses edges from smaller indices if graph was asymmetric).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the time complexity O(V^2) and not O(V + E)?**  
  **A**: Because the graph is given as an $N \times N$ adjacency matrix. To find all neighbors of vertex $u$, we must scan all $N$ entries in row $u$. For all $N$ vertices, total checks $= N \times N = \mathcal{O}(V^2)$.

- **Q2: How does DSU compare to DFS for this problem?**  
  **A**: Both achieve $\mathcal{O}(V^2)$ time. DSU is preferable if edges are streamed online one by one, while DFS is simpler to write and has lower constant factor overhead for a static matrix.

- **Q3: Can BFS be used instead of DFS?**  
  **A**: Yes! Replace `dfs(i)` with `bfs(i)` using a `queue<int>`. Time and space complexities are completely identical.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `Connected Components`, `DSU`, `LeetCode-547`, `Medium`
- **Related problems to practice next**:
- **Connected Components**: General graph components.
- **Number of Islands**: Grid components.
