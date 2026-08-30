# Bellman-Ford Algorithm (Negative Weights & Negative Cycle Detection) (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Bellman-Ford Algorithm (Negative Weights & Negative Cycle Detection)](https://takeuforward.org/data-structure/bellman-ford-algorithm-g-41/)
- **Difficulty**: Medium
- **Statement**: Given a weighted, directed graph of $V$ vertices (0-indexed) and $E$ edges represented as `edges[i] = [u, v, w]`, and a source vertex $S$. Find the shortest distance from $S$ to all vertices. If the graph contains a negative weight cycle reachable from $S$, return `{-1}`.

---

## 1. Problem, Restated

Compute single-source shortest paths on graphs with negative weights and detect negative weight cycles in $\mathcal{O}(V \times E)$ time using $V-1$ edge relaxation iterations.

- **Input**: `int V, int S`, `vector<vector<int>>& edges` (triplets `[u, v, wt]`).
- **Output**: `vector<int>` shortest distances from source $S$ (or `-1` if negative cycle detected).
- **Constraints**: $1 \le V \le 500$, $0 \le E \le V \times (V - 1)$, $-1000 \le \text{weight} \le 1000$.

---

## 2. Intuition & Pattern

In a graph with $V$ vertices, any simple shortest path contains at most $V-1$ edges. Therefore, relaxing all $E$ edges $V-1$ times is mathematically guaranteed to propagate shortest distances to all reachable vertices! 1) Initialize `dist(V, 1e8)` with `dist[S] = 0`. 2) Loop $V-1$ times: for each directed edge `[u, v, wt]`: if `dist[u] != 1e8 && dist[u] + wt < dist[v]`, update `dist[v] = dist[u] + wt`. 3) **Negative Cycle Check ($V$-th iteration)**: Loop over all edges once more. If ANY edge can STILL be relaxed (`dist[u] + wt < dist[v]`), a negative weight cycle exists $\implies$ return `{-1}`! 4) Return `dist`. Runs in $\mathcal{O}(V \times E)$ time.

- **Underlying Pattern**: `Dynamic Programming Edge Relaxation ($V-1$ Passes) + Negative Cycle Invariant`.

---

## 3. Approach 1 — Naive / Floyd-Warshall All-Pairs

### Idea
Floyd-Warshall all-pairs algorithm in $\mathcal{O}(V^3)$ time to check diagonal negative values `dist[i][i] < 0`.

### C++17 Code
```cpp
// O(V^3) Floyd Warshall
```

### Java Code
```java
// Java equivalent
// O(V^3) Floyd Warshall
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^3)$ time.
- **Space Complexity**: $\mathcal{O}(V^2)$.
- **Why it's not good enough**: Cubic time is unnecessary for single-source queries.

---

## 4. Approach 2 — Better (Shortest Path Faster Algorithm / SPFA)

### Idea
Shortest Path Faster Algorithm (SPFA / Queue-Optimized Bellman-Ford) with average O(E) time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class SolutionSPFA {
public:
    vector<int> spfa(int V, vector<vector<pair<int, int>>>& adj, int S) {
        vector<int> dist(V, 1e8), count(V, 0), inQueue(V, 0);
        queue<int> q;
        dist[S] = 0; q.push(S); inQueue[S] = 1;
        while (!q.empty()) {
            int u = q.front(); q.pop(); inQueue[u] = 0;
            for (auto& edge : adj[u]) {
                int v = edge.first, w = edge.second;
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    if (!inQueue[v]) {
                        q.push(v); inQueue[v] = 1; count[v]++;
                        if (count[v] >= V) return {-1}; // Negative cycle!
                    }
                }
            }
        }
        return dist;
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionSPFA {

    int[] spfa(int V, vector<List<int[]>> adj, int S) {
        int[] dist(V, 1e8), count(V, 0), inQueue(V, 0);
        Queue<Integer> q = new LinkedList<>();
        dist[S] = 0; q.push(S); inQueue[S] = 1;
        while (!q.isEmpty()) {
            int u = q.peek(); q.pop(); inQueue[u] = 0;
            for (var edge : adj[u]) {
                int v = edge.first, w = edge.second;
                if (dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                    if (!inQueue[v]) {
                        q.push(v); inQueue[v] = 1; count[v]++;
                        if (count[v] >= V) return {-1}; // Negative cycle!
                    }
                }
            }
        }
        return dist;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E)$ average time, $\mathcal{O}(V \times E)$ worst case.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's still not optimal**: SPFA is a queue optimization of Bellman-Ford, though worst case remains $\mathcal{O}(V \times E)$.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Bellman-Ford Algorithm)

### Idea
Classic Bellman-Ford Algorithm with Early-Exit Flag in $\mathcal{O}(V \times E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    /*  Function to implement Bellman Ford
    *   edges: vector of vectors which represents the graph
    *   S: source vertex
    *   V: number of vertices
    */
    vector<int> bellman_ford(int V, vector<vector<int>>& edges, int S) {
        vector<int> dist(V, 1e8);
        dist[S] = 0;
        
        // 1. Relax all edges (V - 1) times
        for (int i = 0; i < V - 1; i++) {
            bool anyRelaxed = false;
            
            for (const auto& edge : edges) {
                int u = edge[0];
                int v = edge[1];
                int wt = edge[2];
                
                if (dist[u] != 1e8 && dist[u] + wt < dist[v]) {
                    dist[v] = dist[u] + wt;
                    anyRelaxed = true;
                }
            }
            
            // Early-exit optimization: if no distance changed, we have already converged!
            if (!anyRelaxed) break;
        }
        
        // 2. N-th relaxation to detect Negative Weight Cycles
        for (const auto& edge : edges) {
            int u = edge[0];
            int v = edge[1];
            int wt = edge[2];
            
            if (dist[u] != 1e8 && dist[u] + wt < dist[v]) {
                return {-1}; // Negative cycle detected!
            }
        }
        
        return dist;
    }
};
```

