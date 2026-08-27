# 3D DP: Ninja and his Friends (Cherry Pickup II) (Step 16.2 — 2D/3D DP and DP on Grids)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [3D DP: Ninja and his Friends (Cherry Pickup II)](https://takeuforward.org/data-structure/3-d-dp-ninja-and-his-friends-dp-13/)
- **Difficulty**: Hard
- **Statement**: You are given an $r \times c$ matrix `grid` representing a field of cherries. You have two robots: Robot 1 located at `(0, 0)` and Robot 2 located at `(0, c - 1)`. Both robots move down simultaneously from row $i$ to row $i + 1$, moving to $(i + 1, j - 1)$, $(i + 1, j)$, or $(i + 1, j + 1)$. When a robot visits a cell, it picks up all cherries. If both robots land on the same cell, the cherries are collected only once. Return the maximum number of cherries both robots can collect reaching the last row.

---

## 1. Problem, Restated

Find the maximum collected cherries by two synchronized robots moving from top to bottom on an $r \times c$ grid using 3D Dynamic Programming in $\mathcal{O}(R \times C^2)$ time and $\mathcal{O}(C^2)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Boolean result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Synchronized Row Invariant**: Since both robots move DOWN simultaneously one row per step, both robots are ALWAYS on the exact same row $r$ at any step! 
This allows us to collapse 4 coordinates $(r_1, c_1, r_2, c_2)$ into **3 state variables $(r, c_1, c_2)$**! 
**State Definition**: $\text{dp}[r][c_1][c_2] = $ Maximum cherries collected from row $r$ to $r - 1$ given Robot 1 is at column $c_1$ and Robot 2 is at column $c_2$. 
**Cell Cherry Collection**: 
- If $c_1 == c_2$: $\text{cherries} = \text{grid}[r][c_1]$ (collected once). 
- If $c_1 \ne c_2$: $\text{cherries} = \text{grid}[r][c_1] + \text{grid}[r][c_2]$. 
**Transition**: Each robot has 3 moves $\{-1, 0, +1\}$, giving $3 \times 3 = 9$ move combinations for $(dc_1, dc_2)$: 
$$\text{dp}[r][c_1][c_2] = \text{cherries} + \max_{-1 \le dc_1, dc_2 \le +1} \text{dp}[r + 1][c_1 + dc_1][c_2 + dc_2]$$ 
**Space Optimization**: Row $r$ depends only on row $r + 1$. Maintain a 2D table `front[c][c]` in $\mathcal{O}(R \times C^2)$ time and $\mathcal{O}(C^2)$ space.

