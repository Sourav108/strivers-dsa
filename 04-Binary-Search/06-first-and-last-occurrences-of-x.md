# First and Last Occurrences of X in Sorted Array (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [First and Last Occurrences of X in Sorted Array](https://takeuforward.org/data-structure/first-and-last-occurrences-of-x/)
- **Difficulty**: Easy
- **Statement**: Given a sorted array of integers `nums` and an integer `target`, find the starting and ending position of `target` in `nums`. If `target` is not found, return `{-1, -1}`. You must write an algorithm with $\mathcal{O}(\log n)$ runtime complexity.

---

## 1. Problem, Restated

Find the first (leftmost) and last (rightmost) index where `target` appears in a sorted array containing possible duplicate values.

- **Input**: Vector of integers `nums`, plus query parameters.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, elements fit in 32-bit signed integers.

---

## 2. Intuition & Pattern

When `nums[mid] == target`, instead of returning immediately, we remember `ans = mid`. To find the FIRST occurrence, we continue searching leftward `high = mid - 1`. To find the LAST occurrence, we continue searching rightward `low = mid + 1`. Running these two modified binary searches gives the exact bounding interval in $\mathcal{O}(\log n)$.

- **Underlying Pattern**: `Dual Binary Search with Bias (Leftmost and Rightmost)`.
- **The "Aha!" Moment**: Recognizing how monotonic invariants or gradient direction eliminate half the search space.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Traverse the array. Record first occurrence when `nums[i] == target` (if not already set), and continuously update last occurrence until `nums[i] > target`.

### C++17 Code
```cpp
#include <vector>
#include <utility>
using namespace std;

vector<int> searchRangeLinear(const vector<int>& nums, int target) {
    int first = -1, last = -1;
    for (int i = 0; i < (int)nums.size(); i++) {
        if (nums[i] == target) {
            if (first == -1) first = i;
            last = i;
        }
    }
    return {first, last};
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — full array scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Does $\mathcal{O}(n)$ checks on an already sorted array.

---

## 4. Approach 2 — Better

### Idea
STL Lower Bound and Upper Bound: First occurrence is `lb = lower_bound(target)`. If `lb == n || nums[lb] != target`, target does not exist. Otherwise, last occurrence is `ub - 1` where `ub = upper_bound(target)`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

vector<int> searchRangeSTL(const vector<int>& nums, int target) {
    auto lb = lower_bound(nums.begin(), nums.end(), target);
    if (lb == nums.end() || *lb != target) return {-1, -1};
    auto ub = upper_bound(nums.begin(), nums.end(), target);
    return {(int)(lb - nums.begin()), (int)(ub - nums.begin() - 1)};
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log n)$ — two STL binary searches.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's still not optimal**: Relies on library black-box functions; interviewers expect raw pointer manipulation.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Raw Customized Binary Searches (Find First & Find Last): 1) `findFirst`: when `nums[mid] == target`, record `ans = mid, high = mid - 1`. 2) `findLast`: when `nums[mid] == target`, record `ans = mid, low = mid + 1`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int findFirst(const vector<int>& nums, int target) {
    int low = 0, high = (int)nums.size() - 1;
    int first = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) {
            first = mid;
            high = mid - 1; // look for earlier occurrence on left
        } else if (nums[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return first;
}

int findLast(const vector<int>& nums, int target) {
    int low = 0, high = (int)nums.size() - 1;
    int last = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == target) {
            last = mid;
            low = mid + 1;  // look for later occurrence on right
        } else if (nums[mid] < target) {
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return last;
}

vector<int> searchRange(const vector<int>& nums, int target) {
    int first = findFirst(nums, target);
    if (first == -1) return {-1, -1}; // early exit if target not present
    int last = findLast(nums, target);
    return {first, last};
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — two independent binary searches.
- **Space Complexity**: $\mathcal{O}(1)$ — zero dynamic allocations.
- **Why this is optimal**: Achieves strict $\mathcal{O}(\log n)$ worst-case bounds even when all elements are identical ($n$ duplicates).

---

## 6. Dry Run

`nums = [5, 7, 7, 8, 8, 10]`, `target = 8`

| Step | Action / State Change | Result |
|---|---|---|
| `findFirst` | mid=2 (7) < 8 -> low=3. mid=4 (8)==8 -> first=4, high=3. mid=3 (8)==8 -> first=3, high=2 | first = 3 |
| `findLast` | mid=2 (7) < 8 -> low=3. mid=4 (8)==8 -> last=4, low=5. mid=5 (10) > 8 -> high=4 | last = 4 |
| `Result` | Return `{3, 4}` | Range: `[3, 4]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target not present (`nums = [5, 7, 7, 8, 8, 10], target = 6` -> returns `{-1, -1}`).
- Single element matching (`nums = [1], target = 1` -> returns `{0, 0}`).
- All elements match target (`nums = [8, 8, 8, 8], target = 8` -> returns `{0, 3}`).

### Common Bugs to Avoid
- Returning `{first, first}` without searching for `last`.
- Not checking if `first == -1` before calling `findLast`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is `findFirst` and `findLast` preferred over expanding linearly from a single match?**  
  **A**: If the array consists of $10^5$ identical elements `[8, 8, ..., 8]`, finding one match and expanding linearly left and right takes $\mathcal{O}(n)$ time, degrading back to linear. Two binary searches guarantee $\mathcal{O}(\log n)$ in all cases.

- **Q2: How does this relate to database indexing?**  
  **A**: In B+ Tree database indexes, finding all rows matching a non-unique index key uses lower_bound to find the starting leaf and upper_bound to find the end leaf.

- **Q3: Can we combine `findFirst` and `findLast` into one generalized function?**  
  **A**: Yes, by passing a boolean parameter `isFirst`: when `nums[mid] == target`, if `isFirst` set `high = mid - 1`, else set `low = mid + 1`.

- **Q4: What if nums is sorted in descending order?**  
  **A**: Invert comparisons: `nums[mid] > target` moves right, `nums[mid] < target` moves left.

- **Q5: What is the total number of comparisons in the worst case?**  
  **A**: At most $2 \times \lceil \log_2 n \rceil \approx 34$ comparisons for $n = 10^5$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Array`, `LeetCode-34`, `Easy`
- **Related problems to practice next**:
- **Count Occurrences**: Directly uses last - first + 1.
- **Implement Lower Bound**: Provides the first occurrence index.
- **Search Insert Position**: Locates element slot.
