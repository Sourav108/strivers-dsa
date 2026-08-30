# Making a Large Island (Flip 0 to 1 with DSU Size Lookup) (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Making a Large Island (Flip 0 to 1 with DSU Size Lookup)](https://takeuforward.org/data-structure/making-a-large-island-dsu-g-52/)
- **Difficulty**: Hard
- **Statement**: You are given an $n \times n$ binary matrix `grid`. You are allowed to flip at most one `0` to `1`. Return the maximum size of an island in the grid after applying this operation. An island is a 4-directionally connected group of `1`s.

---

## 1. Problem, Restated

Build connected land components with DSU (union-by-size). Simulate flipping each `0` to `1` by bridging unique adjacent component roots and summing their sizes $+ 1$ in $\mathcal{O}(N^2)$ time.

- **Input**: `vector<vector<int>>& grid` of size $N \times N$.
- **Output**: Integer representing maximum island area after flipping at most one 0 to 1.
- **Constraints**: $1 \le n \le 500$, `grid[i][j]` is either `0` or `1`.

---

## 2. Intuition & Pattern

1) **Phase 1: Build Initial Island Components**: 
   - Flatten 2D coordinates: `node = r * n + c`. 
   - Initialize `DisjointSet dsu(n * n)`. 
   - For every land cell `grid[r][c] == 1`, connect with all 4-directional adjacent land cells `grid[nr][nc] == 1` using `dsu.unionBySize(node, adjNode)`.
2) **Phase 2: Evaluate All 0-Flips**: 
   - For every water cell `grid[r][c] == 0`: look at its 4 orthogonal neighbors. 
   - Collect their **ultimate parents (roots)** in an `unordered_set<int> uniqueRoots` to avoid double-counting the same island from multiple sides! 
   - Calculate potential size: $1 + \sum_{root \in uniqueRoots} dsu.size[root]$. 
   - Track `maxIsland = max(maxIsland, totalSize)`.
3) **Phase 3: All-1 Grid Edge Case**: If `grid` contains no `0`s (all 1s), `maxIsland = dsu.size[dsu.findUPar(0)] = n * n`. Runs in $\mathcal{O}(N^2 \cdot \alpha(N^2))$ time.

- **Underlying Pattern**: `Disjoint Set Union (Union by Size) + Neighbor Component Root Deduplication`.

---

## 3. Approach 1 — Naive / Per-Zero BFS Exploration

### Idea
For every `0`, flip it to `1`, run a full matrix BFS/DFS to measure the resulting island, and flip it back in $\mathcal{O}(N^4)$ time.

### C++17 Code
```cpp
// O(N^4) naive BFS per cell flip
```

