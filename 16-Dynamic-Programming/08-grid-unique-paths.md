# Grid Unique Paths (Combinatorics vs DP) (Step 16.2 — 2D/3D DP and DP on Grids)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Grid Unique Paths (Combinatorics vs DP)](https://takeuforward.org/data-structure/grid-unique-paths-dp-on-grids-dp8/)
- **Difficulty**: Medium
- **Statement**: There is a robot on an $m \times n$ grid. The robot is initially located at the top-left corner `(0, 0)` and tries to move to the bottom-right corner `(m - 1, n - 1)`. The robot can only move either down or right at any point in time. Given the two integers $m$ and $n$, return the number of possible unique paths that the robot can take to reach the bottom-right corner.

---

## 1. Problem, Restated

Compute the number of unique paths from $(0, 0)$ to $(m - 1, n - 1)$ moving only Right or Down using Space-Optimized DP in $\mathcal{O}(M \times N)$ time / $\mathcal{O}(N)$ space or Combinatorics in $\mathcal{O}(\min(M, N))$ time.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ or $\mathcal{O}(1)$ auxiliary space.

---

## 2. Intuition & Pattern

**1. Dynamic Programming Formulation**: 
To arrive at cell $(i, j)$, the robot could only arrive from the cell above $(i - 1, j)$ or the cell to the left $(i, j - 1)$: 
$$\text{dp}[i][j] = \text{dp}[i - 1][j] + \text{dp}[i][j - 1]$$ 
Base case: $\text{dp}[0][0] = 1$. 
Space can be reduced from $\mathcal{O}(M \times N)$ to $\mathcal{O}(N)$ by keeping a single row array `prev(n)` where `prev[j]` is updated as `prev[j] = prev[j] + prev[j - 1]`. 
**2. Combinatorics Closed-Form Solution**: 
Total moves to reach destination: $(m - 1)$ Down moves and $(n - 1)$ Right moves. Total steps $= (m - 1) + (n - 1) = m + n - 2$. 
Choosing $(m - 1)$ down steps out of $(m + n - 2)$ total steps gives: 
$$\text{Unique Paths} = \binom{m + n - 2}{m - 1} = \frac{(m + n - 2)!}{(m - 1)! (n - 1)!}$$ 
Calculated in $\mathcal{O}(\min(m, n))$ time and $\mathcal{O}(1)$ space.

- **Underlying Pattern**: `Grid 2D DP / Combinatorics Combination Formula $\binom{m+n-2}{m-1}$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Naive recursion branching into Down and Right at every cell in $\mathcal{O}(2^{M+N})$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int countPaths(int i, int j, int m, int n) {
        if (i == m - 1 && j == n - 1) return 1;
        if (i >= m || j >= n) return 0;
        return countPaths(i + 1, j, m, n) + countPaths(i, j + 1, m, n);
    }
public:
    int uniquePaths(int m, int n) {
        return countPaths(0, 0, m, n);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int countPaths(int i, int j, int m, int n) {
        if (i == m - 1 && j == n - 1) return 1;
        if (i >= m || j >= n) return 0;
        return countPaths(i + 1, j, m, n) + countPaths(i, j + 1, m, n);
    }

    int uniquePaths(int m, int n) {
        return countPaths(0, 0, m, n);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^{M+N})$ time.
- **Space Complexity**: $\mathcal{O}(M + N)$ recursion stack.
- **Why it's not good enough**: Exponential duplicate tree search.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Space-Optimized 1D Row DP in O(M x N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class SolutionDP {
public:
    int uniquePaths(int m, int n) {
        vector<int> prev(n, 1); // Row 0 has 1 way for each column
        
        for (int i = 1; i < m; i++) {
            vector<int> cur(n, 1);
            for (int j = 1; j < n; j++) {
                cur[j] = cur[j - 1] + prev[j];
            }
            prev = cur;
        }
        return prev[n - 1];
    }
};
```

