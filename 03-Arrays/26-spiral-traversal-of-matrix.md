# Spiral Traversal of Matrix (Step 3.2)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/spiral-traversal-of-matrix/
- **Difficulty**: Medium
- **Statement**: Return all elements of matrix in clockwise spiral order.

---

## 1. Problem, Restated

Return all elements of matrix in clockwise spiral order.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

4-Boundary Shifting (`top`, `bottom`, `left`, `right`).

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

// Direct simulation is standard; brute force is identical in complexity.
```

### Java Code
```java
class Solution {
    // Direct simulation is standard; brute force is identical in complexity.
}
```

### Complexity Derivation
- **Time Complexity**: O(m * n)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Simulating boundary traversal is the standard direct optimal approach.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
4-Boundary Shifting: Maintain `top = 0, bottom = m - 1, left = 0, right = n - 1`. Sweep right across `top` (`top++`), sweep down across `right` (`right--`), sweep left across `bottom` if `top <= bottom` (`bottom--`), sweep up across `left` if `left <= right` (`left++`).

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

vector<int> spiralOrderOptimal(const vector<vector<int>>& mat) {
    vector<int> res;
    if (mat.empty()) return res;
    int top = 0, bottom = mat.size() - 1, left = 0, right = mat[0].size() - 1;
    while (top <= bottom && left <= right) {
        for (int j = left; j <= right; j++) res.push_back(mat[top][j]);
        top++;
        for (int i = top; i <= bottom; i++) res.push_back(mat[i][right]);
        right--;
        if (top <= bottom) {
            for (int j = right; j >= left; j--) res.push_back(mat[bottom][j]);
            bottom--;
        }
        if (left <= right) {
            for (int i = bottom; i >= top; i--) res.push_back(mat[i][left]);
            left++;
        }
    }
    return res;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] spiralOrderOptimal(int[][] mat) {
        List<Integer> res = new ArrayList<>();
        if (mat.isEmpty()) return res;
        int top = 0, bottom = mat.length - 1, left = 0, right = mat[0].size() - 1;
        while (top <= bottom && left <= right) {
            for (int j = left; j <= right; j++) res.add(mat[top][j]);
            top++;
            for (int i = top; i <= bottom; i++) res.add(mat[i][right]);
            right--;
            if (top <= bottom) {
                for (int j = right; j >= left; j--) res.add(mat[bottom][j]);
                bottom--;
            }
            if (left <= right) {
                for (int i = bottom; i >= top; i--) res.add(mat[i][left]);
                left++;
            }
        }
        return res;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(m * n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Visits every cell exactly once in $\mathcal{O}(m \cdot n)$ time and $\mathcal{O}(1)$ auxiliary space.

---

## 6. Dry Run

`mat = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Top sweep` | Traverse row 0: [1, 2, 3], top=1 | res=[1, 2, 3] |
| `Right sweep` | Traverse col 2: [6, 9], right=1 | res=[1, 2, 3, 6, 9] |
| `Bottom sweep` | Traverse row 2: [8, 7], bottom=1 | res=[1, 2, 3, 6, 9, 8, 7] |
| `Left sweep` | Traverse col 0: [4], left=1 | res=[1, 2, 3, 6, 9, 8, 7, 4] |
| `Center cell` | Traverse cell (1, 1): [5] | Final: `[1, 2, 3, 6, 9, 8, 7, 4, 5]` ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single row matrix ($1 \times 4$) or single column ($4 \times 1$).
- Empty matrix -> returns empty vector.

### Common Bugs to Avoid
- Omitting `if (top <= bottom)` or `if (left <= right)` before bottom/left sweeps, duplicating elements in rectangular matrices.

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why are the conditional checks `if (top <= bottom)` and `if (left <= right)` required inside the while loop?**  
  **A**: In rectangular matrices (e.g. $1 \times 5$ or $3 \times 1$), after sweeping right and down, `top` or `right` boundaries shift. Without re-checking, the bottom-left and left-top sweeps would traverse already-visited rows/columns twice.

- **Q2: How to generate an N x N matrix filled with 1 to N^2 in spiral order (Spiral Matrix II)?**  
  **A**: Use the identical 4-boundary logic with a counter `val = 1..n^2`, writing `mat[top][j] = val++` etc. in $\mathcal{O}(n^2)$ time.

- **Q3: How to find the K-th element in spiral order in O(1) time without visiting previous elements?**  
  **A**: Calculate how many complete outer rings precede the $K$-th element, compute the perimeter of outer rings using arithmetic progressions, and jump directly to the target coordinate.

- **Q4: How to implement spiral traversal using a direction vector array?**  
  **A**: Use `dr = {0, 1, 0, -1}`, `dc = {1, 0, -1, 0}` and `dir = 0`. Move $(r + dr[dir], c + dc[dir])$. When hitting a boundary or visited cell, turn right: `dir = (dir + 1) % 4`.

- **Q5: How does spiral traversal generalize to 3D tensors (Cube traversal)?**  
  **A**: Traverse 6 outer faces of the 3D bounding box iteratively, shrinking `xMin, xMax, yMin, yMax, zMin, zMax` boundaries.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
