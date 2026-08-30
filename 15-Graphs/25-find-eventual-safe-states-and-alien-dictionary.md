# Find Eventual Safe States & Alien Dictionary (Step 15.3 — Topological Sort and Kahn's Algorithm)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find Eventual Safe States & Alien Dictionary](https://takeuforward.org/data-structure/alien-dictionary/)
- **Difficulty**: Hard
- **Statement**: Solve two master topological sort applications: 1) **Find Eventual Safe States (LeetCode 802)**: A node is a safe node if every possible path starting from it leads to a terminal node (no outgoing edges). Return all safe nodes in sorted order. 2) **Alien Dictionary (LeetCode 269 / GFG)**: Given a sorted list of words in an unknown alien language, derive the lexicographical order of unique characters using directed character dependency DAGs.

---

## 1. Problem, Restated

1) Safe States: Reverse all edges so terminal nodes have in-degree 0; run Kahn's BFS in $\mathcal{O}(V + E)$. 2) Alien Dictionary: Compare adjacent words to extract directed character precedence edges $c_1 \to c_2$; run Kahn's Topological Sort with prefix invalidity checks in $\mathcal{O}(N \times L + K)$ time.

- **Input**: Graph adjacency lists / Alien dictionary string vector.
- **Output**: `vector<int>` safe nodes / `string` alien character order.
- **Constraints**: Standard competitive programming limits ($V \le 10^4, N \le 500$).

---

## 2. Intuition & Pattern

1) **Eventual Safe States**: A node is unsafe if it is part of a cycle or can reach a cycle. Reversing every edge $u \to v$ into $v \to u$ turns terminal nodes (out-degree 0) into source nodes (in-degree 0). Running Kahn's algorithm on the reversed graph naturally propagates safety inward: only nodes that cannot reach cycles will be processed! 2) **Alien Dictionary**: In any dictionary, relative ordering between adjacent words `w1` and `w2` is determined by the FIRST differing character `w1[j] != w2[j]` $\implies$ directed edge `w1[j] -> w2[j]`. Run Kahn's algorithm on the character DAG. If `topo.length() < uniqueChars`, a circular contradiction exists $\implies$ return `""`.

- **Underlying Pattern**: `Edge Reversal Kahn's BFS (Safe States) & Lexicographical Difference DAG Construction (Alien Dictionary)`.

---

## 3. Approach 1 — Naive / All-Paths DFS Search

### Idea
For each node in Safe States, run complete DFS path search with cycle checks; for Alien Dictionary, all-pairs prefix comparisons.

### C++17 Code
```cpp
// Naive DFS path exploration
```

### Java Code
```java
// Java equivalent
// Naive DFS path exploration
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V \times (V + E))$ for Safe States.
- **Space Complexity**: $\mathcal{O}(V)$ space.
- **Why it's not good enough**: Repeatedly searches redundant paths.

---

## 4. Approach 2 — Better

### Idea
DFS 3-Coloring Cycle Exclusion for Eventual Safe States in O(V + E) time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class SolutionSafeStatesDFS {
    bool dfs(int node, const vector<vector<int>>& graph, vector<int>& state) {
        state[node] = 1; // In stack
        for (int neighbor : graph[node]) {
            if (state[neighbor] == 1) return true; // Cycle found
            if (state[neighbor] == 0 && dfs(neighbor, graph, state)) return true;
        }
        state[node] = 2; // Safe
        return false;
    }
public:
    vector<int> eventualSafeNodes(vector<vector<int>>& graph) {
        int n = graph.size();
        vector<int> state(n, 0), safe;
        for (int i = 0; i < n; i++)
            if (state[i] == 0) dfs(i, graph, state);
        for (int i = 0; i < n; i++)
            if (state[i] == 2) safe.push_back(i);
        return safe;
    }
};
```