### Java Code
```java
// Java equivalent
// O(N^4) naive BFS per cell flip
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^4)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ space.
- **Why it's not good enough**: $N^2$ water cells $\times \mathcal{O}(N^2)$ per BFS traversal.

---

## 4. Approach 2 — Better (Two-Pass Island Coloring with Hash Map)

### Idea
Two-Pass Island Coloring with Component Size Hash Map in O(N^2) time.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <algorithm>
using namespace std;

class SolutionColoring {
    int dfs(int r, int c, int id, vector<vector<int>>& grid, int n) {
        grid[r][c] = id;
        int size = 1;
        int dr[] = {-1, 0, 1, 0}, dc[] = {0, 1, 0, -1};
        for (int i = 0; i < 4; i++) {
            int nr = r + dr[i], nc = c + dc[i];
            if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                size += dfs(nr, nc, id, grid, n);
            }
        }
        return size;
    }
public:
    int largestIsland(vector<vector<int>>& grid) {
        int n = grid.size();
        unordered_map<int, int> islandSize;
        int colorId = 2, maxIsland = 0;
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 1) {
                    islandSize[colorId] = dfs(r, c, colorId, grid, n);
                    maxIsland = max(maxIsland, islandSize[colorId]);
                    colorId++;
                }
            }
        }
        int dr[] = {-1, 0, 1, 0}, dc[] = {0, 1, 0, -1};
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 0) {
                    unordered_set<int> seen;
                    int cur = 1;
                    for (int i = 0; i < 4; i++) {
                        int nr = r + dr[i], nc = c + dc[i];
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] > 1) {
                            int id = grid[nr][nc];
                            if (seen.insert(id).second) cur += islandSize[id];
                        }
                    }
                    maxIsland = max(maxIsland, cur);
                }
            }
        }
        return maxIsland;
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionColoring {
    int dfs(int r, int c, int id, int[][] grid, int n) {
        grid[r][c] = id;
        int size = 1;
        int dr[] = {-1, 0, 1, 0}, dc[] = {0, 1, 0, -1};
        for (int i = 0; i < 4; i++) {
            int nr = r + dr[i], nc = c + dc[i];
            if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                size += dfs(nr, nc, id, grid, n);
            }
        }
        return size;
    }

    int largestIsland(int[][] grid) {
        int n = grid.length;
        Map<Integer, Integer> islandSize = new HashMap<>();
        int colorId = 2, maxIsland = 0;
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 1) {
                    islandSize[colorId] = dfs(r, c, colorId, grid, n);
                    maxIsland = Math.max(maxIsland, islandSize[colorId]);
                    colorId++;
                }
            }
        }
        int dr[] = {-1, 0, 1, 0}, dc[] = {0, 1, 0, -1};
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 0) {
                    Set<Integer> seen = new HashSet<>();
                    int cur = 1;
                    for (int i = 0; i < 4; i++) {
                        int nr = r + dr[i], nc = c + dc[i];
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] > 1) {
                            int id = grid[nr][nc];
                            if (seen.add(id).second) cur += islandSize[id];
                        }
                    }
                    maxIsland = Math.max(maxIsland, cur);
                }
            }
        }
        return maxIsland;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ space.
- **Why it's still not optimal**: Coloring array and DSU both run in optimal $\mathcal{O}(N^2)$ time; DSU is naturally extensible for dynamic online queries.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (DSU Union by Size)

### Idea
Disjoint Set Union (DSU) Union by Size with Hash Set Deduplication in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N^2)$ space.

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
        parent.resize(n);
        size.resize(n, 1);
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
    int largestIsland(vector<vector<int>>& grid) {
        int n = grid.size();
        DisjointSet dsu(n * n);
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        // 1. Step 1: Connect all existing 1s into DSU components
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 1) {
                    int node = r * n + c;
                    
                    for (int i = 0; i < 4; i++) {
                        int nr = r + dRow[i];
                        int nc = c + dCol[i];
                        
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                            int adjNode = nr * n + nc;
                            dsu.unionBySize(node, adjNode);
                        }
                    }
                }
            }
        }
        
        // 2. Step 2: Try flipping each 0 to 1 and bridge surrounding components
        int maxIsland = 0;
        bool hasZero = false;
        
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 0) {
                    hasZero = true;
                    unordered_set<int> uniqueRoots;
                    
                    // Check 4 adjacent directions
                    for (int i = 0; i < 4; i++) {
                        int nr = r + dRow[i];
                        int nc = c + dCol[i];
                        
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                            int adjNode = nr * n + nc;
                            uniqueRoots.insert(dsu.findUPar(adjNode));
                        }
                    }
                    
                    // Flipped cell itself counts as size 1
                    int currentTotal = 1;
                    for (int root : uniqueRoots) {
                        currentTotal += dsu.size[root];
                    }
                    
                    maxIsland = max(maxIsland, currentTotal);
                }
            }
        }
        
        // 3. Step 3: Handle edge case where grid is all 1s
        if (!hasZero) {
            return n * n;
        }
        
        return maxIsland;
    }
};
```

