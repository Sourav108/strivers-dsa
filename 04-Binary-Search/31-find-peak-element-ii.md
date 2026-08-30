# Find a Peak Element II (2D Peak) (Step 4.3 — BS on 2D Arrays)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find a Peak Element II (2D Peak)](https://takeuforward.org/binary-search/find-peak-element-in-2d-matrix/)
- **Difficulty**: Hard
- **Statement**: A peak element in a 2D grid is an element that is strictly greater than all of its adjacent neighbors to the left, right, top, and bottom. Given an $m \times n$ matrix `mat` where no two adjacent cells are equal, find any peak element `mat[i][j]` and return its coordinates `[i, j]`. You must write an algorithm that runs in $\mathcal{O}(m \log n)$ or $\mathcal{O}(n \log m)$ time.

---

## 1. Problem, Restated

Find any cell in an $m \times n$ matrix that is strictly larger than its 4 cardinal neighbors.

- **Input**: Matrix or array inputs with query parameters.
- **Output**: Value or coordinates meeting the specification.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Key Insight: If we pick a column `midCol`, find the **row index containing the maximum element** in that column (`maxRow`).
1. Because `mat[maxRow][midCol]` is the MAXIMUM in column `midCol`, it is ALREADY strictly greater than its top neighbor `mat[maxRow-1][midCol]` and bottom neighbor `mat[maxRow+1][midCol]`!
2. Therefore, we only need to compare it against its **left neighbor** (`midCol - 1`) and **right neighbor** (`midCol + 1`):
   - If `mat[maxRow][midCol] > left` AND `> right`: **It is a 2D PEAK!** Return `{maxRow, midCol}`.
   - If `left > mat[maxRow][midCol]`: By the 2D Extreme Value Theorem, a peak is guaranteed to exist in the left half $\implies$ `high = midCol - 1`.
   - Else (`right > mat[maxRow][midCol]`): Peak exists in the right half $\implies$ `low = midCol + 1`.

- **Underlying Pattern**: `Binary Search on Columns + Finding Column Maximum`.
- **The "Aha!" Moment**: Exploiting dimensional geometry or monotonic predicates to eliminate sub-regions in logarithmic time.

---

## 3. Approach 1 — Brute Force

### Idea
Global Maximum / 4-Neighbor Scan: Find the global maximum element of the entire matrix in $\mathcal{O}(m \times n)$ time (the global maximum is guaranteed to be a peak).

### C++17 Code
```cpp
#include <vector>
using namespace std;

vector<int> findPeakGridBrute(vector<vector<int>>& mat) {
    int m = mat.size(), n = mat[0].size();
    int maxVal = -1;
    vector<int> ans = {-1, -1};
    for (int i = 0; i < m; i++) {
        for (int j = 0; j < n; j++) {
            if (mat[i][j] > maxVal) {
                maxVal = mat[i][j];
                ans = {i, j};
            }
        }
    }
    return ans;
}
```

### Java Code
```java
class Solution {
    int[] findPeakGridBrute(int[][] mat) {
        int m = mat.length, n = mat[0].size();
        int maxVal = -1;
        int[] ans = {-1, -1};
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (mat[i][j] > maxVal) {
                    maxVal = mat[i][j];
                    ans = {i, j};
                }
            }
        }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(m \times n)$ — scans all elements.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Fails the problem's strict $\mathcal{O}(m \log n)$ time requirement.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Binary Search on Columns: `low = 0, high = n - 1`. While `low <= high`, find `maxRow = findMaxRow(mat, midCol)`. Check left and right neighbors. If greater than both, return `{maxRow, midCol}`. If `left > current`, `high = midCol - 1`; else `low = midCol + 1`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    int findMaxRowIndex(const vector<vector<int>>& mat, int m, int col) {
        int maxValue = -1;
        int maxIndex = -1;
        for (int i = 0; i < m; i++) {
            if (mat[i][col] > maxValue) {
                maxValue = mat[i][col];
                maxIndex = i;
            }
        }
        return maxIndex;
    }

public:
    vector<int> findPeakGrid(vector<vector<int>>& mat) {
        int m = mat.size();
        int n = mat[0].size();
        
        int low = 0;
        int high = n - 1;
        
        while (low <= high) {
            int midCol = low + (high - low) / 2;
            
            // Find the row index of the maximum element in column midCol
            int maxRow = findMaxRowIndex(mat, m, midCol);
            
            int currentVal = mat[maxRow][midCol];
            int leftVal  = (midCol - 1 >= 0) ? mat[maxRow][midCol - 1] : -1;
            int rightVal = (midCol + 1 < n)  ? mat[maxRow][midCol + 1] : -1;
            
            // If current is strictly greater than both horizontal neighbors, it is a 2D peak!
            if (currentVal > leftVal && currentVal > rightVal) {
                return {maxRow, midCol};
            } else if (leftVal > currentVal) {
                high = midCol - 1; // peak guaranteed on left
            } else {
                low = midCol + 1;  // peak guaranteed on right
            }
        }
        
        return {-1, -1};
    }
};
```

