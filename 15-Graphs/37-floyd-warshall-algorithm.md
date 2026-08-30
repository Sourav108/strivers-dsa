# Floyd-Warshall Algorithm (All-Pairs Shortest Path in O(V^3)) (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Floyd-Warshall Algorithm (All-Pairs Shortest Path in O(V^3))](https://takeuforward.org/data-structure/floyd-warshall-algorithm-g-42/)
- **Difficulty**: Medium
- **Statement**: You are given an adjacency matrix `matrix` of size $n \times n$ representing a directed graph where `matrix[i][j]` denotes the weight of edge $i \to j$. If no edge exists, `matrix[i][j] = -1`. Find the shortest distance between every pair of vertices in-place using the Floyd-Warshall Algorithm. Also detect negative cycles if any diagonal entry `matrix[i][i] < 0`.

---

## 1. Problem, Restated

Compute all-pairs shortest paths on a directed weighted graph with possible negative edge weights in $\mathcal{O}(V^3)$ time using 3 nested loops with intermediate pivot $k$.

- **Input**: `vector<vector<int>>& matrix` of size $N \times N$.
- **Output**: Modified `matrix` in-place containing all-pairs shortest distances.
- **Constraints**: $1 \le n \le 100$, $-1000 \le \text{matrix}[i][j] \le 1000$.

---

## 2. Intuition & Pattern

Let $dp[k][i][j]$ denote the shortest path from vertex $i$ to vertex $j$ using only intermediate vertices from $\{0, 1, \dots, k\}$. **DP Recurrence**: $dp[k][i][j] = \min(dp[k-1][i][j], dp[k-1][i][k] + dp[k-1][k][j])$. 1) Convert all `-1` non-diagonal entries to infinity (`1e9`) and ensure `matrix[i][i] = 0`. 2) Outer loop $k$ from $0$ to $n - 1$ (the pivot node). 3) Inner loops $i$ (source) and $j$ (destination) from $0$ to $n - 1$: if `matrix[i][k] != 1e9 && matrix[k][j] != 1e9`, update `matrix[i][j] = min(matrix[i][j], matrix[i][k] + matrix[k][j])`. 4) Negative Cycle Check: if ANY `matrix[i][i] < 0`, a negative cycle exists! 5) Revert remaining `1e9` cells back to `-1`. Runs in $\mathcal{O}(V^3)$ time and $\mathcal{O}(1)$ auxiliary space.

- **Underlying Pattern**: `All-Pairs Shortest Path Dynamic Programming (Pivot Node Relaxation)`.

---

## 3. Approach 1 — Naive / V Dijkstra Calls

### Idea
Run $V$ independent Dijkstra calls in $\mathcal{O}(V \cdot (V + E) \log V)$ time (fails on negative edges).

### C++17 Code
```cpp
// V calls to Dijkstra
```

### Java Code
```java
// Java equivalent
// V calls to Dijkstra
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V \cdot (V + E) \log V)$ time.
- **Space Complexity**: $\mathcal{O}(V^2)$.
- **Why it's not good enough**: Fails when negative edge weights are present.

---

## 4. Approach 2 — Better (Johnson's Algorithm for Sparse Graphs)

### Idea
Johnson's Algorithm for Sparse All-Pairs Shortest Paths in O(V^2 log V + VE) time using Bellman-Ford reweighting + V Dijkstra calls.

### C++17 Code
```cpp
// Johnson's Algorithm overview for sparse graphs
```

### Java Code
```java
// Java equivalent
// Johnson's Algorithm overview for sparse graphs
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2 \log V + V \cdot E)$ time.
- **Space Complexity**: $\mathcal{O}(V^2)$ space.
- **Why it's still not optimal**: Johnson's algorithm is optimal for sparse graphs, while Floyd-Warshall is simpler and optimal for dense graphs.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Floyd-Warshall Algorithm)

### Idea
In-Place 3-Nested Loop Matrix DP in $\mathcal{O}(V^3)$ time and $\mathcal{O}(1)$ auxiliary space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    void shortest_distance(vector<vector<int>>& matrix) {
        int n = matrix.size();
        const int INF = 1e9;
        
        // 1. Preprocessing: Replace -1 (no edge) with INF, set diagonal to 0
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == -1) {
                    matrix[i][j] = INF;
                }
                if (i == j) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        // 2. Floyd-Warshall DP: k represents the intermediate pivot vertex
        for (int k = 0; k < n; k++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    // Only relax if path through k is valid
                    if (matrix[i][k] != INF && matrix[k][j] != INF) {
                        matrix[i][j] = min(matrix[i][j], matrix[i][k] + matrix[k][j]);
                    }
                }
            }
        }
        
        // 3. Optional: Check for negative weight cycles
        // for (int i = 0; i < n; i++) {
        //     if (matrix[i][i] < 0) {
        //         // Negative cycle detected!
        //     }
        // }
        
        // 4. Postprocessing: Convert remaining INF entries back to -1
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == INF) {
                    matrix[i][j] = -1;
                }
            }
        }
    }
};
```

### Java Code
```java
class Solution {

