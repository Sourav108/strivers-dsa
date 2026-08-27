# Implement Upper Bound (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Implement Upper Bound](https://takeuforward.org/arrays/implement-upper-bound/)
- **Difficulty**: Easy
- **Statement**: Given a sorted array of integers `nums` and an integer $x$, find the upper bound of $x$. The upper bound is defined as the smallest index $i$ such that $nums[i] > x$. If no such index exists, return $n$.

---

## 1. Problem, Restated

Find the first index in a sorted array where the element is STRICTLY GREATER than $x$. If no element is greater than $x$, return $n$.

- **Input**: `const vector<int>& nums` (sorted), query value `x`.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, $-10^9 \le nums[i], x \le 10^9$.

---

## 2. Intuition & Pattern

Predicate $nums[mid] > x$ divides the sorted array into `[false, ..., false, true, ..., true]`. When `nums[mid] > x`, `mid` is a valid upper bound candidate (store `ans = mid`), and we search left `high = mid - 1` for an earlier occurrence. When `nums[mid] <= x`, `mid` and all elements to its left are $\le x$, so we search right `low = mid + 1`.

- **Underlying Pattern**: `Binary Search on Strict Inequality (nums[mid] > x)`.
- **The "Aha!" Moment**: Recognizing that binary search is not just for finding exact values, but for identifying the exact boundary where a boolean condition flips.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Iterate from 0 to $n-1$. The first element with $nums[i] > x$ is returned. If loop finishes without finding any, return $n$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int upperBoundLinear(const vector<int>& nums, int x) {
    int n = nums.size();
    for (int i = 0; i < n; i++) {
        if (nums[i] > x) return i;
    }
    return n;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — sequential linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Does not use the sorted property of the array.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Binary Search with Strict Inequality: Maintain `ans = n`, `low = 0`, `high = n - 1`. If `nums[mid] > x`, update `ans = mid` and search left `high = mid - 1`. Else, search right `low = mid + 1`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int upperBound(const vector<int>& nums, int x) {
    int n = nums.size();
    int low = 0, high = n - 1;
    int ans = n;
    
    while (low <= high) {
        int mid = low + (high - low) / 2;
        
        if (nums[mid] > x) {
            ans = mid;       // candidate found, search left for smaller valid index
            high = mid - 1;
        } else {
            low = mid + 1;   // nums[mid] <= x, search right
        }
    }
    
    return ans;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — halves search space each iteration.
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary memory.
- **Why this is optimal**: Optimal comparison-based bound for finding strict successor in sorted arrays.

---

## 6. Dry Run

`nums = [2, 3, 6, 7, 8, 8, 11, 11, 12]`, `x = 6`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | low=0, high=8, ans=9 | ready |
| `Iter 1` | mid = 4, nums[4] = 8 > 6 | ans = 4, high = 3 |
| `Iter 2` | mid = 1, nums[1] = 3 <= 6 | low = 2 |
| `Iter 3` | mid = 2, nums[2] = 6 <= 6 | low = 3 |
| `Iter 4` | mid = 3, nums[3] = 7 > 6 | ans = 3, high = 2 |
| `Exit` | low=3, high=2 -> low > high | Return ans = 3 (element 7 at index 3) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements <= x (`nums = [1, 2, 3], x = 5` -> returns n = 3).
- All elements > x (`nums = [5, 6, 7], x = 2` -> returns index 0).
- Target present multiple times (`nums = [2, 4, 4, 4, 8], x = 4` -> returns index 4, pointing to 8).

### Common Bugs to Avoid
- Using `>=` instead of `>` (which turns it into lower bound).
- Returning `ans - 1` instead of `ans`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How do `lower_bound` and `upper_bound` define the range of duplicates of x?**  
  **A**: If $x$ exists in the sorted array, all its occurrences reside continuously in the half-open index range `[lower_bound(x), upper_bound(x))`. The number of occurrences is exactly `upper_bound(x) - lower_bound(x)`.

- **Q2: What does `std::equal_range` do in C++ STL?**  
  **A**: `std::equal_range` runs both lower_bound and upper_bound in a single optimized pass, returning `std::pair<Iterator, Iterator>` representing `[first, last)` of equal elements in $\mathcal{O}(\log n)$ time.

- **Q3: Why is `upper_bound` useful for Ceil/Floor queries?**  
  **A**: The element immediately preceding `upper_bound(x)` (i.e. index `upper_bound(x) - 1`) is the greatest element $\le x$ (the Floor of $x$, assuming index $\ge 0$).

- **Q4: Can upper_bound be used to insert elements maintaining stability?**  
  **A**: Yes, inserting a new element at `upper_bound(x)` places it after all existing identical elements, preserving First-In-First-Out (FIFO) stability.

- **Q5: What if nums contains 64-bit floating point numbers?**  
  **A**: The same predicate logic applies cleanly: `nums[mid] > x` without truncation issues.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Upper Bound`, `C++ STL`, `Easy`
- **Related problems to practice next**:
- **Implement Lower Bound**: First index where element >= x.
- **Count Occurrences**: upper_bound(x) - lower_bound(x).
- **Floor and Ceil in Sorted Array**: Locating boundary numbers.
