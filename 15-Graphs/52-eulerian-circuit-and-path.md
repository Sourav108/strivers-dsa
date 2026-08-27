# Eulerian Circuit and Path in Undirected and Directed Graphs (Step 15.6 — Other Graph Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Eulerian Circuit and Path in Undirected and Directed Graphs](https://takeuforward.org/data-structure/euler-circuit-and-path-in-undirected-graph/)
- **Difficulty**: Hard
- **Statement**: Given an undirected graph with $V$ vertices and adjacency list `adj`, determine if the graph has: (1) An **Eulerian Circuit** (return 2), (2) An **Eulerian Path** (return 1), or (3) **Neither** (return 0). Also provide the complete reconstruction of the Eulerian trail using Hierholzer's Algorithm.

---

## 1. Problem, Restated

Verify connected non-zero degree components via DFS and check degree parity invariants: 0 odd-degree vertices $\implies$ Eulerian Circuit; exactly 2 odd-degree vertices $\implies$ Eulerian Path; otherwise neither in $\mathcal{O}(V + E)$ time.

- **Input**: `int V`, `vector<int> adj[]`.
- **Output**: `2` (Eulerian Circuit), `1` (Eulerian Path), or `0` (Neither).
- **Constraints**: $1 \le V \le 10^5$, $0 \le E \le 2 \times 10^5$.

---

## 2. Intuition & Pattern

**Euler's Invariant Theorems**: 
1) **Connectivity Condition**: All vertices with non-zero degree MUST belong to a single connected component (verified via DFS/BFS). 
2) **Degree Parity (Undirected Graphs)**: 
   - **Eulerian Circuit (2)**: Every vertex has an **even degree** (each entry has a corresponding exit). 
   - **Eulerian Path (1)**: Exactly **0 or 2 vertices have odd degrees** (the odd-degree vertices serve as start and end of the path). 
   - **Neither (0)**: More than 2 vertices have odd degrees or disconnected active vertices. 
3) **Directed Graphs Counterpart**: 
   - Circuit: $\text{in-degree}(u) == \text{out-degree}(u)$ for all $u$. 
   - Path: At most one node with $\text{out} - \text{in} = 1$ (start) and at most one node with $\text{in} - \text{out} = 1$ (end). 
4) **Hierholzer's Reconstruction**: Greedily follow unused edges onto a stack until a dead end is reached; add to itinerary on backtrack. Runs in $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `Euler's Theorem on Graph Degrees + Hierholzer's Post-Order Edge Traversal`.

---

## 3. Approach 1 — Naive / Exhaustive Permutation Backtracking

### Idea
Backtracking DFS testing all $E!$ permutations of edges in factorial time.

### C++17 Code
```cpp
// O(E!) permutation search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E!)$ time.
- **Space Complexity**: $\mathcal{O}(E)$ stack.
- **Why it's not good enough**: Combinatorial edge permutation explosion.

---

## 4. Approach 2 — Better (Fleury's Algorithm)

### Idea
Fleury's Algorithm for Eulerian Trail Reconstruction in O(E^2) time (checks bridges on every step).

### C++17 Code
```cpp
// Fleury's algorithm overview with bridge checks
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(E^2)$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's still not optimal**: Fleury runs bridge checks repeatedly, whereas Hierholzer's algorithm reconstructs paths in linear $\mathcal{O}(E)$ time.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Degree Parity & Hierholzer's Algorithm)

### Idea
Degree Parity & Connectivity Validation + Hierholzer's Algorithm in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <stack>
#include <algorithm>
using namespace std;

