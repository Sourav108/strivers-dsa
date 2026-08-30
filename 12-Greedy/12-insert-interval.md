# Insert Interval (Merge new interval in sorted set) (Step 12.2 — Medium / Hard)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Insert Interval (Merge new interval in sorted set)](https://takeuforward.org/data-structure/insert-interval/)
- **Difficulty**: Medium
- **Statement**: You are given an array of non-overlapping intervals `intervals` sorted in ascending order by start time, and a `newInterval`. Insert `newInterval` into `intervals` such that `intervals` is still sorted and non-overlapping.

---

## 1. Problem, Restated

Three-phase single pass: 1) Add intervals ending before `newInterval`, 2) Merge overlapping intervals, 3) Add remaining intervals.

- **Input**: Parameters specified.
- **Output**: Optimal value / boolean / transformed list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Since input is already sorted: 1) **Left Phase**: While `intervals[i][1] < newInterval[0]`, append `intervals[i]`. 2) **Overlap Merge Phase**: While `intervals[i][0] <= newInterval[1]`, expand `newInterval = [min(newInterval[0], intervals[i][0]), max(newInterval[1], intervals[i][1])]`, advance `i++`. Append merged `newInterval`. 3) **Right Phase**: Append all remaining intervals from $i$ to $N-1$ in strict $\mathcal{O}(N)$ time.

- **Underlying Pattern**: `Three-Phase Linear Merge Partitioning`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Append `newInterval`, call `sort()`, and run Merge Intervals in $\mathcal{O}(N \log N)$ time.

### C++17 Code
```cpp
// O(N log N) append and sort approach
```

### Java Code
```java
// Java equivalent
// O(N log N) append and sort approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Re-sorts already sorted input.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard greedy strategy below directly achieves optimal $\mathcal{O}(N \log N)$ or $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Three-Phase 1-Pass Linear Insertion in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
        vector<vector<int>> result;
        int n = intervals.size();
        int i = 0;
        
        // Phase 1: Add all intervals ending before newInterval starts
        while (i < n && intervals[i][1] < newInterval[0]) {
            result.push_back(intervals[i]);
            i++;
        }
        
        // Phase 2: Merge all overlapping intervals with newInterval
        while (i < n && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = min(newInterval[0], intervals[i][0]);
            newInterval[1] = max(newInterval[1], intervals[i][1]);
            i++;
        }
        result.push_back(newInterval); // add merged interval
        
        // Phase 3: Add all remaining intervals
        while (i < n) {
            result.push_back(intervals[i]);
            i++;
        }
        
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int[][] insert(int[][] intervals, int[] newInterval) {
        List<List<Integer>> result = new ArrayList<>();
        int n = intervals.length;
        int i = 0;
        
        // Phase 1: Add all intervals ending before newInterval starts
        while (i < n && intervals[i][1] < newInterval[0]) {
            result.add(intervals[i]);
            i++;
        }
        
        // Phase 2: Merge all overlapping intervals with newInterval
        while (i < n && intervals[i][0] <= newInterval[1]) {
            newInterval[0] = Math.min(newInterval[0], intervals[i][0]);
            newInterval[1] = Math.max(newInterval[1], intervals[i][1]);
            i++;
        }
        result.add(newInterval); // add merged interval
        
        // Phase 3: Add all remaining intervals
        while (i < n) {
            result.add(intervals[i]);
            i++;
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (excluding returned vector).
- **Why this is optimal**: Exploits pre-sorted non-overlapping property to insert in linear time.

---

## 6. Dry Run

`intervals = [[1,3], [6,9]], newInterval = [2,5]`

| Step | Action / State Change | Result |
|---|---|---|
| `Phase 1` | intervals[0][1]=3 >= newInterval[0]=2 -> Phase 1 ends | result: `[]` |
| `Phase 2 (Overlap)` | intervals[0][0]=1 <= 5 -> merge: `[min(2,1), max(5,3)] = [1,5]`, i=1 | newInterval = [1,5] |
| `Phase 2 End` | intervals[1][0]=6 > 5 -> loop ends, push `[1, 5]` | result: `[[1,5]]` |
| `Phase 3` | Push remaining `[6, 9]` | Result: `[[1,5], [6,9]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `intervals` is empty `[]` (returns `[newInterval]`).
- `newInterval` inserted at very beginning or very end.

### Common Bugs to Avoid
- Condition `intervals[i][0] < newInterval[1]` instead of `<=` (fails on touching intervals like `[1, 2]` and `[2, 3]`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can this be done in-place with O(1) extra space?**  
  **A**: Yes, by shifting overlapping elements in vector using `erase()` or overwrite in-place, but creating a new vector is standard $\mathcal{O}(N)$ and avoids vector shifting.

- **Q2: Can Binary Search find the insertion boundaries?**  
  **A**: Yes! Use binary search to find the start index and end index in $\mathcal{O}(\log N)$ time, but copying/merging remaining elements still takes $\mathcal{O}(N)$.

- **Q3: How to handle streaming interval insertions?**  
  **A**: Use a Balanced BST (`std::set` / Interval Tree) to find and merge overlapping intervals in $\mathcal{O}(\log N)$ per insertion.


---

## 9. Tags & Related Problems

- **Tags**: `Greedy`, `Intervals`, `LeetCode-57`, `Medium`
- **Related problems to practice next**:
- **Merge Intervals**: General batch merge.
