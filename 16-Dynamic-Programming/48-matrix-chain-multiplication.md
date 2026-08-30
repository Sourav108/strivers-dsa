# Matrix Chain Multiplication (MCM Partition Template) (Step 16.7 — Matrix Chain Multiplication / Partition DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Matrix Chain Multiplication (MCM Partition Template)](https://takeuforward.org/data-structure/matrix-chain-multiplication-dp-48/)
- **Difficulty**: Hard
- **Statement**: Given a sequence of matrices, find the most efficient way to multiply these matrices together. The problem is not actually to perform the multiplications, but merely to decide in what order to perform the multiplications (parenthesization). Given an array `arr` of dimensions such that matrix $A_i$ has dimension $\text{arr}[i-1] \times \text{arr}[i]$, find the minimum number of scalar multiplications needed to compute the product $A_1 A_2 \dots A_{N-1}$.

---

## 1. Problem, Restated

Find the optimal parenthesization of matrix chain $A_1 \dots A_{N-1}$ to minimize scalar multiplications using the standard Partition DP template in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Length / Count / Cost result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The MCM Partition DP Paradigm**: 
To multiply matrix chain from index $i$ to $j$ ($A_i \dots A_j$): 
We can place the final outer parenthesis between matrix $k$ and matrix $k + 1$ for any pivot $k \in [i, j - 1]$: 
$$(A_i \dots A_k) \times (A_{k+1} \dots A_j)$$ 
**Cost Breakdown for Pivot $k$**: 
1) Cost to multiply left sub-chain: $\text{dp}[i][k]$ 
2) Cost to multiply right sub-chain: $\text{dp}[k + 1][j]$ 
3) Cost to multiply the two resulting matrices $(\text{arr}[i-1] \times \text{arr}[k])$ and $(\text{arr}[k] \times \text{arr}[j])$: 
   $$\text{scalar multiplications} = \text{arr}[i - 1] \times \text{arr}[k] \times \text{arr}[j]$$ 
**Master Recurrence**: 
$$\text{dp}[i][j] = \min_{i \le k < j} (\text{dp}[i][k] + \text{dp}[k + 1][j] + \text{arr}[i - 1] \times \text{arr}[k] \times \text{arr}[j])$$ 
Base Case: $\text{dp}[i][i] = 0$ (single matrix requires 0 multiplications). 
Runs in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

- **Underlying Pattern**: `Partition DP Template / Interval Partitioning across Pivot $k \in [i, j-1]$`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively test all Catalan $(C_{N-1})$ parenthesizations in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
    int solve(int i, int j, const vector<int>& arr) {
        if (i == j) return 0;
        int minOps = 1e9;
        for (int k = i; k < j; k++) {
            int ops = solve(i, k, arr) + solve(k + 1, j, arr) + arr[i - 1] * arr[k] * arr[j];
            minOps = min(minOps, ops);
        }
        return minOps;
    }
public:
    int matrixMultiplication(int N, vector<int>& arr) {
        return solve(1, N - 1, arr);
    }
};
```

### Java Code
```java
class SolutionNaive {
    int solve(int i, int j, int[] arr) {
        if (i == j) return 0;
        int minOps = 1e9;
        for (int k = i; k < j; k++) {
            int ops = solve(i, k, arr) + solve(k + 1, j, arr) + arr[i - 1] * arr[k] * arr[j];
            minOps = Math.min(minOps, ops);
        }
        return minOps;
    }

    int matrixMultiplication(int N, int[] arr) {
        return solve(1, N - 1, arr);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ (Catalan number $C_N = \frac{1}{N+1}\binom{2N}{N}$).
- **Space Complexity**: $\mathcal{O}(N)$ recursion stack.
- **Why it's not good enough**: Combinatorial parenthesization tree.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Top-Down Memoization DP table of size N x N in O(N^3) time and O(N^2) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionMemo {
    int memo(int i, int j, const vector<int>& arr, vector<vector<int>>& dp) {
        if (i == j) return 0;
        if (dp[i][j] != -1) return dp[i][j];
        
        int minOps = 1e9;
        for (int k = i; k < j; k++) {
            int ops = memo(i, k, arr, dp) + memo(k + 1, j, arr, dp) + arr[i - 1] * arr[k] * arr[j];
            minOps = min(minOps, ops);
        }
        return dp[i][j] = minOps;
    }
public:
    int matrixMultiplication(int N, vector<int>& arr) {
        vector<vector<int>> dp(N, vector<int>(N, -1));
        return memo(1, N - 1, arr, dp);
    }
};
```

### Java Code
```java
class SolutionMemo {
    int memo(int i, int j, int[] arr, int[][] dp) {
        if (i == j) return 0;
        if (dp[i][j] != -1) return dp[i][j];
        
        int minOps = 1e9;
        for (int k = i; k < j; k++) {
            int ops = memo(i, k, arr, dp) + memo(k + 1, j, arr, dp) + arr[i - 1] * arr[k] * arr[j];
            minOps = Math.min(minOps, ops);
        }
        return dp[i][j] = minOps;
    }