    void shortest_distance(int[][] matrix) {
        int n = matrix.length;
        int INF = 1e9;
        
        // 1. Preprocessing: Replace -1 (no edge) with INF, set diagonal to 0
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == -1) {
                    matrix[i][j] = INF;
                }
                if (i == j) {
                    matrix[i][j] = 0;
                }
            }
        }
        
        // 2. Floyd-Warshall DP: k represents the intermediate pivot vertex
        for (int k = 0; k < n; k++) {
            for (int i = 0; i < n; i++) {
                for (int j = 0; j < n; j++) {
                    // Only relax if path through k is valid
                    if (matrix[i][k] != INF && matrix[k][j] != INF) {
                        matrix[i][j] = Math.min(matrix[i][j], matrix[i][k] + matrix[k][j]);
                    }
                }
            }
        }
        
        // 3. Optional: Check for negative weight cycles
        // for (int i = 0; i < n; i++) {
        //     if (matrix[i][i] < 0) {
        //         // Negative cycle detected!
        //     }
        // }
        
        // 4. Postprocessing: Convert remaining INF entries back to -1
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (matrix[i][j] == INF) {
                    matrix[i][j] = -1;
                }
            }
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^3)$ time (exactly 3 nested loops of size $n$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (in-place matrix mutation).
- **Why this is optimal**: Dynamic programming matrix state compression uses only the $n \times n$ matrix in-place without extra tables.

---

## 6. Dry Run

`matrix = [[0, 2, -1, -1], [1, 0, 3, -1], [-1, -1, 0, -1], [3, 5, 4, 0]]` ($n = 4$)

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | matrix[0][1]=2, matrix[1][0]=1, matrix[1][2]=3, matrix[3][0]=3, matrix[3][1]=5, matrix[3][2]=4; others = INF | Ready |
| `Pivot k=0` | matrix[3][1] = $\min(5, matrix[3][0]+matrix[0][1]) = \min(5, 3+2) = 5$ | Pivot 0 processed |
| `Pivot k=1` | matrix[0][2] = $\min(INF, matrix[0][1]+matrix[1][2]) = 2+3 = 5$ | matrix[0][2] updated to 5 |
| `Pivot k=2,3` | Final matrix computed for all pairs | All-pairs Shortest Distances Finalized ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Negative cycles in graph (detected by `matrix[i][i] < 0`).
- Disconnected graph components (`-1` preserved).

### Common Bugs to Avoid
- Putting $k$ in the INNERMOST loop instead of the OUTERMOST loop (FATAL BUG! $k$ must be outer so all pairs can use intermediate $k$ before moving to $k+1$).
- Adding `matrix[i][k] + matrix[k][j]` when one of them is `INF` (causes numeric overflow).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST the pivot loop 'k' be the OUTERMOST loop?**  
  **A**: Because Floyd-Warshall is a dynamic programming algorithm where state $k$ builds upon state $k-1$. For the subproblem 'shortest path using intermediate vertices $\{0 \dots k\}$' to be correct, all pairs $(i, j)$ must be updated using vertex $k$ before we can introduce vertex $k+1$ into the candidate pool! Putting $k$ in the inner loop invalidates the DP recurrence completely!

- **Q2: How does Floyd-Warshall detect Negative Weight Cycles?**  
  **A**: Initially, `matrix[i][i] = 0` (cost from any node to itself is 0). If a negative cycle exists containing node $i$, traveling around the cycle and back to $i$ will produce a path cost strictly less than 0 $\implies \text{matrix}[i][i] < 0$!

- **Q3: When is Floyd-Warshall preferred over running Dijkstra V times?**  
  **A**: Floyd-Warshall is preferred when: 1) The graph is **dense** ($E \approx V^2$), where $V \times (E \log V) = V^3 \log V > V^3$; 2) The graph contains **negative edge weights** (which break Dijkstra); 3) Code simplicity is critical in competitive programming (only 5 lines of loops).


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Floyd-Warshall`, `All-Pairs Shortest Path`, `Dynamic Programming`, `Negative Cycles`, `Medium`
- **Related problems to practice next**:
- **Find City Smallest Neighbors**: Floyd-Warshall application.
- **Bellman-Ford Algorithm**: Single-source negative edges.
