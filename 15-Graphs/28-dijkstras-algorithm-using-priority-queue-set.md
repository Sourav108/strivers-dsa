# Dijkstra's Algorithm using Priority Queue / Set (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Dijkstra's Algorithm using Priority Queue / Set](https://takeuforward.org/data-structure/dijkstras-algorithm-using-priority-queue-g-32/)
- **Difficulty**: Medium
- **Statement**: Given a weighted, undirected and connected graph of $V$ vertices (0-indexed) and an adjacency list `adj`, find the shortest distance of all vertices from a given source vertex $S$ using Dijkstra's Algorithm with both a Priority Queue (Min-Heap) and `std::set`.

---

## 1. Problem, Restated

Compute single-source shortest paths on graphs with non-negative edge weights using greedy priority queue / set distance relaxation in $\mathcal{O}((V + E) \log V)$ time.

- **Input**: `int V, int S`, `vector<vector<int>> adj[]`.
- **Output**: `vector<int>` shortest distance array.
- **Constraints**: $1 \le V \le 10^5$, $0 \le \text{edges} \le 3 \times 10^5$, weights $\ge 0$.

---

## 2. Intuition & Pattern

Dijkstra's algorithm greedily expands the currently known closest unvisited vertex: 1) Initialize `dist(V, 1e9)` with `dist[S] = 0`. 2) Min-Heap stores `{distance, node}`. Push `{0, S}`. 3) Pop the minimum `{d, u}`. If `d > dist[u]`, skip (lazy deletion optimization!). 4) For each neighbor `[v, wt]` of $u$: if `d + wt < dist[v]`, update `dist[v] = d + wt` and push `{dist[v], v}` to priority queue. 5) Return `dist`. Alternatively, `std::set` allows erasing outdated pairs directly, saving queue memory.

- **Underlying Pattern**: `Greedy Min-Distance Edge Relaxation (Dijkstra's Algorithm)`.

---

## 3. Approach 1 — Naive / Array-based Scan (Dense Graphs)

### Idea
Array-based linear minimum distance scan in $\mathcal{O}(V^2 + E)$ time (suitable for dense graphs).

### C++17 Code
```cpp
// O(V^2) array-based scan for dense graphs
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ distance array.
- **Why it's not good enough**: Quadratic time is slower than $\mathcal{O}(E \log V)$ on sparse graphs.

---

## 4. Approach 2 — Better (std::set with Active Deletion)

### Idea
std::set Dijkstra with Active Element Deletion in O((V + E) log V) time.

### C++17 Code
```cpp
#include <vector>
#include <set>
using namespace std;

class SolutionSet {
public:
    vector<int> dijkstra(int V, vector<vector<int>> adj[], int S) {
        set<pair<int, int>> st;
        vector<int> dist(V, 1e9);
        
        dist[S] = 0;
        st.insert({0, S});
        
        while (!st.empty()) {
            auto it = *(st.begin());
            int d = it.first;
            int node = it.second;
            st.erase(it);
            
            for (auto edge : adj[node]) {
                int adjNode = edge[0];
                int edgeWeight = edge[1];
                
                if (d + edgeWeight < dist[adjNode]) {
                    // Erase existing stale distance pair if already present
                    if (dist[adjNode] != 1e9) {
                        st.erase({dist[adjNode], adjNode});
                    }
                    dist[adjNode] = d + edgeWeight;
                    st.insert({dist[adjNode], adjNode});
                }
            }
        }
        return dist;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((V + E) \log V)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ set storage.
- **Why it's still not optimal**: Tree-based set incurs larger constant factors than array-backed binary heaps.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Min-Heap Priority Queue)

### Idea
Min-Heap Priority Queue Dijkstra with Lazy Stale-Pair Pruning in $\mathcal{O}((V + E) \log V)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    // Function to find the shortest distance of all the vertices
    // from the source vertex S.
    vector<int> dijkstra(int V, vector<vector<int>> adj[], int S) {
        // Min-heap stores {distance, node}
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
        
        vector<int> dist(V, 1e9);
        dist[S] = 0;
        pq.push({0, S});
        
        while (!pq.empty()) {
            auto [d, node] = pq.top();
            pq.pop();
            
            // Lazy deletion optimization: skip if a shorter distance to node was already found
            if (d > dist[node]) continue;
            
            for (const auto& edge : adj[node]) {
                int adjNode = edge[0];
                int edgeWeight = edge[1];
                
                // Relaxation step
                if (d + edgeWeight < dist[adjNode]) {
                    dist[adjNode] = d + edgeWeight;
                    pq.push({dist[adjNode], adjNode});
                }
            }
        }
        
        return dist;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((V + E) \log V)$ time (at most $E$ insertions into min-heap of size at most $V$).
- **Space Complexity**: $\mathcal{O}(V + E)$ min-heap and distance vector memory.
- **Why this is optimal**: Min-heap provides cache-friendly contiguous array storage and fast $\mathcal{O}(\log V)$ operations.

---

## 6. Dry Run

Graph $V = 3, S = 2$, Edges: `(2,1,1), (1,0,1), (2,0,6)`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist: `[inf, inf, 0]` | pq: `[{0, 2}]` |
| `Pop {0, 2}` | Relax neighbor 1: $0+1=1 < \text{inf} \implies dist[1]=1$, push `{1, 1}`; Relax neighbor 0: $0+6=6 < \text{inf} \implies dist[0]=6$, push `{6, 0}` | pq: `[{1, 1}, {6, 0}]` |
| `Pop {1, 1}` | Relax neighbor 0: $1+1=2 < 6 \implies dist[0]=2$, push `{2, 0}` | pq: `[{2, 0}, {6, 0}]` |
| `Pop {2, 0}` | No further relaxations possible | pq: `[{6, 0}]` |
| `Pop {6, 0}` | d=6 > dist[0]=2 (Skipped via lazy pruning!) | pq empty |
| `Result` | `dist = [2, 1, 0]` | Shortest Distances: `0:2, 1:1, 2:0` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Disconnected vertices (remain `1e9`).
- Multiple edges between same pair of nodes (picks minimum weight).
- Self loops.

### Common Bugs to Avoid
- Forgetting `if (d > dist[node]) continue;` (leads to processing stale duplicate entries and higher runtime).
- Using Dijkstra on graphs with negative edge weights or negative cycles (causes infinite loops / wrong answers).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does Dijkstra's algorithm FAIL on negative edge weights?**  
  **A**: Dijkstra greedily assumes that once a node is popped from the min-heap, its shortest distance is finalized and cannot be improved. A negative edge weight later in the graph can provide a smaller total path to an already 'finalized' node, violating this greedy invariant! Use **Bellman-Ford** or **SPFA** for negative weights.

- **Q2: What is the trade-off between `std::priority_queue` vs `std::set` in Dijkstra?**  
  **A**: 1) `std::priority_queue` is faster in practice due to contiguous array layout (vector) and better cache locality, even though it stores stale entries. 2) `std::set` allows erasing outdated `{old_dist, u}` entries, saving memory in dense graphs, but tree node pointer allocations incur higher constant factor overhead.

- **Q3: What is the time complexity difference with Fibonacci Heaps?**  
  **A**: With Fibonacci Heaps, `decrease-key` takes $\mathcal{O}(1)$ amortized time, bringing the total theoretical runtime down to $\mathcal{O}(E + V \log V)$!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Dijkstra`, `Shortest Path`, `Min-Heap`, `Set`, `Medium`
- **Related problems to practice next**:
- **Print Shortest Path**: Path reconstruction.
- **Network Delay Time**: Dijkstra application.