    int matrixMultiplication(int N, int[] arr) {
        int[][] dp = new int[N][N];
        return memo(1, N - 1, arr, dp);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time.
- **Space Complexity**: $\mathcal{O}(N^2)$ memo space.
- **Why it's still not optimal**: Top-down memoization has recursion stack overhead compared to iterative bottom-up tabulation.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Top-Down Memoized / Bottom-Up Interval DP in $\mathcal{O}(N^3)$ time and $\mathcal{O}(N^2)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    int solve(int i, int j, const vector<int>& arr, vector<vector<int>>& dp) {
        // Base case: A single matrix has 0 multiplication cost
        if (i == j) return 0;
        
        if (dp[i][j] != -1) return dp[i][j];
        
        int minCost = 1e9;
        
        // Partition interval [i ... j] at every pivot k
        for (int k = i; k < j; k++) {
            int steps = solve(i, k, arr, dp) 
                      + solve(k + 1, j, arr, dp) 
                      + arr[i - 1] * arr[k] * arr[j];
                      
            minCost = min(minCost, steps);
        }
        
        return dp[i][j] = minCost;
    }
    
public:
    int matrixMultiplication(int N, vector<int>& arr) {
        vector<vector<int>> dp(N, vector<int>(N, -1));
        
        // Matrices are indexed from 1 to N - 1
        return solve(1, N - 1, arr, dp);
    }
};
```

### Java Code
```java
class Solution {

    int solve(int i, int j, int[] arr, int[][] dp) {
        // Base case: A single matrix has 0 multiplication cost
        if (i == j) return 0;
        
        if (dp[i][j] != -1) return dp[i][j];
        
        int minCost = 1e9;
        
        // Partition interval [i ... j] at every pivot k
        for (int k = i; k < j; k++) {
            int steps = solve(i, k, arr, dp) 
                      + solve(k + 1, j, arr, dp) 
                      + arr[i - 1] * arr[k] * arr[j];
                      
            minCost = Math.min(minCost, steps);
        }
        
        return dp[i][j] = minCost;
    }

    int matrixMultiplication(int N, int[] arr) {
        int[][] dp = new int[N][N];
        
        // Matrices are indexed from 1 to N - 1
        return solve(1, N - 1, arr, dp);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^3)$ time ($\mathcal{O}(N^2)$ subproblems $\times \mathcal{O}(N)$ partition loop).
- **Space Complexity**: $\mathcal{O}(N^2)$ space (DP table of size $N \times N$).
- **Why this is optimal**: Partition DP guarantees evaluating all valid sub-chain intervals exactly once.

---

## 6. Dry Run

`arr = [10, 20, 30, 40, 30]` ($N = 5$, 4 matrices: $A_1(10\times 20), A_2(20\times 30), A_3(30\times 40), A_4(40\times 30)$)

| Step | Action / State Change | Result |
|---|---|---|
| `Base Cases` | dp[1][1]=0, dp[2][2]=0, dp[3][3]=0, dp[4][4]=0 | Single matrices ready |
| `Length 2: dp[1][2]` | $k=1: 0 + 0 + 10 \times 20 \times 30 = 6000$ | dp[1][2] = 6000 |
| `Length 2: dp[2][3]` | $k=2: 0 + 0 + 20 \times 30 \times 40 = 24000$ | dp[2][3] = 24000 |
| `Length 2: dp[3][4]` | $k=3: 0 + 0 + 30 \times 40 \times 30 = 36000$ | dp[3][4] = 36000 |
| `Length 3: dp[1][3]` | $k=1: dp[2][3]+10\times 20\times 40=32000; k=2: dp[1][2]+10\times 30\times 40=18000$ | dp[1][3] = 18000 |
| `Length 4: dp[1][4]` | Pivot $k=3: dp[1][3] + dp[4][4] + 10\times 40\times 30 = 18000 + 0 + 12000 = 30000$ | dp[1][4] = 30000 |
| `Result` | Return dp[1][4] = 30000 (Parenthesization: $((A_1 A_2) A_3) A_4$) | Min Scalar Mults = 30000 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 2$ (only 1 matrix $\implies$ returns 0).
- $N = 3$ (two matrices $\implies \text{arr}[0] \times \text{arr}[1] \times \text{arr}[2]$).
- Dimensions with large values (check 32-bit vs 64-bit limits).

### Common Bugs to Avoid
- Setting pivot range $k \le j$ instead of $k < j$ (leads to infinite recursion on `solve(k+1, j)`).
- Indexing matrices from 0 instead of 1 (dimensions array has size $N$, matrices are $1 \dots N-1$).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the standard Partition DP template defined over interval [i, j] split at pivot k?**  
  **A**: Because any contiguous sequence of operations can be partitioned into 'the very last operation performed'. If the last operation splits the interval at pivot $k$, the left problem $[i, k]$ and right problem $[k+1, j]$ are completely independent! This applies to Burst Balloons, Cut Stick, Palindrome Partitioning, and Boolean Parenthesization!

- **Q2: Can Matrix Chain Multiplication be solved faster than O(N^3)?**  
  **A**: YES! **Hu & Shing Algorithm** solves MCM in $\mathcal{O}(N \log N)$ time by transforming matrix chain multiplication into finding a minimum weight triangulation of a convex polygon!

- **Q3: How to print the optimal parenthesized expression string?**  
  **A**: Maintain a 2D split matrix `bracket[i][j] = k`. Recursively print `"(" + print(i, k) + print(k+1, j) + ")"` in $\mathcal{O}(N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `MCM`, `Partition DP`, `Interval DP`, `Hard`
- **Related problems to practice next**:
- **MCM Tabulation**: Bottom-up counterpart.
- **Burst Balloons**: Partition DP variant.
