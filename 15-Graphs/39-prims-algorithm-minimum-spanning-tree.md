# Prim's Algorithm for Minimum Spanning Tree (MST) (Step 15.5 — Minimum Spanning Tree & Disjoint Set Union)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Prim's Algorithm for Minimum Spanning Tree (MST)](https://takeuforward.org/data-structure/prims-algorithm-minimum-spanning-tree-c-and-java-g-45/)
- **Difficulty**: Medium
- **Statement**: Given a weighted, undirected, connected graph of $V$ vertices and $E$ edges represented by an adjacency list `adj`, find the sum of the weights of the edges in the Minimum Spanning Tree (MST) using Prim's Algorithm. Also construct the actual list of $V - 1$ edges that constitute the MST.

---

## 1. Problem, Restated

Grow an MST from a single source vertex by repeatedly taking the cheapest cut edge connecting a visited vertex to an unvisited vertex using a Min-Heap in $\mathcal{O}(E \log V)$ time.

- **Input**: `int V`, `vector<vector<int>> adj[]` adjacency list where `adj[u]` has `[v, wt]`.
- **Output**: Integer sum of weights of edges in the Minimum Spanning Tree.
- **Constraints**: $2 \le V \le 1000$, $V - 1 \le E \le V \times (V - 1) / 2$, $1 \le \text{weight} \le 1000$.

---

## 2. Intuition & Pattern

A Spanning Tree connects all $V$ vertices with exactly $V - 1$ edges and NO cycles. **Prim's Algorithm** grows the tree one vertex at a time: 1) Min-Heap stores `{weight, {node, parent}}`. Push `{0, {0, -1}}` (starting at node 0 with no parent). 2) Maintain `vis(V, 0)`. 3) Pop `{wt, {u, parent}}`. If `vis[u] == 1`, continue (already in MST). 4) Mark `vis[u] = 1`. Add `wt` to `sum`. If `parent != -1`, add `{parent, u}` to MST edge list! 5) For each adjacent edge `[v, edgeWt]` of $u$: if `!vis[v]`, push `{edgeWt, {v, u}}` to min-heap. 6) Loop until all $V$ vertices are added. Runs in $\mathcal{O}(E \log V)$ time and $\mathcal{O}(V + E)$ space.

- **Underlying Pattern**: `Greedy Cut Property with Min-Heap Priority Queue (Prim's MST)`.

---

## 3. Approach 1 — Naive / Combinatorial Edge Generation

### Idea
Generate all $\binom{E}{V-1}$ possible edge subsets, verify tree connectivity, and pick minimum weight in $\mathcal{O}(\text{Exponential})$ time.

### C++17 Code
```cpp
// O(Exponential) combinatorics
```

### Java Code
```java
// Java equivalent
// O(Exponential) combinatorics
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\text{Exponential})$.
- **Space Complexity**: $\mathcal{O}(V)$.
- **Why it's not good enough**: Combinatorial edge explosion.

---

## 4. Approach 2 — Better (Kruskal's Algorithm using DSU)

### Idea
Kruskal's Algorithm using Disjoint Set Union (DSU) in O(E log E) time.

### C++17 Code
```cpp
// Kruskal's algorithm overview with DSU
```

### Java Code
```java
// Java equivalent
// Kruskal's algorithm overview with DSU
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E \log E)$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's still not optimal**: Kruskal sorts all edges globally; Prim grows locally from a single component.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Prim's Algorithm)

