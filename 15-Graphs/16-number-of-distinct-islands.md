# Number of Distinct Islands (DFS with shape serialization) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Number of Distinct Islands (DFS with shape serialization)](https://takeuforward.org/data-structure/number-of-distinct-islands/)
- **Difficulty**: Medium
- **Statement**: Given a boolean 2D matrix `grid` of size $n \times m$. Find the number of distinct islands where a group of connected 1s (4-directionally) forms an island. Two islands are considered identical if one island can be translated (shifted horizontally/vertically without rotation or reflection) to match the other.

---

## 1. Problem, Restated

Normalize island coordinate geometry relative to starting origin $(r_0, c_0)$ as `(r - r0, c - c0)` and insert into a hash set / `std::set` to count unique island topologies in $\mathcal{O}(N \times M \log K)$ time.

- **Input**: `vector<vector<int>>& grid` binary matrix of size $N \times M$.
- **Output**: Integer count of distinct translation-invariant island shapes.
- **Constraints**: $1 \le n, m \le 500$, `grid[i][j]` is 0 or 1.

---

## 2. Intuition & Pattern

Two islands have the same shape if their relative cell offsets from their top-left starting node $(r_0, c_0)$ are identical! 1) Outer loop over grid. Whenever `grid[i][j] == 1 && !vis[i][j]`: initialize `vector<pair<int, int>> shape`. 2) Call `dfs(i, j, i, j, shape)`. 3) At each visited cell $(r, c)$: record offset `shape.push_back({r - r0, c - c0})`. Explore 4 neighbors in a fixed deterministic order (`Up, Right, Down, Left`). 4) Insert `shape` into `set<vector<pair<int, int>>> distinctIslands`. 5) Return `distinctIslands.size()`!

- **Underlying Pattern**: `Coordinate Translation Normalization / Traversal Path Signature Hashing`.

---

## 3. Approach 1 — Naive / Pairwise Geometry Comparison

### Idea
Pairwise geometric intersection checks between all discovered islands in $\mathcal{O}(K^2 \times (N \times M))$ time.

### C++17 Code
```cpp
// O(K^2 * NM) pairwise comparison
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K^2 \times (N \times M))$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$ space.
- **Why it's not good enough**: Quadratic island comparison is slow when thousands of small islands exist.

---

## 4. Approach 2 — Better

### Idea
Direction Signature String Hashing (e.g. 'U', 'R', 'D', 'L' and 'B' for backtrack) in O(N x M) time.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <unordered_set>
using namespace std;

class SolutionStringHash {
    void dfs(int r, int c, vector<vector<int>>& grid, vector<vector<int>>& vis, string& path, char dir) {
        vis[r][c] = 1;
        path += dir;
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        char dChar[] = {'U', 'R', 'D', 'L'};
        for (int i = 0; i < 4; i++) {
            int nr = r + dRow[i], nc = c + dCol[i];
            if (nr >= 0 && nr < (int)grid.size() && nc >= 0 && nc < (int)grid[0].size() && !vis[nr][nc] && grid[nr][nc] == 1) {
                dfs(nr, nc, grid, vis, path, dChar[i]);
            }
        }
        path += 'B'; // Critical backtrack marker!
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(N \times M)$ space.
- **Why it's still not optimal**: Direction strings require explicit backtrack 'B' characters to avoid hash collisions on different shapes.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Relative Coordinate Vector Set Insertion in $\mathcal{O}(N \times M \log(K))$ time and $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
#include <vector>
#include <set>
using namespace std;

class Solution {
private:
    void dfs(int r, int c, vector<vector<int>>& grid, vector<vector<int>>& vis,
             vector<pair<int, int>>& shape, int r0, int c0) {
        vis[r][c] = 1;
        // Record relative coordinate offset from origin (r0, c0)
        shape.push_back({r - r0, c - c0});
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        int n = grid.size();
        int m = grid[0].size();
        
        // Explore 4 directions in fixed order
        for (int i = 0; i < 4; i++) {
            int nr = r + dRow[i];
            int nc = c + dCol[i];
            
            if (nr >= 0 && nr < n && nc >= 0 && nc < m && !vis[nr][nc] && grid[nr][nc] == 1) {
                dfs(nr, nc, grid, vis, shape, r0, c0);
            }
        }
    }

public:
    int countDistinctIslands(vector<vector<int>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        
        vector<vector<int>> vis(n, vector<int>(m, 0));
        set<vector<pair<int, int>>> distinctIslands;
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                if (!vis[i][j] && grid[i][j] == 1) {
                    vector<pair<int, int>> shape;
                    dfs(i, j, grid, vis, shape, i, j);
                    distinctIslands.insert(shape);
                }
            }
        }
        
        return distinctIslands.size();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M \log(K))$ time (where $K$ is the number of islands inserted into `std::set`).
- **Space Complexity**: $\mathcal{O}(N \times M)$ visited matrix and set storage.
- **Why this is optimal**: Deterministic relative vector offsets guarantee collision-free shape comparison under translation.

---

## 6. Dry Run

`grid = [[1,1,0,1,1],[1,0,0,0,0],[0,0,0,0,1],[1,1,0,1,1]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Island 1 (starts at (0,0))` | Cells: (0,0), (0,1), (1,0) -> Offsets: `[(0,0), (0,1), (1,0)]` | shape 1 saved |
| `Island 2 (starts at (0,3))` | Cells: (0,3), (0,4) -> Offsets: `[(0,0), (0,1)]` | shape 2 saved |
| `Island 3 (starts at (2,4))` | Cells: (2,4), (3,4) -> Offsets: `[(0,0), (1,0)]` | shape 3 saved |
| `Island 4 (starts at (3,0))` | Cells: (3,0), (3,1) -> Offsets: `[(0,0), (0,1)]` (matches Island 2!) | duplicate ignored by set |
| `Island 5 (starts at (3,3))` | Cells: (3,3), (3,4) -> Offsets: `[(0,0), (0,1)]` (matches Island 2!) | duplicate ignored |
| `Result` | Unique Shapes: `[(0,0),(0,1),(1,0)]`, `[(0,0),(0,1)]`, `[(0,0),(1,0)]` -> Count = 3 | Distinct Islands = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No 1s in grid (returns 0).
- All 1s form a single massive island (returns 1).

### Common Bugs to Avoid
- Using directional strings without appending backtrack 'B' characters (causes different shaped branches to produce identical strings).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is relative coordinate offset (r - r0, c - c0) translation invariant?**  
  **A**: Because translating an island shifts all its cells by $(\Delta r, \Delta c)$. Since the origin $(r_0, c_0)$ also shifts by $(\Delta r, \Delta c)$, the difference $(r + \Delta r) - (r_0 + \Delta r) = r - r_0$ remains perfectly invariant!

- **Q2: Why does DFS traversal order matter?**  
  **A**: Because DFS must visit children in the EXACT same deterministic direction order (e.g. `Up -> Right -> Down -> Left`) so that identical shapes produce identically ordered offset vectors.

- **Q3: How would we handle Rotations and Reflections (Number of Distinct Islands II)?**  
  **A**: For each island shape, generate all 8 dihedral transformations (4 rotations $\times$ 2 reflections), normalize each to origin $(0,0)$, sort points, pick the lexicographically smallest representation as the canonical key!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `Matrix`, `Set`, `Hash`, `Medium`
- **Related problems to practice next**:
- **Number of Islands**: Basic component counting.
- **Max Area of Island**: Island area calculation.