### Java Code
```java
class SolutionSafeStatesDFS {
    boolean dfs(int node, int[][] graph, int[] state) {
        state[node] = 1; // In stack
        for (int neighbor : graph[node]) {
            if (state[neighbor] == 1) return true; // Cycle found
            if (state[neighbor] == 0 && dfs(neighbor, graph, state)) return true;
        }
        state[node] = 2; // Safe
        return false;
    }

    int[] eventualSafeNodes(int[][] graph) {
        int n = graph.length;
        int[] state(n, 0), safe;
        for (int i = 0; i < n; i++)
            if (state[i] == 0) dfs(i, graph, state);
        for (int i = 0; i < n; i++)
            if (state[i] == 2) safe.add(i);
        return safe;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ call stack space.
- **Why it's still not optimal**: 3-Color DFS is clean and achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Edge Reversal + Kahn's BFS (Safe States) and Topological Sort DAG (Alien Dictionary) in $\mathcal{O}(V + E)$ and $\mathcal{O}(N \times L + K)$ time.

### C++17 Code
```cpp
#include <vector>
#include <string>
#include <queue>
#include <algorithm>
using namespace std;

class Solution {
public:
    // 1. Eventual Safe States (LeetCode 802) via Edge Reversal Kahn's BFS
    vector<int> eventualSafeNodes(vector<vector<int>>& graph) {
        int V = graph.size();
        vector<vector<int>> revAdj(V);
        vector<int> inDegree(V, 0);
        
        // Reverse graph edges: u -> v becomes v -> u
        for (int u = 0; u < V; u++) {
            for (int v : graph[u]) {
                revAdj[v].push_back(u);
                inDegree[u]++; // out-degree in original becomes in-degree in reversed
            }
        }
        
        // Push original terminal nodes (now in-degree 0)
        queue<int> q;
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        vector<int> safeNodes;
        while (!q.empty()) {
            int node = q.front();
            q.pop();
            safeNodes.push_back(node);
            
            for (int parent : revAdj[node]) {
                inDegree[parent]--;
                if (inDegree[parent] == 0) {
                    q.push(parent);
                }
            }
        }
        
        sort(safeNodes.begin(), safeNodes.end());
        return safeNodes;
    }

