# Course Schedule I (Prerequisites cycle check) (Step 15.3 — Topological Sort and Kahn's Algorithm)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Course Schedule I (Prerequisites cycle check)](https://takeuforward.org/data-structure/course-schedule-i-and-ii/)
- **Difficulty**: Medium
- **Statement**: There are a total of `numCourses` courses you have to take, labeled from 0 to `numCourses - 1`. You are given an array `prerequisites` where `prerequisites[i] = [a_i, b_i]` indicates that you must take course `b_i` first if you want to take course `a_i` ($b_i \to a_i$). Return `true` if you can finish all courses, or `false` otherwise.

---

## 1. Problem, Restated

Determine if the directed course dependency graph is a Directed Acyclic Graph (DAG) by running Kahn's BFS algorithm: return `true` iff `processedCourses == numCourses`.

- **Input**: `int numCourses`, `vector<vector<int>>& prerequisites`.
- **Output**: Boolean `true` if all courses can be finished, `false` otherwise.
- **Constraints**: $1 \le \text{numCourses} \le 2000$, $0 \le \text{prerequisites.length} \le 5000$.

---

## 2. Intuition & Pattern

Taking a course requires all its prerequisites to be taken first. Circular prerequisite dependencies (e.g. course 0 needs 1, course 1 needs 0) make it impossible to finish all courses! 1) Construct directed adjacency list: for each pair `[a, b]`, add directed edge `b -> a` and increment `inDegree[a]++`. 2) Push all courses with `inDegree[i] == 0` (courses with zero prerequisites) into `queue<int> q`. 3) While queue is not empty: pop course, increment `completedCount++`, and decrement `inDegree[nextCourse]--`. If `inDegree[nextCourse] == 0`, push to queue. 4) Return `completedCount == numCourses`. Runs in $\mathcal{O}(V + E)$ time.

- **Underlying Pattern**: `Directed Graph Cycle Detection via Kahn's In-Degree BFS ($b \to a$)`.

---

## 3. Approach 1 — Naive / DFS 3-Coloring Cycle Check

### Idea
DFS with 3-color recursion stack arrays.

### C++17 Code
```cpp
// DFS 3-color cycle check alternative
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time.
- **Space Complexity**: $\mathcal{O}(V)$ stack.
- **Why it's not good enough**: BFS Kahn's algorithm avoids recursion stack depth overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard Kahn's In-Degree BFS algorithm below directly achieves optimal $\mathcal{O}(V + E)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Kahn's In-Degree BFS in $\mathcal{O}(V + E)$ time and $\mathcal{O}(V + E)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
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
        
        int completedCount = 0;
        
        while (!q.empty()) {
            int curr = q.front();
            q.pop();
            completedCount++;
            
            for (int nextCourse : adj[curr]) {
                inDegree[nextCourse]--;
                if (inDegree[nextCourse] == 0) {
                    q.push(nextCourse);
                }
            }
        }
        
        // If all courses were completed without deadlocks
        return completedCount == numCourses;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(V + E)$ time (where $V = \text{numCourses}$ and $E = \text{prerequisites.size()}$).
- **Space Complexity**: $\mathcal{O}(V + E)$ adjacency list, in-degree array, and queue space.
- **Why this is optimal**: In-degree elimination dynamically verifies dependency satisfaction in optimal linear time.

---

## 6. Dry Run

`numCourses = 2`, `prerequisites = [[1, 0]]` (0 -> 1) vs `prerequisites = [[1, 0], [0, 1]]` (Cycle 0 <-> 1)

| Step | Action / State Change | Result |
|---|---|---|
| `Case 1: [[1, 0]]` | inDeg: `0:0, 1:1` -> push 0 -> pop 0, inDeg[1]=0 -> push 1 -> pop 1 -> count = 2 == 2 | Return TRUE ✅ (Possible: `0 -> 1`) |
| `Case 2: [[1,0],[0,1]]` | inDeg: `0:1, 1:1` -> Queue starts empty -> count = 0 != 2 | Return FALSE ❌ (Deadlock Cycle) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `prerequisites = []` (no prerequisites $\implies$ all courses takeable $\implies$ returns `true`).
- Disconnected course subgraphs.

### Common Bugs to Avoid
- Creating edge in wrong direction `a -> b` instead of `b -> a` (inverts prerequisite semantics).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the edge direction b -> a and NOT a -> b?**  
  **A**: Because `[a, b]` means $b$ must be completed BEFORE $a$. Therefore, the flow of dependency is $b \to a$. Once $b$ is completed, it enables course $a$ to decrement its prerequisite counter!

- **Q2: How does Course Schedule II (LeetCode 210) extend this problem?**  
  **A**: Course Schedule II requires returning the ACTUAL ORDER of courses. Simply append `curr` to `vector<int> order` each time it is popped from the queue. If `order.size() == numCourses`, return `order`; else return `{}`!

- **Q3: How are multi-threaded build systems (e.g. Bazel/Make) modeled after this?**  
  **A**: Target build artifacts are nodes, dependency includes are edges. Worker threads consume zero-in-degree jobs from a concurrent work queue in parallel.


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Topological Sort`, `Kahn's Algorithm`, `DAG`, `LeetCode-207`, `Medium`
- **Related problems to practice next**:
- **Course Schedule II**: Return valid schedule order.
- **Detect Cycle in Directed Graph BFS**: Underlying pattern.
