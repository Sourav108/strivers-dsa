# Strongly Connected Components (Kosaraju's Algorithm) (Step 15.6 — Other Graph Algorithms)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Strongly Connected Components (Kosaraju's Algorithm)](https://takeuforward.org/data-structure/strongly-connected-components-kosarajus-algorithm-g-54/)
- **Difficulty**: Hard
- **Statement**: Given a directed graph with $V$ vertices (0-indexed) and $E$ edges represented by an adjacency list `adj`, find the total number of Strongly Connected Components (SCCs) in the graph using Kosaraju's Algorithm. Also extract the actual constituent vertices of each individual SCC.

---

## 1. Problem, Restated

Decompose a directed graph into maximal strongly connected subgraphs where every vertex in a component can reach every other vertex using Kosaraju's 3-Step DFS Edge-Reversal Architecture in $\mathcal{O}(V + E)$ time.

- **Input**: `int V`, `vector<vector<int>>& adj`.
- **Output**: Integer representing total number of Strongly Connected Components.
- **Constraints**: $1 \le V \le 5000$, $0 \le E \le V(V-1)$.

---

## 2. Intuition & Pattern

**Strongly Connected Component (SCC)**: A maximal subset of vertices $C \subseteq V$ such that for any $u, v \in C$, there is a directed path $u \rightsquigarrow v$ AND $v \rightsquigarrow u$. 
**Why Edge Reversal Works (Kosaraju's Magic)**: 
If we compress each SCC into a single super-node, the resulting SCC graph is a **Directed Acyclic Graph (DAG)** with sink and source SCCs! 
**Kosaraju's 3-Step Process**: 
1) **Step 1 (Order by Finish Time)**: Perform DFS on original graph. Push vertices onto a `stack` in post-order (when DFS call finishes). The top of the stack will contain vertices from the 'source' SCCs of the condensation DAG! 
2) **Step 2 (Transpose Graph)**: Reverse all directed edges $u \to v \implies v \to u$ to create `adjT`. This preserves internal SCC reachability while reversing inter-SCC cross edges (turning source SCCs into sink SCCs)! 
3) **Step 3 (DFS on Transpose)**: Pop nodes from the stack. If unvisited in `vis2`, trigger DFS on `adjT`. This DFS explores and absorbs EXACTLY one complete SCC because reversed cross-edges prevent leaking into other SCCs! Increment `sccCount++`. Runs in $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `Kosaraju's 3-Step Algorithm / DFS Finish-Time Stack + Edge Transposition`.

---

## 3. Approach 1 — Naive / Pairwise Reachability Matrix

### Idea
For every pair $(u, v)$, run BFS from $u$ to check if $v$ is reachable AND run BFS from $v$ to check if $u$ is reachable in $\mathcal{O}(V^2 \cdot (V + E))$ time.

### C++17 Code
```cpp
// O(V^2 * (V + E)) pairwise reachability brute force
```

### Java Code
```java
// Java equivalent
// O(V^2 * (V + E)) pairwise reachability brute force
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V^2 \cdot (V + E))$ time.
- **Space Complexity**: $\mathcal{O}(V^2)$ reachability matrix.
- **Why it's not good enough**: Pairwise reachability is cubic.

---

## 4. Approach 2 — Better (Tarjan's Single-Pass SCC Algorithm)

### Idea
Tarjan's Single-Pass SCC Algorithm using low-link values and an active recursion stack in O(V + E) time.

### C++17 Code
```cpp
#include <vector>
#include <stack>
#include <algorithm>
using namespace std;

class SolutionTarjanSCC {
    int timer = 1, sccCount = 0;
    void dfs(int u, vector<vector<int>>& adj, vector<int>& tin, vector<int>& low,
             vector<int>& inStack, stack<int>& st) {
        tin[u] = low[u] = timer++;
        st.push(u); inStack[u] = 1;
        for (int v : adj[u]) {
            if (tin[v] == 0) {
                dfs(v, adj, tin, low, inStack, st);
                low[u] = min(low[u], low[v]);
            } else if (inStack[v]) {
                low[u] = min(low[u], tin[v]);
            }
        }
        if (low[u] == tin[u]) {
            sccCount++;
            while (true) {
                int node = st.top(); st.pop(); inStack[node] = 0;
                if (node == u) break;
            }
        }
    }
public:
    int tarjanSCC(int V, vector<vector<int>>& adj) {
        vector<int> tin(V, 0), low(V, 0), inStack(V, 0);
        stack<int> st;
        for (int i = 0; i < V; i++)
            if (tin[i] == 0) dfs(i, adj, tin, low, inStack, st);
        return sccCount;
    }
};
```

### Java Code
```java
import java.util.*;

