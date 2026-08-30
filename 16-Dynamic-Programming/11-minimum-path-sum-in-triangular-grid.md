# Minimum Path Sum in Triangular Grid (Triangle) (Step 16.2 — 2D/3D DP and DP on Grids)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Minimum Path Sum in Triangular Grid (Triangle)](https://takeuforward.org/data-structure/minimum-path-sum-in-triangular-grid-dp-11/)
- **Difficulty**: Medium
- **Statement**: Given a `triangle` array, return the minimum path sum from top to bottom. For each step, you may move to an adjacent number of the row below. More formally, if you are on index $i$ on the current row, you may move to either index $i$ or index $i + 1$ on the next row.

---

## 1. Problem, Restated

Find the minimum path sum from apex $(0, 0)$ to any base cell in a triangular array where $(r, c)$ can transition to $(r + 1, c)$ or $(r + 1, c + 1)$ in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Boolean result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**Why Bottom-Up is Superior**: 
If we start from the top $(0, 0)$, there is 1 starting point and $N$ possible ending points on the base, requiring a final $\min$ scan across the last row. 
Instead, **start from the bottom base**: 
1) Base Case: $\text{dp}[n - 1][j] = \text{triangle}[n - 1][j]$ for all $0 \le j < n$. 
2) Transition (moving upward from row $n - 2$ to 0): 
   $$\text{dp}[i][j] = \text{triangle}[i][j] + \min(\text{dp}[i + 1][j], \text{dp}[i + 1][j + 1])$$ 
3) Result is directly stored at apex $\text{dp}[0][0]$! 
**Space Optimization**: A single 1D vector `prev(n)` initialized with the base row values; update in-place or using `cur[j] = triangle[i][j] + min(prev[j], prev[j + 1])` in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `Bottom-Up Triangular Grid DP / Base-to-Apex State Propagation`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursive tree exploring Down and Diagonal choices at each cell in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int j, int n, const vector<vector<int>>& t) {
        if (i == n - 1) return t[n - 1][j];
        int down = t[i][j] + solve(i + 1, j, n, t);
        int diag = t[i][j] + solve(i + 1, j + 1, n, t);
        return min(down, diag);
    }
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        return solve(0, 0, triangle.size(), triangle);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int j, int n, int[][] t) {
        if (i == n - 1) return t[n - 1][j];
        int down = t[i][j] + solve(i + 1, j, n, t);
        int diag = t[i][j] + solve(i + 1, j + 1, n, t);
        return Math.min(down, diag);
    }

    int minimumTotal(int[][] triangle) {
        return solve(0, 0, triangle.length, triangle);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Overlapping subproblem branches recomputed exponentially.

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
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        vector<vector<int>> dp(n, vector<int>(n, 0));
        for (int j = 0; j < n; j++) dp[n - 1][j] = triangle[n - 1][j];
        for (int i = n - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[i][j] = triangle[i][j] + min(dp[i + 1][j], dp[i + 1][j + 1]);
            }
        }
        return dp[0][0];
    }
};
```

### Java Code
```java
class Solution2D {

    int minimumTotal(int[][] triangle) {
        int n = triangle.length;
        int[][] dp = new int[n][n];
        for (int j = 0; j < n; j++) dp[n - 1][j] = triangle[n - 1][j];
        for (int i = n - 2; i >= 0; i--) {
            for (int j = 0; j <= i; j++) {
                dp[i][j] = triangle[i][j] + Math.min(dp[i + 1][j], dp[i + 1][j + 1]);
            }
        }
        return dp[0][0];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ table space.
- **Why it's still not optimal**: Storing all previous rows is redundant when only the immediately lower row is needed.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
1D Bottom-Up Space-Optimized DP in $\mathcal{O}(N^2)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int minimumTotal(vector<vector<int>>& triangle) {
        int n = triangle.size();
        
        // 1D array initialized to the bottom base row of the triangle
        vector<int> prev = triangle[n - 1];
        
        // Iterate upward from second-last row down to the top apex
        for (int i = n - 2; i >= 0; i--) {
            vector<int> cur(i + 1, 0);
            for (int j = 0; j <= i; j++) {
                cur[j] = triangle[i][j] + min(prev[j], prev[j + 1]);
            }
            prev = cur;
        }
        
        // Apex holds the minimum path sum
        return prev[0];
    }
};
```

### Java Code
```java
class Solution {

    int minimumTotal(int[][] triangle) {
        int n = triangle.length;
        
        // 1D array initialized to the bottom base row of the triangle
        int[] prev = triangle[n - 1];
        
        // Iterate upward from second-last row down to the top apex
        for (int i = n - 2; i >= 0; i--) {
            int[] cur = new int[i + 1];
            for (int j = 0; j <= i; j++) {
                cur[j] = triangle[i][j] + Math.min(prev[j], prev[j + 1]);
            }
            prev = cur;
        }
        
        // Apex holds the minimum path sum
        return prev[0];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time ($N(N+1)/2$ total cell evaluations).
- **Space Complexity**: $\mathcal{O}(N)$ auxiliary space (single array of size $N$).
- **Why this is optimal**: Working bottom-up completely eliminates out-of-bounds checks and minimizes memory allocations.

---

## 6. Dry Run

`triangle = [[2], [3, 4], [6, 5, 7], [4, 1, 8, 3]]` ($N = 4$)

| Step | Action / State Change | Result |
|---|---|---|
| `Base Row 3` | prev: `[4, 1, 8, 3]` | Base ready |
| `Row 2 (6, 5, 7)` | j=0: 6+min(4,1)=7; j=1: 5+min(1,8)=6; j=2: 7+min(8,3)=10 $\implies prev = [7, 6, 10]$ | Row 2 done |
| `Row 1 (3, 4)` | j=0: 3+min(7,6)=9; j=1: 4+min(6,10)=10 $\implies prev = [9, 10]$ | Row 1 done |
| `Row 0 (2)` | j=0: 2+min(9,10) = 11 $\implies prev = [11]$ | Apex done |
| `Result` | Return prev[0] = 11 (Path: $2 \to 3 \to 5 \to 1 = 11$) | Min Path Sum = 11 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `triangle[0][0]`).
- Negative numbers in triangle.
- Right-leaning skewed optimal path.

### Common Bugs to Avoid
- Iterating top-down without boundary checks for $j = 0$ and $j = i$ (bottom-up avoids all boundary branches!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Bottom-Up DP cleaner and more efficient than Top-Down for Triangle DP?**  
  **A**: Top-Down has 1 start and $N$ base destinations, requiring boundary checks (no left neighbor on left edge, no right neighbor on right edge) and a final loop to find the minimum of $N$ values. Bottom-Up starts at the base and converges to a SINGLE value at the apex $(0, 0)$ with zero boundary conditions!

- **Q2: Can we achieve O(1) auxiliary space by modifying the input in-place?**  
  **A**: Yes! By updating `triangle[i][j] += min(triangle[i+1][j], triangle[i+1][j+1])` in-place, auxiliary space becomes $\mathcal{O}(1)$ without extra vectors.

- **Q3: How to print the minimum path?**  
  **A**: Maintain a choice index array, tracking whether left (`j`) or right (`j + 1`) was picked, then trace top-down in $\mathcal{O}(N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Grid DP`, `Triangle`, `LeetCode-120`, `Medium`
- **Related problems to practice next**:
- **Minimum Path Sum in Grid**: Square grid DP.
- **Falling Path Sum**: Variable start/end DP.
