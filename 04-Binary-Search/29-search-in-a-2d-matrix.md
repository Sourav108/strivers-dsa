# Search in a 2D Matrix I (Strictly Sorted 1D Flattened) (Step 4.3 — BS on 2D Arrays)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Search in a 2D Matrix I (Strictly Sorted 1D Flattened)](https://takeuforward.org/data-structure/search-in-a-sorted-2d-matrix/)
- **Difficulty**: Medium
- **Statement**: You are given an $m \times n$ integer matrix `matrix` with the following two properties: 1) Each row is sorted in non-decreasing order, 2) The first integer of each row is greater than the last integer of the previous row. Given an integer `target`, return `true` if `target` is in `matrix` or `false` otherwise. You must write a solution in $\mathcal{O}(\log(m \times n))$ time.

---

## 1. Problem, Restated

Search for a target in an $m \times n$ matrix where all elements form a single strictly increasing sequence if read row by row.

- **Input**: Matrix or array inputs with query parameters.
- **Output**: Value or boolean meeting the specification.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Because the first element of each row is strictly greater than the last element of the previous row, the entire $m \times n$ matrix is equivalent to a single continuous 1D sorted array of length $m \times n$.
We do NOT need to actually flatten or allocate a 1D array. We can map any 1D index `mid` in range $[0, m \times n - 1]$ to its 2D coordinates in $\mathcal{O}(1)$ time:
- $\text{row} = \text{mid} / n$
- $\text{col} = \text{mid} \% n$

Then execute standard binary search in $\mathcal{O}(\log(m \times n))$.

- **Underlying Pattern**: `Virtual 1D Flattened Binary Search (mid / n, mid % n)`.
- **The "Aha!" Moment**: Mapping 2D coordinates or partition cuts into a unified 1D search space.

---

## 3. Approach 1 — Brute Force

### Idea
Linear Scan: Check all $m \times n$ cells with nested loops in $\mathcal{O}(m \times n)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

bool searchMatrixBrute(vector<vector<int>>& matrix, int target) {
    for (const auto& row : matrix) {
        for (int val : row) {
            if (val == target) return true;
        }
    }
    return false;
}
```

### Java Code
```java
class Solution {
    boolean searchMatrixBrute(int[][] matrix, int target) {
        for (var row : matrix) {
            for (int val : row) {
                if (val == target) return true;
            }
        }
        return false;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(m \times n)$ — scans all cells.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Fails the problem's strict $\mathcal{O}(\log(m \times n))$ requirement.

---

## 4. Approach 2 — Better

### Idea
Two Binary Searches (Row then Column): 1) Binary search the first column to identify the single potential row where $target$ could exist in $\mathcal{O}(\log m)$. 2) Binary search inside that row in $\mathcal{O}(\log n)$. Total time $\mathcal{O}(\log m + \log n) = \mathcal{O}(\log(m \times n))$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

bool searchMatrixTwoBS(vector<vector<int>>& matrix, int target) {
    int m = matrix.size(), n = matrix[0].size();
    
    // Binary search to find row
    int low = 0, high = m - 1, targetRow = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (matrix[mid][0] <= target && target <= matrix[mid][n - 1]) {
            targetRow = mid;
            break;
        } else if (matrix[mid][0] > target) {
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }
    
    if (targetRow == -1) return false;
    
    // Binary search inside targetRow
    return binary_search(matrix[targetRow].begin(), matrix[targetRow].end(), target);
}
```

### Java Code
```java
class Solution {
    boolean searchMatrixTwoBS(int[][] matrix, int target) {
        int m = matrix.length, n = matrix[0].size();
        
        // Binary search to find row
        int low = 0, high = m - 1, targetRow = -1;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            if (matrix[mid][0] <= target && target <= matrix[mid][n - 1]) {
                targetRow = mid;
                break;
            } else if (matrix[mid][0] > target) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        
        if (targetRow == -1) return false;
        
        // Binary search inside targetRow
        return binary_search(matrix[targetRow].begin(), matrix[targetRow].end(), target);
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log m + \log n)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: Requires two separate binary search phases; virtual 1D flattening achieves the same in one unified clean pass.

---

## 5. Approach 3 — Optimal

### Idea
Single Unified Binary Search over $[0, m \times n - 1]$: `low = 0, high = m * n - 1`. Compute `mid`. Convert to 2D cell: `val = matrix[mid / n][mid % n]`. If `val == target` return `true`; else if `val < target` `low = mid + 1`; else `high = mid - 1`.

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
        
