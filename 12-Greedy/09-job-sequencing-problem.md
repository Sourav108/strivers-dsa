# Job Sequencing Problem (Max Profit with Deadlines) (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Job Sequencing Problem (Max Profit with Deadlines)](https://takeuforward.org/data-structure/job-sequencing-problem/)
- **Difficulty**: Medium
- **Statement**: Given a set of $N$ jobs where each job $i$ has a deadline $deadline[i]$ and profit $profit[i]$ (each job takes 1 unit of time), find the number of jobs done and the maximum profit that can be earned.

---

## 1. Problem, Restated

Sort jobs by profit descending, greedily schedule each job on the latest available free time slot $\le deadline$.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Sort jobs in descending order of `profit`. 2) Find max deadline $M$. Create a schedule slot array `slot[M + 1]` initialized to -1. 3) For each job: search for the latest free slot from `job.deadline` down to 1. If slot $k$ is free, assign `slot[k] = job.id`, add profit, increment count. 4) **Optimization**: Use Disjoint Set Union (DSU) to find the latest available slot in $\mathcal{O}(\alpha(M)) \approx \mathcal{O}(1)$ time!

- **Underlying Pattern**: `Greedy Profit Sorting + Disjoint Set Union (DSU) Slot Allocation`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Linear backwards scan for every job taking $\mathcal{O}(N \times M)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;
struct Job { int id, dead, profit; };
vector<int> JobSchedulingLinear(Job arr[], int n) {
    sort(arr, arr + n, [](Job a, Job b) { return a.profit > b.profit; });
    int maxDead = 0;
    for (int i = 0; i < n; i++) maxDead = max(maxDead, arr[i].dead);
    vector<int> slot(maxDead + 1, -1);
    int count = 0, totalProfit = 0;
    for (int i = 0; i < n; i++) {
        for (int j = arr[i].dead; j > 0; j--) {
            if (slot[j] == -1) { slot[j] = arr[i].id; count++; totalProfit += arr[i].profit; break; }
        }
    }
    return {count, totalProfit};
}
```

### Java Code
```java
static class Job { int id, dead, profit; };
int[] JobSchedulingLinear(Job arr[], int n) {
    sort(arr, arr + n, [](Job a, Job b) { return a.profit > b.profit; });
    int maxDead = 0;
    for (int i = 0; i < n; i++) maxDead = Math.max(maxDead, arr[i].dead);
    int[] slot = new int[maxDead + 1];
    int count = 0, totalProfit = 0;
    for (int i = 0; i < n; i++) {
        for (int j = arr[i].dead; j > 0; j--) {
            if (slot[j] == -1) { slot[j] = arr[i].id; count++; totalProfit += arr[i].profit; break; }
        }
    }
    return {count, totalProfit};
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N + N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(M)$ slot space.
- **Why it's not good enough**: Linear slot scanning.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
DSU Path Compression for $\mathcal{O}(N \log N + N \alpha(M))$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

struct Job { 
    int id;
    int dead;
    int profit;
};

class DSU {
public:
    vector<int> parent;
    DSU(int n) : parent(n + 1) {
        iota(parent.begin(), parent.end(), 0);
    }
    
    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]); // path compression
    }
    
    void unite(int u, int v) {
        parent[u] = v; // point occupied slot u to available slot v
    }
};

class Solution {
public:
    vector<int> JobScheduling(Job arr[], int n) {
        sort(arr, arr + n, [](const Job& a, const Job& b) {
            return a.profit > b.profit; // sort descending profit
        });
        
        int maxDeadline = 0;
        for (int i = 0; i < n; i++) {
            maxDeadline = max(maxDeadline, arr[i].dead);
        }
        
        DSU dsu(maxDeadline);
        int jobCount = 0, maxProfit = 0;
        
        for (int i = 0; i < n; i++) {
            // Find latest available slot <= deadline
            int availableSlot = dsu.find(arr[i].dead);
            
            if (availableSlot > 0) {
                jobCount++;
                maxProfit += arr[i].profit;
                // Mark slot occupied by pointing to next available slot (availableSlot - 1)
                dsu.unite(availableSlot, dsu.find(availableSlot - 1));
            }
        }
        
        return {jobCount, maxProfit};
    }
};
```

### Java Code
```java
static class Job { 
    int id;
    int dead;
    int profit;
};

