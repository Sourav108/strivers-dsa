# Flood Fill Algorithm (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Flood Fill Algorithm](https://takeuforward.org/data-structure/flood-fill-algorithm/)
- **Difficulty**: Easy
- **Statement**: An image is represented by an $m \times n$ integer grid `image` where `image[i][j]` represents the pixel value of the image. You are also given three integers `sr`, `sc`, and `color`. You should perform a flood fill on the image starting from the pixel `image[sr][sc]`. To perform a flood fill, consider the starting pixel, plus any pixels connected 4-directionally to the starting pixel of the same color, and so on. Replace the color of all of the aforementioned pixels with `color`. Return the modified image.

---

## 1. Problem, Restated

Connected component color replacement using DFS/BFS: if `image[sr][sc] == color`, return immediately; else recolor all 4-way adjacent pixels sharing `initialColor`.

- **Input**: Parameters specified ($V$ vertices, grid $N \times M$, or adjacency matrix).
- **Output**: Value / count / modified matrix / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Record `initialColor = image[sr][sc]`. 2) If `initialColor == color`, no change needed $\implies$ return `image` (avoids infinite recursion!). 3) Run `dfs(r, c)`: recolor `image[r][c] = color`. 4) For each 4-directional neighbor $(nr, nc)$: if within bounds and `image[nr][nc] == initialColor`, recursively call `dfs(nr, nc)`. Runs in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

- **Underlying Pattern**: `Grid DFS Component Color In-Place Mutation`.

---

## 3. Approach 1 — Naive / Matrix Search

### Idea
Queue BFS storing coordinate pairs.

### C++17 Code
```cpp
// BFS Flood Fill
```

### Java Code
```java
// Java equivalent
// BFS Flood Fill
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$.
- **Space Complexity**: $\mathcal{O}(N \times M)$.
- **Why it's not good enough**: BFS and DFS have identical bounds.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS/DFS / DSU algorithm below directly achieves optimal $\mathcal{O}(V + E)$ or $\mathcal{O}(N \times M)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
In-Place Recursive DFS in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(N \times M)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    void dfs(int r, int c, vector<vector<int>>& image, int initialColor, int newColor) {
        // Change current pixel color
        image[r][c] = newColor;
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        int n = image.size();
        int m = image[0].size();
        
        for (int i = 0; i < 4; i++) {
            int nr = r + dRow[i];
            int nc = c + dCol[i];
            
            // Check grid bounds and matching initial color
            if (nr >= 0 && nr < n && nc >= 0 && nc < m && image[nr][nc] == initialColor) {
                dfs(nr, nc, image, initialColor, newColor);
            }
        }
    }

public:
    vector<vector<int>> floodFill(vector<vector<int>>& image, int sr, int sc, int color) {
        int initialColor = image[sr][sc];
        
        // Critical edge case: if starting pixel already has the new color, return immediately
        if (initialColor != color) {
            dfs(sr, sc, image, initialColor, color);
        }
        
        return image;
    }
};
```

### Java Code
```java
class Solution {

    void dfs(int r, int c, int[][] image, int initialColor, int newColor) {
        // Change current pixel color
        image[r][c] = newColor;
        
        int dRow[] = {-1, 0, 1, 0};
        int dCol[] = {0, 1, 0, -1};
        int n = image.length;
        int m = image[0].size();
        
        for (int i = 0; i < 4; i++) {
            int nr = r + dRow[i];
            int nc = c + dCol[i];
            
            // Check grid bounds and matching initial color
            if (nr >= 0 && nr < n && nc >= 0 && nc < m && image[nr][nc] == initialColor) {
                dfs(nr, nc, image, initialColor, newColor);
            }
        }
    }

    int[][] floodFill(int[][] image, int sr, int sc, int color) {
        int initialColor = image[sr][sc];
        
        // Critical edge case: if starting pixel already has the new color, return immediately
        if (initialColor != color) {
            dfs(sr, sc, image, initialColor, color);
        }
        
        return image;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time (each connected pixel visited once).
- **Space Complexity**: $\mathcal{O}(N \times M)$ recursion call stack space in worst case (full image single color).
- **Why this is optimal**: Recoloring directly in-place acts as its own visited array, eliminating extra memory.

---

## 6. Dry Run

`image = [[1,1,1],[1,1,0],[1,0,1]]`, $sr=1, sc=1, color=2$

| Step | Action / State Change | Result |
|---|---|---|
| `Start at (1,1)` | initialColor = 1, newColor = 2 -> recolor (1,1) to 2 | image[1][1] = 2 |
| `Recurse neighbors` | Dives to (0,1), (0,0), (0,2), (1,0), (2,0) | all connected 1s turned to 2 |
| `Boundary check` | 0s at (1,2) and (2,1) block expansion to bottom-right (2,2) | image[2][2] remains 1 |
| `Result` | `[[2,2,2],[2,2,0],[2,0,1]]` | Flood Fill Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `image[sr][sc] == color` (must return immediately to avoid infinite recursion!).
- $1 \times 1$ grid.

### Common Bugs to Avoid
- Forgetting `initialColor != color` check (causes stack overflow on cycles because recoloring never stops matching!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is checking initialColor != color critical before launching DFS?**  
  **A**: Because if `image[sr][sc] == color`, setting `image[r][c] = color` does NOT change its value. The neighbor condition `image[nr][nc] == initialColor` will remain true endlessly, creating infinite recursive cycles and crashing with Stack Overflow!

- **Q2: How is Flood Fill implemented in paint applications (e.g. MS Paint bucket tool)?**  
  **A**: Paint bucket tools typically use **Scanline Flood Fill** (a BFS/Stack variant that fills horizontal spans of pixels at once) to minimize stack frame allocations.

- **Q3: Can 8-directional flood fill be supported?**  
  **A**: Yes! Expand direction arrays `dRow` and `dCol` to length 8 including diagonals `{-1, -1}, {-1, 1}, {1, -1}, {1, 1}`.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `BFS`, `Matrix`, `LeetCode-733`, `Easy`
- **Related problems to practice next**:
- **Number of Islands**: Grid islands.
- **Surrounded Regions**: Boundary flood fill.
