# 15 — Graphs

> **Topic Problem Count**: 53 Problems  
> **Language**: C++ (with Intuition, Multi-Tier Approaches, and Complexity Analysis)

## 📌 Overview
BFS/DFS, Topological Sort, Shortest Paths (Dijkstra, Bellman-Ford, Floyd-Warshall), Disjoint Set Union, MST (Kruskal/Prim), Bridges, and Kosaraju SCC.

---

## 📋 Problem Checklist

### Step 15.1: Learning (5 Problems)

- [x] **Problem 01**: [Introduction to Graphs, Degrees, and Types](./01-introduction-to-graphs-types-and-degrees.md) — 🟢 `Easy` (Solved)
- [x] **Problem 02**: [Graph Representation in C++ (Adjacency Matrix & Adjacency List)](./02-graph-representation-adjacency-matrix-and-list.md) — 🟢 `Easy` (Solved)
- [x] **Problem 03**: [Connected Components in Graph](./03-connected-components-in-graph.md) — 🟢 `Easy` (Solved)
- [x] **Problem 04**: [Breadth First Search (BFS) Traversal](./04-bfs-traversal-of-graph.md) — 🟢 `Easy` (Solved)
- [x] **Problem 05**: [Depth First Search (DFS) Traversal](./05-dfs-traversal-of-graph.md) — 🟢 `Easy` (Solved)

### Step 15.2: Problems on BFS / DFS (14 Problems)

- [x] **Problem 06**: [Number of Provinces (Connected Components in Disjoint Graph)](./06-number-of-provinces.md) — 🟡 `Medium` (Solved)
- [x] **Problem 07**: [Rotting Oranges (Multi-source BFS)](./07-rotting-oranges.md) — 🟡 `Medium` (Solved)
- [x] **Problem 08**: [Flood Fill Algorithm](./08-flood-fill-algorithm.md) — 🟢 `Easy` (Solved)
- [x] **Problem 09**: [Detect Cycle in an Undirected Graph using BFS](./09-detect-cycle-in-an-undirected-graph-using-bfs.md) — 🟡 `Medium` (Solved)
- [x] **Problem 10**: [Detect Cycle in an Undirected Graph using DFS](./10-detect-cycle-in-an-undirected-graph-using-dfs.md) — 🟡 `Medium` (Solved)
- [x] **Problem 11**: [0/1 Matrix (Distance of Nearest Cell having 1 / 0)](./11-distance-of-nearest-cell-having-1.md) — 🟡 `Medium` (Solved)
- [x] **Problem 12**: [Surrounded Regions (Replace O's with X's on Board)](./12-surrounded-regions-replace-os-with-xs.md) — 🟡 `Medium` (Solved)
- [x] **Problem 13**: [Number of Enclaves (Count unreachable land cells)](./13-number-of-enclaves.md) — 🟡 `Medium` (Solved)
- [x] **Problem 14**: [Word Ladder I (Shortest transformation sequence length)](./14-word-ladder-i.md) — 🔴 `Hard` (Solved)
- [x] **Problem 15**: [Word Ladder II (Find all shortest transformation sequences)](./15-word-ladder-ii.md) — 🔴 `Hard` (Solved)
- [x] **Problem 16**: [Number of Distinct Islands (DFS with shape serialization)](./16-number-of-distinct-islands.md) — 🟡 `Medium` (Solved)
- [x] **Problem 17**: [Check if Graph is Bipartite (2-Coloring via BFS/DFS)](./17-check-if-graph-is-bipartite.md) — 🟡 `Medium` (Solved)
- [x] **Problem 18**: [Detect Cycle in a Directed Graph using DFS (Recursion Stack)](./18-detect-cycle-in-a-directed-graph-using-dfs.md) — 🟡 `Medium` (Solved)
- [x] **Problem 19**: [Number of Islands (Grid DFS/BFS)](./19-number-of-islands.md) — 🟡 `Medium` (Solved)