class DSU {

    int[] parent;
    public DSU(int n) { /* initialized: parent(n + 1)  */ 
        iota(parent.begin(), parent.end(), 0);
     }
    
    int find(int i) {
        if (parent[i] == i) return i;
        return parent[i] = find(parent[i]); // path compression
    }
    
    void unite(int u, int v) {
        parent[u] = v; // point occupied slot u to available slot v
    }
};

class Solution {

    int[] JobScheduling(Job arr[], int n) {
        sort(arr, arr + n, [](Job a, Job b) {
            return a.profit > b.profit; // sort descending profit
        });
        
        int maxDeadline = 0;
        for (int i = 0; i < n; i++) {
            maxDeadline = Math.max(maxDeadline, arr[i].dead);
        }
        
        DSU dsu(maxDeadline);
        int jobCount = 0, maxProfit = 0;
        
        for (int i = 0; i < n; i++) {
            // Find latest available slot <= deadline
            int availableSlot = dsu.find(arr[i].dead);
            
            if (availableSlot > 0) {
                jobCount++;
                maxProfit += arr[i].profit;
                // Mark slot occupied by pointing to next available slot (availableSlot - 1)
                dsu.unite(availableSlot, dsu.find(availableSlot - 1));
            }
        }
        
        return {jobCount, maxProfit};
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N + N \alpha(M))$ time ($M = \max(\text{deadlines})$).
- **Space Complexity**: $\mathcal{O}(M)$ DSU parent array.
- **Why this is optimal**: DSU path compression finds the next available time slot in near $\mathcal{O}(1)$ amortized time.

---

## 6. Dry Run

Jobs: `[(1,4,20), (2,1,10), (3,1,40), (4,1,30)]`

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted by Profit` | Job 3 (d=1, p=40), Job 4 (d=1, p=30), Job 1 (d=4, p=20), Job 2 (d=1, p=10) | Sorted |
| `Job 3 (d=1, p=40)` | find(1)=1 > 0 -> slot 1 taken, unite(1, 0), profit=40 | count=1, profit=40 |
| `Job 4 (d=1, p=30)` | find(1)=0 -> no slot available -> skip | count=1, profit=40 |
| `Job 1 (d=4, p=20)` | find(4)=4 > 0 -> slot 4 taken, unite(4, 3), profit=40+20=60 | count=2, profit=60 |
| `Result` | Jobs done = 2, Total Profit = 60 | `[2, 60]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All deadlines = 1 (can only execute 1 job with max profit).
- Deadlines larger than $N$.

### Common Bugs to Avoid
- Scheduling job on earliest slot (e.g. slot 1) instead of latest available slot (blocks earlier deadlines).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must a job be scheduled at the LATEST possible slot <= deadline?**  
  **A**: Because scheduling it early (e.g. at time 1) might unnecessarily occupy a slot needed by a job with a tighter deadline of 1. Delaying execution to the last possible moment leaves maximum room for other jobs!

- **Q2: How does DSU achieve near O(1) slot lookup?**  
  **A**: Each node in DSU points to the next available free slot. When slot $k$ is occupied, `unite(k, find(k-1))` points $k$ to the next available slot. Path compression makes subsequent lookups $\mathcal{O}(\alpha(M))$.

- **Q3: What if job execution times vary (e.g. duration > 1)?**  
  **A**: Variable duration with deadlines becomes an NP-hard scheduling problem (solvable via Dynamic Programming / Branch & Bound).


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `DSU`, `Sorting`, `Medium`
- **Related problems to practice next**:
- **N Meetings in One Room**: Activity selection.