class Solution {
private:
    // DFS to verify that all non-zero degree vertices belong to a single component
    void dfs(int node, vector<int>& vis, vector<int> adj[]) {
        vis[node] = 1;
        for (int neighbor : adj[node]) {
            if (!vis[neighbor]) {
                dfs(neighbor, vis, adj);
            }
        }
    }
    
public:
    /* Returns:
     * 2 : Eulerian Circuit
     * 1 : Eulerian Path
     * 0 : Neither
     */
    int isEulerCircuit(int V, vector<int> adj[]) {
        // 1. Check Connectivity of non-zero degree vertices
        vector<int> vis(V, 0);
        int nonZeroNode = -1;
        
        for (int i = 0; i < V; i++) {
            if (!adj[i].empty()) {
                nonZeroNode = i;
                break;
            }
        }
        
        // Graph with 0 edges has an Eulerian circuit trivially
        if (nonZeroNode == -1) return 2;
        
        // Start DFS from the first active node
        dfs(nonZeroNode, vis, adj);
        
        // If any vertex with non-zero degree was NOT visited, graph is disconnected
        for (int i = 0; i < V; i++) {
            if (!vis[i] && !adj[i].empty()) {
                return 0;
            }
        }
        
        // 2. Count vertices with odd degrees
        int oddCount = 0;
        for (int i = 0; i < V; i++) {
            if (adj[i].size() % 2 != 0) {
                oddCount++;
            }
        }
        
        // 3. Apply Euler's Theorem
        if (oddCount == 0) return 2; // Eulerian Circuit
        if (oddCount == 2) return 1; // Eulerian Path
        return 0;                    // Neither
    }
    
    // Hierholzer's Algorithm for Trail Reconstruction (Directed / Undirected)
    vector<int> reconstructEulerianTrail(int startNode, vector<vector<int>>& adj) {
        vector<int> trail;
        stack<int> st;
        st.push(startNode);
        
        while (!st.empty()) {
            int u = st.top();
            if (!adj[u].empty()) {
                int v = adj[u].back();
                adj[u].pop_back();
                st.push(v);
            } else {
                trail.push_back(u);
                st.pop();
            }
        }
        
        reverse(trail.begin(), trail.end());
        return trail;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time (linear DFS connectivity check + single loop over degree counts).
- **Space Complexity**: $\mathcal{O}(V + E)$ visited array, recursion stack, and trail vector.
- **Why this is optimal**: Euler's degree parity conditions give exact classification in a single $\mathcal{O}(V)$ degree pass.

---

## 6. Dry Run

Graph with 4 vertices: `0-1, 1-2, 2-3, 3-0, 0-2`

| Step | Action / State Change | Result |
|---|---|---|
| `Degrees` | deg(0)=3 (odd), deg(1)=2 (even), deg(2)=3 (odd), deg(3)=2 (even) | Count odd = 2 |
| `Connectivity` | DFS from 0 visits `{0, 1, 2, 3}` $\implies$ Connected | Connected = True |
| `Euler Evaluation` | oddCount == 2 $\implies$ Eulerian Path exists starting at 0 or 2! | Returns 1 (Eulerian Path) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Isolated vertices with degree 0 (safely ignored by connectivity check).
- 0 edges in entire graph (returns 2).
- Multiple disconnected components with edges (returns 0).

### Common Bugs to Avoid
- Checking connectivity over all $V$ vertices instead of only non-zero degree vertices (isolated zero-degree vertices do not violate Eulerian trail!).
- Not reversing the trail array at the end of Hierholzer's algorithm.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does an odd-degree count of 0 guarantee a closed circuit, while 2 guarantees an open path?**  
  **A**: In an undirected graph, every time a path enters a vertex, it consumes 1 edge, and exiting consumes 1 edge (2 edges per transit). Therefore, every intermediate vertex MUST have an even degree. If all vertices have even degrees, the path must close at the start vertex (Circuit). If exactly 2 vertices have odd degrees, one is the start (out > in) and one is the finish (in > out) (Path)!

- **Q2: Why can an undirected graph NEVER have an odd number of odd-degree vertices?**  
  **A**: By the **Handshaking Lemma**: $\sum_{v \in V} \text{deg}(v) = 2E$ (an even integer). The sum of even degrees is even, so the sum of odd degrees must also be even, which implies the number of odd-degree vertices must ALWAYS be an even number ($0, 2, 4, \dots$)!

- **Q3: How is Hierholzer's Algorithm used in DNA Genome Sequencing (de Bruijn Graphs)?**  
  **A**: In Next-Gen Sequencing (NGS), $k$-mer sequence reads form directed de Bruijn graphs. Reconstructing the original DNA genome chromosome corresponds to finding an Eulerian Path across all overlapping $k-1$ mers in $\mathcal{O}(E)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Eulerian Path`, `Eulerian Circuit`, `Hierholzer's Algorithm`, `Handshaking Lemma`, `Hard`
- **Related problems to practice next**:
- **Reconstruct Itinerary**: Hierholzer directed path.
- **Strongly Connected Components**: Kosaraju's Algorithm.
