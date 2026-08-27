# Merge Intervals (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Merge Intervals](https://takeuforward.org/data-structure/merge-overlapping-sub-intervals/)
- **Difficulty**: Medium
- **Statement**: Given an array of `intervals` where `intervals[i] = [start_i, end_i]`, merge all overlapping intervals, and return an array of the non-overlapping intervals that cover all the intervals in the input.

---

## 1. Problem, Restated

Sort intervals by start time; merge current interval into previous if `curr[0] <= prev[1]`.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Sort intervals by start time `intervals[i][0]` ascending. Iterate through sorted intervals: 1) If `merged` is empty or `intervals[i][0] > merged.back()[1]`, no overlap $\implies$ push `intervals[i]`. 2) If overlapping (`intervals[i][0] <= merged.back()[1]`), extend previous interval: `merged.back()[1] = max(merged.back()[1], intervals[i][1])`.

- **Underlying Pattern**: `Sort by Start Time + In-Place Interval Extension`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Nested loops checking overlaps for every pair of intervals in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) brute merge
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Quadratic pair comparisons.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sort + Single Pass In-Place Extension in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        if (intervals.empty()) return {};
        
        // Sort intervals by start time
        sort(intervals.begin(), intervals.end());
        
        vector<vector<int>> merged;
        merged.push_back(intervals[0]);
        
        for (size_t i = 1; i < intervals.size(); i++) {
            // Check if current interval overlaps with previous merged interval
            if (intervals[i][0] <= merged.back()[1]) {
                merged.back()[1] = max(merged.back()[1], intervals[i][1]);
            } else {
                merged.push_back(intervals[i]);
            }
        }
        
        return merged;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (excluding returned vector).
- **Why this is optimal**: Sorting by start time ensures all overlapping intervals appear contiguously.

---

## 6. Dry Run

`intervals = [[1,3], [2,6], [8,10], [15,18]]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | merged = `[[1, 3]]` | i = 1 |
| `i = 1 `[2, 6]`` | $2 \le 3 \implies$ overlap! `merged.back()[1] = max(3, 6) = 6` | merged: `[[1, 6]]` |
| `i = 2 `[8, 10]`` | $8 > 6 \implies$ no overlap, push `[8, 10]` | merged: `[[1, 6], [8, 10]]` |
| `i = 3 `[15, 18]`` | $15 > 10 \implies$ no overlap, push `[15, 18]` | merged: `[[1, 6], [8, 10], [15, 18]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single interval `[[1, 4]]` $\implies$ `[[1, 4]]`.
- All intervals overlap into one giant interval `[[1, 10], [2, 3], [4, 5]]` $\implies$ `[[1, 10]]`.

### Common Bugs to Avoid
- Setting `merged.back()[1] = intervals[i][1]` instead of `max(merged.back()[1], intervals[i][1])` (breaks when an interval is completely contained inside another!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is max(merged.back()[1], intervals[i][1]) necessary?**  
  **A**: Because a smaller interval might be completely nested inside a larger interval (e.g. `[1, 10]` followed by `[2, 4]`). Simply assigning `intervals[i][1]` would incorrectly shrink the upper boundary from 10 down to 4!

- **Q2: Can this be merged in-place within the input vector?**  
  **A**: Yes! Maintain an `index` pointer in `intervals`, overwrite in-place, and resize `intervals.resize(index + 1)` in $\mathcal{O}(1)$ extra space.

- **Q3: How to handle intervals with negative coordinates?**  
  **A**: Sorting works natively on negative numbers without any code modifications.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Intervals`, `Sorting`, `LeetCode-56`, `Medium`
- **Related problems to practice next**:
- **Insert Interval**: Single insertion.
- **Non-overlapping Intervals**: Removal counterpart.