### Java Code
```java
class Solution {

    int findMaxRowIndex(int[][] mat, int m, int col) {
        int maxValue = -1;
        int maxIndex = -1;
        for (int i = 0; i < m; i++) {
            if (mat[i][col] > maxValue) {
                maxValue = mat[i][col];
                maxIndex = i;
            }
        }
        return maxIndex;
    }

    int[] findPeakGrid(int[][] mat) {
        int m = mat.length;
        int n = mat[0].size();
        
        int low = 0;
        int high = n - 1;
        
        while (low <= high) {
            int midCol = low + (high - low) / 2;
            
            // Find the row index of the maximum element in column midCol
            int maxRow = findMaxRowIndex(mat, m, midCol);
            
            int currentVal = mat[maxRow][midCol];
            int leftVal  = (midCol - 1 >= 0) ? mat[maxRow][midCol - 1] : -1;
            int rightVal = (midCol + 1 < n)  ? mat[maxRow][midCol + 1] : -1;
            
            // If current is strictly greater than both horizontal neighbors, it is a 2D peak!
            if (currentVal > leftVal && currentVal > rightVal) {
                return {maxRow, midCol};
            } else if (leftVal > currentVal) {
                high = midCol - 1; // peak guaranteed on left
            } else {
                low = midCol + 1;  // peak guaranteed on right
            }
        }
        
        return {-1, -1};
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(m \cdot \log_2 n)$ — binary search on columns runs $\log_2 n$ times. Each column maximum search takes $\mathcal{O}(m)$ operations. For $1000 \times 1000$, takes $1000 \times 10 = 10^4$ operations ($< 2\text{ms}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Optimal bound for 2D peak finding without scanning the full matrix.

---

## 6. Dry Run

`mat = [[10, 20, 15], [21, 30, 14], [7, 16, 32]]`, $m=3, n=3$

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | low = 0, high = 2, midCol = 1. Col 1 values: [20, 30, 16] -> maxRow = 1 (val = 30) | left = 21, right = 14. 30 > 21 and 30 > 14 **PEAK FOUND!** |
| `Result` | Peak Coordinates = {1, 1} (Value 30 > 21, 14, 20, 16) | Return {1, 1} ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single column matrix ($n = 1$ -> max element in that column is peak).
- Single row matrix ($m = 1$ -> standard 1D peak).
- Peak at boundary corners (sentinel value `-1` handles edges seamlessly).

### Common Bugs to Avoid
- Checking all 4 neighbors of every cell in the column instead of just finding the column maximum.
- Choosing row binary search when $m > n$ (should binary search on $\min(m, n)$ for $\mathcal{O}(\max(m, n) \log(\min(m, n)))$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the column maximum guaranteed to be greater than its top and bottom neighbors?**  
  **A**: By definition, `maxRow` was chosen such that `mat[maxRow][midCol] >= mat[r][midCol]` for all $r \in [0, m-1]$. Since adjacent cells are distinct, `mat[maxRow][midCol] > mat[maxRow-1][midCol]` and `> mat[maxRow+1][midCol]` unconditionally!

- **Q2: Why does moving towards the larger neighbor guarantee finding a peak?**  
  **A**: If `leftVal > currentVal`, starting from `leftVal` and taking strictly increasing steps must either terminate at a strict local peak or reach the global maximum of the left subgrid, which is guaranteed to be a peak by the discrete Extreme Value Theorem.

- **Q3: How to optimize if m >> n vs n >> m?**  
  **A**: If $m < n$, binary search on columns takes $\mathcal{O}(m \log n)$. If $n < m$, binary search on rows takes $\mathcal{O}(n \log m)$.

- **Q4: What if duplicate elements are allowed?**  
  **A**: With duplicates, plateaus can disguise peaks, causing binary search to fail in the worst case (requiring $\mathcal{O}(m \times n)$).


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `2D Matrix`, `Peak Element`, `LeetCode-1901`, `Hard`
- **Related problems to practice next**:
- **Find Peak Element (1D)**: 1D slope binary search.
- **Search in a 2D Matrix II**: Staircase search.
- **Matrix Median**: 2D value binary search.
