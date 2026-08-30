# Depth First Search (DFS) Traversal (Step 15.1 — Learning)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Depth First Search (DFS) Traversal](https://takeuforward.org/data-structure/depth-first-search-dfs-traversal-graph/)
- **Difficulty**: Easy
- **Statement**: Given a connected undirected graph represented by an adjacency list `adj`, return a vector containing the Depth First Search (DFS) traversal of the graph starting from vertex 0.

---

## 1. Problem, Restated

Deep recursive traversal exploring each branch to its maximum depth before backtracking, using visited array in $\mathcal{O}(V + 2E)$ time.

- **Input**: Parameters specified ($V$ vertices, $E$ edges / adjacency lists).
- **Output**: Traversal vector / count / data structures.
- **Constraints**: Standard competitive programming limits ($V \le 10^5, E \le 2 \times 10^5$).

---

## 2. Intuition & Pattern

DFS dives down a single branch as deeply as possible before backtracking. 1) Mark `vis[node] = 1`, append `node` to `dfs` result. 2) For each neighbor $v$ of `node`: if `!vis[v]`, recursively call `dfsHelper(v)`. Backtrack when all neighbors of a node are fully explored.

- **Underlying Pattern**: `Recursive Call Stack Depth-First Traversal with Backtracking`.

---

## 3. Approach 1 — Naive / Adjacency Matrix

### Idea
Iterative DFS using an explicit LIFO stack.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;
vector<int> dfsIterative(int V, vector<vector<int>>& adj) {
    vector<int> dfs, vis(V, 0);
    stack<int> st;
    st.push(0);
    while (!st.empty()) {
        int node = st.top(); st.pop();
        if (!vis[node]) {
            vis[node] = 1;
            dfs.push_back(node);
            for (auto it = adj[node].rbegin(); it != adj[node].rend(); ++it)
                if (!vis[*it]) st.push(*it);
        }
    }
    return dfs;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[] dfsIterative(int V, int[][] adj) {
        int[] dfs, vis(V, 0);
        Stack<Integer> st = new Stack<>();
        st.push(0);
        while (!st.isEmpty()) {
            int node = st.peek(); st.pop();
            if (!vis[node]) {
                vis[node] = 1;
                dfs.add(node);
                for (var it = adj[node].rbegin(); it != adj[node].rend(); ++it)
                    if (!vis[it]) st.push(it);
            }
        }
        return dfs;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ stack.
- **Why it's not good enough**: Explicit stack iteration matches recursive call frames.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Adjacency List / BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(V + E)$ or $\mathcal{O}(V + 2E)$ time and space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Call Stack DFS in $\mathcal{O}(V + 2E)$ time and $\mathcal{O}(V)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    void dfsHelper(int node, const vector<vector<int>>& adj, vector<int>& vis, vector<int>& dfs) {
        vis[node] = 1;
        dfs.push_back(node);
        
        // Traverse all adjacent vertices recursively
        for (int neighbor : adj[node]) {
            if (!vis[neighbor]) {
                dfsHelper(neighbor, adj, vis, dfs);
            }
        }
    }

public:
    vector<int> dfsOfGraph(int V, vector<vector<int>>& adj) {
        vector<int> dfs;
        vector<int> vis(V, 0);
        
        // Start DFS from vertex 0
        dfsHelper(0, adj, vis, dfs);
        
        return dfs;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void dfsHelper(int node, int[][] adj, int[] vis, int[] dfs) {
        vis[node] = 1;
        dfs.add(node);
        
        // Traverse all adjacent vertices recursively
        for (int neighbor : adj[node]) {
            if (!vis[neighbor]) {
                dfsHelper(neighbor, adj, vis, dfs);
            }
        }
    }

    int[] dfsOfGraph(int V, int[][] adj) {
        List<Integer> dfs = new ArrayList<>();
        int[] vis = new int[V];
        
        // Start DFS from vertex 0
        dfsHelper(0, adj, vis, dfs);
        
        return dfs;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + 2E)$ time (each vertex visited once, each undirected edge inspected twice).
- **Space Complexity**: $\mathcal{O}(V)$ recursion call stack and visited array space.
- **Why this is optimal**: Direct recursion provides clean functional composition with minimal memory overhead.

---

## 6. Dry Run

Graph $V = 5$, Edges: `0-2, 2-4, 0-1, 0-3` (adj: `0:[2,1,3], 2:[0,4], 4:[2], 1:[0], 3:[0]`)

| Step | Action / State Change | Result |
|---|---|---|
| `Call dfs(0)` | vis[0]=1, append 0 -> first neighbor is 2 | dfs: `[0]` |
| `Call dfs(2)` | vis[2]=1, append 2 -> neighbor 0 visited, next is 4 | dfs: `[0, 2]` |
| `Call dfs(4)` | vis[4]=1, append 4 -> neighbor 2 visited -> backtrack to 2 -> backtrack to 0 | dfs: `[0, 2, 4]` |
| `Back at 0` | next neighbor 1 unvisited -> call dfs(1) -> append 1 -> backtrack to 0 | dfs: `[0, 2, 4, 1]` |
| `Back at 0` | next neighbor 3 unvisited -> call dfs(3) -> append 3 -> backtrack | DFS Order: `[0, 2, 4, 1, 3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node graph $V = 1$.
- Linear chain graph $0-1-2-3-4$ (call stack depth $V$).

### Common Bugs to Avoid
- Forgetting `vis[node] = 1` inside recursion (causes infinite recursion and stack overflow on cyclic graphs!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the maximum recursion depth of DFS on a graph with V vertices?**  
  **A**: Worst-case recursion depth is $\mathcal{O}(V)$ (for a linear line graph $0 \to 1 \to 2 \to \dots \to V-1$). On systems with 1 MB default stack, $V > 10^5$ may require increasing stack size or using iterative DFS.

- **Q2: How does DFS classify edges in a directed graph?**  
  **A**: DFS classifies edges into 4 types: **Tree edges** (part of DFS tree), **Back edges** (point to an ancestor $\implies$ indicates cycle!), **Forward edges** (point to a descendant), and **Cross edges** (point to other branches).

- **Q3: When is DFS preferred over BFS?**  
  **A**: DFS is preferred for cycle detection, topological sorting, connected components, finding bridges/articulation points (Tarjan's), and backtracking path searches. BFS is preferred for finding shortest paths in unweighted graphs.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `DFS`, `Traversal`, `Recursion`, `Easy`
- **Related problems to practice next**:
- **BFS Traversal**: Breadth-first counterpart.
- **Detect Cycle in Undirected Graph**: Cycle detection.
