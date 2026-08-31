# Number of Islands II (Online Dynamic Queries with DSU) (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Number of Islands II (Online Dynamic Queries with DSU)](https://takeuforward.org/data-structure/number-of-islands-ii-online-queries-dsu-g-51/)
- **Difficulty**: Hard
- **Statement**: You are given an $n \times m$ matrix initially filled with water (`0`). You receive $k$ online queries `operators` where each query `[r, c]` turns water at `(r, c)` into land. Return an array of size $k$ representing the number of connected 4-directional islands after each individual query.

---

## 1. Problem, Restated

Maintain connected land components dynamically over $k$ online land addition queries using 2D-to-1D coordinate flattening and DSU in $\mathcal{O}(k \cdot \alpha(N \times M))$ time.

- **Input**: `int n, int m`, `vector<vector<int>>& operators`.
- **Output**: `vector<int>` array of island counts after each query.
- **Constraints**: $1 \le n, m \le 1000$, $1 \le \text{operators.length} \le 10^5$.

---

## 2. Intuition & Pattern

Offline DFS/BFS after every single query takes $\mathcal{O}(k \times (n \times m))$ time, causing TLE! **Disjoint Set Union (DSU)** solves online dynamic queries in near $\mathcal{O}(1)$ time per query! 
1) Flatten 2D coordinates: `node = r * m + c` (domain $[0, n \times m - 1]$). 2) Maintain `vis[n][m]` to track created land and an active `islandCount = 0`. 3) For each query `[r, c]`: 
   - If `vis[r][c] == 1` (duplicate land query), append current `islandCount` and continue. 
   - Otherwise, mark `vis[r][c] = 1` and increment `islandCount++`. 
   - Check all 4 adjacent cells $(nr, nc)$: if neighbor is valid land (`vis[nr][nc] == 1`), compute `adjNode = nr * m + nc`. If `dsu.unionBySize(node, adjNode)` successfully merges two previously distinct island components, decrement `islandCount--`! 
   - Append `islandCount` to the result list. Runs in $\mathcal{O}(k \cdot \alpha(N \times M))$ time.

- **Underlying Pattern**: `2D-to-1D Coordinate Flattening (`r * m + c`) + Online Dynamic DSU Island Counter`.

---

## 3. Approach 1 — Naive / Per-Query Grid BFS

### Idea
Run full grid BFS/DFS after every land query in $\mathcal{O}(k \times (n \times m))$ time.

### C++17 Code
```cpp
// O(k * n * m) re-running full BFS on each query
```

