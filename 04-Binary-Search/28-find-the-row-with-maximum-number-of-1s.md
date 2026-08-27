# Find the Row with Maximum Number of 1s (Step 4.3 — BS on 2D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find the Row with Maximum Number of 1s](https://takeuforward.org/data-structure/find-the-row-with-maximum-number-of-1s/)
- **Difficulty**: Easy
- **Statement**: Given a boolean 2D array `mat` of dimensions $n \times m$ where each row is sorted in non-decreasing order (all 0s appear before 1s), find the 0-based index of the first row that has the maximum number of 1s. If no row contains any 1, return `-1`.

---

## 1. Problem, Restated

In a binary matrix where every row is sorted (`000...111`), identify which row contains the most 1s.

- **Input**: Matrix or array inputs with query parameters.
- **Output**: Value or boolean meeting the specification.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Because each row is sorted, all 1s are contiguous at the end of each row. The number of 1s in row $i$ is $m - \text{firstIndexOf1}(i)$.
- **Method 1 (Binary Search)**: Run `lower_bound(row, 1)` on each row in $\mathcal{O}(n \log m)$.
- **Method 2 (Optimal Staircase Walk)**: Start at the top-right corner $(row = 0, col = m - 1)$. If `mat[row][col] == 1`, we move **left** (`col--`) to find an even longer sequence of 1s and update `maxRow = row`. If `mat[row][col] == 0`, this row cannot beat our current max, so we move **down** (`row++`). Runs in $\mathcal{O}(n + m)$ time!

- **Underlying Pattern**: `Binary Search on Rows / Staircase Search (Top-Right Walk)`.
- **The "Aha!" Moment**: Mapping 2D coordinates or partition cuts into a unified 1D search space.

---

## 3. Approach 1 — Brute Force

### Idea
Linear Scan of All Matrix Cells: Count 1s in each row with nested loops in $\mathcal{O}(n \times m)$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int rowWithMax1sBrute(vector<vector<int>>& mat) {
    int n = mat.size(), m = mat[0].size();
    int maxCount = 0, maxRow = -1;
    for (int i = 0; i < n; i++) {
        int count = 0;
        for (int j = 0; j < m; j++) {
            if (mat[i][j] == 1) count++;
        }
        if (count > maxCount) {
            maxCount = count;
            maxRow = i;
        }
    }
    return maxRow;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \times m)$ — checks all $n \times m$ cells.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Ignores the sorted row property.

---

## 4. Approach 2 — Better

### Idea
Binary Search Lower Bound per Row: For each row, binary search the first index of 1 using `lower_bound`. Count of 1s is $m - \text{firstIdx}$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

int firstOccurrence(const vector<int>& row, int m) {
    int low = 0, high = m - 1;
    int first = m;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (row[mid] == 1) {
            first = mid;
            high = mid - 1; // look for earlier 1 on left
        } else {
            low = mid + 1;
        }
    }
    return first;
}

int rowWithMax1sBS(vector<vector<int>>& mat) {
    int n = mat.size(), m = mat[0].size();
    int maxCount = 0, maxRow = -1;
    
    for (int i = 0; i < n; i++) {
        int first = firstOccurrence(mat[i], m);
        int count = m - first;
        if (count > maxCount) {
            maxCount = count;
            maxRow = i;
        }
    }
    return maxRow;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log_2 m)$ — runs binary search on each of the $n$ rows.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: Excellent approach, but Top-Right Staircase traversal improves time to $\mathcal{O}(n + m)$.

---

## 5. Approach 3 — Optimal

### Idea
Top-Right Staircase Traversal (O(n + m)): Start at $(r = 0, c = m - 1)$. While $r < n$ and $c \ge 0$: if `mat[r][c] == 1`, record `maxRow = r` and move left `c--` (testing if previous rows had fewer 1s). If `mat[r][c] == 0`, move down `r++`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int rowWithMax1s(vector<vector<int>>& mat) {
        int n = mat.size();
        int m = mat[0].size();
        
        int r = 0;
        int c = m - 1;
        int maxRow = -1;
        
        // Start from top-right corner and traverse left/down
        while (r < n && c >= 0) {
            if (mat[r][c] == 1) {
                maxRow = r; // current row has more 1s than any previously seen row
                c--;        // step left to see if this row has even more 1s
            } else {
                r++;        // 0 encountered, move to next row to find more 1s
            }
        }
        
        return maxRow;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n + m)$ — in each step, we either decrement $c$ (at most $m$ times) or increment $r$ (at most $n$ times). Total cell visits $\le n + m$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Linear in matrix dimensions with zero redundant checks, optimal for 2D sorted binary grids.

---

## 6. Dry Run

`mat = [[0, 1, 1, 1], [0, 0, 1, 1], [1, 1, 1, 1], [0, 0, 0, 0]]`, $n=4, m=4$

| Step | Action / State Change | Result |
|---|---|---|
| `Start` | r=0, c=3. mat[0][3] = 1 | maxRow = 0, c = 2 |
| `Step 2` | r=0, c=2. mat[0][2] = 1 | maxRow = 0, c = 1 |
| `Step 3` | r=0, c=1. mat[0][1] = 1 | maxRow = 0, c = 0 |
| `Step 4` | r=0, c=0. mat[0][0] = 0 | r = 1 |
| `Step 5` | r=1, c=0. mat[1][0] = 0 | r = 2 |
| `Step 6` | r=2, c=0. mat[2][0] = 1 | maxRow = 2, c = -1 |
| `Exit` | c < 0 -> loop ends | Return maxRow = 2 (Row 2 has all four 1s) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- No 1s in entire matrix (`all 0s` -> `maxRow` remains -1, returns `-1`).
- All cells are 1s (`all 1s` -> returns row 0).
- Multiple rows have identical maximum 1s count (returns the first row index with that count).

### Common Bugs to Avoid
- Overwriting `maxRow` on tie: must only update when strictly more 1s are found.
- Loop bounds out of bounds ($c < 0$ or $r \ge n$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Staircase search visit at most n + m cells?**  
  **A**: Starting at $(0, m-1)$, every step either moves left ($c$ decreases by 1) or moves down ($r$ increases by 1). Since $c$ can only decrease $m$ times and $r$ can only increase $n$ times, total iterations are strictly $\le n + m$.

- **Q2: When is Binary Search O(n log m) preferred over Staircase O(n + m)?**  
  **A**: When $m \gg n$ (e.g. $n = 10, m = 10^9$), $n \log m = 10 \times 30 = 300$, whereas $n + m = 10^9$. For extremely wide matrices, Binary Search is significantly faster.

- **Q3: How to handle tie-breaking when the problem asks for the LAST row with max 1s?**  
  **A**: Invert row iteration order starting from bottom-right $(n-1, m-1)$.

- **Q4: What if matrix is not sorted?**  
  **A**: If unsorted, linear scan $\mathcal{O}(n \times m)$ is required.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `2D Matrix`, `Staircase Search`, `Easy`
- **Related problems to practice next**:
- **Search in a 2D Matrix I**: 1D flattened binary search.
- **Search in a 2D Matrix II**: Row and column sorted staircase.
- **Matrix Median**: 2D binary search count.
