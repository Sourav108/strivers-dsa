# Topological Sort using DFS (Finish time stack) (Step 15.3 — Topological Sort and Kahn's Algorithm)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Topological Sort using DFS (Finish time stack)](https://takeuforward.org/data-structure/topological-sort-algorithm-dfs/)
- **Difficulty**: Medium
- **Statement**: Given a Directed Acyclic Graph (DAG) with $V$ vertices and $E$ edges represented by an adjacency list `adj`, return a vector containing a valid Topological Sorting of the graph. A topological sort is a linear ordering of vertices such that for every directed edge $u \to v$, node $u$ comes before node $v$ in the ordering.

---

## 1. Problem, Restated

Order vertices of a DAG linearly using DFS: push each node onto a LIFO stack upon FINISHING all its outgoing edges, then pop the stack into the result vector in $\mathcal{O}(V + E)$ time.

- **Input**: $V$ vertices, $E$ directed edges as `vector<vector<int>>& adj`.
- **Output**: `vector<int>` valid topological sort ordering.
- **Constraints**: $1 \le V, E \le 10^5$, graph is a DAG.

---

## 2. Intuition & Pattern

For every directed edge $u \to v$, all descendants of $v$ must be fully explored and finished BEFORE $u$ finishes! When we push a node onto a stack at the END of its DFS call (postorder finish time), $v$ will be pushed onto the stack before $u$. When we pop from the stack at the end, $u$ will naturally be popped BEFORE $v$, satisfying the $u \to v$ topological dependency! Runs in $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `DFS Reverse Postorder Finish-Time Stack Insertion`.

---

## 3. Approach 1 — Naive / Repeated In-Degree Search

### Idea
Repeatedly find a vertex with in-degree 0, remove it, and repeat $V$ times with adjacency list scans in $\mathcal{O}(V^2)$ time.

### C++17 Code
```cpp
// O(V^2) repeated in-degree scan
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2)$ time.
- **Space Complexity**: $\mathcal{O}(V)$.
- **Why it's not good enough**: Quadratic scanning of vertices.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS Finish-Time Stack algorithm below directly achieves optimal $\mathcal{O}(V + E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
DFS Finish-Time Stack in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;

class Solution {
private:
    void dfs(int node, const vector<vector<int>>& adj, vector<int>& vis, stack<int>& st) {
        vis[node] = 1;
        
        // Explore all outgoing directed neighbors
        for (int neighbor : adj[node]) {
            if (!vis[neighbor]) {
                dfs(neighbor, adj, vis, st);
            }
        }
        
        // Push node onto stack ONLY after all its dependencies/descendants finish
        st.push(node);
    }

public:
    vector<int> topoSort(int V, vector<vector<int>>& adj) {
        vector<int> vis(V, 0);
        stack<int> st;
        
        // Multi-component loop for disconnected DAGs
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                dfs(i, adj, vis, st);
            }
        }
        
        // Extract topological order from stack
        vector<int> topo;
        while (!st.empty()) {
            topo.push_back(st.top());
            st.pop();
        }
        
        return topo;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time (each vertex and directed edge traversed once).
- **Space Complexity**: $\mathcal{O}(V)$ recursion call stack, visited array, and LIFO finish stack.
- **Why this is optimal**: Reverse postorder stack insertion guarantees parent vertices precede child vertices upon stack popping.

---

## 6. Dry Run

DAG $V = 6$, Edges: `5->0, 5->2, 4->0, 4->1, 2->3, 3->1`

| Step | Action / State Change | Result |
|---|---|---|
| `dfs(5)` | Dives $5 \to 2 \to 3 \to 1$. Node 1 finishes -> pushes 1. Node 3 finishes -> pushes 3. Node 2 finishes -> pushes 2. Edge $5 \to 0$: node 0 finishes -> pushes 0. Node 5 finishes -> pushes 5 | st: `[1, 3, 2, 0, 5]` |
| `dfs(4)` | Edge $4 \to 0$ (0 visited), $4 \to 1$ (1 visited). Node 4 finishes -> pushes 4 | st: `[1, 3, 2, 0, 5, 4]` |
| `Drain Stack` | Pop: 4, then 5, then 0, then 2, then 3, then 1 | Topo: `[4, 5, 0, 2, 3, 1]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Graph contains isolated disconnected components.
- Graph has no edges (any permutation $0 \dots V-1$ is valid).

### Common Bugs to Avoid
- Pushing `st.push(node)` at the START of DFS instead of at the end (produces preorder rather than reverse postorder!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Topological Sort ONLY exist for Directed Acyclic Graphs (DAGs)?**  
  **A**: 1) If the graph is **undirected**, an edge $u-v$ means $u$ must come before $v$ AND $v$ must come before $u$, which is contradictory. 2) If the graph has a **directed cycle** ($u \to v \to w \to u$), then $u$ must precede $v$, $v$ must precede $w$, and $w$ must precede $u$, making linear ordering mathematically impossible!

- **Q2: What is the difference between DFS Topo Sort and Kahn's BFS Algorithm?**  
  **A**: DFS Topo Sort uses finish-time stacks (top-down postorder), while Kahn's Algorithm uses BFS with in-degree counters (bottom-up from in-degree 0 sources). Kahn's algorithm also inherently detects cycles when `topo.size() < V`.

- **Q3: How is Topological Sort used in real-world systems?**  
  **A**: Build dependency resolution (Make/Bazel/Webpack compiling dependent libraries first), task scheduling in Airflow/Kubernetes DAG pipelines, and spreadsheet formula evaluation order.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `Topological Sort`, `DAG`, `Medium`
- **Related problems to practice next**:
- **Kahn's Algorithm**: BFS topological sort.
- **Course Schedule I & II**: Prerequisites ordering.
