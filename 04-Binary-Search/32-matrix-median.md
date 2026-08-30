# Matrix Median in a Row-Wise Sorted Matrix (Step 4.3 — BS on 2D Arrays)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Matrix Median in a Row-Wise Sorted Matrix](https://takeuforward.org/data-structure/median-of-row-wise-sorted-matrix/)
- **Difficulty**: Hard
- **Statement**: Given a row-wise sorted matrix of size $R \times C$ where $R \times C$ is odd, find the median of the matrix. The median of a sorted list of $R \times C$ numbers is the element at index $(R \times C) / 2$, which means exactly $(R \times C) / 2$ elements are strictly smaller than or equal to it.

---

## 1. Problem, Restated

Find the median of an $R \times C$ matrix where each row is sorted individually, without sorting all $R \times C$ elements.

- **Input**: Matrix or array inputs with query parameters.
- **Output**: Value or coordinates meeting the specification.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

In an odd-sized matrix of $R \times C = N$ elements, the median is the smallest number $X$ such that the total count of numbers in the matrix $\le X$ is strictly greater than $N / 2$ (i.e. $\text{count} \ge \lfloor N / 2 \rfloor + 1$).

### Binary Search on Value Domain:
- `low` = minimum element in the matrix (minimum of first column `matrix[i][0]`).
- `high` = maximum element in the matrix (maximum of last column `matrix[i][C-1]`).

For any guessed midpoint `mid`:
- Count how many elements in row $i$ are $\le mid$ using `upper_bound(row.begin(), row.end(), mid)` in $\mathcal{O}(\log C)$.
- Sum counts across all $R$ rows in $\mathcal{O}(R \log C)$.
- If $\text{totalCount} \le (R \times C) / 2$: $mid$ is too small, search right `low = mid + 1`.
- Else: search left `high = mid - 1`.
- Return `low` at loop termination.

- **Underlying Pattern**: `Binary Search on Value Range + Row-wise Upper Bound`.
- **The "Aha!" Moment**: Exploiting dimensional geometry or monotonic predicates to eliminate sub-regions in logarithmic time.

---

## 3. Approach 1 — Brute Force

### Idea
Flatten and Sort: Copy all $R \times C$ elements into a 1D vector, sort it in $\mathcal{O}(R \cdot C \log(R \cdot C))$ time and space, and return the middle element.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

int matrixMedianBrute(vector<vector<int>>& mat) {
    int r = mat.size(), c = mat[0].size();
    vector<int> flat;
    for (int i = 0; i < r; i++) {
        for (int j = 0; j < c; j++) {
            flat.push_back(mat[i][j]);
        }
    }
    sort(flat.begin(), flat.end());
    return flat[(r * c) / 2];
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int matrixMedianBrute(int[][] mat) {
        int r = mat.length, c = mat[0].size();
        List<Integer> flat = new ArrayList<>();
        for (int i = 0; i < r; i++) {
            for (int j = 0; j < c; j++) {
                flat.add(mat[i][j]);
            }
        }
        Arrays.sort(flat);
        return flat[(r * c) / 2];
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R \cdot C \log(R \cdot C))$ time.
- **Space Complexity**: $\mathcal{O}(R \cdot C)$ memory.
- **Why it's not good enough**: Uses extra memory and fails when $R \times C > 10^6$.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Binary Search on Value Range with Upper Bound Counting: `low = min(col 0), high = max(col C-1)`. Target count `req = (R * C) / 2`. While `low <= high`, compute `mid`. Total elements $\le mid$ is $\sum \text{upper\_bound}(\text{row}, mid)$. If `count <= req`, `low = mid + 1`; else `high = mid - 1`. Return `low`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
private:
    int countSmallerOrEqual(const vector<int>& row, int x) {
        // upper_bound returns iterator to first element > x, index equals count of elements <= x
        return upper_bound(row.begin(), row.end(), x) - row.begin();
    }

public:
    int median(vector<vector<int>>& matrix, int R, int C) {
        int low = INT_MAX;
        int high = INT_MIN;
        
        // Find minimum element (in column 0) and maximum element (in column C - 1)
        for (int i = 0; i < R; i++) {
            low = min(low, matrix[i][0]);
            high = max(high, matrix[i][C - 1]);
        }
        
        int required = (R * C) / 2;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            // Count elements <= mid across all rows
            int count = 0;
            for (int i = 0; i < R; i++) {
                count += countSmallerOrEqual(matrix[i], mid);
            }
            
            if (count <= required) {
                low = mid + 1; // median must be strictly greater
            } else {
                high = mid - 1; // candidate found, try to find smaller on left
            }
        }
        
        return low;
    }
};
```

### Java Code
```java
class Solution {

