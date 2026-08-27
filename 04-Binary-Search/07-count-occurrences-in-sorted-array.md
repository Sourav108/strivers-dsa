# Count Occurrences of a Number in Sorted Array (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Count Occurrences of a Number in Sorted Array](https://takeuforward.org/data-structure/count-occurrences-in-sorted-array/)
- **Difficulty**: Easy
- **Statement**: Given a sorted array `nums` of $n$ integers and a target integer $x$, count the number of occurrences of $x$ in `nums`. You must achieve $\mathcal{O}(\log n)$ runtime complexity.

---

## 1. Problem, Restated

Find how many times a given number $x$ appears in a sorted array. If $x$ is not present, return 0.

- **Input**: Vector of integers `nums`, plus query parameters.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, elements fit in 32-bit signed integers.

---

## 2. Intuition & Pattern

In a sorted array, all duplicate occurrences of $x$ are contiguous. If the first occurrence is at index `first` and the last occurrence is at index `last`, the total count is simply `(last - first + 1)`. If $x$ does not appear, `first == -1`, so return 0.

- **Underlying Pattern**: `Range Length via First and Last Occurrences`.
- **The "Aha!" Moment**: Recognizing how monotonic invariants or gradient direction eliminate half the search space.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Iterate through the array and count elements equal to $x$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int countOccurrencesLinear(const vector<int>& nums, int x) {
    int cnt = 0;
    for (int num : nums) {
        if (num == x) cnt++;
        else if (num > x) break;
    }
    return cnt;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Fails to meet $\mathcal{O}(\log n)$ time requirement.

---

## 4. Approach 2 — Better

### Idea
STL upper_bound minus lower_bound: `return upper_bound(x) - lower_bound(x)`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

int countOccurrencesSTL(const vector<int>& nums, int x) {
    auto lb = lower_bound(nums.begin(), nums.end(), x);
    auto ub = upper_bound(nums.begin(), nums.end(), x);
    return ub - lb;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log n)$ — two binary search calls.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's still not optimal**: Uses STL abstractions; writing the underlying first/last binary searches demonstrates core algorithmic mastery.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Binary Search First and Last Index: 1) Find `first` index of $x$. If `first == -1`, return 0. 2) Find `last` index of $x$. 3) Return `last - first + 1`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int findFirst(const vector<int>& nums, int x) {
    int low = 0, high = (int)nums.size() - 1, first = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == x) {
            first = mid;
            high = mid - 1;
        } else if (nums[mid] < x) low = mid + 1;
        else high = mid - 1;
    }
    return first;
}

int findLast(const vector<int>& nums, int x) {
    int low = 0, high = (int)nums.size() - 1, last = -1;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        if (nums[mid] == x) {
            last = mid;
            low = mid + 1;
        } else if (nums[mid] < x) low = mid + 1;
        else high = mid - 1;
    }
    return last;
}

int countOccurrences(const vector<int>& nums, int x) {
    int first = findFirst(nums, x);
    if (first == -1) return 0;
    int last = findLast(nums, x);
    return last - first + 1;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — two binary searches of $\log_2 n$ operations each.
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary space.
- **Why this is optimal**: Mathematically impossible to count in fewer comparisons without preprocessing, matching $\Omega(\log n)$ comparison bound.

---

## 6. Dry Run

`nums = [1, 1, 2, 2, 2, 2, 3]`, `x = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `findFirst(2)` | Discovers leftmost index at mid=2 | first = 2 |
| `findLast(2)` | Discovers rightmost index at mid=5 | last = 5 |
| `Count calculation` | count = last - first + 1 = 5 - 2 + 1 = 4 | Result: 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- x is not in array (`x = 4` -> `first = -1`, returns 0).
- All elements equal to x (`nums = [2, 2, 2], x = 2` -> `last=2, first=0`, returns 3).
- x occurs exactly once (`nums = [1, 2, 3], x = 2` -> `2 - 2 + 1 = 1`).

### Common Bugs to Avoid
- Forgetting `+ 1` in `last - first + 1` (fence-post error).
- Calling `findLast` when `first == -1`, doing redundant work.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does `std::equal_range` optimize this in C++?**  
  **A**: `std::equal_range(nums.begin(), nums.end(), x)` returns a pair of iterators `[first, last)`. The distance `std::distance(p.first, p.second)` gives the count in $\mathcal{O}(\log n)$.

- **Q2: Why is hash map frequency counting inferior here?**  
  **A**: Building a hash map takes $\mathcal{O}(n)$ time and $\mathcal{O}(n)$ space. Binary search answers the query in $\mathcal{O}(\log n)$ time and $\mathcal{O}(1)$ space on sorted data.

- **Q3: What if we have Q queries asking for counts of different numbers in a static sorted array?**  
  **A**: Each query takes $\mathcal{O}(\log n)$ time, leading to $\mathcal{O}(Q \log n)$ total time and $\mathcal{O}(1)$ space.

- **Q4: How would you parallelize this across multiple CPU cores?**  
  **A**: Run `findFirst` on Thread 1 and `findLast` on Thread 2 simultaneously in parallel.

- **Q5: Can this count elements in a sorted matrix?**  
  **A**: Yes, apply binary search on row bounds or flatten matrix to 1D.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Array`, `Counting`, `Easy`
- **Related problems to practice next**:
- **First and Last Occurrences**: Underlying building block.
- **Implement Lower Bound**: Provides first index.
- **Implement Upper Bound**: Provides last index + 1.
