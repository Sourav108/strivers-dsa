# Matrix Chain Multiplication - Tabulation (Bottom-Up) (Step 16.7 — Matrix Chain Multiplication / Partition DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Matrix Chain Multiplication - Tabulation (Bottom-Up)](https://takeuforward.org/data-structure/matrix-chain-multiplication-tabulation-mcm-dp-49/)
- **Difficulty**: Hard
- **Statement**: Implement Matrix Chain Multiplication using purely iterative Bottom-Up Tabulation DP. Given an array `arr` of dimensions representing $N - 1$ matrices, compute the minimum scalar multiplications in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space without recursion.

---

## 1. Problem, Restated

Compute minimum scalar multiplications using iterative length-based bottom-up table filling in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Length / Count / Cost result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Bottom-Up Interval Progression**: 
In Top-Down recursion, we start with large interval $[1, N-1]$ and break down into smaller sub-intervals. 
In **Bottom-Up Tabulation**, we MUST evaluate smaller chain lengths first: 
1) Base Case: Chain length 1 ($i == j$) has 0 cost $\implies \text{dp}[i][i] = 0$ for all $i$. 
2) Loop over chain length $len = 2$ to $N - 1$: 
3) For each start index $i \in [1, N - len]$: 
   - End index $j = i + len - 1$ 
   - Pivot $k \in [i, j - 1]$: 
     $$\text{dp}[i][j] = \min_{i \le k < j} (\text{dp}[i][k] + \text{dp}[k + 1][j] + \text{arr}[i - 1] \times \text{arr}[k] \times \text{arr}[j])$$ 
4) Result is $\text{dp}[1][N - 1]$. Runs in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

- **Underlying Pattern**: `Bottom-Up Interval DP / Diagonal Chain Length Progression ($len = 2 \dots N-1$)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Memoized Top-Down recursion with function call stack overhead in $\mathcal{O}(N^3)$ time.

### C++17 Code
```cpp
// Top-down memoization
```

### Java Code
```java
// Java equivalent
// Top-down memoization
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ memo table + call stack.
- **Why it's not good enough**: Recursion stack frames.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DP below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative Diagonal Length Bottom-Up Tabulation in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int matrixMultiplication(int N, vector<int>& arr) {
        // dp[i][j] stores min scalar multiplications for matrix chain A_i ... A_j
        vector<vector<int>> dp(N, vector<int>(N, 0));
        
        // Base case: dp[i][i] = 0 (already initialized to 0)
        
        // Iterate chain length from 2 up to N - 1
        for (int len = 2; len < N; len++) {
            for (int i = 1; i <= N - len; i++) {
                int j = i + len - 1;
                dp[i][j] = 1e9;
                
                // Try all split points k between i and j - 1
                for (int k = i; k < j; k++) {
                    int cost = dp[i][k] + dp[k + 1][j] + arr[i - 1] * arr[k] * arr[j];
                    dp[i][j] = min(dp[i][j], cost);
                }
            }
        }
        
        // Entire chain from matrix 1 to matrix N - 1
        return dp[1][N - 1];
    }
};
```

### Java Code
```java
class Solution {

    int matrixMultiplication(int N, int[] arr) {
        // dp[i][j] stores min scalar multiplications for matrix chain A_i ... A_j
        int[][] dp = new int[N][N];
        
        // Base case: dp[i][i] = 0 (already initialized to 0)
        
        // Iterate chain length from 2 up to N - 1
        for (int len = 2; len < N; len++) {
            for (int i = 1; i <= N - len; i++) {
                int j = i + len - 1;
                dp[i][j] = 1e9;
                
                // Try all split points k between i and j - 1
                for (int k = i; k < j; k++) {
                    int cost = dp[i][k] + dp[k + 1][j] + arr[i - 1] * arr[k] * arr[j];
                    dp[i][j] = Math.min(dp[i][j], cost);
                }
            }
        }
        
        // Entire chain from matrix 1 to matrix N - 1
        return dp[1][N - 1];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time ($\approx N^3 / 6$ inner loop operations).
- **Space Complexity**: $\mathcal{O}(N^2)$ space (contiguous 2D DP matrix).
- **Why this is optimal**: Eliminates recursion stack completely with optimal cache-friendly loop ordering.

---

## 6. Dry Run

`arr = [40, 20, 30, 10, 30]` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `len = 2` | dp[1][2] = 24000; dp[2][3] = 6000; dp[3][4] = 9000 | Lengths of size 2 |
| `len = 3` | dp[1][3] = min(dp[1][1]+dp[2][3]+40*20*10=14000, dp[1][2]+dp[3][3]+40*30*10=36000) = 14000 | dp[1][3] = 14000 |
| `len = 3` | dp[2][4] = min(dp[2][2]+dp[3][4]+20*30*30=27000, dp[2][3]+dp[4][4]+20*10*30=12000) = 12000 | dp[2][4] = 12000 |
| `len = 4` | dp[1][4] = min(k=1: 0+12000+40*20*30=36000; k=2: 24000+9000+40*30*30=69000; k=3: 14000+0+40*10*30=26000) = 26000 | dp[1][4] = 26000 |
| `Result` | Return dp[1][4] = 26000 | Min Cost = 26000 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 2$ (0 multiplications).
- $N = 3$ (single matrix multiplication).
- Equal dimensions.

### Common Bugs to Avoid
- Incorrect outer loop ordering (using standard `for i` and `for j` before sub-intervals are computed; MUST iterate by chain length `len`!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST bottom-up tabulation iterate by chain length 'len' rather than standard nested (i, j)?**  
  **A**: Because computing $\text{dp}[i][j]$ requires smaller intervals $\text{dp}[i][k]$ and $\text{dp}[k+1][j]$. If we iterated row-by-row ($i=1 \dots N$), the entry $\text{dp}[k+1][j]$ on a future row would NOT be computed yet! Iterating by interval length guarantees all sub-problems of length $< len$ are solved!

- **Q2: How does this length loop order compare to reverse row iteration (for i from N-1 down to 1)?**  
  **A**: Alternatively, running `for (int i = N-1; i >= 1; i--) for (int j = i + 1; j < N; j++)` ALSO computes subproblems correctly because row $k+1 > i$ is already computed! Both achieve $\mathcal{O}(N^3)$.

- **Q3: What is the memory access pattern efficiency of Tabulation vs Memoization?**  
  **A**: Tabulation accesses contiguous memory linearly along diagonals, giving high L1 data cache line hits and zero branch prediction recursion misses.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `MCM`, `Tabulation`, `Partition DP`, `Hard`
- **Related problems to practice next**:
- **MCM Memoization**: Top-down counterpart.
- **Minimum Cost to Cut a Stick**: Stick cutting.