        int low = 0;
        int high = m * n - 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            // Map 1D index to 2D row and column coordinates
            int row = mid / n;
            int col = mid % n;
            int val = matrix[row][col];
            
            if (val == target) {
                return true;
            } else if (val < target) {
                low = mid + 1;  // search right
            } else {
                high = mid - 1; // search left
            }
        }
        
        return false;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    boolean searchMatrix(int[][] matrix, int target) {
        if (matrix.isEmpty() || matrix[0].isEmpty()) return false;
        
        int m = matrix.length;
        int n = matrix[0].size();
        
        int low = 0;
        int high = m * n - 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            // Map 1D index to 2D row and column coordinates
            int row = mid / n;
            int col = mid % n;
            int val = matrix[row][col];
            
            if (val == target) {
                return true;
            } else if (val < target) {
                low = mid + 1;  // search right
            } else {
                high = mid - 1; // search left
            }
        }
        
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2(m \times n))$ — exact binary search over total element count $m \times n$. For a $1000 \times 1000$ matrix ($10^6$ elements), executes in at most $20$ iterations!
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Information-theoretic lower bound for comparison search in sorted arrays.

---

## 6. Dry Run

`matrix = [[1, 3, 5, 7], [10, 11, 16, 20], [23, 30, 34, 60]]`, $m=3, n=4$. `target = 3`.
`low = 0, high = 3*4 - 1 = 11`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | mid = 5 -> row = 5/4 = 1, col = 5%4 = 1. val = matrix[1][1] = 11 | 11 > 3 -> high = mid - 1 = 4 |
| `Iter 2` | mid = 2 -> row = 2/4 = 0, col = 2%4 = 2. val = matrix[0][2] = 5 | 5 > 3 -> high = mid - 1 = 1 |
| `Iter 3` | mid = 0 -> row = 0/4 = 0, col = 0%4 = 0. val = matrix[0][0] = 1 | 1 < 3 -> low = mid + 1 = 1 |
| `Iter 4` | mid = 1 -> row = 1/4 = 0, col = 1%4 = 1. val = matrix[0][1] = 3 | **val == target (3 == 3)! Return TRUE** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- 1x1 matrix (`[[5]], target = 5` -> returns true).
- Target smaller than `matrix[0][0]` -> returns false immediately.
- Target larger than `matrix[m-1][n-1]` -> returns false immediately.

### Common Bugs to Avoid
- Using `mid / m` and `mid % m` instead of dividing/modulo by the number of columns `n`.
- 32-bit overflow on `m * n - 1` if $m, n > 5 \times 10^4$ (use `long long`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is division by n (number of columns) used for row indexing, not m?**  
  **A**: Each row contains exactly $n$ columns. Therefore, each block of $n$ elements in the 1D flattened representation corresponds to one full row. Thus, `row = mid / n` and the offset within that row is `col = mid % n`.

- **Q2: How does this problem differ from Search in a 2D Matrix II (LeetCode 240)?**  
  **A**: In Matrix I, the entire matrix is strictly sorted end-to-end ($	ext{matrix}[i][n-1] < 	ext{matrix}[i+1][0]$), allowing 1D binary search. In Matrix II, rows and columns are independently sorted, requiring Top-Right Staircase $\mathcal{O}(m + n)$ search.

- **Q3: How to prevent integer overflow in mid calculation for huge matrices?**  
  **A**: Compute `mid = low + (high - low) / 2` with `long long` types if $m \times n > 2^{31}-1$.

- **Q4: Can this approach be extended to 3D tensors (D x H x W)?**  
  **A**: Yes! Coordinate mapping: $d = idx / (H \cdot W)$, $r = (idx \% (H \cdot W)) / W$, $c = idx \% W$. Binary search remains $\mathcal{O}(\log(D \cdot H \cdot W))$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `2D Matrix`, `LeetCode-74`, `Medium`
- **Related problems to practice next**:
- **Search in a 2D Matrix II**: Independent row/col sorted search.
- **Find Peak Element II**: 2D matrix peak search.
- **Matrix Median**: 2D binary search count.