    int countSmallerOrEqual(int[] row, int x) {
        // upper_bound returns iterator to first element > x, index equals count of elements <= x
        return upper_bound(row.begin(), row.end(), x) - row.begin();
    }

    int median(int[][] matrix, int R, int C) {
        int low = Integer.MAX_VALUE;
        int high = Integer.MIN_VALUE;
        
        // Find minimum element (in column 0) and maximum element (in column C - 1)
        for (int i = 0; i < R; i++) {
            low = Math.min(low, matrix[i][0]);
            high = Math.max(high, matrix[i][C - 1]);
        }
        
        int required = (R * C) / 2;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            // Count elements <= mid across all rows
            int count = 0;
            for (int i = 0; i < R; i++) {
                count += countSmallerOrEqual(matrix[i], mid);
            }
            
            if (count <= required) {
                low = mid + 1; // median must be strictly greater
            } else {
                high = mid - 1; // candidate found, try to find smaller on left
            }
        }
        
        return low;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R \cdot \log_2 C \cdot \log_2(\max - \min))$ — for a $1000 \times 1000$ matrix with values up to $10^9$, binary search runs $\approx 30$ iterations. Each iteration does $1000 \times \log_2(1000) \approx 10^4$ operations. Total operations: $30 \times 10^4 = 3 \times 10^5$, executing in $\approx 5\text{ms}$ with $\mathcal{O}(1)$ space!
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Optimal logarithmic value-range search without storing flattened arrays.

---

## 6. Dry Run

`matrix = [[1, 3, 5], [2, 6, 9], [3, 6, 9]]`, $R=3, C=3$. Total $= 9$. `req = 9/2 = 4`
`low = min(1, 2, 3) = 1, high = max(5, 9, 9) = 9`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | mid = 5. Row counts <= 5: R1=[1,3,5](3), R2=[2](1), R3=[3](1) -> Total count = 5 | count=5 > 4 -> high = mid - 1 = 4 |
| `Iter 2` | mid = 2. Row counts <= 2: R1=[1](1), R2=[2](1), R3=[](0) -> Total count = 2 | count=2 <= 4 -> low = mid + 1 = 3 |
| `Iter 3` | mid = 3. Row counts <= 3: R1=[1,3](2), R2=[2](1), R3=[3](1) -> Total count = 4 | count=4 <= 4 -> low = mid + 1 = 4 |
| `Iter 4` | mid = 4. Row counts <= 4: R1=[1,3](2), R2=[2](1), R3=[3](1) -> Total count = 4 | count=4 <= 4 -> low = mid + 1 = 5 |
| `Exit` | low (5) > high (4) -> loop terminates | Return low = 5 ✅ (Sorted: [1, 2, 3, 3, 5, 6, 6, 9, 9] -> Median at index 4 is 5) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements in matrix identical (`all 5s` -> returns 5).
- 1x1 matrix (`[[7]]` -> returns 7).
- Wide matrix ($R=1, C=10^5$ -> returns `matrix[0][C/2]`).

### Common Bugs to Avoid
- Using `lower_bound` instead of `upper_bound`: `lower_bound` counts elements $< x$, but we need count of elements $\le x$ (which `upper_bound` provides).
- Using `count < required` instead of `count <= required`: if `count == required`, $mid$ is strictly NOT the median yet (since the median must have at least $(N/2)+1$ elements $\le$ it).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is upper_bound used instead of lower_bound?**  
  **A**: `upper_bound(row, x)` returns an iterator to the first element strictly $> x$. The index distance `it - row.begin()` gives the exact number of elements $\le x$. `lower_bound` would count elements $< x$, omitting duplicates equal to $x$.

- **Q2: Why does returning low guarantee returning an element that actually exists in the matrix?**  
  **A**: Because `low` is the smallest integer where the count transitions from $\le \text{req}$ to $> \text{req}$. If $X$ were not in the matrix, the count $\le X$ would be identical to count $\le (X-1)$, so the boundary transition can only happen at an actual value present in the matrix!

- **Q3: How does this compare to finding the K-th smallest element in a matrix?**  
  **A**: Set `required = k - 1`. The exact same algorithm finds the $K$-th smallest element in $\mathcal{O}(R \log C \log(\text{range}))$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `2D Matrix`, `Matrix Median`, `Hard`
- **Related problems to practice next**:
- **Kth Element in Sorted Matrix**: Generalization to arbitrary K.
- **Median of Two Sorted Arrays**: Two array median.
- **Search in a 2D Matrix II**: Staircase search.
