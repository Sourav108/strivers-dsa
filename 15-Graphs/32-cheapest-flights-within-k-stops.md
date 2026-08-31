# Cheapest Flights Within K Stops (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Cheapest Flights Within K Stops](https://takeuforward.org/data-structure/g-38-cheapest-flights-within-k-stops/)
- **Difficulty**: Medium
- **Statement**: There are $n$ cities connected by some number of flights. You are given an array `flights` where `flights[i] = [from_i, to_i, price_i]`. Given integers `src`, `dst`, and `k`, return the cheapest price from `src` to `dst` with at most $k$ intermediate stops. If no such route exists, return -1.

---

## 1. Problem, Restated

Find the minimum cost path from `src` to `dst` with at most $k + 1$ edges using level-order BFS Queue prioritized strictly by stops count in $\mathcal{O}(K \times E)$ time.

- **Input**: `int n, int src, int dst, int k`, `vector<vector<int>>& flights`.
- **Output**: Integer representing minimum cost within $k$ stops (or -1).
- **Constraints**: $1 \le n \le 100$, $0 \le \text{flights.length} \le (n \times (n - 1) / 2)$, $0 \le k < n$.

---

## 2. Intuition & Pattern

Standard Dijkstra prioritizes minimum *cost*, which can exhaust all $k$ stops prematurely on a cheap path that never reaches the destination! Instead, **we MUST prioritize STOPS**: since stops increment monotonically by $+1$ each hop, a standard **FIFO Queue `{stops, {node, cost}}`** naturally processes flights level by level ($0 \dots k$). 1) Initialize `dist(n, 1e9)` with `dist[src] = 0`. 2) Push `{0, {src, 0}}` to queue. 3) Pop `{stops, {u, cost}}`. If `stops > k`, continue. 4) For each outgoing flight `[v, price]`: if `cost + price < dist[v]`, update `dist[v] = cost + price` and push `{stops + 1, {v, cost + price}}`. 5) Return `dist[dst] == 1e9 ? -1 : dist[dst]`. Runs in $\mathcal{O}(K \times E)$ time.

- **Underlying Pattern**: `Stop-Count Prioritized BFS Queue / Bellman-Ford ($k+1$ Edge Relaxations)`.

---

## 3. Approach 1 — Naive / DFS Backtracking

### Idea
DFS backtracking with stops tracking in $\mathcal{O}(V!)$ time.

### C++17 Code
```cpp
// O(V!) DFS all paths with stops count
```

### Java Code
```java
// Java equivalent
// O(V!) DFS all paths with stops count
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V!)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ stack.
- **Why it's not good enough**: DFS without distance pruning explores exponential duplicate paths.

---

## 4. Approach 2 — Better (Bellman-Ford with k+1 Iterations)

### Idea
Bellman-Ford Algorithm with (k + 1) Iterations in O(K x E) time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionBellmanFord {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        vector<int> dist(n, 1e9);
        dist[src] = 0;
        
        // Relax all edges exactly (k + 1) times
        for (int i = 0; i <= k; i++) {
            vector<int> temp = dist;
            for (const auto& f : flights) {
                int u = f[0], v = f[1], price = f[2];
                if (dist[u] != 1e9 && dist[u] + price < temp[v]) {
                    temp[v] = dist[u] + price;
                }
            }
            dist = temp;
        }
        return dist[dst] == 1e9 ? -1 : dist[dst];
    }
};
```

