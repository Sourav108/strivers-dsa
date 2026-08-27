# Network Delay Time (Signal propagation via Dijkstra) (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Network Delay Time (Signal propagation via Dijkstra)](https://takeuforward.org/data-structure/network-delay-time/)
- **Difficulty**: Medium
- **Statement**: You are given a network of $n$ nodes, labeled from 1 to $n$. You are also given `times`, a list of travel times as directed edges `times[i] = (u, v, w)`. A signal is sent from node $k$. Return the minimum time it takes for all $n$ nodes to receive the signal. If it is impossible for all $n$ nodes to receive the signal, return -1.

---

## 1. Problem, Restated

Compute shortest path from node $k$ to all nodes using Dijkstra's algorithm: return $\max_{1 \le i \le n}(\text{dist}[i])$ if all nodes are reachable, else return -1 in $\mathcal{O}((V + E) \log V)$ time.

- **Input**: `vector<vector<int>>& times` (triplets `[u, v, w]`), `int n, int k`.
- **Output**: Integer representing minimum delay time for all nodes to receive signal (or -1).
- **Constraints**: $1 \le k \le n \le 100$, $1 \le \text{times.length} \le 6000$, $0 \le w_i \le 100$.

---

## 2. Intuition & Pattern

Signal travels in parallel along all directed edges at uniform speed. The total time for ALL nodes to receive the signal is dictated by the node that takes the LONGEST minimum time from source $k$! 1) Build 1-indexed directed adjacency list. 2) Initialize `dist(n + 1, 1e9)` with `dist[k] = 0`. 3) Min-Heap stores `{time, node}`. Push `{0, k}`. 4) Pop `{d, u}`. If `d > dist[u]`, continue. For each outgoing edge `[v, wt]`: if `d + wt < dist[v]`, update `dist[v] = d + wt` and push `{dist[v], v}`. 5) Find maximum time: `maxTime = max(dist[1...n])`. If `maxTime == 1e9`, at least one node is unreachable $\implies$ return -1; else return `maxTime`. Runs in $\mathcal{O}((V + E) \log V)$ time.

- **Underlying Pattern**: `Single-Source Dijkstra's Algorithm + Maximum Element Reduction`.

---

## 3. Approach 1 — Naive / Floyd-Warshall Algorithm

### Idea
Floyd-Warshall all-pairs shortest paths in $\mathcal{O}(V^3)$ time.

### C++17 Code
```cpp
// O(V^3) Floyd Warshall
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^3)$ time.
- **Space Complexity**: $\mathcal{O}(V^2)$.
- **Why it's not good enough**: Cubic time is unnecessary for single-source queries.

---

## 4. Approach 2 — Better (Bellman-Ford Algorithm)

### Idea
Bellman-Ford Algorithm in O(V x E) time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionBellmanFord {
public:
    int networkDelayTime(vector<vector<int>>& times, int n, int k) {
        vector<int> dist(n + 1, 1e9);
        dist[k] = 0;
        for (int i = 1; i < n; i++) {
            for (const auto& t : times) {
                int u = t[0], v = t[1], w = t[2];
                if (dist[u] != 1e9 && dist[u] + w < dist[v]) {
                    dist[v] = dist[u] + w;
                }
            }
        }
        int maxTime = 0;
        for (int i = 1; i <= n; i++) {
            if (dist[i] == 1e9) return -1;
            maxTime = max(maxTime, dist[i]);
        }
        return maxTime;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V \times E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ array.
- **Why it's still not optimal**: Dijkstra is faster than Bellman-Ford on non-negative weighted graphs.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Min-Heap Dijkstra)

### Idea
Min-Heap Dijkstra's Algorithm in $\mathcal{O}((V + E) \log V)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
#include <algorithm>
using namespace std;

class Solution {
public:
    int networkDelayTime(vector<vector<int>>& times, int n, int k) {
        // 1. 1-indexed directed adjacency list: {neighbor, weight}
        vector<vector<pair<int, int>>> adj(n + 1);
        for (const auto& t : times) {
            int u = t[0];
            int v = t[1];
            int w = t[2];
            adj[u].push_back({v, w});
        }
        
        // 2. Min-Heap stores {time, node}
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
        
        vector<int> dist(n + 1, 1e9);
        dist[k] = 0;
        pq.push({0, k});
        
        // 3. Dijkstra shortest path traversal
        while (!pq.empty()) {
            auto [time, node] = pq.top();
            pq.pop();
            
            if (time > dist[node]) continue;
            
            for (const auto& edge : adj[node]) {
                int adjNode = edge.first;
                int weight = edge.second;
                
                if (time + weight < dist[adjNode]) {
                    dist[adjNode] = time + weight;
                    pq.push({dist[adjNode], adjNode});
                }
            }
        }
        
        // 4. Find the bottleneck maximum time among all nodes 1...n
        int maxDelay = 0;
        for (int i = 1; i <= n; i++) {
            if (dist[i] == 1e9) {
                return -1; // Node i is unreachable
            }
            maxDelay = max(maxDelay, dist[i]);
        }
        
        return maxDelay;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((V + E) \log V)$ time (where $V = n, E = \text{times.size()}$).
- **Space Complexity**: $\mathcal{O}(V + E)$ adjacency list, distance array, and min-heap space.
- **Why this is optimal**: Greedy Dijkstra finds the earliest signal arrival time for every node in optimal heap-driven time.

---

## 6. Dry Run

`times = [[2,1,1],[2,3,1],[3,4,1]]`, $n = 4, k = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist: `[inf, inf, 0, inf, inf]` | pq: `[{0, 2}]` |
| `Pop {0, 2}` | Relax: $2 \to 1 (wt 1) \implies dist[1]=1$; $2 \to 3 (wt 1) \implies dist[3]=1$ | pq: `[{1, 1}, {1, 3}]` |
| `Pop {1, 1}` | Node 1 has no outgoing edges | pq: `[{1, 3}]` |
| `Pop {1, 3}` | Relax: $3 \to 4 (wt 1) \implies dist[4]=1+1=2$ | pq: `[{2, 4}]` |
| `Pop {2, 4}` | Node 4 has no outgoing edges | pq empty |
| `Max Delay` | dist: `dist[1]=1, dist[2]=0, dist[3]=1, dist[4]=2` $\implies \max = 2$ | Network Delay Time = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Disconnected nodes (returns -1).
- $n = 1$ with $k = 1$ (returns 0).
- Source has no outgoing edges with $n > 1$ (returns -1).

### Common Bugs to Avoid
- 0-indexing vs 1-indexing mismatch (looping $0 \dots n-1$ instead of $1 \dots n$).
- Returning sum of distances instead of maximum distance.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the total time the MAXIMUM of all shortest path distances?**  
  **A**: Because the signal propagates concurrently across all channels. All nodes receive signals in parallel, so the time when the ENTIRE network has finished receiving is precisely when the furthest node receives its earliest packet!

- **Q2: How does this problem relate to Real-World Multicast Routing Protocol (PIM)?**  
  **A**: In computer networks and CDNs, Multicast Shortest Path Trees (Reverse Path Forwarding) compute minimum delay spanning routes to minimize packet broadcasting latency across autonomous systems.

- **Q3: What if edge weights were negative?**  
  **A**: Dijkstra would produce incorrect answers due to greedy violation; we would have to use Bellman-Ford or SPFA (Shortest Path Faster Algorithm).


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Dijkstra`, `Shortest Path`, `Min-Heap`, `LeetCode-743`, `Medium`
- **Related problems to practice next**:
- **Dijkstra Algorithm**: Base implementation.
- **Cheapest Flights Within K Stops**: Constrained shortest path.
