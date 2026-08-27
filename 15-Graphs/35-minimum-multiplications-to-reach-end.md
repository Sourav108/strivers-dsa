# Minimum Multiplications to Reach End (Modulo 100,000 BFS) (Step 15.4 — Shortest Path Algorithms)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Minimum Multiplications to Reach End (Modulo 100,000 BFS)](https://takeuforward.org/data-structure/g-39-minimum-multiplications-to-reach-end/)
- **Difficulty**: Medium
- **Statement**: Given `start`, `end`, and an array `arr` of integers, find the minimum number of multiplications required to reach `end` from `start` by multiplying `start` with elements in `arr` modulo $100000$ ($10^5$). If it is impossible to reach `end`, return -1.

---

## 1. Problem, Restated

Find the shortest path in a finite directed state space graph of size $V = 100,000$ where edges represent $(u \times arr[i]) \pmod{100000}$ with unit weight 1, using FIFO Queue BFS in $\mathcal{O}(10^5 \times N)$ time.

- **Input**: `vector<int>& arr`, `int start, int end`.
- **Output**: Integer representing minimum multiplications to reach `end` (or -1).
- **Constraints**: $1 \le \text{arr.length} \le 10^4$, $1 \le \text{arr}[i] \le 10^4$, $0 \le \text{start, end} < 10^5$.

---

## 2. Intuition & Pattern

Every multiplication by an element in `arr` modulo $100000$ maps a number in $[0, 99999]$ to another number in $[0, 99999]$. This creates a finite directed graph of exactly **$100,000$ vertices** where every edge transition costs exactly 1 multiplication step! 1) If `start == end`, return 0. 2) Initialize `dist(100000, 1e9)` with `dist[start] = 0`. 3) Queue stores `{node, steps}`. Push `{start, 0}`. 4) Pop `{u, steps}`. For each multiplier $x$ in `arr`: calculate `num = (u * x) % 100000`. If `steps + 1 < dist[num]`: update `dist[num] = steps + 1`. If `num == end`, return `steps + 1` immediately! Push `{num, steps + 1}` to queue. 5) If queue becomes empty, return -1. Runs in $\mathcal{O}(100000 \times N)$ time.

- **Underlying Pattern**: `Finite Modulo State Space Graph with Unit-Weight FIFO Queue BFS`.

---

## 3. Approach 1 — Naive / DFS Backtracking

### Idea
DFS backtracking with recursion depth limits (can loop infinitely on cyclic modulo states).

### C++17 Code
```cpp
// Naive DFS cycle risk
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\text{Exponential})$.
- **Space Complexity**: $\mathcal{O}(10^5)$ stack.
- **Why it's not good enough**: Modulo multiplication contains dense cycles, causing DFS to loop or explore redundant long paths.

---

## 4. Approach 2 — Better (Dijkstra Algorithm)

### Idea
Dijkstra Algorithm with Priority Queue in O(10^5 x N x log(10^5)) time.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class SolutionDijkstra {
public:
    int minimumMultiplications(vector<int>& arr, int start, int end) {
        if (start == end) return 0;
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;
        vector<int> dist(100000, 1e9);
        dist[start] = 0;
        pq.push({0, start});
        while (!pq.empty()) {
            auto [steps, node] = pq.top(); pq.pop();
            if (node == end) return steps;
            if (steps > dist[node]) continue;
            for (int x : arr) {
                int num = (1LL * node * x) % 100000;
                if (steps + 1 < dist[num]) {
                    dist[num] = steps + 1;
                    pq.push({steps + 1, num});
                }
            }
        }
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(10^5 \cdot N \log(10^5))$ time.
- **Space Complexity**: $\mathcal{O}(10^5)$ space.
- **Why it's still not optimal**: All edge weights are 1, so priority queue sorting is completely unnecessary overhead.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Modulo State Space BFS)

### Idea
Standard FIFO Queue BFS in $\mathcal{O}(10^5 \times N)$ time and $\mathcal{O}(10^5)$ space.

### C++17 Code
```cpp
#include <vector>
#include <queue>
using namespace std;

class Solution {
public:
    int minimumMultiplications(vector<int>& arr, int start, int end) {
        // Base case
        if (start == end) return 0;
        
        const int MOD = 100000;
        
        // Queue stores {current_number, steps}
        queue<pair<int, int>> q;
        q.push({start, 0});
        
        vector<int> dist(MOD, 1e9);
        dist[start] = 0;
        
        while (!q.empty()) {
            auto [node, steps] = q.front();
            q.pop();
            
            for (int x : arr) {
                int num = (1LL * node * x) % MOD;
                
                // Relaxation step
                if (steps + 1 < dist[num]) {
                    dist[num] = steps + 1;
                    
                    // Early exit when target is discovered
                    if (num == end) {
                        return steps + 1;
                    }
                    
                    q.push({num, steps + 1});
                }
            }
        }
        
        return -1; // End is unreachable
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(10^5 \times N)$ time (where $N = \text{arr.size()}$, exactly $10^5$ distinct vertices).
- **Space Complexity**: $\mathcal{O}(10^5)$ distance vector and queue space.
- **Why this is optimal**: Unit edge weights guarantee the first level-order discovery of `end` is optimal without heap overhead.

---

## 6. Dry Run

`arr = [2, 5, 7]`, `start = 3`, `end = 30`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | dist[3]=0 | q: `[{3, 0}]` |
| `Pop {3, 0}` | num: $(3 \times 2)\%10^5=6$ (d=1); $(3 \times 5)=15$ (d=1); $(3 \times 7)=21$ (d=1) | q: `[{6,1},{15,1},{21,1}]` |
| `Pop {6, 1}` | num: $(6 \times 2)=12$; $(6 \times 5)=30$ == END! $\implies$ Return steps = 2! | Reached in 2 steps: $3 \times 2 \times 5 = 30$ ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `start == end` (returns 0).
- `arr` contains 1 (multiplication by 1 is a self-loop, handled by `dist` array).
- `end` unreachable (returns -1).

### Common Bugs to Avoid
- Using integer without modulo `1LL * node * x % 100000` (can overflow 32-bit `int` intermediate product before `%`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the state space strictly bounded by 100,000 vertices?**  
  **A**: Because the modulo operator `% 100000` restricts all possible numerical values to integers strictly in the range $[0, 99999]$. No matter how many multiplications are performed, there can only ever be $100,000$ unique states!

- **Q2: Why is BFS strictly preferred over Dijkstra for this problem?**  
  **A**: Because each multiplication counts as 1 step (all edge weights $= 1$). A FIFO queue processes states in strictly increasing order of steps ($0, 1, 2, \dots$), completely removing the $\mathcal{O}(\log V)$ overhead of Dijkstra!

- **Q3: What is the maximum possible number of edges in this graph?**  
  **A**: With $|\text{arr}| = N$, each of the $100,000$ vertices has at most $N$ outgoing edges $\implies E \le 10^5 \times N$. For $N = 10$, $E \le 10^6$, which executes in less than 15ms in C++!


---

## 9. Tags & Related Problems

- **Tags**: `Graph`, `BFS`, `Modulo Arithmetic`, `State Space Search`, `Medium`
- **Related problems to practice next**:
- **Shortest Path Unit Weights**: Base unweighted BFS.
- **Word Ladder I**: Implicit graph BFS.
