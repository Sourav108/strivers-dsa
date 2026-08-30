# Non-overlapping Intervals (Min interval removals) (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Non-overlapping Intervals (Min interval removals)](https://takeuforward.org/data-structure/non-overlapping-intervals/)
- **Difficulty**: Medium
- **Statement**: Given an array of intervals `intervals` where `intervals[i] = [start_i, end_i]`, return the minimum number of intervals you need to remove to make the rest of the intervals non-overlapping.

---

## 1. Problem, Restated

Minimum removals $= N - \text{Maximum Non-Overlapping Intervals (Activity Selection)}$.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

To MINIMIZE removed intervals, we must MAXIMIZE retained non-overlapping intervals (which is exactly the N Meetings in One Room problem!). Sort intervals by end time `interval[1]` ascending. Track `lastEnd = intervals[0][1]`. For each subsequent interval: if `intervals[i][0] < lastEnd`, overlap occurs $\implies$ increment `removals++`; else update `lastEnd = intervals[i][1]`.

- **Underlying Pattern**: `Earliest End-Time Sorting & Inverted Removal Counting`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Recursion exploring remove vs keep for each interval in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
// O(2^N) recursion
```

### Java Code
```java
// Java equivalent
// O(2^N) recursion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Exponential combinations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Greedy Earliest End Time in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int eraseOverlapIntervals(vector<vector<int>>& intervals) {
        if (intervals.empty()) return 0;
        
        // Sort by end time ascending
        sort(intervals.begin(), intervals.end(), [](const vector<int>& a, const vector<int>& b) {
            return a[1] < b[1];
        });
        
        int removals = 0;
        int lastEnd = intervals[0][1];
        
        for (size_t i = 1; i < intervals.size(); i++) {
            if (intervals[i][0] < lastEnd) {
                // Overlap: greedily remove current interval
                removals++;
            } else {
                // No overlap: keep interval and update lastEnd
                lastEnd = intervals[i][1];
            }
        }
        
        return removals;
    }
};
```

### Java Code
```java
class Solution {

    int eraseOverlapIntervals(int[][] intervals) {
        if (intervals.isEmpty()) return 0;
        
        // Sort by end time ascending
        sort(intervals.begin(), intervals.end(), [](int[] a, int[] b) {
            return a[1] < b[1];
        });
        
        int removals = 0;
        int lastEnd = intervals[0][1];
        
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] < lastEnd) {
                // Overlap: greedily remove current interval
                removals++;
            } else {
                // No overlap: keep interval and update lastEnd
                lastEnd = intervals[i][1];
            }
        }
        
        return removals;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Minimizing removals is dual to maximizing retained activities via earliest finish time.

---

## 6. Dry Run

`intervals = [[1, 2], [2, 3], [3, 4], [1, 3]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted by end time` | `[[1, 2], [2, 3], [1, 3], [3, 4]]` | lastEnd = 2 |
| `i = 1 `[2, 3]`` | $2 \ge 2 \implies$ No overlap, keep! lastEnd = 3 | removals = 0 |
| `i = 2 `[1, 3]`` | $1 < 3 \implies$ Overlap! Remove `[1, 3]` | removals = 1 |
| `i = 3 `[3, 4]`` | $3 \ge 3 \implies$ No overlap, keep! lastEnd = 4 | removals = 1 |
| `Result` | Total intervals removed = 1 | Result = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Touching intervals `[1, 2]` and `[2, 3]` (non-overlapping, $2 \ge 2$ is allowed).
- All intervals identical.

### Common Bugs to Avoid
- Treating touching boundaries (`start == end`) as overlapping (problem states touching points are valid).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can we sort by start time instead?**  
  **A**: Yes! If sorting by start time, when an overlap occurs (`curr[0] < prev[1]`), you must greedily keep the interval with the SMALLER end time: `prevEnd = min(prevEnd, curr[1])` and increment removals.

- **Q2: What is the relationship with LeetCode 452 (Minimum Number of Arrows to Burst Balloons)?**  
  **A**: LeetCode 452 is identical: each non-overlapping cluster requires exactly one arrow. The number of arrows needed equals the maximum number of non-overlapping intervals!

- **Q3: How to handle 64-bit coordinate ranges?**  
  **A**: Use standard 64-bit integer vectors to prevent arithmetic overflow in case distances are computed.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Intervals`, `Sorting`, `LeetCode-435`, `Medium`
- **Related problems to practice next**:
- **N Meetings in One Room**: Activity selection.
- **Merge Intervals**: Interval merging.