class SolutionTarjanSCC {
    int timer = 1, sccCount = 0;
    void dfs(int u, int[][] adj, int[] tin, int[] low,
             int[] inStack, Stack<Integer> st) {
        tin[u] = low[u] = timer++;
        st.push(u); inStack[u] = 1;
        for (int v : adj[u]) {
            if (tin[v] == 0) {
                dfs(v, adj, tin, low, inStack, st);
                low[u] = Math.min(low[u], low[v]);
            } else if (inStack[v]) {
                low[u] = Math.min(low[u], tin[v]);
            }
        }
        if (low[u] == tin[u]) {
            sccCount++;
            while (true) {
                int node = st.peek(); st.pop(); inStack[node] = 0;
                if (node == u) break;
            }
        }
    }

    int tarjanSCC(int V, int[][] adj) {
        int[] tin(V, 0), low(V, 0), inStack(V, 0);
        Stack<Integer> st = new Stack<>();
        for (int i = 0; i < V; i++)
            if (tin[i] == 0) dfs(i, adj, tin, low, inStack, st);
        return sccCount;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ space.
- **Why it's still not optimal**: Tarjan's algorithm computes SCCs in a single DFS pass instead of two passes + transposition, but Kosaraju's is conceptually simpler to implement.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Kosaraju's 3-Step Algorithm)

### Idea
Kosaraju's 3-Step DFS Edge-Reversal Algorithm in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;

class Solution {
private:
    // Step 1: DFS to sort vertices by finish time onto stack
    void dfsFinishTime(int node, vector<vector<int>>& adj, vector<int>& vis, stack<int>& st) {
        vis[node] = 1;
        for (int neighbor : adj[node]) {
            if (!vis[neighbor]) {
                dfsFinishTime(neighbor, adj, vis, st);
            }
        }
        st.push(node); // Push upon completion (finish time order)
    }
    
    // Step 3: DFS on transposed graph to collect a single SCC
    void dfsTranspose(int node, vector<vector<int>>& adjT, vector<int>& vis, vector<int>& currentSCC) {
        vis[node] = 1;
        currentSCC.push_back(node);
        for (int neighbor : adjT[node]) {
            if (!vis[neighbor]) {
                dfsTranspose(neighbor, adjT, vis, currentSCC);
            }
        }
    }
    
public:
    // Function to find number of strongly connected components in the graph.
    int kosaraju(int V, vector<vector<int>>& adj) {
        // Step 1: Compute finish time order
        vector<int> vis(V, 0);
        stack<int> st;
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                dfsFinishTime(i, adj, vis, st);
            }
        }
        
        // Step 2: Transpose all directed edges (u -> v becomes v -> u)
        vector<vector<int>> adjT(V);
        for (int u = 0; u < V; u++) {
            for (int v : adj[u]) {
                adjT[v].push_back(u);
            }
        }
        
        // Step 3: Process nodes in decreasing finish time order on transposed graph
        vector<int> vis2(V, 0);
        int sccCount = 0;
        vector<vector<int>> allSCCs; // Stores all individual SCC components
        
        while (!st.empty()) {
            int node = st.top();
            st.pop();
            
            if (!vis2[node]) {
                sccCount++;
                vector<int> currentSCC;
                dfsTranspose(node, adjT, vis2, currentSCC);
                allSCCs.push_back(currentSCC);
            }
        }
        
        return sccCount;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    // Step 1: DFS to sort vertices by finish time onto stack
    void dfsFinishTime(int node, int[][] adj, int[] vis, Stack<Integer> st) {
        vis[node] = 1;
        for (int neighbor : adj[node]) {
            if (!vis[neighbor]) {
                dfsFinishTime(neighbor, adj, vis, st);
            }
        }
        st.push(node); // Push upon completion (finish time order)
    }
    
    // Step 3: DFS on transposed graph to collect a single SCC
    void dfsTranspose(int node, int[][] adjT, int[] vis, int[] currentSCC) {
        vis[node] = 1;
        currentSCC.add(node);
        for (int neighbor : adjT[node]) {
            if (!vis[neighbor]) {
                dfsTranspose(neighbor, adjT, vis, currentSCC);
            }
        }
    }

