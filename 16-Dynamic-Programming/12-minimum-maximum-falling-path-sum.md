# Minimum / Maximum Falling Path Sum (Variable start/end points) (Step 16.2 — 2D/3D DP and DP on Grids)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Minimum / Maximum Falling Path Sum (Variable start/end points)](https://takeuforward.org/data-structure/minimum-maximum-falling-path-sum-dp-12/)
- **Difficulty**: Medium
- **Statement**: Given an $n \times n$ array of integers `matrix`, return the minimum sum of any falling path through `matrix`. A falling path starts at any element in the first row and chooses the element in the next row that is either directly below $(r + 1, c)$ or diagonally left/right $(r + 1, c - 1)$ or $(r + 1, c + 1)$.

---

## 1. Problem, Restated

Find the minimum path sum starting from ANY cell in row 0 and ending at ANY cell in row $n - 1$ moving $\pm 1$ or 0 horizontally in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Boolean result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

To reach cell $(i, j)$ on row $i$, the path could have originated from 3 possible parent cells on row $i - 1$: 
1) Top-Left $(i - 1, j - 1)$ (if $j > 0$) 
2) Directly Above $(i - 1, j)$ 
3) Top-Right $(i - 1, j + 1)$ (if $j < n - 1$) 
**Recurrence**: 
$$\text{dp}[i][j] = \text{matrix}[i][j] + \min(\text{dp}[i - 1][j - 1], \text{dp}[i - 1][j], \text{dp}[i - 1][j + 1])$$ 
**Base Case**: Row 0 is directly initialized to `matrix[0]`. 
**Space Optimization**: Only row $i - 1$ is needed to compute row $i$. Maintain a 1D vector `prev(n)` in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space. Result is $\min_{0 \le j < n}(\text{prev}[j])$.

- **Underlying Pattern**: `2D Grid DP with 3-Way Branching ($c-1, c, c+1$) & Variable Endpoints`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all 3 choices for every cell starting from each column in row 0 in $\mathcal{O}(N \cdot 3^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int j, int n, const vector<vector<int>>& m) {
        if (j < 0 || j >= n) return 1e9;
        if (i == 0) return m[0][j];
        int up = solve(i - 1, j, n, m);
        int left = solve(i - 1, j - 1, n, m);
        int right = solve(i - 1, j + 1, n, m);
        return m[i][j] + min({up, left, right});
    }
public:
    int minFallingPathSum(vector<vector<int>>& matrix) {
        int n = matrix.size(), ans = 1e9;
        for (int j = 0; j < n; j++) ans = min(ans, solve(n - 1, j, n, matrix));
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 3^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Ternary recursive tree with massive overlapping states.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
2D Tabulation DP table in O(N^2) time and O(N^2) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution2D {
public:
    int minFallingPathSum(vector<vector<int>>& matrix) {
        int n = matrix.size();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        for (int j = 0; j < n; j++) dp[0][j] = matrix[0][j];
        for (int i = 1; i < n; i++) {
            for (int j = 0; j < n; j++) {
                int up = dp[i - 1][j];
                int left = (j > 0) ? dp[i - 1][j - 1] : 1e9;
                int right = (j < n - 1) ? dp[i - 1][j + 1] : 1e9;
                dp[i][j] = matrix[i][j] + min({up, left, right});
            }
        }
        int ans = 1e9;
        for (int j = 0; j < n; j++) ans = min(ans, dp[n - 1][j]);
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ table space.
- **Why it's still not optimal**: Full 2D matrix allocation is redundant.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Space-Optimized DP in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minFallingPathSum(vector<vector<int>>& matrix) {
        int n = matrix.size();
        
        // prev[j] stores minimum falling path sum to reach (previous_row, j)
        vector<int> prev = matrix[0];
        
        for (int i = 1; i < n; i++) {
            vector<int> cur(n, 0);
            for (int j = 0; j < n; j++) {
                int up = prev[j];
                int left = (j > 0) ? prev[j - 1] : 1e9;
                int right = (j < n - 1) ? prev[j + 1] : 1e9;
                
                cur[j] = matrix[i][j] + min({up, left, right});
            }
            prev = cur;
        }
        
        // Find minimum among all column endings in the last row
        int minSum = 1e9;
        for (int j = 0; j < n; j++) {
            minSum = min(minSum, prev[j]);
        }
        
        return minSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time (each of the $N \times N$ cells evaluated in $\mathcal{O}(1)$).
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary space (two row vectors).
- **Why this is optimal**: 3-way ancestor transition requires only the immediately preceding row.

---

## 6. Dry Run

`matrix = [[2,1,3],[6,5,4],[7,8,9]]` ($N = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Row 0` | prev: `[2, 1, 3]` | Base ready |
| `Row 1 (6, 5, 4)` | j=0: 6+min(2,1)=7; j=1: 5+min(2,1,3)=6; j=2: 4+min(1,3)=5 $\implies prev = [7, 6, 5]$ | Row 1 done |
| `Row 2 (7, 8, 9)` | j=0: 7+min(7,6)=13; j=1: 8+min(7,6,5)=13; j=2: 9+min(6,5)=14 $\implies prev = [13, 13, 14]$ | Row 2 done |
| `Result` | min(13, 13, 14) = 13 (Path: $1 \to 5 \to 7 = 13$ or $1 \to 4 \to 8 = 13$) | Min Falling Sum = 13 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `matrix[0][0]`).
- All negative numbers (finds most negative path).
- $100 \times 100$ grid.

### Common Bugs to Avoid
- Returning 0 for out-of-bounds columns instead of infinity `1e9` (leads to incorrect minimum selection on boundary cells).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to solve Maximum Falling Path Sum?**  
  **A**: Replace $\min$ with $\max$ and initialize out-of-bounds guards to `-1e9`. Recurrence: $\text{dp}[i][j] = \text{matrix}[i][j] + \max(\text{left}, \text{up}, \text{right})$.

- **Q2: How to handle Non-Square rectangular matrices (M x N)?**  
  **A**: Outer loop runs $M$ times, inner loop runs $N$ times. Space complexity is $\mathcal{O}(N)$.

- **Q3: How does Falling Path Sum II (LeetCode 1289) differ?**  
  **A**: In Falling Path Sum II, you cannot choose the same column in the next row (any $k \ne j$). We optimize from $\mathcal{O}(N^3)$ to $\mathcal{O}(N^2)$ by tracking the **minimum and second minimum** of the previous row!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Grid DP`, `Falling Path`, `LeetCode-931`, `Medium`
- **Related problems to practice next**:
- **Minimum Path Sum in Triangular Grid**: 2-choice triangular DP.
- **Cherry Pickup II**: Dual-agent 3D DP.