### Java Code
```java
import java.util.*;

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

    int largestIsland(int[][] grid) {
        int n = grid.length;
        DisjointSet dsu(n * n);
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        // 1. Step 1: Connect all existing 1s into DSU components
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 1) {
                    int node = r * n + c;
                    
                    for (int i = 0; i < 4; i++) {
                        int nr = r + dRow[i];
                        int nc = c + dCol[i];
                        
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                            int adjNode = nr * n + nc;
                            dsu.unionBySize(node, adjNode);
                        }
                    }
                }
            }
        }
        
        // 2. Step 2: Try flipping each 0 to 1 and bridge surrounding components
        int maxIsland = 0;
        boolean hasZero = false;
        
        for (int r = 0; r < n; r++) {
            for (int c = 0; c < n; c++) {
                if (grid[r][c] == 0) {
                    hasZero = true;
                    Set<Integer> uniqueRoots = new HashSet<>();
                    
                    // Check 4 adjacent directions
                    for (int i = 0; i < 4; i++) {
                        int nr = r + dRow[i];
                        int nc = c + dCol[i];
                        
                        if (nr >= 0 && nr < n && nc >= 0 && nc < n && grid[nr][nc] == 1) {
                            int adjNode = nr * n + nc;
                            uniqueRoots.add(dsu.findUPar(adjNode));
                        }
                    }
                    
                    // Flipped cell itself counts as size 1
                    int currentTotal = 1;
                    for (int root : uniqueRoots) {
                        currentTotal += dsu.size[root];
                    }
                    
                    maxIsland = Math.max(maxIsland, currentTotal);
                }
            }
        }
        
        // 3. Step 3: Handle edge case where grid is all 1s
        if (hasZero == null) {
            return n * n;
        }
        
        return maxIsland;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (two passes over $N \times N$ cells with $\mathcal{O}(\alpha(N^2)) \approx \mathcal{O}(1)$ DSU queries).
- **Space Complexity**: $\mathcal{O}(N^2)$ DSU parent and size arrays.
- **Why this is optimal**: Precomputing connected island sizes via DSU union-by-size enables instant $\mathcal{O}(1)$ bridging evaluations.

---

## 6. Dry Run

`grid = [[1, 0], [0, 1]]` ($N = 2$)

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | dsu size: node 0 `(0,0)` size 1, node 3 `(1,1)` size 1 | 2 separate islands |
| `Evaluate [0,1]` | Neighbors: `(0,0)` (root 0, size 1), `(1,1)` (root 3, size 1) $\implies$ total = $1 + 1 + 1 = 3$ | max = 3 |
| `Evaluate [1,0]` | Neighbors: `(0,0)` (root 0, size 1), `(1,1)` (root 3, size 1) $\implies$ total = $1 + 1 + 1 = 3$ | max = 3 |
| `Result` | Max Island = 3 (Flipping either water cell connects both lands) | Size = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Grid is 100% water (all 0s $\implies$ returns 1).
- Grid is 100% land (all 1s $\implies$ returns $n^2$).
- $N = 1$.

### Common Bugs to Avoid
- Double-counting the same island when two adjacent sides connect to the SAME component (prevented by `unordered_set<int> uniqueRoots`).
- Forgetting the all-1s edge case.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is 'unordered_set<int> uniqueRoots' strictly required when evaluating 0-cells?**  
  **A**: Consider a U-shaped island wrapping around a `0` cell. That single island touches the `0` cell from both the top and the left! If we simply summed `dsu.size[adjNode]`, that island's size would be added TWICE! The set stores root IDs, ensuring each distinct component is counted at most once!

- **Q2: How does Union by Size make this $\mathcal{O}(1)$ per neighbor query?**  
  **A**: Union by size continuously tracks the size of the root component in `dsu.size[root]`. We do not have to perform any subtree traversals to count elements!

- **Q3: What if we could flip K zeros instead of 1?**  
  **A**: For $k=1$, local boundary evaluation is $\mathcal{O}(N^2)$. For $k > 1$, this problem becomes NP-Hard (Maximum Weight Subgraph), requiring heuristic search or branch-and-bound integer programming.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DSU`, `Disjoint Set Union`, `Grid`, `LeetCode-827`, `Hard`
- **Related problems to practice next**:
- **Number of Islands II**: Online dynamic DSU.
- **Max Area of Island**: Static component sizing.
