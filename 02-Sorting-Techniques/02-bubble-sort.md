# Bubble Sort Algorithm (Step 2.1 — Sorting-I)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Bubble Sort Algorithm](https://takeuforward.org/data-structure/bubble-sort-algorithm/)
- **Difficulty**: Easy
- **Statement**: Sort an array `nums` in non-decreasing order using Bubble Sort. Bubble Sort repeatedly steps through the list, compares adjacent elements, and swaps them if they are in the wrong order until the entire list is sorted.

---

## 1. Problem, Restated

Sort an array `nums` in non-decreasing order using Bubble Sort. Bubble Sort repeatedly steps through the list, compares adjacent elements, and swaps them if they are in the wrong order until the entire list is sorted.

- **Input**: Vector of integers `nums`.
- **Output**: Array sorted in non-decreasing order in-place.
- **Key Constraints**: $n$ up to $10^5$, integers can be positive, negative, or zero.

---

## 2. Intuition & Pattern

Adjacent Pair Swapping & Bubble Up. In each pass $i$ from 0 to $n-1$, compare adjacent pairs `nums[j]` and `nums[j+1]`. If `nums[j] > nums[j+1]`, swap them. The largest unsorted element 'bubbles up' to its correct final position at the end of the array. Adding a `swapped` boolean flag enables early termination in $\mathcal{O}(n)$ time for already-sorted inputs.

- **Underlying Pattern**: Sorting / Partitioning / Divide and Conquer.
- **The "Aha!" Moment**: Understanding how each comparison or swap establishes a permanent sorted boundary invariant.

---

## 3. Approach 1 — Brute Force

### Idea
Naive or non-optimized formulation of the sorting algorithm.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

void bubbleSortBrute(vector<int>& nums) {
    int n = nums.size();
    for (int i = 0; i < n - 1; i++) {
        for (int j = 0; j < n - i - 1; j++) {
            if (nums[j] > nums[j + 1]) {
                swap(nums[j], nums[j + 1]);
            }
        }
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n^2)$ in all cases (always executes $\frac{n(n-1)}{2}$ comparisons even on sorted arrays).
- **Space Complexity**: $\mathcal{O}(1)$ — in-place.
- **Why it's not good enough**: Fails to detect when the array is already sorted, performing redundant checks.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Optimized Bubble Sort with Early Exit: Maintain a boolean `swapped`. If no swaps occur in a full pass, the array is guaranteed sorted and we break immediately.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

void bubbleSort(vector<int>& nums) {
    int n = nums.size();
    for (int i = 0; i < n - 1; i++) {
        bool swapped = false;
        for (int j = 0; j < n - i - 1; j++) {
            if (nums[j] > nums[j + 1]) {
                swap(nums[j], nums[j + 1]);
                swapped = true;
            }
        }
        // If no two elements were swapped in inner loop, array is sorted
        if (!swapped) break;
    }
}
```

### Complexity Derivation
- **Time Complexity**: Best Case: $\mathcal{O}(n)$ (already sorted). Average & Worst Case: $\mathcal{O}(n^2)$ (reverse sorted).
- **Space Complexity**: $\mathcal{O}(1)$ — strictly in-place.
- **Why this is optimal**: Achieves $\mathcal{O}(n)$ linear time verification while maintaining algorithmic stability.

---

## 6. Dry Run

`nums = [5, 1, 4, 2, 8]`

| Step | Trace / Comparisons | Result State |
|---|---|---|
| `Pass 1` | 5>1 swap [1,5,4,2,8], 5>4 swap [1,4,5,2,8], 5>2 swap [1,4,2,5,8], 5<=8 no swap | Result: [1, 4, 2, 5, 8] (8 in place) |
| `Pass 2` | 1<=4 ok, 4>2 swap [1,2,4,5,8], 4<=5 ok | Result: [1, 2, 4, 5, 8] (5 in place) |
| `Pass 3` | 1<=2 ok, 2<=4 ok | No swaps! `swapped == false` -> early exit ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already sorted array (`[1, 2, 3]` -> terminates after 1 pass in $\mathcal{O}(n)$).
- All identical elements (`[4, 4, 4]` -> terminates after 1 pass in $\mathcal{O}(n)$).
- Reverse sorted array (`[3, 2, 1]` -> maximum swaps $= \frac{n(n-1)}{2}$).
- Single element array (`[1]` -> 0 iterations).

### Common Bugs to Avoid
- Inner loop going up to `n - 1` instead of `n - i - 1` (re-inspecting already sorted suffix).
- Using `>=` instead of `>` in comparison, destroying stability.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Is Bubble Sort stable?**  
  **A**: Yes! Because elements are swapped only when `nums[j] > nums[j+1]`. When two equal elements are compared, no swap occurs, preserving their original relative order.

- **Q2: What is the relationship between Bubble Sort swaps and Array Inversions?**  
  **A**: Each swap in Bubble Sort resolves exactly ONE inversion pair $(i, j)$ where $i < j$ and $nums[i] > nums[j]$. The total number of swaps executed by Bubble Sort equals the exact inversion count of the array.

- **Q3: What is the Cocktail Shaker Sort (Bidirectional Bubble Sort)?**  
  **A**: Cocktail Sort traverses alternatingly from left-to-right (bubbling the largest element to the end) and right-to-left (bubbling the smallest element to the front), resolving 'turtles' (small values near the end) much faster.

- **Q4: Can Bubble Sort be parallelized?**  
  **A**: Yes, **Odd-Even Transposition Sort** parallelizes Bubble Sort: in odd steps, all odd index pairs $(2i+1, 2i+2)$ are compared and swapped concurrently; in even steps, all even pairs $(2i, 2i+1)$ are swapped concurrently on GPU/SIMD in $\mathcal{O}(n)$ parallel time.

- **Q5: Why is Bubble Sort rarely used in production?**  
  **A**: Because of poor cache locality and high number of memory writes ($\mathcal{O}(n^2)$ writes vs Selection Sort's $\mathcal{O}(n)$ writes).


---

## 9. Tags & Related Problems

- **Tags**: `Sorting`, `Stable-Sort`, `Adaptive`
- **Related problems to practice next**:
- **Recursive Bubble Sort**: Recursive formulation of Bubble Sort.
- **Count Inversions in an Array**: Counts the exact number of swaps Bubble Sort would make.
- **Insertion Sort**: Another adaptive stable $\mathcal{O}(n^2)$ sorting algorithm.
