# Search in Rotated Sorted Array II (Duplicate Elements) (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Search in Rotated Sorted Array II (Duplicate Elements)](https://takeuforward.org/arrays/search-in-rotated-sorted-array-ii/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums` sorted in non-decreasing order (not necessarily distinct values) that has been rotated at an unknown pivot, and an integer `target`, return `true` if `target` is in `nums`, or `false` if it is not. You must decrease the overall operation steps as much as possible.

---

## 1. Problem, Restated

Determine if a target exists in a rotated sorted array that may contain DUPLICATE elements.

- **Input**: Vector of integers `nums`, plus query parameters.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, elements fit in 32-bit signed integers.

---

## 2. Intuition & Pattern

When duplicate elements are present, we can encounter the edge case where `nums[low] == nums[mid] == nums[high]`. In this state (e.g. `[3, 1, 2, 3, 3, 3, 3]`), we cannot determine whether the left or right half is sorted. To resolve the ambiguity, we simply shrink both boundaries: `low++` and `high--`. In all other cases, standard rotated binary search applies.

- **Underlying Pattern**: `Rotated Binary Search with Ambiguity Shrinking (`nums[low] == nums[mid] == nums[high]`)`.
- **The "Aha!" Moment**: Recognizing how monotonic invariants or gradient direction eliminate half the search space.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Check every element sequentially in $\mathcal{O}(n)$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

bool searchDuplicatesLinear(const vector<int>& nums, int target) {
    for (int x : nums) {
        if (x == target) return true;
    }
    return false;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Does not take advantage of binary search when duplicates do not cause ambiguity.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Modified Binary Search with Duplicate Trim: If `nums[mid] == target`, return `true`. If `nums[low] == nums[mid] && nums[mid] == nums[high]`, execute `low++, high--` and continue. Otherwise, proceed with standard sorted-half identification.

### C++17 Code
```cpp
#include <vector>
using namespace std;

bool search(const vector<int>& nums, int target) {
    int low = 0, high = (int)nums.size() - 1;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        
        if (nums[mid] == target) {
            return true;
        }
        
        // Ambiguity case: cannot tell which half is sorted
        if (nums[low] == nums[mid] && nums[mid] == nums[high]) {
            low++;
            high--;
            continue;
        }
        
        // Left half is sorted
        if (nums[low] <= nums[mid]) {
            if (nums[low] <= target && target < nums[mid]) {
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        } 
        // Right half is sorted
        else {
            if (nums[mid] < target && target <= nums[high]) {
                low = mid + 1;
            } else {
                high = mid - 1;
            }
        }
    }
    
    return false;
}
```

### Complexity Derivation
- **Time Complexity**: Average: $\mathcal{O}(\log n)$. Worst Case: $\mathcal{O}(n/2) = \mathcal{O}(n)$ when all elements are duplicates (e.g. `[1, 1, 1, 1, 1, 1]` with `target = 2`).
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary memory.
- **Why this is optimal**: The $\mathcal{O}(n)$ worst case is an unavoidable information-theoretic bound because the target could be hidden anywhere in an array of identical numbers.

---

## 6. Dry Run

`nums = [2, 5, 6, 0, 0, 1, 2]`, `target = 0`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | low=0 (2), high=6 (2), mid=3 (0) | nums[mid] = 0 == target! **Returns TRUE immediately!** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements duplicate except target (`[3, 3, 3, 1, 3, 3]` -> trim steps shrink until 1 is isolated).
- Target not in array of duplicates (`[1, 1, 1, 1], target = 2` -> returns `false`).

### Common Bugs to Avoid
- Forgetting `continue` after `low++, high--`, which erroneously tries to use invalid half-sorted assumptions.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does presence of duplicates degrade worst-case complexity from O(log n) to O(n)?**  
  **A**: Consider `[3, 3, 3, 3, 3, 3, 3]`. Here `nums[low] == nums[mid] == nums[high] = 3`. Target could be `1` at ANY index (or nowhere). No comparison can eliminate half the array without inspecting elements, requiring linear $\mathcal{O}(n)$ time in the worst case.

- **Q2: Why is it safe to do `low++` and `high--` when `nums[low] == nums[mid] == nums[high]`?**  
  **A**: Because we already verified `nums[mid] != target`. Since `nums[low] == nums[mid]` and `nums[high] == nums[mid]`, neither `nums[low]` nor `nums[high]` can be the target. Trimming them loses zero information.

- **Q3: Why does LeetCode 81 return bool instead of index?**  
  **A**: Because with duplicates, multiple indices may hold the target; returning existence boolean is the canonical problem specification.

- **Q4: Can we optimize the duplicate trim step?**  
  **A**: Yes, `while (low < mid && nums[low] == nums[mid]) low++;` skips multiple identical elements at once.

- **Q5: How does this algorithm compare to QuickSort partition with duplicates?**  
  **A**: Similar to 3-way Dutch National Flag partitioning, it groups equal values to bypass redundant comparisons.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Rotated Array`, `Duplicates`, `LeetCode-81`, `Medium`
- **Related problems to practice next**:
- **Search in Rotated Sorted Array I**: Unique elements version with strict O(log n).
- **Find Minimum in Rotated Sorted Array**: Pivot discovery.
- **Find Peak Element**: Monotonic peak detection.
