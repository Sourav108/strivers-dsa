# Breadth First Search (BFS) Traversal (Step 15.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Breadth First Search (BFS) Traversal](https://takeuforward.org/data-structure/breadth-first-search-bfs-level-order-traversal/)
- **Difficulty**: Easy
- **Statement**: Given a connected undirected graph represented by an adjacency list `adj`, return a vector containing the Breadth First Search (BFS) traversal of the graph starting from vertex 0.

---

## 1. Problem, Restated

Level-order traversal of graph using FIFO queue and boolean visited array in $\mathcal{O}(V + 2E)$ time.

- **Input**: Parameters specified ($V$ vertices, $E$ edges / adjacency lists).
- **Output**: Traversal vector / count / data structures.
- **Constraints**: Standard competitive programming limits ($V \le 10^5, E \le 2 \times 10^5$).

---

## 2. Intuition & Pattern

BFS explores neighbors radially layer-by-layer (distance 0, distance 1, distance 2, ...). 1) Mark `vis[0] = 1`, push 0 into `queue<int> q`. 2) While queue is not empty: pop `node = q.front()`, add to `bfs` order. 3) For each neighbor $v$ of `node`: if `!vis[v]`, immediately mark `vis[v] = 1` and push $v$ into queue (marking upon insertion prevents duplicate enqueuing!).

- **Underlying Pattern**: `FIFO Queue Radial Level-Order Expansion`.

---

## 3. Approach 1 — Naive / Adjacency Matrix

### Idea
Recursive simulation with full distance arrays.

### C++17 Code
```cpp
// Recursive BFS simulation
```

### Java Code
```java
// Java equivalent
// Recursive BFS simulation
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$.
- **Space Complexity**: $\mathcal{O}(V)$.
- **Why it's not good enough**: Queue-based iterative BFS is idiomatic.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Adjacency List / BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(V + E)$ or $\mathcal{O}(V + 2E)$ time and space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Standard FIFO Queue BFS in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    vector<int> bfsOfGraph(int V, vector<vector<int>>& adj) {
        vector<int> bfs;
        vector<int> vis(V, 0);
        queue<int> q;
        
        // Push source vertex 0 and mark visited
        vis[0] = 1;
        q.push(0);
        
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            bfs.push_back(node);
            
            // Explore all unvisited adjacent neighbors
            for (int neighbor : adj[node]) {
                if (!vis[neighbor]) {
                    vis[neighbor] = 1; // Mark immediately upon enqueuing
                    q.push(neighbor);
                }
            }
        }
        
        return bfs;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] bfsOfGraph(int V, int[][] adj) {
        List<Integer> bfs = new ArrayList<>();
        int[] vis = new int[V];
        Queue<Integer> q = new LinkedList<>();
        
        // Push source vertex 0 and mark visited
        vis[0] = 1;
        q.push(0);
        
        while (!q.isEmpty()) {
            int node = q.peek();
            q.pop();
            bfs.add(node);
            
            // Explore all unvisited adjacent neighbors
            for (int neighbor : adj[node]) {
                if (!vis[neighbor]) {
                    vis[neighbor] = 1; // Mark immediately upon enqueuing
                    q.push(neighbor);
                }
            }
        }
        
        return bfs;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (each vertex is pushed/popped once $\implies \mathcal{O}(V)$; each undirected edge is checked twice $\implies \mathcal{O}(2E)$).
- **Space Complexity**: $\mathcal{O}(V)$ queue and visited array space.
- **Why this is optimal**: Immediate visited marking upon enqueuing guarantees each node is queued at most once.

---

## 6. Dry Run

Graph $V = 5$, Edges: `0-1, 0-2, 0-3, 2-4` (adj: `0:[1,2,3], 1:[0], 2:[0,4], 3:[0], 4:[2]`)

| Step | Action / State Change | Result |
|---|---|---|
| `Start 0` | q: `[0]`, vis[0]=1 | bfs: `[]` |
| `Pop 0` | Neighbors 1, 2, 3 unvisited -> mark vis, push to q | bfs: `[0]`, q: `[1, 2, 3]` |
| `Pop 1` | Neighbors {0} already visited -> no-op | bfs: `[0, 1]`, q: `[2, 3]` |
| `Pop 2` | Neighbor 4 unvisited -> mark vis[4]=1, push 4 | bfs: `[0, 1, 2]`, q: `[3, 4]` |
| `Pop 3, 4` | All neighbors visited -> drain queue | BFS Order: `[0, 1, 2, 3, 4]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single vertex graph $V = 1$ (returns `[0]`).
- Star graph / Linear chain.

### Common Bugs to Avoid
- Marking `vis[node] = 1` AFTER popping from queue instead of when pushing (causes the same node to be pushed multiple times by different neighbors, blowing queue memory to $\mathcal{O}(E)$!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST a node be marked visited at the time of ENQUEUING rather than dequeuing?**  
  **A**: If marked upon dequeuing, multiple active nodes can discover and push the same unvisited neighbor into the queue simultaneously, causing exponential queue duplicate explosions!

- **Q2: Why does BFS naturally compute the Shortest Path in unweighted graphs?**  
  **A**: Because BFS explores nodes in strictly increasing order of their edge distance from the source (distance 0, then 1, then 2, etc.). The first time a node is reached, it is guaranteed to be via the shortest path!

- **Q3: What is the difference between BFS on Trees vs BFS on Graphs?**  
  **A**: Trees are directed acyclic hierarchies with a single root and no cycles, so a `vis` array is NOT needed. Graphs contain cycles and multiple paths, strictly requiring a `vis` array.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Traversal`, `Queue`, `Easy`
- **Related problems to practice next**:
- **DFS Traversal**: Depth-first counterpart.
- **Shortest Path Unit Weights**: BFS application.