    // Function to find number of strongly connected components in the graph.
    int kosaraju(int V, int[][] adj) {
        // Step 1: Compute finish time order
        int[] vis = new int[V];
        Stack<Integer> st = new Stack<>();
        for (int i = 0; i < V; i++) {
            if (!vis[i]) {
                dfsFinishTime(i, adj, vis, st);
            }
        }
        
        // Step 2: Transpose all directed edges (u . v becomes v . u)
        int[][] adjT(V);
        for (int u = 0; u < V; u++) {
            for (int v : adj[u]) {
                adjT[v].add(u);
            }
        }
        
        // Step 3: Process nodes in decreasing finish time order on transposed graph
        int[] vis2 = new int[V];
        int sccCount = 0;
        List<List<Integer>> allSCCs = new ArrayList<>(); // Stores all individual SCC components
        
        while (!st.isEmpty()) {
            int node = st.peek();
            st.pop();
            
            if (!vis2[node]) {
                sccCount++;
                List<Integer> currentSCC = new ArrayList<>();
                dfsTranspose(node, adjT, vis2, currentSCC);
                allSCCs.add(currentSCC);
            }
        }
        
        return sccCount;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time (Step 1 DFS: $\mathcal{O}(V + E)$; Step 2 Transposition: $\mathcal{O}(V + E)$; Step 3 DFS: $\mathcal{O}(V + E)$).
- **Space Complexity**: $\mathcal{O}(V + E)$ transposed adjacency list, visited arrays, and stack memory.
- **Why this is optimal**: Edge transposition isolates SCC components by turning cross-component edges into backward barriers.

---

## 6. Dry Run

Directed Graph $V = 5$: `0->2, 2->1, 1->0, 0->3, 3->4`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1 (Finish Times)` | DFS visits `0->2->1` (cycle) and `3->4`. Stack top to bottom: `[0, 1, 2, 3, 4]` (or similar finish order) | Stack populated |
| `Step 2 (Transpose)` | Reversed edges: `2->0, 1->2, 0->1, 3->0, 4->3` | adjT constructed |
| `Step 3: Pop 0` | dfsTranspose(0): visits `2, 1` (since $3 \to 0$ is reversed, DFS CANNOT enter component {3,4}!). SCC 1: `{0, 2, 1}` | sccCount = 1 |
| `Step 3: Pop 3` | dfsTranspose(3): visits `3`. (Cannot enter 4 because edge is $4 \to 3$). SCC 2: `{3}` | sccCount = 2 |
| `Step 3: Pop 4` | dfsTranspose(4): visits `4`. SCC 3: `{4}` | sccCount = 3 |
| `Result` | Total Strongly Connected Components = 3 (`{0,1,2}`, `{3}`, `{4}`) | sccCount = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Graph is a complete cycle (sccCount = 1).
- Graph is a DAG with no cycles (each of the $V$ vertices is its own SCC $\implies$ sccCount = $V$).
- $V = 1$.

### Common Bugs to Avoid
- Re-using `vis` array from Step 1 without re-initializing `vis2` for Step 3.
- Transposing undirected graphs (Kosaraju is strictly for DIRECTED graphs).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does transposing edges prevent DFS from leaking across different SCCs in Step 3?**  
  **A**: In the original graph DAG condensation, there was a directed cross-edge from $\text{SCC}_A \to \text{SCC}_B$. Finish-time ordering ensures $\text{SCC}_A$ finishes AFTER $\text{SCC}_B$, so $\text{SCC}_A$ nodes are on top of the stack. When edges are reversed ($	ext{SCC}_B \to \text{SCC}_A$), popping $\text{SCC}_A$ cannot traverse to $\text{SCC}_B$ because the arrow now points backwards! Thus DFS isolates $\text{SCC}_A$ perfectly!

- **Q2: How does Kosaraju's Algorithm compare to Tarjan's SCC Algorithm?**  
  **A**: 1) **Kosaraju's**: Requires 2 DFS passes + explicit graph transposition $\mathcal{O}(V + E)$. Easier to reason about and code. 2) **Tarjan's**: Requires only 1 DFS pass + an explicit runtime stack and `low` links. Faster in practice (single cache-friendly traversal) and needs no transposed graph allocation.

- **Q3: What is 2-SAT (2-Satisfiability) and how is SCC used to solve it?**  
  **A**: In boolean 2-SAT, each clause $(A \lor B)$ converts to implication edges $(\neg A \implies B)$ and $(\neg B \implies A)$. The formula is satisfiable if and only if no variable $x$ and its negation $\neg x$ belong to the SAME Strongly Connected Component (solvable in $\mathcal{O}(V + E)$ via Kosaraju/Tarjan!).


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `SCC`, `Kosaraju's Algorithm`, `Tarjan's Algorithm`, `2-SAT`, `Hard`
- **Related problems to practice next**:
- **Articulation Point**: Cut vertices via Tarjan.
- **Topological Sort**: DAG finish-time ordering.
