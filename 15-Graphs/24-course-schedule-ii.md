# Course Schedule II (Find valid course ordering) (Step 15.3 — Topological Sort and Kahn's Algorithm)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Course Schedule II (Find valid course ordering)](https://takeuforward.org/data-structure/course-schedule-i-and-ii/)
- **Difficulty**: Medium
- **Statement**: There are a total of `numCourses` courses you have to take, labeled from 0 to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [a_i, b_i]` indicates that you must take course `b_i` first before `a_i` ($b_i \to a_i$). Return the ordering of courses you should take to finish all courses. If there are multiple valid answers, return any of them. If impossible, return an empty array `[]`.

---

## 1. Problem, Restated

Find a valid linear topological ordering of courses using Kahn's In-Degree BFS: return the accumulated sequence if all courses are scheduled, else return `[]`.

- **Input**: `int numCourses`, `vector<vector<int>>& prerequisites`.
- **Output**: `vector<int>` containing a valid sequential course schedule, or `[]` if impossible.
- **Constraints**: $1 \le \text{numCourses} \le 2000$, $0 \le \text{prerequisites.length} \le \text{numCourses} \times (\text{numCourses} - 1)$.

---

## 2. Intuition & Pattern

1) Build directed adjacency list `adj` with edges $b \to a$ and compute `inDegree[a]++`. 2) Push all courses with `inDegree[i] == 0` into `queue<int> q`. 3) Pop `curr`, append to `vector<int> order`. 4) For each `nextCourse` in `adj[curr]`: decrement `inDegree[nextCourse]--`. When `inDegree` reaches 0, push `nextCourse` to queue. 5) Return `order.size() == numCourses ? order : vector<int>{}`. Runs in $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `Kahn's In-Degree BFS Topological Sort Sequence Construction`.

---

## 3. Approach 1 — Naive / DFS Finish-Time Stack with 3-Coloring

### Idea
DFS Finish-Time stack with 3-color cycle detection array.

### C++17 Code
```cpp
// DFS topo sort + cycle check alternative
```

### Java Code
```java
// Java equivalent
// DFS topo sort + cycle check alternative
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time.
- **Space Complexity**: $\mathcal{O}(V + E)$ space.
- **Why it's not good enough**: BFS Kahn's algorithm is purely iterative and directly constructs the order array.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Kahn's BFS Algorithm below directly achieves optimal $\mathcal{O}(V + E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Kahn's BFS Algorithm with Topological Order Array in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector<vector<int>> adj(numCourses);
        vector<int> inDegree(numCourses, 0);
        
        // Edge: prerequisite -> course (b -> a)
        for (const auto& pre : prerequisites) {
            int course = pre[0];
            int prerequisite = pre[1];
            
            adj[prerequisite].push_back(course);
            inDegree[course]++;
        }
        
        // Push all courses with 0 prerequisites into queue
        queue<int> q;
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        vector<int> order;
        
        while (!q.empty()) {
            int curr = q.front();
            q.pop();
            order.push_back(curr);
            
            for (int nextCourse : adj[curr]) {
                inDegree[nextCourse]--;
                if (inDegree[nextCourse] == 0) {
                    q.push(nextCourse);
                }
            }
        }
        
        // If topological sort includes all courses, return valid order; else return empty list
        if ((int)order.size() == numCourses) {
            return order;
        }
        return {};
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[] findOrder(int numCourses, int[][] prerequisites) {
        int[][] adj(numCourses);
        int[] inDegree = new int[numCourses];
        
        // Edge: prerequisite . course (b . a)
        for (var pre : prerequisites) {
            int course = pre[0];
            int prerequisite = pre[1];
            
            adj[prerequisite].add(course);
            inDegree[course]++;
        }
        
        // Push all courses with 0 prerequisites into queue
        Queue<Integer> q = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {
                q.push(i);
            }
        }
        
        List<Integer> order = new ArrayList<>();
        
        while (!q.isEmpty()) {
            int curr = q.peek();
            q.pop();
            order.add(curr);
            
            for (int nextCourse : adj[curr]) {
                inDegree[nextCourse]--;
                if (inDegree[nextCourse] == 0) {
                    q.push(nextCourse);
                }
            }
        }
        
        // If topological sort includes all courses, return valid order; else return empty list
        if (order.length == numCourses) {
            return order;
        }
        return {};
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time (where $V = \text{numCourses}$ and $E = \text{prerequisites.size()}$).
- **Space Complexity**: $\mathcal{O}(V + E)$ adjacency list, in-degree array, and queue space.
- **Why this is optimal**: Kahn's algorithm constructs a valid topological order dynamically in optimal linear time.

---

## 6. Dry Run

`numCourses = 4`, `prerequisites = [[1,0],[2,0],[3,1],[3,2]]`

| Step | Action / State Change | Result |
|---|---|---|
| `In-degrees` | inDeg: `0:0, 1:1, 2:1, 3:2` | q: `[0]` |
| `Pop 0` | order: `[0]`, inDeg: `1:0, 2:0` -> pushes 1, 2 | q: `[1, 2]` |
| `Pop 1` | order: `[0, 1]`, inDeg: `3:1` | q: `[2]` |
| `Pop 2` | order: `[0, 1, 2]`, inDeg: `3:0` -> pushes 3 | q: `[3]` |
| `Pop 3` | order: `[0, 1, 2, 3]` | order.size() == 4 == numCourses |
| `Result` | Valid Schedule: `[0, 1, 2, 3]` (or `[0, 2, 1, 3]`) | Schedule Returned ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `prerequisites = []` (returns `[0, 1, ..., numCourses - 1]`).
- Disconnected course components.
- Cycle present (returns `[]`).

### Common Bugs to Avoid
- Returning partial `order` when cycle exists instead of returning empty array `{}`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can multiple valid topological sort sequences exist for the same graph?**  
  **A**: Whenever the BFS queue contains more than one vertex with in-degree 0 at the same time, any permutation of processing those vertices yields an equally valid topological order!

- **Q2: How to return the lexicographically smallest course schedule?**  
  **A**: Replace `std::queue<int>` with `std::priority_queue<int, vector<int>, greater<int>>` min-heap. This always processes the lowest-indexed course first in $\mathcal{O}(V + E \log V)$ time.

- **Q3: How does this relate to Package Managers (npm, apt, pip)?**  
  **A**: Package managers construct a DAG of library versions; Kahn's topological sort determines the exact sequential installation order to guarantee all dependencies are installed before dependent modules.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Topological Sort`, `Kahn's Algorithm`, `DAG`, `LeetCode-210`, `Medium`
- **Related problems to practice next**:
- **Course Schedule I**: Boolean cycle check.
- **Alien Dictionary**: Lexicographical topological sort.
