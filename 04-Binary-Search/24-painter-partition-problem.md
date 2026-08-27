# Painter's Partition Problem (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Painter's Partition Problem](https://takeuforward.org/binary-search/painters-partition-problem/)
- **Difficulty**: Hard
- **Statement**: Given an array `boards` of $n$ integers where `boards[i]` represents the length of the $i$-th board, and an integer $k$ representing the number of painters available. Each painter takes 1 unit of time to paint 1 unit of board. Painters can only paint continuous sections of boards. Find the minimum time required to paint all boards.

---

## 1. Problem, Restated

Partition the array `boards` into at most $k$ contiguous subarrays such that the maximum sum of any subarray is minimized.

- **Input**: Input arrays/parameters.
- **Output**: Value meeting the specification.
- **Constraints**: $1 \le n, m \le 10^5$, standard 32/64-bit limits.

---

## 2. Intuition & Pattern

Each painter paints a contiguous set of boards. The total time taken by any painter is the sum of lengths of the boards assigned to them. To finish the entire job in minimum time, we must minimize the maximum workload assigned to any single painter. The search space for the maximum time $T$ is bounded by `low = max(boards)` (a painter must paint at least the longest single board) and `high = sum(boards)` (one painter paints everything).

- **Underlying Pattern**: `Minimax Contiguous Partition (BS on Time/Capacity Space)`.
- **The "Aha!" Moment**: Recognizing the monotonic boundary or partition cut that enables logarithmic halving.

---

## 3. Approach 1 — Brute Force

### Idea
Linear Scan of Maximum Workload: Iterate workload $T$ from $\max(\text{boards})$ to $\sum \text{boards}$. Return the first $T$ that requires $\le k$ painters.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

int countPainters(const vector<int>& boards, int maxTime) {
    int painters = 1, currentTime = 0;
    for (int b : boards) {
        if (currentTime + b > maxTime) {
            painters++;
            currentTime = b;
        } else {
            currentTime += b;
        }
    }
    return painters;
}

int findLargestMinDistanceLinear(vector<int>& boards, int k) {
    int low = *max_element(boards.begin(), boards.end());
    int high = accumulate(boards.begin(), boards.end(), 0);
    for (int time = low; time <= high; time++) {
        if (countPainters(boards, time) <= k) return time;
    }
    return low;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\sum - \max) \times n)$ — linear range scan causes TLE.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Testing every possible time unit sequentially is too slow for large board lengths.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Binary Search on Workload Capacity: `low = *max_element(boards), high = accumulate(boards)`. While `low <= high`, if `countPainters(boards, mid) <= k`, record `ans = mid, high = mid - 1` (minimize maximum workload); else `low = mid + 1`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

class Solution {
private:
    int countPainters(const vector<int>& boards, long long maxTime) {
        int painters = 1;
        long long currentLoad = 0;
        
        for (int b : boards) {
            if (currentLoad + b > maxTime) {
                painters++;
                currentLoad = b;
            } else {
                currentLoad += b;
            }
        }
        return painters;
    }

public:
    int findLargestMinDistance(vector<int>& boards, int k) {
        int low = *max_element(boards.begin(), boards.end());
        long long high = accumulate(boards.begin(), boards.end(), 0LL);
        int ans = low;
        
        while (low <= high) {
            long long mid = low + (high - low) / 2;
            
            if (countPainters(boards, mid) <= k) {
                ans = mid;        // feasible workload, try to minimize further
                high = mid - 1;
            } else {
                low = mid + 1;    // too many painters needed, increase allowed workload
            }
        }
        
        return ans; // or low at termination
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log_2(\sum \text{boards}))$ — binary search takes $\approx 32$ iterations of $\mathcal{O}(n)$ checks.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Information-theoretic lower bound for minimax contiguous sequence partitioning.

---

## 6. Dry Run

`boards = [2, 1, 5, 6, 2, 3]`, $k = 2$. `low = 6, high = 19`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | mid = 12. P1: [2,1,5] (8), P2: [6,2,3] (11) -> 2 painters | painters=2 <= 2 -> ans = 12, high = 11 |
| `Iter 2` | mid = 8. P1: [2,1,5] (8), P2: [6,2] (8), P3: [3] (3) -> 3 painters | painters=3 > 2 -> low = 9 |
| `Iter 3` | mid = 10. P1: [2,1,5] (8), P2: [6,2] (8), P3: [3] (3) -> 3 painters | painters=3 > 2 -> low = 11 |
| `Iter 4` | mid = 11. P1: [2,1,5] (8), P2: [6,2,3] (11) -> 2 painters | painters=2 <= 2 -> ans = 11, high = 10 |
| `Exit` | low (11) > high (10) -> loop terminates | Return ans = 11 ✅ (P1: [2,1,5]=8, P2: [6,2,3]=11) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- k >= n (each painter takes 1 board $\implies$ answer is $\max(\text{boards})$).
- k == 1 (one painter takes all boards $\implies$ answer is $\sum \text{boards}$).
- All boards equal length (`[5, 5, 5, 5], k = 2` $\implies$ answer is 10).

### Common Bugs to Avoid
- Setting `low = 1` or `low = 0` instead of `*max_element(boards)`.
- Using 32-bit `int` for `high` and `currentLoad` when sums exceed $2^{31}-1$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the exact relation to Book Allocation Problem?**  
  **A**: Boards length $\leftrightarrow$ Book pages, Painters $\leftrightarrow$ Students, Minimum Time $\leftrightarrow$ Minimum Maximum Pages. Both problems share identical algorithm and code structure.

- **Q2: What if painting speed differs per painter (Painter i takes C[i] time per unit)?**  
  **A**: If painters have non-uniform speeds, greedy monotonic partitioning breaks down; Dynamic Programming with state compression or branch-and-bound is required.

- **Q3: Why does greedy allocation inside countPainters minimize painter count?**  
  **A**: Because boards must be painted contiguously in order, assigning as many boards as possible to painter $i$ without exceeding `maxTime` leaves the maximum remaining work for subsequent painters.

- **Q4: Can we early-exit in countPainters?**  
  **A**: Yes, if `painters > k`, return immediately to save operations on large arrays.

- **Q5: What if boards can be painted in non-contiguous chunks?**  
  **A**: It becomes the **Multiprocessor Scheduling problem (NP-Hard)**.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Greedy`, `Hard`
- **Related problems to practice next**:
- **Book Allocation Problem**: Identical twin problem.
- **Split Array Largest Sum**: LeetCode 410 clone.
- **Capacity to Ship Packages**: Conveyor belt capacity variant.
