# Search in Rotated Sorted Array I (Unique Elements) (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Search in Rotated Sorted Array I (Unique Elements)](https://takeuforward.org/data-structure/search-element-in-a-rotated-sorted-array/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums` sorted in ascending order with distinct values, which has been rotated at an unknown pivot, and an integer `target`, return the index of `target` if it is in `nums`, or `-1` if it is not. You must achieve $\mathcal{O}(\log n)$ runtime complexity.

---

## 1. Problem, Restated

Search for a target in a sorted array that was rotated around some pivot point (e.g. `[4, 5, 6, 7, 0, 1, 2]`). All elements are distinct.

- **Input**: Vector of integers `nums`, plus query parameters.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, elements fit in 32-bit signed integers.

---

## 2. Intuition & Pattern

In any rotated sorted array, whenever you pick the midpoint `mid`, **AT LEAST ONE HALF (left `[low..mid]` OR right `[mid..high]`) IS ALWAYS NORMALLY SORTED**.
1. Check if left half `[low..mid]` is sorted (`nums[low] <= nums[mid]`):
   - If target lies within this range (`nums[low] <= target && target < nums[mid]`), eliminate right half (`high = mid - 1`).
   - Else, eliminate left half (`low = mid + 1`).
2. Otherwise, right half `[mid..high]` MUST be sorted:
   - If target lies within this range (`nums[mid] < target && target <= nums[high]`), eliminate left half (`low = mid + 1`).
   - Else, eliminate right half (`high = mid - 1`).

- **Underlying Pattern**: `Rotated Array Invariant (At least one half is always normally sorted)`.
- **The "Aha!" Moment**: Recognizing how monotonic invariants or gradient direction eliminate half the search space.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Ignore rotation and linearly scan each element.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int searchRotatedLinear(const vector<int>& nums, int target) {
    for (int i = 0; i < (int)nums.size(); i++) {
        if (nums[i] == target) return i;
    }
    return -1;
}
```

### Java Code
```java
class Solution {
    int searchRotatedLinear(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] == target) return i;
        }
        return -1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — auxiliary space.
- **Why it's not good enough**: Fails the $\mathcal{O}(\log n)$ interview requirement.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Single-Pass Binary Search on Sorted Half: Determine which half is sorted using `nums[low] <= nums[mid]`. Check if target falls inside that sorted interval to decide whether to search left or right.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int search(const vector<int>& nums, int target) {
    int low = 0, high = (int)nums.size() - 1;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        
        if (nums[mid] == target) {
            return mid;
        }
        
        // Check if Left Half [low..mid] is sorted
        if (nums[low] <= nums[mid]) {
            if (nums[low] <= target && target < nums[mid]) {
                high = mid - 1; // target is within left sorted range
            } else {
                low = mid + 1;  // target is in right half
            }
        } 
        // Otherwise, Right Half [mid..high] must be sorted
        else {
            if (nums[mid] < target && target <= nums[high]) {
                low = mid + 1;  // target is within right sorted range
            } else {
                high = mid - 1; // target is in left half
            }
        }
    }
    
    return -1;
}
```

### Java Code
```java
class Solution {
    int search(int[] nums, int target) {
        int low = 0, high = nums.length - 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (nums[mid] == target) {
                return mid;
            }
            
            // Check if Left Half [low..mid] is sorted
            if (nums[low] <= nums[mid]) {
                if (nums[low] <= target && target < nums[mid]) {
                    high = mid - 1; // target is within left sorted range
                } else {
                    low = mid + 1;  // target is in right half
                }
            } 
            // Otherwise, Right Half [mid..high] must be sorted
            else {
                if (nums[mid] < target && target <= nums[high]) {
                    low = mid + 1;  // target is within right sorted range
                } else {
                    high = mid - 1; // target is in left half
                }
            }
        }
        
        return -1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — strictly eliminates half the search space in every comparison.
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary memory.
- **Why this is optimal**: Achieves optimal $\mathcal{O}(\log n)$ time in a single pass without finding the pivot index first.

---

## 6. Dry Run

`nums = [4, 5, 6, 7, 0, 1, 2]`, `target = 0`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | low=0 (4), high=6 (2), mid=3 (7) | nums[0]=4 <= nums[3]=7 (Left sorted!). Target 0 not in [4..7] -> low = mid + 1 = 4 |
| `Iter 2` | low=4 (0), high=6 (2), mid=5 (1) | nums[4]=0 <= nums[5]=1 (Left sorted!). Target 0 in [0..1] (0<=0 < 1) -> high = mid - 1 = 4 |
| `Iter 3` | low=4 (0), high=4 (0), mid=4 (0) | nums[4] = 0 == target! **Found at index 4!** |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Array not rotated at all (`[1, 2, 3, 4, 5]` -> behaves as standard binary search).
- Single element array (`[1], target = 0` -> returns -1; `target = 1` -> returns 0).
- Target is at pivot boundary (`target = 4` or `target = 2`).

### Common Bugs to Avoid
- Using `<` instead of `<=` in `nums[low] <= nums[mid]` (fails when `low == mid`).
- Using `<=` instead of `<` in `target < nums[mid]` (must be `<` since `nums[mid] == target` is already checked).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is it guaranteed that AT LEAST one half is always sorted?**  
  **A**: A rotated sorted array has at most ONE discontinuity (pivot drop). Dividing an array with at most 1 discontinuity into two halves leaves at least one half completely free of discontinuities (i.e. monotonically sorted).

- **Q2: Can we solve this by finding the pivot index first?**  
  **A**: Yes, binary search can find the minimum element (pivot) in $\mathcal{O}(\log n)$, then run standard binary search on either $[0, pivot-1]$ or $[pivot, n-1]$. However, the single-pass approach above is cleaner and does fewer total comparisons.

- **Q3: What happens if duplicate elements are present?**  
  **A**: If duplicates exist, `nums[low] == nums[mid] == nums[high]` can occur, making it impossible to determine which half is sorted in $\mathcal{O}(1)$. This degrades worst-case to $\mathcal{O}(n)$ (addressed in Search in Rotated Sorted Array II).

- **Q4: How to handle descending sorted rotated arrays?**  
  **A**: Change condition to `nums[low] >= nums[mid]` for left sorted check.

- **Q5: What is the maximum number of rotations possible?**  
  **A**: An array of size $n$ can be rotated $0$ to $n-1$ times (rotation by $n$ restores original array).


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Rotated Array`, `LeetCode-33`, `Medium`
- **Related problems to practice next**:
- **Search in Rotated Sorted Array II**: Handles duplicates.
- **Find Minimum in Rotated Sorted Array**: Locates pivot element.
- **Find Rotation Count**: Determines rotation index.
