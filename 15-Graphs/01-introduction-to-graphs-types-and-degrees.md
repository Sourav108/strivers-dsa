# Introduction to Graphs, Degrees, and Types (Step 15.1 — Learning)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Introduction to Graphs, Degrees, and Types](https://takeuforward.org/graph/introduction-to-graph/)
- **Difficulty**: Easy
- **Statement**: Explain fundamental graph theory definitions: Directed vs Undirected graphs, Weighted vs Unweighted graphs, Cycles, Directed Acyclic Graphs (DAG), In-degree, Out-degree, and the Handshaking Lemma ($\sum \text{deg}(v) = 2E$).

---

## 1. Problem, Restated

Formal theoretical foundation on graph taxonomy, degree summation properties, and structural representations.

- **Input**: Parameters specified ($V$ vertices, $E$ edges / adjacency lists).
- **Output**: Traversal vector / count / data structures.
- **Constraints**: Standard competitive programming limits ($V \le 10^5, E \le 2 \times 10^5$).

---

## 2. Intuition & Pattern

A graph $G = (V, E)$ consists of vertices $V$ and edges $E$. **Handshaking Lemma**: In an undirected graph, every edge contributes $+1$ degree to two endpoints, so the sum of all node degrees is exactly $2E$. In a directed graph, $\sum \text{in-deg}(v) = \sum \text{out-deg}(v) = E$.

- **Underlying Pattern**: `Graph Taxonomy & Handshaking Degree Summation ($\sum \text{deg}(v) = 2E$)`.

---

## 3. Approach 1 — Naive / Adjacency Matrix

### Idea
Representing dense relations without formal degree tracking.

### C++17 Code
```cpp
// Basic terminology overview
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Theoretical concepts.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Adjacency List / BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(V + E)$ or $\mathcal{O}(V + 2E)$ time and space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Degree Verification and Handshaking Lemma Validation in $\mathcal{O}(V + E)$ time.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

class GraphBasics {
public:
    // Handshaking Lemma: Total degree sum of undirected graph == 2 * E
    bool verifyHandshaking(int V, const vector<vector<int>>& adj, int E) {
        int totalDegree = 0;
        for (int u = 0; u < V; u++) {
            totalDegree += adj[u].size();
        }
        return (totalDegree == 2 * E);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time to inspect all adjacency lists.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Direct mathematical derivation from the fundamental Handshaking Lemma.

---

## 6. Dry Run

Graph with $V = 3, E = 3$ (Triangle: edges (0,1), (1,2), (2,0))

| Step | Action / State Change | Result |
|---|---|---|
| `Degrees` | deg(0)=2, deg(1)=2, deg(2)=2 | Total Sum = 6 |
| `Handshaking Check` | $2 \times E = 2 \times 3 = 6 == 6$ | Verified ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Disconnected graph with isolated vertices (degree 0).
- Self-loops (contribute +2 to degree in undirected graphs).

### Common Bugs to Avoid
- Assuming the number of odd-degree vertices can be odd (by Handshaking Lemma, the number of vertices with odd degree is ALWAYS EVEN!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the number of vertices with odd degree always even?**  
  **A**: Because $\sum \text{deg}(v) = 2E$ (an even number). The sum of all even-degree vertices is even. For the total sum to remain even, the sum of all odd-degree vertices must also be even, which is only possible if there is an EVEN count of odd-degree vertices!

- **Q2: What is the maximum number of edges in a simple undirected graph with V vertices?**  
  **A**: $\binom{V}{2} = \frac{V(V-1)}{2} = \mathcal{O}(V^2)$ edges (a Complete Graph $K_V$).

- **Q3: What is the difference between a Tree and a DAG?**  
  **A**: A Tree is a connected undirected acyclic graph with exactly $V - 1$ edges. A DAG (Directed Acyclic Graph) is a directed graph with no directed cycles; a DAG does not need to be connected and can have multiple incoming edges per node.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Theory`, `Basics`, `Easy`
- **Related problems to practice next**:
- **Graph Representation**: Matrix vs List.
- **Connected Components**: Sub-graphs.