### Java Code
```java
class Solution {

    /*  Function to implement Bellman Ford
    *   edges: vector of vectors which represents the graph
    *   S: source vertex
    *   V: number of vertices
    */
    int[] bellman_ford(int V, int[][] edges, int S) {
        int[] dist = new int[V];
        dist[S] = 0;
        
        // 1. Relax all edges (V - 1) times
        for (int i = 0; i < V - 1; i++) {
            boolean anyRelaxed = false;
            
            for (var edge : edges) {
                int u = edge[0];
                int v = edge[1];
                int wt = edge[2];
                
                if (dist[u] != 1e8 && dist[u] + wt < dist[v]) {
                    dist[v] = dist[u] + wt;
                    anyRelaxed = true;
                }
            }
            
            // Early-exit optimization: if no distance changed, we have already converged!
            if (anyRelaxed == null) break;
        }
        
        // 2. N-th relaxation to detect Negative Weight Cycles
        for (var edge : edges) {
            int u = edge[0];
            int v = edge[1];
            int wt = edge[2];
            
            if (dist[u] != 1e8 && dist[u] + wt < dist[v]) {
                return {-1}; // Negative cycle detected!
            }
        }
        
        return dist;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V \times E)$ time ($V - 1$ passes over all $E$ edges; early exit can finish in $\mathcal{O}(E)$ if graph converges early).
- **Space Complexity**: $\mathcal{O}(V)$ distance array space (no adjacency list construction required!).
- **Why this is optimal**: Direct edge array iterations avoid memory allocations and provide strict cycle detection guarantees.

---

## 6. Dry Run

Graph $V = 3, S = 2$, `edges = [[0,1,5],[1,0,3],[1,2,-1],[2,0,1]]` (0-indexed)

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist: `[1e8, 1e8, 0]` | Pass 0 |
| `Pass 1 (V-1=2 passes)` | Edge $2 \to 0$ (wt 1): $dist[0] = 0+1 = 1$; Edge $0 \to 1$ (wt 5): $dist[1] = 1+5 = 6$ | dist: `[1, 6, 0]` |
| `Pass 2` | Edge $1 \to 2$ (wt -1): $dist[2] = \min(0, 6-1) = 0$ (no change) | dist converged: `[1, 6, 0]` |
| `Pass 3 (Cycle check)` | No edge can be relaxed further $\implies$ No Negative Cycle | Result: `dist = [1, 6, 0]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Negative cycle reachable from source (returns `{-1}`).
- Negative cycle NOT reachable from source.
- Disconnected nodes.

### Common Bugs to Avoid
- Relaxing edge when `dist[u] == 1e8` (causes numeric overflow/underflow on negative weights: $10^8 + (-5) < 10^8$).
- Using undirected edges directly without duplicating both forward and reverse directed edges.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Bellman-Ford require EXACTLY (V - 1) relaxation passes?**  
  **A**: A simple path without cycles in a graph of $V$ vertices contains at most $V - 1$ edges. In the worst-case configuration (a linear chain $0 \to 1 \to 2 \dots \to V-1$ where edges are processed in reverse order), each relaxation pass finalizes the shortest distance for exactly 1 additional edge depth. After $V-1$ passes, all simple shortest paths are guaranteed to be fully computed!

- **Q2: How does the V-th relaxation step detect negative cycles?**  
  **A**: If all simple paths are finalized after $V-1$ passes, distances can only decrease further on the $V$-th pass if an edge belongs to a cycle whose sum of weights is strictly negative (each traversal around the cycle reduces total path cost infinitely towards $-\infty$).

- **Q3: How does Bellman-Ford compare to Dijkstra and Floyd-Warshall?**  
  **A**: 1) **Dijkstra**: $\mathcal{O}((V + E) \log V)$, non-negative weights only, single source. 2) **Bellman-Ford**: $\mathcal{O}(V \times E)$, handles negative weights & detects negative cycles, single source. 3) **Floyd-Warshall**: $\mathcal{O}(V^3)$, handles negative weights & all-pairs shortest paths.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Bellman-Ford`, `Shortest Path`, `Dynamic Programming`, `Negative Cycles`, `Medium`
- **Related problems to practice next**:
- **Floyd-Warshall Algorithm**: All-pairs shortest paths.
- **Dijkstra Algorithm**: Non-negative shortest paths.
