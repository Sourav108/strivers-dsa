# Floor and Ceil in Sorted Array (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Floor and Ceil in Sorted Array](https://takeuforward.org/arrays/floor-and-ceil-in-sorted-array/)
- **Difficulty**: Easy
- **Statement**: Given a sorted array `nums` and an integer $x$, find the Floor and Ceil of $x$ in `nums`. Floor is the largest element in array smaller than or equal to $x$ ($nums[i] \le x$). Ceil is the smallest element in array greater than or equal to $x$ ($nums[i] \ge x$). Return `{floor, ceil}`. If floor or ceil does not exist, return `-1` for that value.

---

## 1. Problem, Restated

Find the closest numbers in the array flanking $x$: the greatest number $\le x$ (Floor) and the smallest number $\ge x$ (Ceil).

- **Input**: `const vector<int>& nums` (sorted), query value `x`.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, $-10^9 \le nums[i], x \le 10^9$.

---

## 2. Intuition & Pattern

Ceil is simply the Lower Bound value ($nums[lower\_bound(x)]$). Floor is the symmetrical inverse: when `nums[mid] <= x`, `mid` is a valid candidate for floor (store `floor = nums[mid]`) and we search right `low = mid + 1` for a larger candidate. When `nums[mid] > x`, we search left `high = mid - 1`.

- **Underlying Pattern**: `Dual Binary Search (Floor <= x, Ceil >= x)`.
- **The "Aha!" Moment**: Recognizing that binary search is not just for finding exact values, but for identifying the exact boundary where a boolean condition flips.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Traverse the array. Keep track of maximum element $\le x$ and minimum element $\ge x$.

### C++17 Code
```cpp
#include <vector>
#include <utility>
using namespace std;

pair<int, int> getFloorAndCeilLinear(const vector<int>& nums, int x) {
    int f = -1, c = -1;
    for (int num : nums) {
        if (num <= x && (f == -1 || num > f)) f = num;
        if (num >= x && (c == -1 || num < c)) c = num;
    }
    return {f, c};
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — single linear pass.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Checks all elements sequentially instead of using binary search.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Independent Binary Searches for Floor and Ceil: 1) For Floor: if `nums[mid] <= x`, `f = nums[mid], low = mid + 1`; else `high = mid - 1`. 2) For Ceil: if `nums[mid] >= x`, `c = nums[mid], high = mid - 1`; else `low = mid + 1`.

### C++17 Code
```cpp
#include <vector>
#include <utility>
using namespace std;

int findFloor(const vector<int>& nums, int x) {
    int low = 0, high = (int)nums.size() - 1;
    int ans = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] <= x) {
            ans = nums[mid]; // candidate floor, try to find larger on right
            low = mid + 1;
        } else {
            high = mid - 1;
        }
    }
    return ans;
}

int findCeil(const vector<int>& nums, int x) {
    int low = 0, high = (int)nums.size() - 1;
    int ans = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] >= x) {
            ans = nums[mid]; // candidate ceil, try to find smaller on left
            high = mid - 1;
        } else {
            low = mid + 1;
        }
    }
    return ans;
}

pair<int, int> getFloorAndCeil(const vector<int>& nums, int x) {
    return {findFloor(nums, x), findCeil(nums, x)};
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — two binary searches of $\mathcal{O}(\log n)$ each.
- **Space Complexity**: $\mathcal{O}(1)$ — auxiliary space.
- **Why this is optimal**: Optimal logarithmic query time for nearest neighbor queries on static sorted arrays.

---

## 6. Dry Run

`nums = [10, 20, 30, 40, 50]`, `x = 25`

| Step | Action / State Change | Result |
|---|---|---|
| `Floor Search` | mid=2 (30) > 25 -> high=1. mid=0 (10) <= 25 -> f=10, low=1. mid=1 (20) <= 25 -> f=20, low=2 | Floor = 20 |
| `Ceil Search` | mid=2 (30) >= 25 -> c=30, high=1. mid=0 (10) < 25 -> low=1. mid=1 (20) < 25 -> low=2 | Ceil = 30 |
| `Combined` | Return {20, 30} | Result: `{20, 30}` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- x smaller than smallest element (`x = 5` in `[10, 20]` -> Floor = -1, Ceil = 10).
- x larger than largest element (`x = 60` in `[10, 20]` -> Floor = 20, Ceil = -1).
- x matches element exactly (`x = 20` -> Floor = 20, Ceil = 20).

### Common Bugs to Avoid
- Moving `high = mid - 1` when `nums[mid] <= x` during floor search (must move right to find larger valid floor).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can we find both Floor and Ceil in a SINGLE binary search pass?**  
  **A**: Yes! While `low <= high`, if `nums[mid] == x`, both floor and ceil are $x$. When the loop ends with `low > high` (and $x$ not found), `high` points to Floor (if $high \ge 0$) and `low` points to Ceil (if $low < n$)!

- **Q2: How does C++ `std::set` support floor and ceil?**  
  **A**: `std::set::lower_bound(x)` returns iterator to Ceil. Preceding iterator `std::prev(it)` returns Floor (if `it != set.begin()`).

- **Q3: How does this apply to database B+ Trees?**  
  **A**: B+ Trees use floor/ceil binary search at each node level to decide which child pointer to follow during range queries.

- **Q4: What if the array has negative numbers?**  
  **A**: The arithmetic `nums[mid] <= x` handles negative numbers flawlessly without any special casing.

- **Q5: How to handle dynamic stream of numbers with floor/ceil queries?**  
  **A**: Use a Self-Balancing BST (`std::set` in C++ or `TreeSet` in Java) for dynamic $\mathcal{O}(\log n)$ insertions and queries.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Floor`, `Ceil`, `Easy`
- **Related problems to practice next**:
- **Implement Lower Bound**: Ceil is lower_bound value.
- **Search Insert Position**: Insertion slot relates to Ceil index.
- **First and Last Occurrences**: Boundary bounding searches.