### Java Code
```java
class SolutionDP {

    int uniquePaths(int m, int n) {
        int[] prev = new int[n]; // Row 0 has 1 way for each column
        
        for (int i = 1; i < m; i++) {
            int[] cur = new int[n];
            for (int j = 1; j < n; j++) {
                cur[j] = cur[j - 1] + prev[j];
            }
            prev = cur;
        }
        return prev[n - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M \times N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: DP is optimal when obstacles are present (Unique Paths II); for unconstrained grids, Combinatorics is $\mathcal{O}(\min(M, N))$.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Optimal Combinatorics $\binom{m+n-2}{r}$ in $\mathcal{O}(\min(M, N))$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
class Solution {
public:
    int uniquePaths(int m, int n) {
        int N = m + n - 2;
        int r = min(m - 1, n - 1); // Choose smaller value to minimize multiplications
        
        double res = 1;
        
        // Compute NCr = (N * (N-1) * ... * (N-r+1)) / (1 * 2 * ... * r)
        for (int i = 1; i <= r; i++) {
            res = res * (N - r + i) / i;
        }
        
        return (int)(res + 0.5); // Round to avoid precision issues
    }
};
```

### Java Code
```java
class Solution {

    int uniquePaths(int m, int n) {
        int N = m + n - 2;
        int r = Math.min(m - 1, n - 1); // Choose smaller value to minimize multiplications
        
        double res = 1;
        
        // Compute NCr = (N * (N-1) * ... * (N-r+1)) / (1 * 2 * ... * r)
        for (int i = 1; i <= r; i++) {
            res = res * (N - r + i) / i;
        }
        
        return (int)(res + 0.5); // Round to avoid precision issues
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\min(M, N))$ time (single loop of size $\le 100$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Analytical combinatorics evaluates closed-form permutation without matrix simulation.

---

## 6. Dry Run

$m = 3, n = 7 \implies N = 3 + 7 - 2 = 8, r = \min(2, 6) = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `i = 1` | res = $1 \times (8 - 2 + 1) / 1 = 7.0$ | res = 7 |
| `i = 2` | res = $7.0 \times (8 - 2 + 2) / 2 = 7.0 \times 8 / 2 = 28.0$ | res = 28 |
| `Result` | Unique Paths = $\binom{8}{2} = \frac{8 \times 7}{2} = 28$ | Total Paths = 28 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $m = 1$ or $n = 1$ (returns 1: only single straight line).
- $m = 100, n = 100$.

### Common Bugs to Avoid
- Integer overflow when computing factorials naively (prevented by multiplying and dividing on the fly `res = res * (N - r + i) / i`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the combinatorial formula $\binom{m+n-2}{m-1}$ strictly equal the number of grid paths?**  
  **A**: Every valid path from $(0,0)$ to $(m-1, n-1)$ is a sequence of length $(m-1) + (n-1)$ containing exactly $(m-1)$ 'Down' steps and $(n-1)$ 'Right' steps. Choosing which $(m-1)$ positions in the sequence are 'Down' steps uniquely determines the path: $\binom{m+n-2}{m-1}$!

- **Q2: When does the combinatorial method fail and force us to use DP?**  
  **A**: When the grid has **obstacles** (Unique Paths II) or variable cost weights (Min Path Sum). Combinatorics requires an unconstrained lattice; DP handles arbitrary geometric constraints seamlessly!

- **Q3: How to handle modulo arithmetic when calculating $\binom{N}{r} \pmod{10^9+7}$?**  
  **A**: Precompute factorials and modular inverse factorials using Fermat's Little Theorem: $a^{-1} \equiv a^{MOD-2} \pmod{MOD}$.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `Grid DP`, `Combinatorics`, `Math`, `LeetCode-62`, `Medium`
- **Related problems to practice next**:
- **Grid Unique Paths II**: Paths with obstacles.
- **Minimum Path Sum**: Min-cost grid paths.
