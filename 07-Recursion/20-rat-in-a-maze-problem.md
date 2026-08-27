# Rat in a Maze Problem (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Rat in a Maze Problem](https://takeuforward.org/data-structure/rat-in-a-maze/)
- **Difficulty**: Medium
- **Statement**: Consider a rat placed at $(0, 0)$ in a square matrix `mat` of order $N \times N$. Find all sorted path directions (`'D'`, `'L'`, `'R'`, `'U'`) for the rat to reach $(N-1, N-1)$ where `1` represents an open cell and `0` represents a blocked cell.

---

## 1. Problem, Restated

Find all non-intersecting grid paths from $(0, 0)$ to $(N-1, N-1)$ in lexicographical order (`D -> L -> R -> U`).

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Explore directions in exact alphabetical order: Down $(+1, 0)$, Left $(0, -1)$, Right $(0, +1)$, Up $(-1, 0)$. Mark visited `mat[r][c] = 0`, recurse, unmark `mat[r][c] = 1`. This naturally generates paths in lexicographically sorted order without needing `std::sort` at the end!

- **Underlying Pattern**: `Lexicographical Direction Backtracking (`D, L, R, U` with In-Place Visited Marking)`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Generating random paths with visited matrix and sorting result list.

### C++17 Code
```cpp
// Unordered directions with sort
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(4^{N^2} + K \log K)$.
- **Space Complexity**: $\mathcal{O}(N^2)$.
- **Why it's not good enough**: Extra sorting step.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Ordered Direction Vectors (`di`, `dj`, `dirChar`) with In-Place Grid Masking.

### C++17 Code
```cpp
#include <vector>
#include <string>
using namespace std;

class Solution {
private:
    // Lexicographical order: 'D', 'L', 'R', 'U'
    const int di[4] = {+1, 0, 0, -1};
    const int dj[4] = {0, -1, +1, 0};
    const char dirChar[4] = {'D', 'L', 'R', 'U'};
    
    void solve(int i, int j, vector<vector<int>>& mat, int n, string& path, vector<string>& ans) {
        if (i == n - 1 && j == n - 1) {
            ans.push_back(path);
            return;
        }
        
        // Mark cell visited
        mat[i][j] = 0;
        
        for (int k = 0; k < 4; k++) {
            int nextI = i + di[k];
            int nextJ = j + dj[k];
            
            if (nextI >= 0 && nextI < n && nextJ >= 0 && nextJ < n && mat[nextI][nextJ] == 1) {
                path.push_back(dirChar[k]);
                solve(nextI, nextJ, mat, n, path, ans);
                path.pop_back(); // backtrack
            }
        }
        
        // Restore cell (backtrack)
        mat[i][j] = 1;
    }

public:
    vector<string> findPath(vector<vector<int>>& mat) {
        vector<string> ans;
        int n = mat.size();
        if (mat[0][0] == 0 || mat[n - 1][n - 1] == 0) return ans;
        
        string path = "";
        solve(0, 0, mat, n, path, ans);
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(4^{N^2})$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ recursion call stack space.
- **Why this is optimal**: Direction order `D-L-R-U` guarantees output is sorted without $\mathcal{O}(K \log K)$ post-sorting.

---

## 6. Dry Run

Grid $N = 2$: `[[1, 0], [1, 1]]`

| Step | Action / State Change | Result |
|---|---|---|
| `(0,0)` | Down to (1,0) | path = "D" |
| `(1,0)` | Right to (1,1) | path = "DR" |
| `(1,1)` | Reached destination $(N-1, N-1)$ | Save `"DR"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Starting cell `mat[0][0] == 0` (returns `[]`).
- Destination cell `mat[n-1][n-1] == 0`.

### Common Bugs to Avoid
- Forgetting to restore `mat[i][j] = 1` on backtracking return.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why are D, L, R, U directions used?**  
  **A**: Because alphabetical ordering of characters is 'D' < 'L' < 'R' < 'U'. Traversing in this order produces lexicographically sorted string results automatically.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Grid`, `Medium`
- **Related problems to practice next**:
- **Word Search**: Grid backtracking.
