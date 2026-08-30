# Detect Cycle in a Directed Graph using BFS (Kahn's Algorithm) (Step 15.3 — Topological Sort and Kahn's Algorithm)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Detect Cycle in a Directed Graph using BFS (Kahn's Algorithm)](https://takeuforward.org/data-structure/detect-a-cycle-in-directed-graph-using-bfs/)
- **Difficulty**: Medium
- **Statement**: Given a Directed Graph with $V$ vertices and $E$ edges represented by an adjacency list `adj`, determine whether the graph contains a cycle using Kahn's Algorithm (BFS).

---

## 1. Problem, Restated

Execute Kahn's algorithm: if total nodes processed into topological order is strictly less than $V$ (`count < V`), the graph contains a directed cycle.

- **Input**: $V$ vertices, $E$ directed edges represented as `vector<vector<int>>& adj`.
- **Output**: Boolean `true` if a cycle exists, `false` otherwise.
- **Constraints**: $1 \le V, E \le 10^5$.

---

## 2. Intuition & Pattern

Topological sort is ONLY possible on Directed Acyclic Graphs (DAGs). In any directed cycle, every vertex in the cycle has an in-degree $\ge 1$ caused by its predecessor. Therefore, **none of the vertices in a cycle can EVER reach in-degree 0**! 1) Compute in-degrees for all $V$ nodes. 2) Enqueue nodes with `inDegree[i] == 0`. 3) Pop from queue, increment `count++`, and decrement neighbor in-degrees. If neighbor reaches 0, enqueue it. 4) If `count == V`, all nodes were ordered without cycles $\implies$ return `false` (No Cycle). If `count < V`, vertices trapped in the cycle were never processed $\implies$ return `true` (Cycle Exists)! Runs in $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `Kahn's Algorithm Topological Sort Node Count Invariant (`count < V` $\implies$ Cycle)`.

---

## 3. Approach 1 — Naive / DFS Recursion Stack

### Idea
DFS cycle detection with recursion stack arrays in $\mathcal{O}(V + E)$ time.

### C++17 Code
```cpp
// DFS 3-color cycle detection
```

### Java Code
```java
// Java equivalent
// DFS 3-color cycle detection
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ stack.
- **Why it's not good enough**: BFS and DFS have identical linear complexity.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Kahn's BFS In-Degree Count algorithm below directly achieves optimal $\mathcal{O}(V + E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Kahn's BFS In-Degree Node Counter in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    bool isCyclic(int V, vector<vector<int>>& adj) {
        vector<int> inDegree(V, 0);
        
        // 1. Calculate in-degree for all vertices
        for (int u = 0; u < V; u++) {
            for (int v : adj[u]) {
                inDegree[v]++;
            }
        }
        
        // 2. Enqueue all vertices with in-degree 0
        queue<int> q;
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        int count = 0;
        
        // 3. Process BFS
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            count++; // Track count of processed vertices
            
            for (int neighbor : adj[node]) {
                inDegree[neighbor]--;
                if (inDegree[neighbor] == 0) {
                    q.push(neighbor);
                }
            }
        }
        
        // If topological sort includes all V vertices -> DAG (No cycle)
        // If count < V -> At least one directed cycle prevented in-degree from reaching 0
        return (count != V);
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    boolean isCyclic(int V, int[][] adj) {
        int[] inDegree = new int[V];
        
        // 1. Calculate in-degree for all vertices
        for (int u = 0; u < V; u++) {
            for (int v : adj[u]) {
                inDegree[v]++;
            }
        }
        
        // 2. Enqueue all vertices with in-degree 0
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        int count = 0;
        
        // 3. Process BFS
        while (!q.isEmpty()) {
            int node = q.peek();
            q.pop();
            count++; // Track count of processed vertices
            
            for (int neighbor : adj[node]) {
                inDegree[neighbor]--;
                if (inDegree[neighbor] == 0) {
                    q.push(neighbor);
                }
            }
        }
        
        // If topological sort includes all V vertices . DAG (No cycle)
        // If count < V . At least one directed cycle prevented in-degree from reaching 0
        return (count != V);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time (linear graph sweep and BFS).
- **Space Complexity**: $\mathcal{O}(V)$ in-degree array and queue space.
- **Why this is optimal**: Count comparison eliminates recursion stack overflow risks entirely.

---

## 6. Dry Run

Graph 1: `0->1->2->0` (Cycle $0-1-2-0$) vs Graph 2: `0->1->2` (Acyclic)

| Step | Action / State Change | Result |
|---|---|---|
| `Graph 1 (Cycle)` | inDeg: `0:1, 1:1, 2:1` -> No vertex has in-degree 0! Queue starts EMPTY -> count = 0 != 3 | Return TRUE ✅ (Cycle Detected) |
| `Graph 2 (Acyclic)` | inDeg: `0:0, 1:1, 2:1` -> pushes 0 -> pops 0 (inDeg 1:0, pushes 1) -> pops 1 (inDeg 2:0, pushes 2) -> pops 2 -> count = 3 == 3 | Return FALSE ✅ (No Cycle) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Self loops $0 \to 0$ (`inDegree[0] = 1` prevents enqueuing).
- Disconnected graph with cycle in one subcomponent.
- Single node $V = 1$.

### Common Bugs to Avoid
- Returning `count > 0` instead of `count != V` (if a graph has 10 nodes and 3 are outside a cycle, count will be 3, falsely indicating no cycle if checked carelessly!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does a cycle prevent vertices from reaching in-degree 0?**  
  **A**: Because every vertex in a cycle has at least one incoming edge coming from another vertex in the same cycle. For any vertex to reach in-degree 0, its predecessor must be removed first, which is impossible due to circular dependency!

- **Q2: What is the advantage of BFS Kahn's algorithm over DFS for cycle detection in production?**  
  **A**: DFS can cause stack overflow on very deep chains (e.g. $10^5$ vertices). Kahn's BFS algorithm is purely iterative with memory allocated on the heap, making it safe from stack overflows.

- **Q3: How does Course Schedule I (LeetCode 207) map directly to this problem?**  
  **A**: Courses are vertices, prerequisite pairs $[u, v]$ represent directed edges $v \to u$. If `isCyclic` returns `true`, the courses CANNOT be finished!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Cycle Detection`, `Topological Sort`, `Kahn's Algorithm`, `Medium`
- **Related problems to practice next**:
- **Course Schedule I**: Prerequisites verification.
- **Topological Sort BFS**: Base Kahn's algorithm.
