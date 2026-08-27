# Kth Ancestor of a Node in Binary Tree (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Kth Ancestor of a Node in Binary Tree](https://takeuforward.org/data-structure/kth-ancestor-of-a-node-in-binary-tree/)
- **Difficulty**: Hard
- **Statement**: You are given a tree with $n$ nodes numbered $0$ to $n - 1$ in the form of a parent array `parent` where `parent[i]` is the parent of $i^{\text{th}}$ node. The root is node 0 with `parent[0] = -1`. Implement `TreeAncestor(int n, int[] parent)` and `getKthAncestor(int node, int k)` in $\mathcal{O}(\log K)$ time per query.

---

## 1. Problem, Restated

Implement Binary Lifting: precompute $2^j$-th ancestor `up[node][j]` table to jump powers of 2 in $\mathcal{O}(\log K)$ time.

- **Input**: Parameters specified.
- **Output**: Traversal vector / string / node value.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Any integer $k$ can be uniquely decomposed into powers of 2 (binary bits: e.g. $13 = 8 + 4 + 1$). 1) **Precompute Sparse Table**: `up[node][j]` = the $2^j$-th ancestor of `node`. Base case: `up[node][0] = parent[node]`. Transition: `up[node][j] = up[up[node][j-1]][j-1]` (the $2^j$-th ancestor is the $2^{j-1}$-th ancestor of the $2^{j-1}$-th ancestor!). 2) **Query `getKthAncestor(node, k)`**: For each bit $j$ where $(k \& (1 \ll j)) \ne 0$: jump `node = up[node][j]`. If `node == -1`, return -1. Total query time is $\mathcal{O}(\log K)$!

- **Underlying Pattern**: `Binary Lifting Sparse Table (`up[node][j] = up[up[node][j-1]][j-1]`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Step up parent pointers one by one in $\mathcal{O}(K)$ per query (times out on $Q = 5 \times 10^4$ queries).

### C++17 Code
```cpp
// O(K) linear stepping
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K)$ per query $\implies \mathcal{O}(Q \cdot K)$ total.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Linear ancestor traversal.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / Binary Lifting algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(\log K)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Binary Lifting Precomputation in $\mathcal{O}(N \log N)$ preprocessing and $\mathcal{O}(\log K)$ query time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class TreeAncestor {
private:
    // up[node][j] stores the 2^j-th ancestor of node
    vector<vector<int>> up;
    int maxLog;

public:
    TreeAncestor(int n, vector<int>& parent) {
        maxLog = 20; // 2^19 > 500,000 nodes
        up.assign(n, vector<int>(maxLog, -1));
        
        // Base case: 2^0 = 1st ancestor is direct parent
        for (int i = 0; i < n; i++) {
            up[i][0] = parent[i];
        }
        
        // Binary lifting DP transition
        for (int j = 1; j < maxLog; j++) {
            for (int i = 0; i < n; i++) {
                if (up[i][j - 1] != -1) {
                    up[i][j] = up[up[i][j - 1]][j - 1];
                } else {
                    up[i][j] = -1;
                }
            }
        }
    }
    
    int getKthAncestor(int node, int k) {
        for (int j = 0; j < maxLog; j++) {
            // If the j-th bit of k is set, jump 2^j steps
            if (k & (1 << j)) {
                node = up[node][j];
                if (node == -1) return -1; // reached beyond root
            }
        }
        return node;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ initialization, strictly $\mathcal{O}(\log K)$ per query.
- **Space Complexity**: $\mathcal{O}(N \log N)$ table memory.
- **Why this is optimal**: Binary exponentiation decomposition enables $\mathcal{O}(\log K)$ jump steps across tree heights.

---

## 6. Dry Run

Tree: `0 -> 1 -> 2 -> 3 -> 4 -> 5 -> 6`, Query `getKthAncestor(6, 5)` ($5 = 4 + 1 = 2^2 + 2^0$)

| Step | Action / State Change | Result |
|---|---|---|
| `k = 5 (binary 101)` | Bits 0 and 2 set | start node = 6 |
| `Bit 0 (jump 2^0 = 1)` | node = up[6][0] = 5 | node = 5 |
| `Bit 2 (jump 2^2 = 4)` | node = up[5][2] = 1 | node = 1 |
| `Result` | 5th Ancestor of 6 is 1 (`6 -> 5 -> 4 -> 3 -> 2 -> 1`) | Result = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k > \text{depth}$ (returns -1).
- $k = 0$ (returns `node`).
- Query on root node.

### Common Bugs to Avoid
- Outer loop over `i` and inner loop over `j` in DP transition (must loop `j` in outer loop so $2^{j-1}$ ancestors are fully computed first!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must the outer loop be j (powers of 2) and inner loop be i (nodes) during preprocessing?**  
  **A**: Because computing $2^j$-th ancestors relies on having ALREADY computed all $2^{j-1}$-th ancestors for ALL nodes in the tree!

- **Q2: How is Binary Lifting used to find Lowest Common Ancestor (LCA) in O(log N)?**  
  **A**: 1) Lift the deeper node until both nodes are at the same depth. 2) If $u == v$, return $u$. 3) Jump both nodes simultaneously by powers of 2 from $j = 19$ down to 0 while `up[u][j] != up[v][j]`. LCA is `up[u][0]`!

- **Q3: What is the maximum value of maxLog for N = 10^5?**  
  **A**: Since $2^{16} = 65,536$ and $2^{17} = 131,072$, `maxLog = 18` is sufficient for $N = 10^5$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Binary Lifting`, `Dynamic Programming`, `Sparse Table`, `LeetCode-1483`, `Hard`
- **Related problems to practice next**:
- **Lowest Common Ancestor**: LCA via Binary Lifting.