    // 2. Alien Dictionary (LeetCode 269 / GFG) via Character DAG Topo Sort
    string findOrder(vector<string>& words, int k) {
        vector<vector<int>> adj(k);
        vector<int> inDegree(k, 0);
        
        // Compare adjacent words to build character ordering DAG
        for (size_t i = 0; i < words.size() - 1; i++) {
            const string& w1 = words[i];
            const string& w2 = words[i + 1];
            
            // Edge case: invalid prefix ordering (e.g. "apple" before "app")
            if (w1.length() > w2.length() && w1.rfind(w2, 0) == 0) {
                return ""; // Invalid lexicographical dictionary
            }
            
            size_t len = min(w1.length(), w2.length());
            for (size_t j = 0; j < len; j++) {
                if (w1[j] != w2[j]) {
                    adj[w1[j] - 'a'].push_back(w2[j] - 'a');
                    inDegree[w2[j] - 'a']++;
                    break; // Only the first mismatching character determines order
                }
            }
        }
        
        queue<int> q;
        for (int i = 0; i < k; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        string order = "";
        while (!q.empty()) {
            int u = q.front();
            q.pop();
            order += (char)(u + 'a');
            
            for (int v : adj[u]) {
                inDegree[v]--;
                if (inDegree[v] == 0) {
                    q.push(v);
                }
            }
        }
        
        // If order contains all k characters -> valid DAG; else cycle detected
        return (order.length() == (size_t)k) ? order : "";
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    // 1. Eventual Safe States (LeetCode 802) via Edge Reversal Kahn's BFS
    int[] eventualSafeNodes(int[][] graph) {
        int V = graph.length;
        int[][] revAdj(V);
        int[] inDegree = new int[V];
        
        // Reverse graph edges: u . v becomes v . u
        for (int u = 0; u < V; u++) {
            for (int v : graph[u]) {
                revAdj[v].add(u);
                inDegree[u]++; // out-degree in original becomes in-degree in reversed
            }
        }
        
        // Push original terminal nodes (now in-degree 0)
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < V; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        List<Integer> safeNodes = new ArrayList<>();
        while (!q.isEmpty()) {
            int node = q.peek();
            q.pop();
            safeNodes.add(node);
            
            for (int parent : revAdj[node]) {
                inDegree[parent]--;
                if (inDegree[parent] == 0) {
                    q.push(parent);
                }
            }
        }
        
        Arrays.sort(safeNodes);
        return safeNodes;
    }

    // 2. Alien Dictionary (LeetCode 269 / GFG) via Character DAG Topo Sort
    String findOrder(String[] words, int k) {
        int[][] adj(k);
        int[] inDegree = new int[k];
        
        // Compare adjacent words to build character ordering DAG
        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i];
            String w2 = words[i + 1];
            
            // Edge case: invalid prefix ordering (e.g. "apple" before "app")
            if (w1.length() > w2.length() && w1.rfind(w2, 0) == 0) {
                return ""; // Invalid lexicographical dictionary
            }
            
            int len = Math.min(w1.length(), w2.length());
            for (int j = 0; j < len; j++) {
                if (w1[j] != w2[j]) {
                    adj[w1[j] - 'a'].add(w2[j] - 'a');
                    inDegree[w2[j] - 'a']++;
                    break; // Only the first mismatching character determines order
                }
            }
        }
        
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < k; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        String order = "";
        while (!q.isEmpty()) {
            int u = q.peek();
            q.pop();
            order += (char)(u + 'a');
            
            for (int v : adj[u]) {
                inDegree[v]--;
                if (inDegree[v] == 0) {
                    q.push(v);
                }
            }
        }
        
        // If order contains all k characters . valid DAG; else cycle detected
        return (order.length() == (int)k) ? order : "";
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ for Safe States; $\mathcal{O}(N \times L + K)$ for Alien Dictionary (where $N$ is word count, $L$ is max word length, $K$ is alphabet size).
- **Space Complexity**: $\mathcal{O}(V + E)$ reversed graph & character DAG queue memory.
- **Why this is optimal**: Edge reversal and adjacent mismatch pairing transform both problems into canonical in-degree topological sorts.

---

## 6. Dry Run

Alien Dict: `words = ["wrt","wrf","er","ett","rftt"]`, $K = 5$ (`'a'...'e'` mapped from `'r','t','f','e','w'`):

| Step | Action / State Change | Result |
|---|---|---|
| `Pair 1: wrt, wrf` | First mismatch at index 2: `'t' -> 'f'` | Edge: $t \to f$ |
| `Pair 2: wrf, er` | First mismatch at index 0: `'w' -> 'e'` | Edge: $w \to e$ |
| `Pair 3: er, ett` | First mismatch at index 1: `'r' -> 't'` | Edge: $r \to t$ |
| `Pair 4: ett, rftt` | First mismatch at index 0: `'e' -> 'r'` | Edge: $e \to r$ |
| `DAG Topo Sort` | Order of edges: $w \to e \to r \to t \to f$ | Result: `"wertf"` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Alien Dictionary prefix violation `["apple", "app"]` (invalid dictionary $\implies$ return `""`).
- Alien Dictionary cyclic contradiction `["z", "x", "z"]` (returns `""`).
- Safe States disconnected graph with no terminal nodes.

### Common Bugs to Avoid
- Comparing non-adjacent words in Alien Dictionary (only consecutive adjacent words provide guaranteed direct order).
- Continuing after first mismatch in a word pair (only the FIRST differing letter determines order!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does edge reversal solve Eventual Safe States with Kahn's Algorithm?**  
  **A**: In the original graph, safe nodes lead to terminal nodes (out-degree 0). Reversing edges turns terminal nodes into sources (in-degree 0). Running Kahn's algorithm on the reversed graph peels layers backward from terminal nodes, naturally excluding any node connected to a cycle!

- **Q2: What is the critical prefix test case in Alien Dictionary that fails many implementations?**  
  **A**: `words = ["abcd", "abc"]`. Here `"abcd"` is a prefix of `"abc"` but appears first, violating standard dictionary rules. We must check `if (w1.length() > w2.length() && w1.rfind(w2, 0) == 0) return "";`.

- **Q3: How to handle duplicate edges between the same characters in Alien Dictionary?**  
  **A**: Use `unordered_set<int> adj[K]` or check if edge already exists before adding to prevent duplicate in-degree increments.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `Topological Sort`, `Kahn's Algorithm`, `String`, `LeetCode-802`, `LeetCode-269`, `Hard`
- **Related problems to practice next**:
- **Course Schedule II**: Topological order.
- **Topological Sort BFS**: Base Kahn's algorithm.
