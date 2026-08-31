# Kahn's Algorithm (Topological Sort using BFS In-Degree) (Step 15.3 — Topological Sort and Kahn's Algorithm)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Kahn's Algorithm (Topological Sort using BFS In-Degree)](https://takeuforward.org/data-structure/topological-sort-bfs/)
- **Difficulty**: Medium
- **Statement**: Given a Directed Acyclic Graph (DAG) with $V$ vertices and $E$ edges represented by an adjacency list `adj`, return a topological sort of the graph using Kahn's Algorithm (BFS with in-degrees).

---

## 1. Problem, Restated

Compute in-degrees for all vertices; push in-degree 0 vertices into a BFS queue; pop, append to topo order, decrement neighbors' in-degrees, and enqueue neighbors whose in-degree reaches 0 in $\mathcal{O}(V + E)$ time.

- **Input**: $V$ vertices, $E$ directed edges represented as `vector<vector<int>>& adj`.
- **Output**: `vector<int>` valid topological sort ordering.
- **Constraints**: $1 \le V, E \le 10^5$, graph is a DAG.

---

## 2. Intuition & Pattern

A vertex with in-degree 0 has NO remaining dependencies, so it can be safely executed immediately! 1) Compute `inDegree[V]` by scanning all edges ($u \to v \implies \text{inDegree}[v]++$). 2) Push all nodes with `inDegree[i] == 0` into `queue<int> q`. 3) While queue is not empty: pop `node = q.front()`, add to `topo` result. 4) For each neighbor $v$ of `node`: decrement `inDegree[v]--`. If `inDegree[v] == 0`, all prerequisites for $v$ have been satisfied $\implies$ push $v$ into queue! 5) Return `topo`. Runs in strictly linear $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `In-Degree Elimination BFS / Kahn's Algorithm`.

---

## 3. Approach 1 — Naive / Repeated In-Degree Scan

### Idea
Repeated linear scan of inDegree array finding 0s without a queue in $\mathcal{O}(V^2)$ time.

### C++17 Code
```cpp
// O(V^2) linear in-degree scan
```

### Java Code
```java
// Java equivalent
// O(V^2) linear in-degree scan
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ array.
- **Why it's not good enough**: Scans all vertices on each extraction.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Kahn's BFS Algorithm below directly achieves optimal $\mathcal{O}(V + E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Kahn's Algorithm with FIFO Queue in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    vector<int> topoSort(int V, vector<vector<int>>& adj) {
        vector<int> inDegree(V, 0);
        
        // 1. Calculate in-degree for each vertex
        for (int u = 0; u < V; u++) {
            for (int v : adj[u]) {
                inDegree[v]++;
            }
        }
        
        // 2. Enqueue all vertices with in-degree 0 (no prerequisites)
        queue<int> q;
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        vector<int> topo;
        
        // 3. Process BFS queue
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            topo.push_back(node);
            
            // Remove node from graph: decrement in-degree of its neighbors
            for (int neighbor : adj[node]) {
                inDegree[neighbor]--;
                
                // If in-degree becomes 0, all dependencies are resolved
                if (inDegree[neighbor] == 0) {
                    q.push(neighbor);
                }
            }
        }
        
        return topo;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] topoSort(int V, int[][] adj) {
        int[] inDegree = new int[V];
        
        // 1. Calculate in-degree for each vertex
        for (int u = 0; u < V; u++) {
            for (int v : adj[u]) {
                inDegree[v]++;
            }
        }
        
        // 2. Enqueue all vertices with in-degree 0 (no prerequisites)
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        int[] topo;
        
        // 3. Process BFS queue
        while (!q.isEmpty()) {
            int node = q.peek();
            q.pop();
            topo.add(node);
            
            // Remove node from graph: decrement in-degree of its neighbors
            for (int neighbor : adj[node]) {
                inDegree[neighbor]--;
                
                // If in-degree becomes 0, all dependencies are resolved
                if (inDegree[neighbor] == 0) {
                    q.push(neighbor);
                }
            }
        }
        
        return topo;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time (in-degree calculation takes $\mathcal{O}(V + E)$, each vertex and edge processed once in BFS).
- **Space Complexity**: $\mathcal{O}(V)$ in-degree vector and queue space.
- **Why this is optimal**: Queue tracks zero-dependency nodes dynamically in $\mathcal{O}(1)$ time without scanning.

---

## 6. Dry Run

DAG $V = 6$, Edges: `5->0, 5->2, 4->0, 4->1, 2->3, 3->1`

| Step | Action / State Change | Result |
|---|---|---|
| `In-degrees` | inDeg: `0:2, 1:2, 2:1, 3:1, 4:0, 5:0` | q: `[4, 5]` |
| `Pop 4` | topo: `[4]`, inDeg: `0:1, 1:1` -> no new 0s | q: `[5]` |
| `Pop 5` | topo: `[4, 5]`, inDeg: `0:0 (push 0!), 2:0 (push 2!)` | q: `[0, 2]` |
| `Pop 0` | topo: `[4, 5, 0]` (no outgoing) | q: `[2]` |
| `Pop 2` | topo: `[4, 5, 0, 2]`, inDeg: `3:0 (push 3!)` | q: `[3]` |
| `Pop 3` | topo: `[4, 5, 0, 2, 3]`, inDeg: `1:0 (push 1!)` | q: `[1]` |
| `Pop 1` | topo: `[4, 5, 0, 2, 3, 1]` | Kahn's Topo Order Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Disconnected DAG.
- Multiple valid topological orderings (any valid topological order is acceptable).

### Common Bugs to Avoid
- Calculating out-degree instead of in-degree (causes reversed or invalid execution order).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does Kahn's Algorithm inherently detect cycles?**  
  **A**: If the graph contains a directed cycle, vertices in the cycle will NEVER reach in-degree 0, so they will never enter the queue. If `topo.size() < V`, the graph contains a cycle!

- **Q2: Can a Priority Queue (min-heap) be used instead of a standard FIFO queue in Kahn's algorithm?**  
  **A**: YES! Using `priority_queue<int, vector<int>, greater<int>>` produces the **Lexicographically Smallest Topological Sort** in $\mathcal{O}(V + E \log V)$ time!

- **Q3: Why does inDegree[v] == 0 signify that v can be processed?**  
  **A**: Because in a dependency graph, `inDegree[v]` represents the number of unfinished prerequisites for $v$. When `inDegree[v] == 0`, ALL dependencies have already been executed.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Topological Sort`, `Kahn's Algorithm`, `DAG`, `Medium`
- **Related problems to practice next**:
- **Detect Cycle in Directed Graph BFS**: Kahn's cycle check.
- **Course Schedule II**: Prerequisites ordering.
