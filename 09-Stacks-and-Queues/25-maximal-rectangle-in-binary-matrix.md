# Maximal Rectangle in Binary Matrix (2D Histogram) (Step 9.3 — Monotonic Stack / Queue)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Maximal Rectangle in Binary Matrix (2D Histogram)](https://takeuforward.org/data-structure/maximal-rectangle-in-binary-matrix/)
- **Difficulty**: Hard
- **Statement**: Given a $rows \times cols$ binary `matrix` filled with `0`s and `1`s, find the largest rectangle containing only `1`s and return its area.

---

## 1. Problem, Restated

Convert 2D matrix into dynamic 1D histograms across rows, solving each row via Largest Rectangle in Histogram.

- **Input**: Array / Data Stream / Class method calls.
- **Output**: Resulting vector of elements / integer answer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain a 1D height array `heights[cols]`. For each row: if `matrix[r][c] == '1'`, `heights[c] += 1`; if `matrix[r][c] == '0'`, `heights[c] = 0` (resets height base). Run `largestRectangleArea(heights)` on each row and track the maximum rectangle found across all rows in $\mathcal{O}(R \times C)$ time!

- **Underlying Pattern**: `2D Cumulative Heights Accumulator + 1D Histogram Solver`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Test all $(R \times C)^2$ sub-rectangles and validate all 1s in $\mathcal{O}((R \times C)^3)$ time.

### C++17 Code
```cpp
// O((R*C)^3) brute check
```

### Java Code
```java
// Java equivalent
// O((R*C)^3) brute check
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((R \cdot C)^3)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Impossible cubic runtime.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — monotonic data structure below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Row-by-Row Histogram Stack in $\mathcal{O}(R \times C)$ time.

### C++17 Code
```cpp
#include <vector>
#include <stack>
#include <algorithm>
using namespace std;

class Solution {
private:
    int largestRectangleArea(const vector<int>& heights) {
        int n = heights.size();
        stack<int> st;
        int maxArea = 0;
        
        for (int i = 0; i <= n; i++) {
            int curH = (i == n) ? 0 : heights[i];
            while (!st.empty() && heights[st.top()] >= curH) {
                int h = heights[st.top()];
                st.pop();
                int w = st.empty() ? i : (i - st.top() - 1);
                maxArea = max(maxArea, h * w);
            }
            st.push(i);
        }
        return maxArea;
    }

public:
    int maximalRectangle(vector<vector<char>>& matrix) {
        if (matrix.empty() || matrix[0].empty()) return 0;
        
        int rows = matrix.size();
        int cols = matrix[0].size();
        vector<int> heights(cols, 0);
        int maxRectangle = 0;
        
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (matrix[r][c] == '1') {
                    heights[c] += 1;
                } else {
                    heights[c] = 0; // reset base
                }
            }
            maxRectangle = max(maxRectangle, largestRectangleArea(heights));
        }
        
        return maxRectangle;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int largestRectangleArea(int[] heights) {
        int n = heights.length;
        Stack<Integer> st = new Stack<>();
        int maxArea = 0;
        
        for (int i = 0; i <= n; i++) {
            int curH = (i == n) ? 0 : heights[i];
            while (!st.isEmpty() && heights[st.peek()] >= curH) {
                int h = heights[st.peek()];
                st.pop();
                int w = st.isEmpty() ? i : (i - st.peek() - 1);
                maxArea = Math.max(maxArea, h * w);
            }
            st.push(i);
        }
        return maxArea;
    }

    int maximalRectangle(char[][] matrix) {
        if (matrix.isEmpty() || matrix[0].isEmpty()) return 0;
        
        int rows = matrix.length;
        int cols = matrix[0].size();
        int[] heights = new int[cols];
        int maxRectangle = 0;
        
        for (int r = 0; r < rows; r++) {
            for (int c = 0; c < cols; c++) {
                if (matrix[r][c] == '1') {
                    heights[c] += 1;
                } else {
                    heights[c] = 0; // reset base
                }
            }
            maxRectangle = Math.max(maxRectangle, largestRectangleArea(heights));
        }
        
        return maxRectangle;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R \times C)$ time.
- **Space Complexity**: $\mathcal{O}(C)$ space for 1D histogram heights array.
- **Why this is optimal**: Reduces 2D geometry problem to $R$ independent linear histogram queries.

---

## 6. Dry Run

`matrix` ($4 \times 5$): Row 0, 1, 2, 3

| Step | Action / State Change | Result |
|---|---|---|
| `Row 0 `[1, 0, 1, 0, 0]`` | heights = `[1, 0, 1, 0, 0]` -> area = 1 | max = 1 |
| `Row 1 `[1, 0, 1, 1, 1]`` | heights = `[2, 0, 2, 1, 1]` -> area = 3 | max = 3 |
| `Row 2 `[1, 1, 1, 1, 1]`` | heights = `[3, 1, 3, 2, 2]` -> area = 6! | max = 6 |
| `Result` | Largest rectangle area | `6` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty matrix `[]` (returns 0).
- Matrix with all 0s (returns 0).
- Matrix with all 1s ($R \times C$).

### Common Bugs to Avoid
- Forgetting to reset `heights[c] = 0` when `matrix[r][c] == '0'`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must heights[c] be reset to 0 upon encountering '0'?**  
  **A**: Because a rectangle in the current row must have a continuous column of 1s resting on the current row. A '0' breaks continuity, resetting the column height to 0.


---

## 9. Tags & Related Problems

- **Tags**: `Stack`, `Monotonic Stack`, `Dynamic Programming`, `LeetCode-85`, `Hard`
- **Related problems to practice next**:
- **Largest Rectangle in Histogram**: 1D foundation.