### Step 15.3: Topological Sort and Kahn's Algorithm (6 Problems)

- [x] **Problem 20**: [Topological Sort using DFS (Finish time stack)](./20-topological-sort-using-dfs.md) — 🟡 `Medium` (Solved)
- [x] **Problem 21**: [Kahn's Algorithm (Topological Sort using BFS In-Degree)](./21-kahns-algorithm-topological-sort-bfs.md) — 🟡 `Medium` (Solved)
- [x] **Problem 22**: [Detect Cycle in a Directed Graph using BFS (Kahn's Algorithm)](./22-detect-cycle-in-a-directed-graph-using-bfs.md) — 🟡 `Medium` (Solved)
- [x] **Problem 23**: [Course Schedule I (Prerequisites cycle check)](./23-course-schedule-i.md) — 🟡 `Medium` (Solved)
- [x] **Problem 24**: [Course Schedule II (Find valid course ordering)](./24-course-schedule-ii.md) — 🟡 `Medium` (Solved)
- [x] **Problem 25**: [Find Eventual Safe States & Alien Dictionary](./25-find-eventual-safe-states-and-alien-dictionary.md) — 🔴 `Hard` (Solved)

### Step 15.4: Shortest Path Algorithms (13 Problems)

- [x] **Problem 26**: [Shortest Path in Undirected Graph with Unit Weights](./26-shortest-path-in-undirected-graph-with-unit-weights.md) — 🟡 `Medium` (Solved)
- [x] **Problem 27**: [Shortest Path in Directed Acyclic Graph (DAG) using Topo Sort](./27-shortest-path-in-directed-acyclic-graph-dag-using-topo-sort.md) — 🟡 `Medium` (Solved)
- [x] **Problem 28**: [Dijkstra's Algorithm using Priority Queue / Set](./28-dijkstras-algorithm-using-priority-queue-set.md) — 🟡 `Medium` (Solved)
- [ ] **Problem 29**: [Print Shortest Path in Weighted Undirected Graph (Dijkstra Parent array)](https://takeuforward.org/data-structure/g-35-print-shortest-path-dijkstras-algorithm/) — 🟡 `Medium`
- [ ] **Problem 30**: [Shortest Path in Binary Matrix (Maze BFS)](https://takeuforward.org/data-structure/g-36-shortest-distance-in-a-binary-maze/) — 🟡 `Medium`
- [ ] **Problem 31**: [Path with Minimum Effort (Dijkstra on 2D Matrix)](https://takeuforward.org/data-structure/g-37-path-with-minimum-effort/) — 🟡 `Medium`
- [ ] **Problem 32**: [Cheapest Flights Within K Stops](https://takeuforward.org/data-structure/g-38-cheapest-flights-within-k-stops/) — 🟡 `Medium`
- [ ] **Problem 33**: [Network Delay Time](https://takeuforward.org/data-structure/network-delay-time/) — 🟡 `Medium`
- [ ] **Problem 34**: [Number of Ways to Arrive at Destination](https://takeuforward.org/data-structure/g-40-number-of-ways-to-arrive-at-destination/) — 🟡 `Medium`
- [ ] **Problem 35**: [Minimum Multiplications to Reach End (Modulo BFS)](https://takeuforward.org/data-structure/g-39-minimum-multiplications-to-reach-end/) — 🟡 `Medium`
- [ ] **Problem 36**: [Bellman-Ford Algorithm (Negative Weights & Negative Cycle Detection)](https://takeuforward.org/data-structure/bellman-ford-algorithm-g-41/) — 🟡 `Medium`
- [ ] **Problem 37**: [Floyd-Warshall Algorithm (All-Pairs Shortest Path in O(V^3))](https://takeuforward.org/data-structure/floyd-warshall-algorithm-g-42/) — 🟡 `Medium`
- [ ] **Problem 38**: [Find the City with the Smallest Number of Neighbors at a Threshold Distance](https://takeuforward.org/data-structure/find-the-city-with-the-smallest-number-of-neighbors-at-a-threshold-distance-g-43/) — 🟡 `Medium`

### Step 15.5: Minimum Spanning Tree & Disjoint Set Union (10 Problems)

- [ ] **Problem 39**: [Prim's Algorithm for Minimum Spanning Tree (MST)](https://takeuforward.org/data-structure/prims-algorithm-minimum-spanning-tree-c-and-java-g-45/) — 🟡 `Medium`
- [ ] **Problem 40**: [Disjoint Set Union (DSU by Rank & Size with Path Compression)](https://takeuforward.org/data-structure/disjoint-set-union-by-rank-union-by-size-path-compression-g-46/) — 🟡 `Medium`
- [ ] **Problem 41**: [Kruskal's Algorithm for Minimum Spanning Tree (Edge list sort + DSU)](https://takeuforward.org/data-structure/kruskals-algorithm-minimum-spanning-tree-g-47/) — 🟡 `Medium`
- [ ] **Problem 42**: [Number of Operations to Make Network Connected](https://takeuforward.org/data-structure/number-of-operations-to-make-network-connected-dsu-g-49/) — 🟡 `Medium`
- [ ] **Problem 43**: [Most Stones Removed with Same Row or Column](https://takeuforward.org/data-structure/most-stones-removed-with-same-row-or-column-dsu-g-53/) — 🟡 `Medium`
- [ ] **Problem 44**: [Accounts Merge (String identifier grouping with DSU)](https://takeuforward.org/data-structure/accounts-merge-dsu-g-50/) — 🔴 `Hard`
- [ ] **Problem 45**: [Number of Islands II (Online Dynamic Island Grid with DSU)](https://takeuforward.org/data-structure/number-of-islands-ii-online-queries-dsu-g-51/) — 🔴 `Hard`
- [ ] **Problem 46**: [Making a Large Island (Flip at most one 0 to 1 with DSU)](https://takeuforward.org/data-structure/making-a-large-island-dsu-g-52/) — 🔴 `Hard`
- [ ] **Problem 47**: [Swim in Rising Water (Dijkstra / Binary Search + DSU)](https://takeuforward.org/data-structure/swim-in-rising-water/) — 🔴 `Hard`
- [ ] **Problem 48**: [Redundant Connection (Find cycle edge in tree via DSU)](https://takeuforward.org/data-structure/redundant-connection/) — 🟡 `Medium`

### Step 15.6: Other Advanced Graph Algorithms (5 Problems)

- [ ] **Problem 49**: [Bridges in Graph (Tarjan's Algorithm with Time of Insertion & Low Time)](https://takeuforward.org/data-structure/bridges-in-graph-using-tarjans-algorithm-g-55/) — 🔴 `Hard`
- [ ] **Problem 50**: [Articulation Points in Graph (Cut Vertices via Tarjan's Low Array)](https://takeuforward.org/data-structure/articulation-point-in-graph-g-56/) — 🔴 `Hard`
- [ ] **Problem 51**: [Strongly Connected Components (Kosaraju's Algorithm 3-Step)](https://takeuforward.org/data-structure/strongly-connected-components-kosarajus-algorithm-g-54/) — 🔴 `Hard`
- [ ] **Problem 52**: [Eulerian Circuit and Path in Directed & Undirected Graphs](https://takeuforward.org/graph/eulerian-path-and-circuit/) — 🔴 `Hard`
- [ ] **Problem 53**: [Tarjan's Algorithm for Strongly Connected Components in Single Pass](https://takeuforward.org/graph/tarjans-scc-algorithm/) — 🔴 `Hard`

---

## 💡 How to Add Solutions

To add a solution for any problem in this section:
1. Copy the prompt template from [`../AI_PROMPT_TEMPLATE.md`](../AI_PROMPT_TEMPLATE.md).
2. Generate the C++ solution with intuition, brute-force, better, and optimal implementations.
3. Save the solution as `NN-problem-slug.md` inside this folder and tick off the checklist box above!
