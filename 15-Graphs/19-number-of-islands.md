# Number of Islands (Grid DFS/BFS) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Number of Islands (Grid DFS/BFS)](https://takeuforward.org/data-structure/find-the-number-of-islands-using-dsu/)
- **Difficulty**: Medium
- **Statement**: Given an $m \times n$ 2D binary grid `grid` which represents a map of `'1'`s (land) and `'0'`s (water), return the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically (4-directionally, or 8-directionally in GFG variant).

---

## 1. Problem, Restated

Count connected components of '1's in a 2D matrix using in-place land sinking DFS/BFS in $\mathcal{O}(N \times M)$ time.

- **Input**: `vector<vector<char>>& grid` binary matrix of size $M \times N$.
- **Output**: Integer count of connected islands.
- **Constraints**: $1 \le m, n \le 300$, `grid[i][j]` is `'0'` or `'1'`.

---

## 2. Intuition & Pattern

Every connected cluster of `'1'`s constitutes 1 island. 1) Loop over every cell $(i, j)$ in the matrix. 2) Whenever `grid[i][j] == '1'`: we found a new island $\implies$ increment `islands++` and launch `dfs(i, j)`. 3) In `dfs(r, c)`: **sink the land immediately** by mutating `grid[r][c] = '0'` (this prevents re-visiting without requiring an auxiliary `vis` matrix!). 4) Recurse on all 4 adjacent neighbors (or 8 for GFG). Runs in strictly linear $\mathcal{O}(N \times M)$ time.

- **Underlying Pattern**: `Grid Connected Components with In-Place Land Sinking / Disjoint Set Union`.

---

## 3. Approach 1 — Naive / Explicit Adjacency Graph

### Idea
Build explicit adjacency list of $(N \times M)$ nodes with edge conversions, then run graph BFS in $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
// Graph list conversion approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$ graph memory.
- **Why it's not good enough**: Unnecessary memory overhead when grid already represents implicit adjacency.

---

## 4. Approach 2 — Better

### Idea
Disjoint Set Union (DSU) uniting adjacent 1-cells dynamically in O(N x M x alpha(NM)) time.

### C++17 Code
```cpp
#include <vector>
#include <numeric>
using namespace std;

class DSUGrid {
    vector<int> parent;
    int count;
public:
    DSUGrid(int n) : parent(n), count(0) {
        iota(parent.begin(), parent.end(), 0);
    }
    void setCount(int c) { count = c; }
    int find(int i) { return parent[i] == i ? i : parent[i] = find(parent[i]); }
    void unite(int i, int j) {
        int rI = find(i), rJ = find(j);
        if (rI != rJ) { parent[rI] = rJ; count--; }
    }
    int getCount() const { return count; }
};

int numIslandsDSU(vector<vector<char>>& grid) {
    int n = grid.size(), m = grid[0].size();
    DSUGrid dsu(n * m);
    int ones = 0;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            if (grid[i][j] == '1') ones++;
    dsu.setCount(ones);
    
    int dRow[] = {0, 1};
    int dCol[] = {1, 0};
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (grid[i][j] == '1') {
                for (int d = 0; d < 2; d++) {
                    int ni = i + dRow[d], nj = j + dCol[d];
                    if (ni < n && nj < m && grid[ni][nj] == '1')
                        dsu.unite(i * m + j, ni * m + nj);
                }
            }
        }
    }
    return dsu.getCount();
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M \cdot \alpha(N \times M))$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$ DSU parent array.
- **Why it's still not optimal**: DSU is optimal for dynamic online cell additions (Number of Islands II).

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-Place Land Sinking DFS in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ recursion stack space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    void dfs(int r, int c, vector<vector<char>>& grid) {
        // Sink the current land cell to prevent re-visiting
        grid[r][c] = '0';
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        int n = grid.size();
        int m = grid[0].size();
        
        for (int i = 0; i < 4; i++) {
            int nr = r + dRow[i];
            int nc = c + dCol[i];
            
            // Recurse into valid un-sunk adjacent land cells
            if (nr >= 0 && nr < n && nc >= 0 && nc < m && grid[nr][nc] == '1') {
                dfs(nr, nc, grid);
            }
        }
    }

public:
    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size();
        if (n == 0) return 0;
        int m = grid[0].size();
        
        int islandCount = 0;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (grid[i][j] == '1') {
                    islandCount++;
                    dfs(i, j, grid); // Sinks the entire connected island
                }
            }
        }
        
        return islandCount;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each cell visited at most a constant number of times).
- **Space Complexity**: $\mathcal{O}(N \times M)$ worst-case call stack memory (when entire grid is land); $\mathcal{O}(1)$ auxiliary space without extra visited matrix.
- **Why this is optimal**: Direct grid mutation eliminates auxiliary memory allocations while preserving linear traversal.

---

## 6. Dry Run

`grid = [["1","1","0","0"],["1","1","0","0"],["0","0","1","0"],["0","0","0","1"]]`

| Step | Action / State Change | Result |
|---|---|---|
| `i=0, j=0 (grid[0][0]='1')` | islands = 1 -> dfs(0,0) sinks (0,0), (0,1), (1,0), (1,1) to '0' | Island 1 sunk |
| `i=2, j=2 (grid[2][2]='1')` | islands = 2 -> dfs(2,2) sinks (2,2) to '0' | Island 2 sunk |
| `i=3, j=3 (grid[3][3]='1')` | islands = 3 -> dfs(3,3) sinks (3,3) to '0' | Island 3 sunk |
| `Result` | Total Islands = 3 | Islands = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Grid contains only '0's (returns 0).
- Grid contains only '1's (returns 1).
- 1x1 grid.

### Common Bugs to Avoid
- Sinking land after exploring children rather than before (causes infinite recursive loops between adjacent land cells).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does mutating grid[r][c] = '0' work as an in-place visited marker?**  
  **A**: Because once a land cell is converted to water `'0'`, no future DFS or outer-loop iteration will ever trigger on it again. This completely eliminates the need for an $\mathcal{O}(N \times M)$ `vis` array!

- **Q2: How does the GFG variation (8-directional connectivity) differ?**  
  **A**: Expand `dRow` and `dCol` arrays to size 8 by including the 4 diagonal vectors `{-1, -1}, {-1, 1}, {1, -1}, {1, 1}`.

- **Q3: How does Number of Islands II (LeetCode 305) work dynamically?**  
  **A**: Number of Islands II adds land cells one by one via queries. Disjoint Set Union (DSU) is strictly required to unite components dynamically in $\mathcal{O}(K \cdot \alpha(N \times M))$ time without restarting DFS from scratch!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `BFS`, `Matrix`, `DSU`, `LeetCode-200`, `Medium`
- **Related problems to practice next**:
- **Max Area of Island**: Size of largest island.
- **Number of Distinct Islands**: Unique island shapes.