### Java Code
```java
// Java equivalent
// O(k * n * m) re-running full BFS on each query
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(k \times (n \times m))$ time.
- **Space Complexity**: $\mathcal{O}(n \times m)$ matrix.
- **Why it's not good enough**: Redundant full matrix scans for each single pixel addition.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Online Dynamic DSU algorithm below directly achieves optimal $\mathcal{O}(k \cdot \alpha(N \times M))$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Online Dynamic DSU)

### Idea
Online Dynamic DSU with 2D Coordinate Flattening in $\mathcal{O}(k \cdot \alpha(N \times M))$ time and $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
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

class Solution {
public:
    vector<int> numOfIslands(int n, int m, vector<vector<int>>& operators) {
        DisjointSet dsu(n * m);
        vector<vector<int>> vis(n, vector<int>(m, 0));
        
        int islandCount = 0;
        vector<int> ans;
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        for (const auto& op : operators) {
            int r = op[0];
            int c = op[1];
            
            // Handle duplicate query on existing land
            if (vis[r][c] == 1) {
                ans.push_back(islandCount);
                continue;
            }
            
            vis[r][c] = 1;
            islandCount++; // Newly created isolated land
            
            int node = r * m + c;
            
            // Check 4-directional adjacent neighbors
            for (int i = 0; i < 4; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < m && vis[nr][nc] == 1) {
                    int adjNode = nr * m + nc;
                    
                    // If successfully united two different island components
                    if (dsu.unionBySize(node, adjNode)) {
                        islandCount--;
                    }
                }
            }
            
            ans.push_back(islandCount);
        }
        
        return ans;
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

class Solution {

    int[] numOfIslands(int n, int m, int[][] operators) {
        DisjointSet dsu(n * m);
        int[][] vis = new int[n][m];
        
        int islandCount = 0;
        int[] ans;
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        
        for (var op : operators) {
            int r = op[0];
            int c = op[1];
            
            // Handle duplicate query on existing land
            if (vis[r][c] == 1) {
                ans.add(islandCount);
                continue;
            }
            
            vis[r][c] = 1;
            islandCount++; // Newly created isolated land
            
            int node = r * m + c;
            
            // Check 4-directional adjacent neighbors
            for (int i = 0; i < 4; i++) {
                int nr = r + dRow[i];
                int nc = c + dCol[i];
                
                if (nr >= 0 && nr < n && nc >= 0 && nc < m && vis[nr][nc] == 1) {
                    int adjNode = nr * m + nc;
                    
                    // If successfully united two different island components
                    if (dsu.unionBySize(node, adjNode)) {
                        islandCount--;
                    }
                }
            }
            
            ans.add(islandCount);
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(k \cdot \alpha(N \times M))$ time (where $k = \text{operators.size()}$, 4 union calls per query take near $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(N \times M)$ DSU array and visited grid memory.
- **Why this is optimal**: Local 4-directional boundary unions update global component counts in $\mathcal{O}(1)$ without scanning the grid.

---

## 6. Dry Run

$n = 4, m = 5, \text{queries} = [[0,0],[0,0],[1,1],[1,0],[0,1]]$

| Step | Action / State Change | Result |
|---|---|---|
| `Query [0,0]` | vis[0][0]=1, count=1, 0 neighbors -> ans: `[1]` | Island 1 |
| `Query [0,0]` | vis[0][0]==1 (duplicate) -> ans: `[1, 1]` | Ignored duplicate |
| `Query [1,1]` | vis[1][1]=1, count=2, 0 neighbors -> ans: `[1, 1, 2]` | Island 2 |
| `Query [1,0]` | vis[1][0]=1, count=3. Neighbors: [0,0] united (count=2), [1,1] united (count=1) -> ans: `[1, 1, 2, 1]` | Merged 3 lands into 1! |
| `Query [0,1]` | vis[0][1]=1, count=2. Neighbors: [0,0] united, [1,1] united (already same root) -> count=1 -> ans: `[1, 1, 2, 1, 1]` | Final count = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Duplicate queries placing land on an already active land cell.
- Out of bounds checks.
- Single cell grid $1 \times 1$.

### Common Bugs to Avoid
- Incrementing `islandCount` on duplicate land queries without checking `vis[r][c] == 1`.
- Forgetting 2D to 1D index mapping formula `r * m + c`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does offline BFS fail the time limits for this problem?**  
  **A**: If there are $k = 10^5$ queries on a $1000 \times 1000$ grid ($10^6$ cells), full BFS takes $k \times (N \times M) = 10^{11}$ operations (TLE, taking several minutes). Online DSU executes in $10^5 \times 4 \times \alpha(10^6) \approx 1.6 \times 10^6$ operations (runs in ~20ms)!

- **Q2: What if the queries support REMOVING land (turning 1 to 0)?**  
  **A**: DSU naturally supports edge additions (unions), but DOES NOT support dynamic edge deletions! To handle land removals, we reverse the queries from last to first (time reversal) and treat removals as additions in backward order!

- **Q3: How to generalize this to 3D voxel grids?**  
  **A**: Map 3D coordinates $(x, y, z)$ to 1D via `x * (Y * Z) + y * Z + z` and check 6-directional spatial neighbors!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DSU`, `Disjoint Set Union`, `Online Queries`, `Grid`, `LeetCode-305`, `Hard`
- **Related problems to practice next**:
- **Number of Islands**: Static grid BFS/DFS.
- **Making A Large Island**: Max component merging.
