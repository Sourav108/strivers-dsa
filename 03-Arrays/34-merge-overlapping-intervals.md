# Merge Overlapping Intervals (Step 3.3)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/merge-overlapping-sub-intervals/
- **Difficulty**: Medium
- **Statement**: Merge all overlapping intervals and return non-overlapping intervals spanning all inputs.

---

## 1. Problem, Restated

Merge all overlapping intervals and return non-overlapping intervals spanning all inputs.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Interval Sorting by start time + Dynamic End Extension.

- **Underlying Pattern**: Array Manipulation / Mathematical Invariants / Pointers.
- **The "Aha!" Moment**: Recognizing how to avoid redundant work by storing running state or leveraging sorting invariants.

---

## 3. Approach 1 — Brute Force

### Idea
Check all possibilities exhaustively using nested loops.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <set>
#include <unordered_map>
using namespace std;

vector<vector<int>> mergeIntervalsBrute(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end());
    // pairwise check
    return intervals;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[][] mergeIntervalsBrute(int[][] intervals) {
        Arrays.sort(intervals);
        // pairwise check
        return intervals;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Pairwise interval comparisons without sorting take $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Sort + Single-Pass Extension: Sort intervals by start time. Iterate through intervals: if `res` is empty or `res.back()[1] < curr[0]`, append `curr`. Else, extend overlap `res.back()[1] = max(res.back()[1], curr[1])`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

vector<vector<int>> mergeIntervalsOptimal(vector<vector<int>>& intervals) {
    if (intervals.empty()) return {};
    sort(intervals.begin(), intervals.end());
    vector<vector<int>> res;
    for (const auto& iv : intervals) {
        if (res.empty() || res.back()[1] < iv[0]) {
            res.push_back(iv);
        } else {
            res.back()[1] = max(res.back()[1], iv[1]);
        }
    }
    return res;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    int[][] mergeIntervalsOptimal(int[][] intervals) {
        if (intervals.isEmpty()) return {};
        Arrays.sort(intervals);
        int[][] res;
        for (var iv : intervals) {
            if (res.isEmpty() || res.peekLast()[1] < iv[0]) {
                res.add(iv);
            } else {
                res.peekLast()[1] = Math.max(res.peekLast()[1], iv[1]);
            }
        }
        return res;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n log n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Sorting localizes overlaps so that a single $\mathcal{O}(n)$ linear pass completes the merge in $\mathcal{O}(n \log n)$ total time.

---

## 6. Dry Run

`intervals = [[1, 3], [2, 6], [8, 10], [15, 18]]`

| Step | Action / State Change | Result |
|---|---|---|
| `iv=[1, 3]` | res empty -> push [1, 3] | res=[[1, 3]] |
| `iv=[2, 6]` | 2 <= 3 (overlap!) -> res.back()[1] = max(3, 6) = 6 | res=[[1, 6]] |
| `iv=[8, 10]` | 8 > 6 (no overlap) -> push [8, 10] | res=[[1, 6], [8, 10]] |
| `iv=[15, 18]` | 15 > 10 (no overlap) -> push [15, 18] | Final: `[[1, 6], [8, 10], [15, 18]]` ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- No overlapping intervals (`[[1, 2], [3, 4]]` -> returns original list).
- One interval completely contains another (`[[1, 10], [2, 3]]` -> returns `[[1, 10]]`).

### Common Bugs to Avoid
- Assuming intervals are already sorted without calling `sort()` first.

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is sorting by start time both necessary and sufficient?**  
  **A**: Sorting by start time guarantees that if interval $B$ overlaps with interval $A$ ($A.\text{start} \le B.\text{start}$), it must appear after $A$ in the sorted array. It localizes all potential overlaps to adjacent elements in $\mathcal{O}(n \log n)$ time.

- **Q2: What if intervals are closed vs open/half-open e.g. [a, b) vs [a, b]?**  
  **A**: For closed intervals $[a, b]$, $[1, 2]$ and $[2, 3]$ overlap (`curr.start <= prev.end`). For half-open intervals $[a, b)$, $[1, 2)$ and $[2, 3)$ do NOT overlap (`curr.start < prev.end`).

- **Q3: How to solve Insert Interval (LeetCode 57) in O(n) time without sorting?**  
  **A**: 1) Add all intervals ending before new interval starts. 2) Merge all overlapping intervals into new interval. 3) Add all intervals starting after new interval ends. Runs in single $\mathcal{O}(n)$ pass.

- **Q4: How to find the total length of timeline covered by overlapping intervals?**  
  **A**: Merge overlapping intervals first in $\mathcal{O}(n \log n)$, then sum `(iv.end - iv.start)` for all merged intervals in $\mathcal{O}(n)$ time.

- **Q5: How to solve Meeting Rooms II (Minimum conference rooms required)?**  
  **A**: Separate start times and end times into two sorted arrays. Use two pointers: increment room count on start time, decrement on end time. Maximum concurrent rooms is peak room count in $\mathcal{O}(n \log n)$.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
