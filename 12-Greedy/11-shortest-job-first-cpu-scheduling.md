# Shortest Job First (SJF) CPU Scheduling (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Shortest Job First (SJF) CPU Scheduling](https://takeuforward.org/greedy/shortest-job-first/)
- **Difficulty**: Easy
- **Statement**: Given an array of integers `bt` representing burst times of $N$ processes, calculate the average waiting time of all processes under non-preemptive Shortest Job First (SJF) scheduling.

---

## 1. Problem, Restated

Sort burst times ascending to minimize cumulative waiting time $\sum_{i=0}^{N-1} (N - 1 - i) \times bt[i]$.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

The first scheduled job's burst time is added to the waiting time of all subsequent $(N - 1)$ jobs. The $i^{\text{th}}$ job's burst time is suffered by $(N - 1 - i)$ remaining jobs. To MINIMIZE total waiting time, give the smallest burst times the highest coefficients by sorting `bt` in ascending order! Total waiting time $= \sum (N - 1 - i) \times bt[i]$, average $= \text{total} / N$.

- **Underlying Pattern**: `Greedy Shortest Processing Time Order`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Test all $N!$ scheduling permutations in $\mathcal{O}(N!)$ time.

### C++17 Code
```cpp
// O(N!) permutation search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N!)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Factorial search.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Greedy Ascending Sort in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    long long solve(vector<int>& bt) {
        sort(bt.begin(), bt.end());
        
        long long totalWaitTime = 0;
        long long currentWaitTime = 0;
        int n = bt.size();
        
        for (int i = 0; i < n; i++) {
            totalWaitTime += currentWaitTime;
            currentWaitTime += bt[i];
        }
        
        return totalWaitTime / n; // integer average
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Shortest Processing Time (SPT) rule provably minimizes mean waiting time in queuing theory (Smith's Rule).

---

## 6. Dry Run

`bt = [4, 3, 7, 1, 2]` ($N = 5$)

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted bt` | `[1, 2, 3, 4, 7]` | Sorted |
| `Job 0 (bt=1)` | wait = 0, nextWait = 1 | totalWait = 0 |
| `Job 1 (bt=2)` | wait = 1, nextWait = 3 | totalWait = 1 |
| `Job 2 (bt=3)` | wait = 3, nextWait = 6 | totalWait = 4 |
| `Job 3 (bt=4)` | wait = 6, nextWait = 10 | totalWait = 10 |
| `Job 4 (bt=7)` | wait = 10 | totalWait = 20 |
| `Average` | $20 / 5 = 4$ | Average Wait Time = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (wait time is 0).
- 64-bit integer overflow on large sums.

### Common Bugs to Avoid
- Integer overflow during `totalWaitTime` accumulation without `long long`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is Smith's Rule in scheduling theory?**  
  **A**: Smith's Rule states that scheduling jobs in increasing order of processing time $p_i / w_i$ minimizes the weighted sum of completion times $\sum w_i C_i$. When all weights $w_i = 1$, this simplifies to SJF!

- **Q2: Why is SJF rarely used in real OS schedulers without preemption?**  
  **A**: Because burst times are unknown in advance, and SJF can cause **starvation** for long processes if short processes continuously arrive.

- **Q3: How does an OS estimate burst times for SJF in practice?**  
  **A**: Using exponential smoothing (moving average): $\tau_{n+1} = \alpha t_n + (1 - \alpha) \tau_n$.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `CPU Scheduling`, `Sorting`, `Easy`
- **Related problems to practice next**:
- **Task Scheduler**: Cool down scheduling.
