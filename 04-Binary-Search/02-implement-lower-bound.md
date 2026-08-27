# Implement Lower Bound (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Lower Bound](https://takeuforward.org/arrays/implement-lower-bound-bs-2/)
- **Difficulty**: Easy
- **Statement**: Given a sorted array of integers `nums` and an integer $x$, find the lower bound of $x$. The lower bound of $x$ is the smallest index $i$ such that $nums[i] \ge x$. If no such index exists, return $n$.

---

## 1. Problem, Restated

Find the first index in a sorted array where the value is greater than or equal to $x$. If all numbers are smaller than $x$, return $n$ (the size of the array).

- **Input**: `const vector<int>& nums` (sorted), query value `x`.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, $-10^9 \le nums[i], x \le 10^9$.

---

## 2. Intuition & Pattern

The condition $nums[mid] \ge x$ divides the sorted array into two contiguous segments: `[false, false, ..., false, true, true, ..., true]`. We want the FIRST `true`. If `nums[mid] >= x`, then `mid` is a valid candidate (so we store `ans = mid`) and any smaller valid index must lie to the left, so we narrow `high = mid - 1`. If `nums[mid] < x`, `mid` and everything to its left is invalid, so we search right `low = mid + 1`.

- **Underlying Pattern**: `Binary Search on Monotonic Boundary (nums[mid] >= x)`.
- **The "Aha!" Moment**: Recognizing that binary search is not just for finding exact values, but for identifying the exact boundary where a boolean condition flips.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Iterate index $i$ from 0 to $n-1$. The first index where $nums[i] \ge x$ is our answer. If loop finishes without finding any such element, return $n$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int lowerBoundLinear(const vector<int>& nums, int x) {
    int n = nums.size();
    for (int i = 0; i < n; i++) {
        if (nums[i] >= x) return i;
    }
    return n;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — worst case scans all $n$ elements.
- **Space Complexity**: $\mathcal{O}(1)$ — constant memory.
- **Why it's not good enough**: Fails to exploit sorted order, checking elements sequentially when binary search can eliminate half the search space in each step.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Binary Search with Candidate Accumulator: Maintain `ans = n`, `low = 0`, `high = n - 1`. While `low <= high`, compute `mid`. If `nums[mid] >= x`, record `ans = mid` and move left `high = mid - 1`. Else, move right `low = mid + 1`. Return `ans`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int lowerBound(const vector<int>& nums, int x) {
    int n = nums.size();
    int low = 0, high = n - 1;
    int ans = n; // default if no element >= x exists
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        
        if (nums[mid] >= x) {
            ans = mid;       // candidate found, search for earlier index on left
            high = mid - 1;
        } else {
            low = mid + 1;   // nums[mid] < x, search on right
        }
    }
    
    return ans;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — halving search space on each iteration.
- **Space Complexity**: $\mathcal{O}(1)$ — two pointers and one answer variable.
- **Why this is optimal**: Optimal logarithmic lower bound for monotonic predicate boundary search on sorted arrays.

---

## 6. Dry Run

`nums = [1, 2, 4, 4, 7, 9]`, `x = 4`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | low=0, high=5, ans=6 | ready |
| `Iter 1` | mid = 0 + (5-0)/2 = 2, nums[2] = 4 >= 4 | ans = 2, high = mid - 1 = 1 |
| `Iter 2` | mid = 0 + (1-0)/2 = 0, nums[0] = 1 < 4 | low = mid + 1 = 1 |
| `Iter 3` | mid = 1 + (1-1)/2 = 1, nums[1] = 2 < 4 | low = mid + 1 = 2 |
| `Exit` | low=2, high=1 -> low > high | Return ans = 2 (first 4 at index 2) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements >= x (`nums = [5, 6, 7], x = 2` -> returns index 0).
- All elements < x (`nums = [1, 2, 3], x = 10` -> returns index n = 3).
- Target present multiple times (`nums = [2, 4, 4, 4, 8], x = 4` -> returns first index 1).

### Common Bugs to Avoid
- Initializing `ans = -1` instead of `n` (conventionally, out-of-range lower bound is index `n`).
- Setting `high = mid` instead of `high = mid - 1` when `nums[mid] >= x` while using `low <= high`, causing an infinite loop.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does C++ STL implement `std::lower_bound` under the hood?**  
  **A**: `std::lower_bound` takes a forward or random-access iterator range `[first, last)`. It computes `count = distance(first, last)`. While `count > 0`, it computes `step = count / 2`, advances iterator `it = first + step`. If `*it < x`, `first = ++it; count -= step + 1;` else `count = step;`. Returns `first` in $\mathcal{O}(\log n)$ comparisons for random access iterators.

- **Q2: Why is Lower Bound fundamental to Range Queries (Count of elements in [L, R])?**  
  **A**: In a sorted array, the number of elements in range $[L, R]$ is given by `upper_bound(R) - lower_bound(L)` in $\mathcal{O}(\log n)$ time.

- **Q3: Can lower_bound work on descending sorted arrays?**  
  **A**: Yes, with custom comparator `std::greater<int>()` or predicate `nums[mid] <= x` searching for the first element smaller than or equal to $x$.

- **Q4: What is the difference between `lower_bound` and `upper_bound`?**  
  **A**: `lower_bound` finds the first element $\ge x$ (first candidate that could be $x$). `upper_bound` finds the first element strictly $> x$ (first element strictly past $x$).

- **Q5: How does Lower Bound handle duplicate values?**  
  **A**: Because we always record `ans = mid` and move left `high = mid - 1` when `nums[mid] >= x`, it aggressively searches leftward and is mathematically guaranteed to terminate at the very first occurrence of $x$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Lower Bound`, `Monotonic Predicate`, `Easy`
- **Related problems to practice next**:
- **Implement Upper Bound**: First index where element > x.
- **Search Insert Position**: Direct application of lower bound.
- **First and Last Occurrences**: Lower bound gives the first occurrence.
