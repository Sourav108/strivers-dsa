# N Meetings in One Room (Activity Selection) (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [N Meetings in One Room (Activity Selection)](https://takeuforward.org/data-structure/n-meetings-in-one-room/)
- **Difficulty**: Easy
- **Statement**: There is one meeting room in a firm. There are $N$ meetings in the form of `(start[i], end[i])`. Find the maximum number of meetings that can be accommodated in the meeting room if only one meeting can be held at a time.

---

## 1. Problem, Restated

Classic Activity Selection Problem: sort meetings by end time ascending and greedily pick non-overlapping meetings.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

To maximize meetings accommodated in ONE room, always pick the meeting that **finishes earliest** (leaving maximum remaining time for subsequent meetings). 1) Bundle into `(end[i], start[i])`. 2) Sort in ascending order of end times. 3) Pick first meeting: `count = 1, lastEndTime = meeting[0].end`. 4) Loop remaining meetings: if `meeting[i].start > lastEndTime`, pick meeting `count++`, `lastEndTime = meeting[i].end`.

- **Underlying Pattern**: `Greedy Earliest Finish Time Selection (Activity Selection Theorem)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Generate all $2^N$ subsets of meetings, checking for non-overlapping feasibility in $\mathcal{O}(2^N \cdot N)$ time.

### C++17 Code
```cpp
// O(2^N) brute subset search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 2^N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Exponential search.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Earliest End-Time Greedy Sort in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

struct Meeting {
    int start;
    int end;
    int pos;
};

class Solution {
public:
    int maxMeetings(int start[], int end[], int n) {
        vector<Meeting> meetings(n);
        for (int i = 0; i < n; i++) {
            meetings[i] = {start[i], end[i], i + 1};
        }
        
        // Sort by end time ascending
        sort(meetings.begin(), meetings.end(), [](const Meeting& a, const Meeting& b) {
            if (a.end != b.end) return a.end < b.end;
            return a.pos < b.pos;
        });
        
        int count = 1;
        int lastEndTime = meetings[0].end;
        
        for (int i = 1; i < n; i++) {
            if (meetings[i].start > lastEndTime) {
                count++;
                lastEndTime = meetings[i].end;
            }
        }
        
        return count;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space for meeting structures.
- **Why this is optimal**: Earliest finishing time frees the room soonest, provably maximizing the total accommodated activities.

---

## 6. Dry Run

`start = [1, 3, 0, 5, 8, 5], end = [2, 4, 6, 7, 9, 9]`

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted by end time` | `[(1,2), (3,4), (0,6), (5,7), (8,9), (5,9)]` | Sorted |
| `Meeting 1 `(1, 2)`` | Pick! lastEndTime = 2, count = 1 | count = 1 |
| `Meeting 2 `(3, 4)`` | $3 > 2 \implies$ Pick! lastEndTime = 4, count = 2 | count = 2 |
| `Meeting 3 `(0, 6)`` | $0 \le 4 \implies$ Skip conflict | count = 2 |
| `Meeting 4 `(5, 7)`` | $5 > 4 \implies$ Pick! lastEndTime = 7, count = 3 | count = 3 |
| `Meeting 5 `(8, 9)`` | $8 > 7 \implies$ Pick! lastEndTime = 9, count = 4 | count = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns 1).
- Meetings with identical start and end times.

### Common Bugs to Avoid
- Sorting by start time instead of end time (fails on long meetings starting early like `(0, 100)`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does sorting by start time fail?**  
  **A**: A meeting starting early might run for an extremely long duration (e.g. `(0, 100)`), blocking dozens of short meetings `(1, 2), (2, 3), (3, 4)`. Sorting by END time guarantees room is freed earliest!

- **Q2: How to return the actual meeting IDs accommodated?**  
  **A**: Record `meetings[i].pos` whenever `meetings[i].start > lastEndTime` and return the collected vector.

- **Q3: What if meetings have weights (profit) and we want max profit?**  
  **A**: Weighted Interval Scheduling cannot be solved greedily! It requires Dynamic Programming with Binary Search (`dp[i] = max(dp[i-1], profit[i] + dp[prevNonOverlapping])`) in $\mathcal{O}(N \log N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Activity Selection`, `Intervals`, `Easy`
- **Related problems to practice next**:
- **Non-overlapping Intervals**: Interval removals.
- **Minimum Platforms**: Parallel rooms.
