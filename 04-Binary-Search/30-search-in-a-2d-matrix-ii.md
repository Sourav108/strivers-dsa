# Search in a 2D Matrix II (Row and Column Sorted) (Step 4.3 — BS on 2D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Search in a 2D Matrix II (Row and Column Sorted)](https://takeuforward.org/data-structure/search-in-a-row-and-column-wise-sorted-matrix/)
- **Difficulty**: Medium
- **Statement**: Write an efficient algorithm that searches for a value `target` in an $m \times n$ integer matrix `matrix`. This matrix has the following properties: 1) Integers in each row are sorted in ascending from left to right, 2) Integers in each column are sorted in ascending from top to bottom.

---

## 1. Problem, Restated

Determine whether `target` is present in an $m \times n$ matrix where each row and column is independently sorted in ascending order.

- **Input**: Matrix or array inputs with query parameters.
- **Output**: Value or coordinates meeting the specification.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Unlike Matrix I, the entire matrix cannot be flattened into a single 1D sorted array because row boundaries do not strictly connect (e.g. `matrix[0][n-1]` may be larger than `matrix[1][0]`).
However, starting from the **Top-Right Corner** $(r = 0, c = n - 1)$ gives a unique decision property:
- All elements to its **left** in the same row are **smaller** ($c - 1$).
- All elements **below** it in the same column are **larger** ($r + 1$).

At any cell $(r, c)$:
- If `matrix[r][c] == target`: found! Return `true`.
- If `matrix[r][c] > target`: target cannot exist in this entire column $\implies$ eliminate column (`c--`).
- If `matrix[r][c] < target`: target cannot exist in this entire row $\implies$ eliminate row (`r++`).

- **Underlying Pattern**: `Staircase Search (Top-Right / Bottom-Left Corner Traversal)`.
- **The "Aha!" Moment**: Exploiting dimensional geometry or monotonic predicates to eliminate sub-regions in logarithmic time.

---

## 3. Approach 1 — Brute Force

### Idea
Linear Scan of All Elements: Traverse every cell in nested loops in $\mathcal{O}(m \times n)$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

bool searchMatrixIIBrute(vector<vector<int>>& matrix, int target) {
    for (const auto& row : matrix) {
        for (int val : row) {
            if (val == target) return true;
        }
    }
    return false;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(m \times n)$ — scans all $m \times n$ elements.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Ignores the sorted properties of rows and columns.

---

## 4. Approach 2 — Better

### Idea
Binary Search on Each Row: Run binary search on each of the $m$ rows in $\mathcal{O}(m \log n)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

bool searchMatrixIIBS(vector<vector<int>>& matrix, int target) {
    for (const auto& row : matrix) {
        if (binary_search(row.begin(), row.end(), target)) {
            return true;
        }
    }
    return false;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(m \log_2 n)$ — binary searches each row independently.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: Staircase search improves time to $\mathcal{O}(m + n)$ by simultaneously pruning entire rows and columns.

---

## 5. Approach 3 — Optimal

### Idea
Staircase Search starting from Top-Right Corner $(0, n-1)$: `r = 0, c = n - 1`. While `r < m && c >= 0`, if `matrix[r][c] == target` return `true`; else if `matrix[r][c] > target` `c--`; else `r++`. Return `false`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {
        if (matrix.empty() || matrix[0].empty()) return false;
        
        int m = matrix.size();
        int n = matrix[0].size();
        
        // Start at top-right corner
        int r = 0;
        int c = n - 1;
        
        while (r < m && c >= 0) {
            int val = matrix[r][c];
            
            if (val == target) {
                return true;
            } else if (val > target) {
                c--; // eliminate entire column (all elements below are larger)
            } else {
                r++; // eliminate entire row (all elements to the left are smaller)
            }
        }
        
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(m + n)$ — each step either decrements $c$ or increments $r$. Maximum total steps $\le m + n$. For $1000 \times 1000$, takes at most $2000$ operations ($< 1\text{ms}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Optimal single-pass elimination without redundant checks.

---

## 6. Dry Run

`matrix = [[1, 4, 7, 11], [2, 5, 8, 12], [3, 6, 9, 16], [10, 13, 14, 17]]`, `target = 5`

| Step | Action / State Change | Result |
|---|---|---|
| `Start` | r = 0, c = 3. val = matrix[0][3] = 11 | 11 > 5 -> move left: c = 2 |
| `Step 2` | r = 0, c = 2. val = matrix[0][2] = 7 | 7 > 5 -> move left: c = 1 |
| `Step 3` | r = 0, c = 1. val = matrix[0][1] = 4 | 4 < 5 -> move down: r = 1 |
| `Step 4` | r = 1, c = 1. val = matrix[1][1] = 5 | **5 == 5 (Target Found!) Return TRUE** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target smaller than `matrix[0][0]` (returns `false` immediately).
- Target larger than `matrix[m-1][n-1]` (returns `false` immediately).
- 1x1 matrix (`[[5]], target = 5` -> `true`).

### Common Bugs to Avoid
- Starting at top-left $(0, 0)$ or bottom-right $(m-1, n-1)$: at $(0, 0)$, both right and down increase in value, providing no direction to prune. Must start at top-right or bottom-left.
- Loop condition off-by-one: using `c > 0` instead of `c >= 0`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does top-left (0, 0) NOT work as a starting point?**  
  **A**: At $(0, 0)$, moving right increases the value and moving down also increases the value. If `target > matrix[0][0]`, we cannot know whether to explore right or down, losing the $\mathcal{O}(1)$ pruning invariant.

- **Q2: Can we start from the bottom-left corner (m-1, 0)?**  
  **A**: Yes! At $(m-1, 0)$, moving up decreases the value (`val > target -> r--`) and moving right increases the value (`val < target -> c++`). It has identical $\mathcal{O}(m + n)$ complexity.

- **Q3: Can we achieve O(log m + log n) using Divide and Conquer?**  
  **A**: Yes, quad-partition divide-and-conquer achieves $\mathcal{O}(m^{\log_2 3}) \approx \mathcal{O}(m^{1.58})$, but Staircase $\mathcal{O}(m + n)$ is faster and simpler for $m \approx n$.

- **Q4: How to handle target counts (number of times target appears)?**  
  **A**: When `val == target`, increment count and either move $c--$ or $r++$ depending on row/col uniqueness.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `2D Matrix`, `Staircase Search`, `LeetCode-240`, `Medium`
- **Related problems to practice next**:
- **Search in a 2D Matrix I**: Strictly sorted flattened 1D.
- **Find Peak Element II**: 2D matrix peak search.
- **Matrix Median**: 2D binary search count.