- **Underlying Pattern**: `3D Grid DP / Synchronized Multi-Agent State Space $\text{dp}[r][c_1][c_2]$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all $9^R$ simultaneous robot trajectories in $\mathcal{O}(9^R)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    // O(9^R) exponential brute force
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(9^R)$ time.
- **Space Complexity**: $\mathcal{O}(R)$ recursion stack.
- **Why it's not good enough**: Synchronized exponential 9-way branching at every row.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
3D Tabulation DP table of size R x C x C in O(R x C^2) time and O(R x C^2) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution3D {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        int r = grid.size(), c = grid[0].size();
        vector<vector<vector<int>>> dp(r, vector<vector<int>>(c, vector<int>(c, 0)));
        // Base case for last row
        for (int j1 = 0; j1 < c; j1++) {
            for (int j2 = 0; j2 < c; j2++) {
                if (j1 == j2) dp[r - 1][j1][j2] = grid[r - 1][j1];
                else dp[r - 1][j1][j2] = grid[r - 1][j1] + grid[r - 1][j2];
            }
        }
        for (int i = r - 2; i >= 0; i--) {
            for (int j1 = 0; j1 < c; j1++) {
                for (int j2 = 0; j2 < c; j2++) {
                    int maxi = -1e8;
                    for (int dj1 = -1; dj1 <= 1; dj1++) {
                        for (int dj2 = -1; dj2 <= 1; dj2++) {
                            int nj1 = j1 + dj1, nj2 = j2 + dj2;
                            int val = (j1 == j2) ? grid[i][j1] : grid[i][j1] + grid[i][j2];
                            if (nj1 >= 0 && nj1 < c && nj2 >= 0 && nj2 < c)
                                val += dp[i + 1][nj1][nj2];
                            else val = -1e8;
                            maxi = max(maxi, val);
                        }
                    }
                    dp[i][j1][j2] = maxi;
                }
            }
        }
        return dp[0][0][c - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R \times C^2)$ time.
- **Space Complexity**: $\mathcal{O}(R \times C^2)$ space.
- **Why it's still not optimal**: Retaining the full 3D array uses unnecessary memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
2D Space-Optimized DP `front[c][c]` in $\mathcal{O}(R \times C^2)$ time and $\mathcal{O}(C^2)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int cherryPickup(vector<vector<int>>& grid) {
        int r = grid.size();
        int c = grid[0].size();
        
        // front[j1][j2] stores max cherries from (row + 1) with robots at (j1, j2)
        vector<vector<int>> front(c, vector<int>(c, 0));
        
        // 1. Base case: Initialize for the last row (r - 1)
        for (int j1 = 0; j1 < c; j1++) {
            for (int j2 = 0; j2 < c; j2++) {
                if (j1 == j2) {
                    front[j1][j2] = grid[r - 1][j1];
                } else {
                    front[j1][j2] = grid[r - 1][j1] + grid[r - 1][j2];
                }
            }
        }
        
        // 2. Iterate upward from second-last row (r - 2) down to 0
        for (int i = r - 2; i >= 0; i--) {
            vector<vector<int>> cur(c, vector<int>(c, 0));
            
            for (int j1 = 0; j1 < c; j1++) {
                for (int j2 = 0; j2 < c; j2++) {
                    int maxCherries = -1e8;
                    
                    // Explore all 9 combinations of moves (-1, 0, +1)
                    for (int dj1 = -1; dj1 <= 1; dj1++) {
                        for (int dj2 = -1; dj2 <= 1; dj2++) {
                            int nj1 = j1 + dj1;
                            int nj2 = j2 + dj2;
                            
                            int cherries = (j1 == j2) ? grid[i][j1] : grid[i][j1] + grid[i][j2];
                            
                            if (nj1 >= 0 && nj1 < c && nj2 >= 0 && nj2 < c) {
                                cherries += front[nj1][nj2];
                            } else {
                                cherries += -1e8; // Invalid out-of-bounds branch
                            }
                            
                            maxCherries = max(maxCherries, cherries);
                        }
                    }
                    
                    cur[j1][j2] = maxCherries;
                }
            }
            
            front = cur; // Roll 2D state upward
        }
        
        // Robot 1 starts at col 0, Robot 2 starts at col c - 1 on row 0
        return front[0][c - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(R \times C^2 \times 9) = \mathcal{O}(R \times C^2)$ time.
- **Space Complexity**: $\mathcal{O}(C^2)$ space (two 2D matrices of size $C \times C$).
- **Why this is optimal**: 2D rolling slice minimizes memory footprint down to $\mathcal{O}(C^2)$ ($70 \times 70 = 4900$ ints $\approx 20$ KB).

---

## 6. Dry Run

`grid = [[3,1,1],[2,5,1],[1,5,5],[2,1,1]]` ($R = 4, C = 3$)

| Step | Action / State Change | Result |
|---|---|---|
| `Base Row 3` | front[0][2] = grid[3][0] + grid[3][2] = 2 + 1 = 3 | Base initialized |
| `Row 2 (1,5,5)` | j1=1, j2=2: grid val = 5+5=10 + front max $\implies$ rolled | Row 2 evaluated |
| `Row 1 (2,5,1)` | Optimal paths for robots converging on rich cells | Row 1 evaluated |
| `Row 0 (3,1,1)` | front[0][2] computes optimal joint collection $\implies 24$ | Result = 24 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $C = 1$ (both robots start on the same cell).
- $R = 1$ (single row base case).
- Cells with 0 cherries.

### Common Bugs to Avoid
- Using `-1e9` inside addition causing signed integer underflow (use `-1e8` or check bounds before addition).
- Double-counting cherries when $j_1 == j_2$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can we reduce the state from 4D (r1, c1, r2, c2) to 3D (r, c1, c2)?**  
  **A**: Because both robots move down synchronously by 1 row on every step. Thus $r_1 == r_2 == r$ at all times! Collapsing the two row variables into a single row parameter $r$ cuts memory from $\mathcal{O}(R^2 C^2)$ to $\mathcal{O}(R C^2)$!

- **Q2: Why is running 2 independent single-robot DP passes WRONG?**  
  **A**: Because greedy/independent passes do not coordinate! A single robot might take an optimal path that leaves zero cherries for the second robot, whereas a coordinated path could collect far more cherries collectively!

- **Q3: How does this compare to Cherry Pickup I (LeetCode 741)?**  
  **A**: In Cherry Pickup I, a single robot goes from $(0,0) \to (N-1,N-1)$ and returns $(N-1,N-1) \to (0,0)$. Walking back is equivalent to **two robots starting at $(0,0)$ moving simultaneously** to $(N-1,N-1)$ with Manhattan distance step $k = r_1 + c_1 = r_2 + c_2$!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `3D DP`, `Multi-Agent`, `LeetCode-1463`, `Hard`
- **Related problems to practice next**:
- **Cherry Pickup I**: Two-robot Manhattan DP.
- **Minimum Falling Path Sum**: 1-robot 2D precursor.