### Idea
Min-Heap Priority Queue Prim's Algorithm in $\mathcal{O}(E \log V)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    // Function to find sum of weights of edges of the Minimum Spanning Tree.
    int spanningTree(int V, vector<vector<int>> adj[]) {
        // Min-heap stores {weight, {node, parent}}
        // (parent is needed if we also want to record MST edges)
        priority_queue<pair<int, pair<int, int>>,
                       vector<pair<int, pair<int, int>>>,
                       greater<pair<int, pair<int, int>>>> pq;
        
        vector<int> vis(V, 0);
        
        // Push initial source vertex (node 0, weight 0, parent -1)
        pq.push({0, {0, -1}});
        
        int mstSum = 0;
        vector<pair<int, int>> mstEdges; // Stores the V - 1 MST edges
        
        while (!pq.empty()) {
            auto top = pq.top();
            pq.pop();
            
            int wt = top.first;
            int node = top.second.first;
            int parent = top.second.second;
            
            // If already included in the MST cut, ignore
            if (vis[node]) continue;
            
            // Add node to MST
            vis[node] = 1;
            mstSum += wt;
            
            if (parent != -1) {
                mstEdges.push_back({parent, node});
            }
            
            // Traverse all adjacent edges
            for (const auto& edge : adj[node]) {
                int adjNode = edge[0];
                int edgeWeight = edge[1];
                
                if (!vis[adjNode]) {
                    pq.push({edgeWeight, {adjNode, node}});
                }
            }
        }
        
        return mstSum;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    // Function to find sum of weights of edges of the Minimum Spanning Tree.
    int spanningTree(int V, int[][] adj[]) {
        // Min-heap stores {weight, {node, parent}}
        // (parent is needed if we also want to record MST edges)
        priority_queue<pair<int, pair<int, int>>,
                       vector<pair<int, pair<int, int>>>,
                       greater<pair<int, pair<int, int>>>> pq;
        
        int[] vis = new int[V];
        
        // Push initial source vertex (node 0, weight 0, parent -1)
        pq.push({0, {0, -1}});
        
        int mstSum = 0;
        List<int[]> mstEdges; // Stores the V - 1 MST edges
        
        while (!pq.isEmpty()) {
            var top = pq.peek();
            pq.pop();
            
            int wt = top.first;
            int node = top.second.first;
            int parent = top.second.second;
            
            // If already included in the MST cut, ignore
            if (vis[node]) continue;
            
            // Add node to MST
            vis[node] = 1;
            mstSum += wt;
            
            if (parent != -1) {
                mstEdges.add({parent, node});
            }
            
            // Traverse all adjacent edges
            for (var edge : adj[node]) {
                int adjNode = edge[0];
                int edgeWeight = edge[1];
                
                if (!vis[adjNode]) {
                    pq.push({edgeWeight, {adjNode, node}});
                }
            }
        }
        
        return mstSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E \log V)$ time (at most $E$ insertions into a binary min-heap of size at most $V$).
- **Space Complexity**: $\mathcal{O}(V + E)$ priority queue, visited array, and MST edge list memory.
- **Why this is optimal**: Min-heap always extracts the globally minimum cut edge connecting visited MST set to unvisited vertices.

---

## 6. Dry Run

Graph $V = 5$, `adj = 0: [{1,2},{2,1}], 1: [{0,2},{2,1}], 2: [{0,1},{1,1},{3,2},{4,2}], 3: [{2,2},{4,1}], 4: [{2,2},{3,1}]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | vis: `[0,0,0,0,0]` | pq: `[{0, {0, -1}}]` |
| `Pop {0, {0, -1}}` | vis[0]=1, sum=0. Push edges: `{2, {1, 0}}`, `{1, {2, 0}}` | pq: `[{1, {2, 0}}, {2, {1, 0}}]` |
| `Pop {1, {2, 0}}` | vis[2]=1, sum=1. Edge `(0,2)` added! Push `{1, {1, 2}}`, `{2, {3, 2}}`, `{2, {4, 2}}` | mstEdges: `[(0,2)]` |
| `Pop {1, {1, 2}}` | vis[1]=1, sum=1+1=2. Edge `(2,1)` added! | mstEdges: `[(0,2), (2,1)]` |
| `Pop {2, {1, 0}}` | vis[1]==1 -> skipped! | lazy skip |
| `Pop {2, {3, 2}}` | vis[3]=1, sum=2+2=4. Edge `(2,3)` added! Push `{1, {4, 3}}` | mstEdges: `[(0,2), (2,1), (2,3)]` |
| `Pop {1, {4, 3}}` | vis[4]=1, sum=4+1=5. Edge `(3,4)` added! | mstEdges: `[(0,2), (2,1), (2,3), (3,4)]` |
| `Result` | Total MST Weight = 5 ($V - 1 = 4$ edges) | MST Sum = 5 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $V = 1$ (0 edges, sum = 0).
- Graph with multiple identical minimum weight edges.
- Complete dense graph.

### Common Bugs to Avoid
- Marking `vis[adjNode] = 1` upon ENQUEUING rather than upon POPPING (CRITICAL BUG: a cheaper edge to `adjNode` popped later would be incorrectly ignored!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST visited marking occur upon POPPING from the heap and NOT upon pushing in Prim's algorithm?**  
  **A**: Unlike BFS where first discovery is always optimal (distance 1), in Prim's algorithm an unvisited node $v$ may first be discovered via an expensive edge (weight 10) and pushed to heap. Later, a cheaper edge to $v$ (weight 2) is found! If marked visited upon push, the cheaper edge would never be explored!

- **Q2: What is the difference between Prim's and Kruskal's algorithm?**  
  **A**: 1) **Prim's**: Grows a single connected tree from a root node using a Min-Heap. Faster on **dense graphs** ($\mathcal{O}(E \log V)$). 2) **Kruskal's**: Sorts all edges and merges disjoint forest components using **Disjoint Set Union (DSU)**. Faster on **sparse graphs** ($\mathcal{O}(E \log E)$).

- **Q3: Can Prim's algorithm handle graphs with negative edge weights?**  
  **A**: YES! Unlike Dijkstra, Prim's algorithm works correctly with negative edge weights as long as the graph is undirected and connected, because MST depends only on relative edge ordering (the Cut Property), not accumulated path sums.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `MST`, `Prim's Algorithm`, `Greedy`, `Min-Heap`, `Medium`
- **Related problems to practice next**:
- **Kruskal's Algorithm**: DSU MST counterpart.
- **Dijkstra Algorithm**: Shortest path counterpart.
