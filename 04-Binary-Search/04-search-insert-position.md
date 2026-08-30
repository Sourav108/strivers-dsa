# Search Insert Position (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Search Insert Position](https://takeuforward.org/arrays/search-insert-position/)
- **Difficulty**: Easy
- **Statement**: Given a sorted array of distinct integers and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order. You must write an algorithm with $\mathcal{O}(\log n)$ runtime complexity.

---

## 1. Problem, Restated

Find the index of `target` in a sorted array, or find the index where `target` should be placed to keep the array sorted. This is identical to finding the Lower Bound of `target`.

- **Input**: `const vector<int>& nums` (sorted), query value `x`.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, $-10^9 \le nums[i], x \le 10^9$.

---

## 2. Intuition & Pattern

If `target` exists in the array, its index is the smallest index $i$ where $nums[i] \ge target$ ($nums[i] == target$). If `target` does not exist, the correct insertion position is also the smallest index $i$ where $nums[i] \ge target$ (everything to the left is strictly smaller). Thus, Search Insert Position is mathematically identical to Lower Bound.

- **Underlying Pattern**: `Lower Bound Equivalence`.
- **The "Aha!" Moment**: Recognizing that binary search is not just for finding exact values, but for identifying the exact boundary where a boolean condition flips.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Iterate $i$ from 0 to $n-1$. The first element where $nums[i] \ge target$ gives the insertion position. If none found, return $n$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int searchInsertLinear(const vector<int>& nums, int target) {
    for (int i = 0; i < (int)nums.size(); i++) {
        if (nums[i] >= target) return i;
    }
    return nums.size();
}
```

### Java Code
```java
class Solution {
    int searchInsertLinear(int[] nums, int target) {
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] >= target) return i;
        }
        return nums.length;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Fails the problem's explicit $\mathcal{O}(\log n)$ time requirement.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Binary Search Lower Bound: Maintain `low = 0, high = n - 1, ans = n`. While `low <= high`, if `nums[mid] >= target`, `ans = mid, high = mid - 1`; else `low = mid + 1`. Return `ans` (or equivalently return `low` after loop exits).

### C++17 Code
```cpp
#include <vector>
using namespace std;

int searchInsert(const vector<int>& nums, int target) {
    int low = 0, high = (int)nums.size() - 1;
    int ans = nums.size();
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        
        if (nums[mid] >= target) {
            ans = mid;
            high = mid - 1; // look for smaller index on left
        } else {
            low = mid + 1;  // look on right
        }
    }
    
    return ans; // or return low directly
}
```

### Java Code
```java
class Solution {
    int searchInsert(int[] nums, int target) {
        int low = 0, high = nums.length - 1;
        int ans = nums.length;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (nums[mid] >= target) {
                ans = mid;
                high = mid - 1; // look for smaller index on left
            } else {
                low = mid + 1;  // look on right
            }
        }
        
        return ans; // or return low directly
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — binary search halving.
- **Space Complexity**: $\mathcal{O}(1)$ — auxiliary space.
- **Why this is optimal**: Matches optimal $\Omega(\log n)$ comparison lower bound.

---

## 6. Dry Run

`nums = [1, 3, 5, 6]`, `target = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | low=0, high=3, ans=4 | ready |
| `Iter 1` | mid = 1, nums[1] = 3 >= 2 -> ans = 1, high = 0 | ans=1, high=0 |
| `Iter 2` | mid = 0, nums[0] = 1 < 2 -> low = 1 | low=1, high=0 |
| `Exit` | low=1 > high=0 -> Loop ends | Return ans = 1 (insert at index 1 -> [1, 2, 3, 5, 6]) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target smaller than all elements (`target = 0` -> returns 0).
- Target larger than all elements (`target = 7` -> returns n = 4).
- Target already in array (`target = 5` -> returns index 2).

### Common Bugs to Avoid
- Returning `-1` when target not found instead of the insertion index.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does `low` always point to the insertion index when the while loop `low <= high` terminates?**  
  **A**: When the loop finishes, `high` and `low` cross. All elements at index $\le high$ are strictly $< target$, and all elements at index $\ge low$ are $\ge target$. Therefore, `low` is mathematically the exact boundary index.

- **Q2: How does this problem relate to Building a Binary Search Tree (BST)?**  
  **A**: Finding the insertion position in a sorted array is the array equivalent of traversing down a BST to find the parent leaf node for a new key in $\mathcal{O}(\log n)$.

- **Q3: What if duplicate elements were allowed in `nums`?**  
  **A**: If duplicates exist, `searchInsert` returns the index of the first occurrence of `target`, maintaining non-decreasing sorted order.

- **Q4: Can this be used for Longest Increasing Subsequence (LIS)?**  
  **A**: Yes! The $\mathcal{O}(n \log n)$ patience sorting algorithm for LIS uses `searchInsert` (`lower_bound`) to find where to replace elements in the active tails array.

- **Q5: How to make this function strictly branchless?**  
  **A**: Use conditional pointer additions `low += (nums[mid] < target) * (half + 1)` with power-of-two window sizes.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Array`, `LeetCode-35`, `Easy`
- **Related problems to practice next**:
- **Implement Lower Bound**: Identical theoretical formulation.
- **Longest Increasing Subsequence**: Uses search insert position inside DP tails.
- **First and Last Occurrences**: Finds bounding range.
