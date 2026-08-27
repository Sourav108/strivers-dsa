# Largest Element in an Array (Step 3.1 — Easy Array Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [TakeUForward - Largest Element](https://takeuforward.org/data-structure/find-the-largest-element-in-an-array/)
- **Difficulty**: Easy
- **Statement**: Given an unsorted array of integers `nums`, find and return the maximum value. The array has size $n \ge 1$ and can contain positive, negative, or duplicate numbers.

---

## 1. Problem, Restated

Find the largest integer in an unsorted list of numbers. Constraints that matter: $n$ can range up to $10^5$, elements can be negative, and the array is not sorted.

---

## 2. Intuition & Pattern

Linear Scan (Running Maximum). To find the maximum without rearranging elements, assume the first element `nums[0]` is the largest seen so far. As you traverse through the array, whenever you find an element strictly greater than `maxVal`, update `maxVal`. This guarantees you inspect every candidate in a single pass.

---

## 3. Approach 1 — Brute Force

### Idea
Pairwise Comparison: Compare every element against all other elements using two nested loops. An element is the maximum if no element in the array is strictly greater than it.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int findLargestBrute(const vector<int>& nums) {
    int n = nums.size();
    for (int i = 0; i < n; i++) {
        bool isMax = true;
        for (int j = 0; j < n; j++) {
            if (nums[j] > nums[i]) {
                isMax = false;
                break;
            }
        }
        if (isMax) return nums[i];
    }
    return nums[0];
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n^2)$ — for each of the $n$ elements, we compare against up to $n$ other elements in the array.
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary memory.
- **Why it's not good enough**: Performs $n^2$ redundant pairwise comparisons across the entire array when maintaining a single running scalar is sufficient.

---

## 4. Approach 2 — Better

### Idea
Sorting Approach: Sort a copy of the array in ascending order. The largest element will naturally be placed at the last index $n - 1$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

int findLargestBetter(vector<int> nums) { // pass-by-value prevents mutating caller array
    sort(nums.begin(), nums.end());
    return nums.back();
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n)$ — dominated by comparison sort.
- **Space Complexity**: $\mathcal{O}(n)$ — pass-by-value copy of array.
- **Why it's still not optimal**: Rearranges all $n$ elements, performing unnecessary comparisons when we only need the single maximum.

---

## 5. Approach 3 — Optimal

### Idea
Single-Pass Linear Scan: Maintain `maxVal = nums[0]`. Traverse indices 1 to $n-1$, updating `maxVal = max(maxVal, nums[i])` at each element.

### C++17 Code
```cpp
#include <vector>
#include <stdexcept>
using namespace std;

int findLargestOptimal(const vector<int>& nums) {
    if (nums.empty()) {
        throw invalid_argument("Array must not be empty.");
    }
    int maxVal = nums[0];
    for (size_t i = 1; i < nums.size(); i++) {
        if (nums[i] > maxVal) {
            maxVal = nums[i];
        }
    }
    return maxVal;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — single linear pass examining each element exactly once.
- **Space Complexity**: $\mathcal{O}(1)$ — uses only a single integer variable `maxVal`.
- **Why this is optimal**: Any deterministic algorithm on an unsorted array has an information-theoretic lower bound of $\Omega(n)$ because the maximum element could reside at the very last unexamined index.

---

## 6. Dry Run

`nums = [3, 2, 1, 5, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | maxVal = nums[0] = 3 | maxVal = 3 |
| `i=1` | nums[1] = 2 < 3 -> no change | maxVal = 3 |
| `i=2` | nums[2] = 1 < 3 -> no change | maxVal = 3 |
| `i=3` | nums[3] = 5 > 3 -> maxVal = 5 | maxVal = 5 |
| `i=4` | nums[4] = 2 < 5 -> no change | maxVal = 5 (Final Output: 5) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **Empty vector**: Guarded with explicit check throwing `std::invalid_argument` (prevents undefined behavior on `nums[0]`).
- **Single element array** (`[42]` -> loop doesn't execute, returns `42`).
- **All negative numbers** (`[-10, -50, -2]` -> returns `-2` because `maxVal` starts at `nums[0] = -10`, not `0`).
- **Duplicate maximums** (`[7, 3, 7]` -> returns `7`).

### Common Bugs to Avoid
- Initializing `maxVal = 0` instead of `nums[0]` or `INT_MIN`, which fails for arrays containing only negative numbers.
- Accessing `nums[0]` without checking if the array is empty, leading to undefined memory access.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How would you find the maximum element in an array distributed across 1,000 machines (MapReduce / Distributed System)?**  
  **A**: Run a local linear scan on each of the 1,000 worker nodes to compute local maximums in $\mathcal{O}(n/1000)$ parallel time. Then stream the 1,000 local maximums to a central reducer node to compute the global maximum in $\mathcal{O}(1000)$ time.

- **Q2: What if elements arrive in an infinite real-time stream?**  
  **A**: Maintain a single state variable `currentMax = INT_MIN`. As each stream element $x$ arrives, update `currentMax = max(currentMax, x)` in $\mathcal{O}(1)$ time and $\mathcal{O}(1)$ space.

- **Q3: Can we use CPU SIMD vectorization (AVX-512) to speed up the search in C++?**  
  **A**: Yes, load 16 32-bit integers into a 512-bit vector register (`__m512i`) and use `_mm512_max_epi32` instructions to compute 16 parallel maximums per CPU clock cycle, achieving a theoretical $\approx 16\times$ speedup.

- **Q4: What is the theoretical minimum number of comparisons needed to find the maximum among $n$ elements?**  
  **A**: Exactly $n - 1$ comparisons. In an adversary tournament model, every element except the maximum must lose at least one comparison, giving an information-theoretic lower bound of $\Omega(n)$.

- **Q5: How to simultaneously find both the Maximum and Minimum elements with the absolute fewest comparisons?**  
  **A**: Process elements in pairs $(x, y)$: compare $x$ and $y$ (1 comparison), compare the larger with `maxVal` (1 comparison), and the smaller with `minVal` (1 comparison). This requires $\approx 3 \lfloor n/2 \rfloor \approx 1.5n$ comparisons instead of $2n$.

---

## 9. Tags & Related Problems

- **Tags**: `Array`, `Linear Scan`, `Basics`
- **Related problems to practice next**:
  - **Second Largest Element in an Array**: Tracks top two running maximums in single pass.
  - **Leaders in an Array**: Tracks running suffix maximum from right to left.
  - **Kth Largest Element in an Array**: Finds arbitrary K-th largest using Quickselect / Min-Heap.
