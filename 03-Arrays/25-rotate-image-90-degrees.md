# Rotate Image by 90 Degrees Clockwise (Step 3.2)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/rotate-image-by-90-degree/
- **Difficulty**: Medium
- **Statement**: Rotate an $n \times n$ matrix 90 degrees clockwise in-place.

---

## 1. Problem, Restated

Rotate an $n \times n$ matrix 90 degrees clockwise in-place.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Transpose + Reverse Rows.

- **Underlying Pattern**: Array Manipulation / Mathematical Invariants / Pointers.
- **The "Aha!" Moment**: Recognizing how to avoid redundant work by storing running state or leveraging sorting invariants.

---

## 3. Approach 1 — Brute Force

### Idea
Check all possibilities exhaustively using nested loops.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <set>
#include <unordered_map>
using namespace std;

void rotateMatrixBrute(vector<vector<int>>& mat) {
    int n = mat.size();
    vector<vector<int>> res(n, vector<int>(n));
    for (int i = 0; i < n; i++)
        for (int j = 0; j < n; j++)
            res[j][n - 1 - i] = mat[i][j];
    mat = res;
}
```

### Java Code
```java
class Solution {
    void rotateMatrixBrute(int[][] mat) {
        int n = mat.length;
        int[][] res = new int[n][n];
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                res[j][n - 1 - i] = mat[i][j];
        mat = res;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(n^2)
- **Why it's not good enough**: Using an auxiliary $n \times n$ matrix consumes $\mathcal{O}(n^2)$ extra memory.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Transpose + Reverse Rows: 1) Transpose matrix by swapping `mat[i][j]` with `mat[j][i]` for $i < j$. 2) Reverse every row `reverse(mat[i].begin(), mat[i].end())`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

void rotateMatrixOptimal(vector<vector<int>>& mat) {
    int n = mat.size();
    // Transpose
    for (int i = 0; i < n; i++)
        for (int j = i + 1; j < n; j++)
            swap(mat[i][j], mat[j][i]);
    // Reverse each row
    for (int i = 0; i < n; i++)
        reverse(mat[i].begin(), mat[i].end());
}
```

### Java Code
```java
class Solution {
    void rotateMatrixOptimal(int[][] mat) {
        int n = mat.length;
        // Transpose
        for (int i = 0; i < n; i++)
            for (int j = i + 1; j < n; j++)
                int temp = mat[i][j]; mat[i][j] = mat[j][i]; mat[j][i] = temp;
        // Reverse each row
        for (int i = 0; i < n; i++)
            reverse(mat[i].begin(), mat[i].end());
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why this is optimal**: Rotates the $n \times n$ matrix in-place in $\mathcal{O}(n^2)$ time and $\mathcal{O}(1)$ auxiliary space.

---

## 6. Dry Run

`mat = [[1, 2], [3, 4]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Transpose` | swap(mat[0][1], mat[1][0]) -> `[[1, 3], [2, 4]]` | transpose done |
| `Reverse Rows` | Row 0: [3, 1], Row 1: [4, 2] | Final: `[[3, 1], [4, 2]]` ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- 1x1 matrix (`[[1]]` -> unchanged).
- Matrix with all identical elements.

### Common Bugs to Avoid
- Swapping `mat[i][j]` across full $n \times n$ instead of $j > i$, swapping twice and undoing transpose.

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to rotate a matrix 90 degrees ANTI-CLOCKWISE in-place?**  
  **A**: Two ways: 1) Reverse each row, then Transpose. 2) Transpose, then Reverse each column (reverse across vertical centerline).

- **Q2: How to rotate a matrix 180 degrees in-place?**  
  **A**: Reverse all rows top-to-bottom, then reverse each individual row left-to-right in $\mathcal{O}(n^2)$ time and $\mathcal{O}(1)$ space.

- **Q3: What is the 4-way cyclic swap algorithm for rotating layer by layer?**  
  **A**: For each concentric layer from outer to inner, perform a 4-way variable cycle: `temp = top[i]; top[i] = left[i]; left[i] = bottom[i]; bottom[i] = right[i]; right[i] = temp;` in $\mathcal{O}(n^2)$ time and $\mathcal{O}(1)$ space.

- **Q4: Why is in-place rotation impossible for rectangular matrices ($M \times N$ where $M \neq N$)?**  
  **A**: Because rotating changes matrix dimensions from $M \times N$ to $N \times M$, requiring dynamic reallocation of memory layout unless complex in-place cycle leader permutation is used.

- **Q5: How does CPU cache hierarchy affect matrix transpose performance?**  
  **A**: Standard transpose accesses column elements with stride $N$, causing L1 cache misses. **Cache Tiling / Blocking** (processing $32 \times 32$ sub-matrices that fit in L1 cache) speeds up transpose by $4-8\times$.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