### Java Code
```java
class SolutionBellmanFord {

    int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        int[] dist = new int[n];
        dist[src] = 0;
        
        // Relax all edges exactly (k + 1) times
        for (int i = 0; i <= k; i++) {
            int[] temp = dist;
            for (var f : flights) {
                int u = f[0], v = f[1], price = f[2];
                if (dist[u] != 1e9 && dist[u] + price < temp[v]) {
                    temp[v] = dist[u] + price;
                }
            }
            dist = temp;
        }
        return dist[dst] == 1e9 ? -1 : dist[dst];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K \times E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ array.
- **Why it's still not optimal**: Bellman-Ford $k+1$ iterations are optimal and clean, using a copy array `temp` to prevent multi-hop cascades in a single round.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Stop-Count BFS Queue)

### Idea
FIFO Queue Level-Order BFS prioritized by Stops in $\mathcal{O}(K \times E)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int findCheapestPrice(int n, vector<vector<int>>& flights, int src, int dst, int k) {
        // 1. Build adjacency list: {neighbor, price}
        vector<vector<pair<int, int>>> adj(n);
        for (const auto& flight : flights) {
            int u = flight[0];
            int v = flight[1];
            int price = flight[2];
            adj[u].push_back({v, price});
        }
        
        // 2. Queue stores {stops, {node, cost}}
        // Monotonically increasing stops ensures FIFO queue acts as a level-order frontier
        queue<pair<int, pair<int, int>>> q;
        q.push({0, {src, 0}});
        
        vector<int> dist(n, 1e9);
        dist[src] = 0;
        
        while (!q.empty()) {
            auto [stops, nodeInfo] = q.front();
            q.pop();
            
            int node = nodeInfo.first;
            int cost = nodeInfo.second;
            
            // If stops exceed k, do not expand further
            if (stops > k) continue;
            
            for (const auto& edge : adj[node]) {
                int adjNode = edge.first;
                int price = edge.second;
                
                // Relaxation step
                if (cost + price < dist[adjNode] && stops <= k) {
                    dist[adjNode] = cost + price;
                    q.push({stops + 1, {adjNode, cost + price}});
                }
            }
        }
        
        return dist[dst] == 1e9 ? -1 : dist[dst];
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        // 1. Build adjacency list: {neighbor, price}
        List<List<int[]>> adj(n);
        for (var flight : flights) {
            int u = flight[0];
            int v = flight[1];
            int price = flight[2];
            adj[u].add({v, price});
        }
        
        // 2. Queue stores {stops, {node, cost}}
        // Monotonically increasing stops ensures FIFO queue acts as a level-order frontier
        queue<pair<int, pair<int, int>>> q;
        q.push({0, {src, 0}});
        
        int[] dist = new int[n];
        dist[src] = 0;
        
        while (!q.isEmpty()) {
            var [stops, nodeInfo] = q.peek();
            q.pop();
            
            int node = nodeInfo.first;
            int cost = nodeInfo.second;
            
            // If stops exceed k, do not expand further
            if (stops > k) continue;
            
            for (var edge : adj[node]) {
                int adjNode = edge.first;
                int price = edge.second;
                
                // Relaxation step
                if (cost + price < dist[adjNode] && stops <= k) {
                    dist[adjNode] = cost + price;
                    q.push({stops + 1, {adjNode, cost + price}});
                }
            }
        }
        
        return dist[dst] == 1e9 ? -1 : dist[dst];
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(K \times E)$ time (where $E = \text{flights.size()}$, each edge explored at most $k + 1$ times).
- **Space Complexity**: $\mathcal{O}(V + E)$ adjacency list, distance array, and queue memory.
- **Why this is optimal**: Prioritizing by stop count allows a simple $\mathcal{O}(1)$ queue without min-heap logarithmic overhead.

---

## 6. Dry Run

$n = 4, \text{src} = 0, \text{dst} = 3, k = 1$, `flights = [[0,1,100],[1,2,100],[2,0,100],[1,3,600],[2,3,200]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist[0]=0 | q: `[{0, {0, 0}}]` |
| `Pop {0, {0, 0}}` | Neighbors: 1 (cost 100, stops 1) | dist[1]=100, q: `[{1, {1, 100}}]` |
| `Pop {1, {1, 100}}` | Neighbors: 2 (cost 200, stops 2); 3 (cost 700, stops 2) | dist[2]=200, dist[3]=700, q: `[{2,{2,200}}, {2,{3,700}}]` |
| `Pop {2, {2, 200}}` | stops = 2 > k (k=1) -> pruned! (Cannot take $2 \to 3$ with 2 stops) | no enqueue |
| `Pop {2, {3, 700}}` | stops = 2 > k -> pruned! | q empty |
| `Result` | dist[3] = 700 (Cheapest within 1 stop is $0 \to 1 \to 3$) | Cost = 700 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Destination unreachable within $k$ stops (returns -1).
- $k = 0$ (direct flight only).
- No flights available.

### Common Bugs to Avoid
- Using Min-Heap Priority Queue on `cost` instead of `stops` (a cheaper path with $k+2$ stops can lock `dist[dst]`, preventing a valid slightly more expensive path with $\le k$ stops from reaching the destination!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does standard Dijkstra on (cost) FAIL on this problem?**  
  **A**: Standard Dijkstra pops the globally cheapest state first. Suppose path A reaches node $X$ with cost 10 and 3 stops, while path B reaches node $X$ with cost 20 and 1 stop. If $k=1$, path A will finalize `dist[X]=10` and prune path B! But path A will fail at destination due to stop limit, whereas path B could have reached `dst` within $k$ stops! Prioritizing by STOPS guarantees all valid $\le k$ stop paths are explored!

- **Q2: Why is a standard FIFO queue sufficient without a priority queue?**  
  **A**: Because the stop count increments by exactly 1 on every step ($0, 1, 2, \dots, k$). In a FIFO queue, elements naturally emerge in non-decreasing order of stops without any sorting needed!

- **Q3: Why do we need a copy array `temp` in the Bellman-Ford approach?**  
  **A**: Without `temp = dist`, relaxing edge $u \to v$ and then immediately relaxing edge $v \to w$ in the SAME iteration uses 2 flight hops in a single round, violating the 1-hop-per-iteration invariant!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Dijkstra`, `Bellman-Ford`, `LeetCode-787`, `Medium`
- **Related problems to practice next**:
- **Path with Minimum Effort**: Minimax Dijkstra.
- **Shortest Path Unit Weights**: Base BFS.
