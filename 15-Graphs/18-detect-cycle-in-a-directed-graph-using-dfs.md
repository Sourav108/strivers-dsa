# Detect Cycle in a Directed Graph using DFS (Recursion Stack) (Step 15.2 — Problems on BFS / DFS)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Detect Cycle in a Directed Graph using DFS (Recursion Stack)](https://takeuforward.org/data-structure/detect-a-cycle-in-directed-graph-using-dfs/)
- **Difficulty**: Medium
- **Statement**: Given a Directed Graph with $V$ vertices and $E$ edges represented by an adjacency list `adj`, determine whether the graph contains a cycle using Depth First Search (DFS).

---

## 1. Problem, Restated

Track nodes in the CURRENT recursion path using `pathVis[u] = 1`. If an edge leads to a node that is ALREADY on the current recursion path (`pathVis[v] == 1`), a directed cycle / back-edge exists.

- **Input**: $V$ vertices, $E$ directed edges represented as `vector<vector<int>>& adj`.
- **Output**: Boolean `true` if a directed cycle exists, `false` otherwise.
- **Constraints**: $1 \le V, E \le 10^5$.

---

## 2. Intuition & Pattern

In a directed graph, a cycle exists if and only if a directed back-edge points to an ancestor currently in the **active call stack path**! 1) Maintain two arrays: `vis[V]` (globally visited) and `pathVis[V]` (currently on active recursion stack). 2) Call `dfs(u)`: set `vis[u] = 1, pathVis[u] = 1`. 3) For each directed neighbor $v$ of $u$: if `!vis[v]`, recursively call `dfs(v)`. If recursion returns `true`, return `true` immediately. Else if `pathVis[v] == 1`, $v$ is an active ancestor on the current path $\implies$ CYCLE DETECTED! Return `true`. 4) **Backtrack**: Upon exiting $u$, reset `pathVis[u] = 0`! Runs in $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `Recursion Stack Path Tracking / 3-Color DFS State Model (White, Gray, Black)`.

---

## 3. Approach 1 — Naive / All-Pairs Transitive Closure

### Idea
Floyd-Warshall all-pairs reachability matrix in $\mathcal{O}(V^3)$ time to check if any node can reach itself (`reach[i][i] == 1`).

### C++17 Code
```cpp
// O(V^3) Floyd Warshall self-reachability
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^3)$ time.
- **Space Complexity**: $\mathcal{O}(V^2)$.
- **Why it's not good enough**: Cubic time is too slow for $V = 10^5$.

---

## 4. Approach 2 — Better

### Idea
3-Color State Array (0: White/Unvisited, 1: Gray/In Stack, 2: Black/Visited) in O(V + E) time and single O(V) array space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution3Color {
    bool dfs(int node, const vector<vector<int>>& adj, vector<int>& state) {
        state[node] = 1; // 1 = Gray (currently in recursion stack)
        for (int neighbor : adj[node]) {
            if (state[neighbor] == 1) return true; // Back-edge cycle found!
            if (state[neighbor] == 0 && dfs(neighbor, adj, state)) return true;
        }
        state[node] = 2; // 2 = Black (fully processed)
        return false;
    }
public:
    bool isCyclic(int V, vector<vector<int>>& adj) {
        vector<int> state(V, 0);
        for (int i = 0; i < V; i++)
            if (state[i] == 0 && dfs(i, adj, state)) return true;
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ single state array.
- **Why it's still not optimal**: Both 3-color and dual-array approaches achieve optimal asymptotic bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Dual-Array Visited & Path-Visited DFS in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    bool dfsCheck(int node, const vector<vector<int>>& adj, vector<int>& vis, vector<int>& pathVis) {
        vis[node] = 1;
        pathVis[node] = 1; // Mark node as part of current active recursion path
        
        // Traverse directed adjacent neighbors
        for (int neighbor : adj[node]) {
            // If the adjacent node has not been visited yet
            if (!vis[neighbor]) {
                if (dfsCheck(neighbor, adj, vis, pathVis)) {
                    return true;
                }
            }
            // If the adjacent node is ALREADY on the current recursion path -> CYCLE!
            else if (pathVis[neighbor]) {
                return true;
            }
        }
        
        // Backtrack: remove node from current recursion path before returning
        pathVis[node] = 0;
        return false;
    }

public:
    bool isCyclic(int V, vector<vector<int>>& adj) {
        vector<int> vis(V, 0);
        vector<int> pathVis(V, 0);
        
        // Outer loop for disconnected components
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                if (dfsCheck(i, adj, vis, pathVis)) {
                    return true;
                }
            }
        }
        
        return false;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time (each directed edge inspected exactly once).
- **Space Complexity**: $\mathcal{O}(V)$ recursion call stack, visited, and path-visited arrays.
- **Why this is optimal**: Path-visited backtracking correctly distinguishes cross-edges from true directed back-edge cycles.

---

## 6. Dry Run

Graph 1: `0 -> 1 -> 2 -> 3 -> 1` (Cycle $1-2-3-1$) vs Graph 2: `0 -> 1 -> 3` and `0 -> 2 -> 3` (DAG Cross Edge)

| Step | Action / State Change | Result |
|---|---|---|
| `Graph 1 (Directed Cycle)` | Path: `0 -> 1 -> 2 -> 3`. At 3: edge $3 \to 1$. vis[1]=1 and pathVis[1]=1! $\implies$ CYCLE FOUND! | Return TRUE ✅ |
| `Graph 2 (DAG Cross Edge)` | Path 1: `0 -> 1 -> 3` finishes -> pathVis[3]=0. Path 2: `0 -> 2 -> 3`. At 2: edge $2 \to 3$. vis[3]=1 but pathVis[3]=0 (not a cycle!) | Return FALSE ✅ (No Cycle) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Self loops $u \to u$ (`pathVis[u] == 1` catches immediately).
- Linear chain $0 \to 1 \to 2 \to 3$ (returns `false`).
- Disconnected components.

### Common Bugs to Avoid
- Forgetting to backtrack `pathVis[node] = 0` upon function return (falsely treats valid DAG cross-edges as cycles!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does the undirected cycle detection algorithm FAIL on directed graphs?**  
  **A**: In an undirected graph, checking if an adjacent node was visited (and not parent) is sufficient. But in a directed graph, two independent paths can converge on the same node (e.g. $0 \to 1 \to 3$ and $0 \to 2 \to 3$). Node 3 is already visited, but there is NO cycle! We must strictly check if node 3 is on the ACTIVE path (`pathVis == 1`).

- **Q2: How to solve directed cycle detection using BFS?**  
  **A**: Use **Kahn's Algorithm (Topological Sort)**! Compute in-degrees. Enqueue in-degree 0 nodes. Count popped nodes. If `count < V`, the graph contains a directed cycle!

- **Q3: What is the difference between Tree, Back, Forward, and Cross edges in DFS?**  
  **A**: Tree edges discover new nodes (`vis == 0`); Back edges point to an ancestor (`pathVis == 1` $\implies$ cycle); Forward/Cross edges point to already finished nodes (`vis == 1 && pathVis == 0`).


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `Cycle Detection`, `Directed Graph`, `LeetCode-207`, `Medium`
- **Related problems to practice next**:
- **Course Schedule I**: Course prerequisites cycle.
- **Topological Sort**: Kahn's algorithm cycle check.
