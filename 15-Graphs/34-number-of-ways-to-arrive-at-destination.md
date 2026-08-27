# Number of Ways to Arrive at Destination (Modulo Dijkstra Counting) (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Number of Ways to Arrive at Destination (Modulo Dijkstra Counting)](https://takeuforward.org/data-structure/g-40-number-of-ways-to-arrive-at-destination/)
- **Difficulty**: Medium
- **Statement**: You are in a city consisting of $n$ intersections numbered 0 to $n - 1$ with bi-directional roads given as `roads[i] = [u, v, time]`. Return the number of ways you can arrive at destination $n - 1$ from source 0 in the shortest amount of time, modulo $10^9 + 7$.

---

## 1. Problem, Restated

Compute the number of distinct shortest paths from 0 to $n - 1$ using Dijkstra's algorithm augmented with dynamic programming path counters (`ways` array) in $\mathcal{O}((V + E) \log V)$ time.

- **Input**: `int n`, `vector<vector<int>>& roads` (triplets `[u, v, time]`).
- **Output**: Integer representing number of shortest path ways modulo $10^9 + 7$.
- **Constraints**: $1 \le n \le 200$, $n - 1 \le \text{roads.length} \le n \times (n - 1) / 2$, $1 \le \text{time}_i \le 10^9$.

---

## 2. Intuition & Pattern

Dijkstra finds the minimum distance, while a companion `ways` array counts how many unique paths achieve that minimum distance! 1) Use 64-bit `long long` for `dist` array to avoid overflow (`dist(n, 1e18)` with `dist[0] = 0`). 2) Maintain `ways(n, 0)` with `ways[0] = 1`. 3) Min-Heap stores `{distance, node}`. Push `{0, 0}`. 4) Pop `{d, u}`. If `d > dist[u]`, continue. 5) For each neighbor `[v, time]`: 
   - **Case 1 (Strictly shorter path discovered)**: `if (d + time < dist[v])`: update `dist[v] = d + time`, reset `ways[v] = ways[u]`, and push `{dist[v], v}` to min-heap.
   - **Case 2 (Alternative path with equal minimum distance)**: `if (d + time == dist[v])`: accumulate paths: `ways[v] = (ways[v] + ways[u]) % MOD` (do NOT push to min-heap again!). 6) Return `ways[n - 1]`. Runs in $\mathcal{O}((V + E) \log V)$ time.

- **Underlying Pattern**: `Dijkstra Min-Heap with Dynamic Programming Path Frequency Accumulation`.

---

## 3. Approach 1 — Naive / All-Paths DFS Backtracking

### Idea
DFS backtracking all simple paths to find minimum path length and count frequency in $\mathcal{O}(V!)$ time.

### C++17 Code
```cpp
// O(V!) DFS all paths
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V!)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ stack.
- **Why it's not good enough**: Factorial search across all possible paths.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Dijkstra with DP Frequency Accumulation below directly achieves optimal $\mathcal{O}((V + E) \log V)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Dijkstra + DP Path Counter)

### Idea
Dijkstra with DP Path Counter (Modulo 10^9 + 7) in $\mathcal{O}((V + E) \log V)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int countPaths(int n, vector<vector<int>>& roads) {
        // 1. Build adjacency list: {neighbor, time}
        vector<vector<pair<int, long long>>> adj(n);
        for (const auto& r : roads) {
            int u = r[0];
            int v = r[1];
            long long time = r[2];
            adj[u].push_back({v, time});
            adj[v].push_back({u, time});
        }
        
        const int MOD = 1e9 + 7;
        
        // Min-heap stores {distance, node}
        priority_queue<pair<long long, int>, 
                       vector<pair<long long, int>>, 
                       greater<pair<long long, int>>> pq;
        
        // Use long long for distances to prevent 32-bit overflow
        vector<long long> dist(n, 1e18);
        vector<int> ways(n, 0);
        
        dist[0] = 0;
        ways[0] = 1;
        pq.push({0, 0});
        
        while (!pq.empty()) {
            auto [d, node] = pq.top();
            pq.pop();
            
            if (d > dist[node]) continue;
            
            for (const auto& edge : adj[node]) {
                int adjNode = edge.first;
                long long time = edge.second;
                
                // Case 1: Found a strictly shorter path to adjNode
                if (d + time < dist[adjNode]) {
                    dist[adjNode] = d + time;
                    ways[adjNode] = ways[node]; // Inherit path count
                    pq.push({dist[adjNode], adjNode});
                }
                // Case 2: Found another path with the EXACT SAME minimum distance
                else if (d + time == dist[adjNode]) {
                    ways[adjNode] = (ways[adjNode] + ways[node]) % MOD; // Accumulate paths
                }
            }
        }
        
        return ways[n - 1] % MOD;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((V + E) \log V)$ time (where $V = n, E = \text{roads.size()}$).
- **Space Complexity**: $\mathcal{O}(V + E)$ adjacency list, 64-bit distance array, ways array, and min-heap memory.
- **Why this is optimal**: Simultaneous distance relaxation and DP accumulation computes exact shortest path counts in a single Dijkstra pass.

---

## 6. Dry Run

$n = 7$, `roads = [[0,6,7],[0,1,2],[1,2,3],[1,3,3],[6,3,3],[3,5,1],[6,5,1],[2,5,1],[0,4,5],[4,6,2]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist[0]=0, ways[0]=1 | pq: `[{0, 0}]` |
| `Pop 0` | Nodes 1(2, w=1), 4(5, w=1), 6(7, w=1) | dist & ways updated |
| `Explore` | Multiple paths to node 6: `0->6 (7)`, `0->4->6 (5+2=7)`, `0->1->2->5->6 (2+3+1+1=7)`, `0->1->3->5->6 (2+3+1+1=7)` | 4 shortest paths of weight 7 found! |
| `Result` | dist[6] = 7, ways[6] = 4 | Total Ways = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $n = 1$ (returns 1).
- Large graph with huge edge weights (handled by `long long` 1e18).
- Single path.

### Common Bugs to Avoid
- Using 32-bit `int` for distances (leads to integer overflow with large weights and $n = 200$).
- Pushing to min-heap on `d + time == dist[adjNode]` (causes exponential duplicate traversals!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why MUST we use `long long` for the distance array?**  
  **A**: Because weights can be up to $10^9$ and $n \le 200$, making total path distances reach $200 \times 10^9 = 2 \times 10^{11}$, which far exceeds standard 32-bit `INT_MAX` ($2.14 \times 10^9$)!

- **Q2: Why should we NOT push to `pq` when `d + time == dist[adjNode]`?**  
  **A**: When `d + time == dist[adjNode]`, the optimal distance to `adjNode` is ALREADY in the priority queue (or already popped). Re-inserting `adjNode` will duplicate all downstream branch explorations, blowing up time complexity from $\mathcal{O}(E \log V)$ to exponential!

- **Q3: How is this related to Shortest Path DAG (SPDAG) Counting?**  
  **A**: Dijkstra implicitly constructs a Directed Acyclic Graph (DAG) containing only shortest-path edges. The `ways` array computes the number of paths from source to destination on this DAG via standard topological DP.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Dijkstra`, `Dynamic Programming`, `Shortest Path`, `LeetCode-1976`, `Medium`
- **Related problems to practice next**:
- **Dijkstra Algorithm**: Base shortest path.
- **Print Shortest Path**: Path reconstruction.
