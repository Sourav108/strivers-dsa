# M-Coloring Problem (Graph vertex coloring) (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [M-Coloring Problem (Graph vertex coloring)](https://takeuforward.org/data-structure/m-coloring-problem/)
- **Difficulty**: Hard
- **Statement**: Given an undirected graph and an integer $M$, determine if the graph can be colored with at most $M$ colors such that no two adjacent vertices of the graph are assigned the same color.

---

## 1. Problem, Restated

Verify if the chromatic number $\chi(G) \le M$ using vertex-by-vertex backtracking.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

For vertex `node`: try colors $c \in [1, M]$. Check safety: `isSafe(node, color)` ensures no neighbor $k$ has `color[k] == c`. If safe: assign `color[node] = c`, recurse `solve(node + 1)`. If true, return true. Otherwise backtrack `color[node] = 0`.

- **Underlying Pattern**: `Graph Vertex Color Assignment Backtracking`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Generate all $M^V$ coloring permutations and check edge conflicts in $\mathcal{O}(M^V \cdot V^2)$.

### C++17 Code
```cpp
// M^V permutation approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M^V \cdot V^2)$.
- **Space Complexity**: $\mathcal{O}(V)$.
- **Why it's not good enough**: Explores invalid assignments.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Pruned Vertex Backtracking in $\mathcal{O}(M^V)$ worst-case time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    bool isSafe(int node, const vector<vector<int>>& graph, const vector<int>& color, int n, int col) {
        for (int k = 0; k < n; k++) {
            // Adjacent node with same color
            if (k != node && graph[node][k] == 1 && color[k] == col) {
                return false;
            }
        }
        return true;
    }
    
    bool solve(int node, const vector<vector<int>>& graph, vector<int>& color, int m, int n) {
        if (node == n) return true; // all vertices colored
        
        for (int c = 1; c <= m; c++) {
            if (isSafe(node, graph, color, n, c)) {
                color[node] = c;
                if (solve(node + 1, graph, color, m, n)) return true;
                color[node] = 0; // backtrack
            }
        }
        
        return false;
    }

public:
    bool graphColoring(vector<vector<int>>& graph, int m, int n) {
        vector<int> color(n, 0);
        return solve(0, graph, color, m, n);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(M^V)$ time in worst-case.
- **Space Complexity**: $\mathcal{O}(V)$ auxiliary space for color array and recursion stack.
- **Why this is optimal**: Prunes search on the first adjacent color conflict.

---

## 6. Dry Run

Graph with 4 vertices, $M = 3$ colors

| Step | Action / State Change | Result |
|---|---|---|
| `Vertex 0` | Color 1 assigned | color[0] = 1 |
| `Vertex 1` | Connected to 0 -> Color 2 assigned | color[1] = 2 |
| `Vertex 2` | Connected to 0, 1 -> Color 3 assigned | color[2] = 3 |
| `Vertex 3` | Connected to 0, 2 -> Color 2 assigned | Valid 3-coloring found ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $M = 1$ on disconnected graph (valid) vs connected graph (invalid).
- Complete graph $K_N$ (requires $M \ge N$).

### Common Bugs to Avoid
- Passing `k == node` in neighbor check.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the Four Color Theorem?**  
  **A**: The Four Color Theorem states that any planar graph can be colored with at most $M = 4$ colors such that no two adjacent vertices share the same color.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Graphs`, `Hard`
- **Related problems to practice next**:
- **N-Queens**: Constraint placement.
