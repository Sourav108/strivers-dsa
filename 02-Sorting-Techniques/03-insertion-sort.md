# Insertion Sort Algorithm (Step 2.1 — Sorting-I)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Insertion Sort Algorithm](https://takeuforward.org/data-structure/insertion-sort-algorithm/)
- **Difficulty**: Easy
- **Statement**: Sort an array `nums` in non-decreasing order using Insertion Sort. Insertion Sort builds the final sorted array one item at a time by repeatedly taking the next element and inserting it into its correct position among the already-sorted prefix.

---

## 1. Problem, Restated

Sort an array `nums` in non-decreasing order using Insertion Sort. Insertion Sort builds the final sorted array one item at a time by repeatedly taking the next element and inserting it into its correct position among the already-sorted prefix.

- **Input**: Vector of integers `nums`.
- **Output**: Array sorted in non-decreasing order in-place.
- **Key Constraints**: $n$ up to $10^5$, integers can be positive, negative, or zero.

---

## 2. Intuition & Pattern

Card Hand Insertion Pattern. Think of sorting a hand of playing cards: you pick card `key = nums[i]`, compare it with cards to its left (`nums[j]`), shift larger cards one position to the right, and insert `key` into the vacated slot. If the array is already sorted, each element makes 1 comparison and 0 shifts ($\mathcal{O}(n)$ time).

- **Underlying Pattern**: Sorting / Partitioning / Divide and Conquer.
- **The "Aha!" Moment**: Understanding how each comparison or swap establishes a permanent sorted boundary invariant.

---

## 3. Approach 1 — Brute Force

### Idea
Naive or non-optimized formulation of the sorting algorithm.

### C++17 Code
```cpp
// Insertion sort naturally operates by shifting.
// Naive swap-based insertion does multiple full swaps instead of shifts:
#include <vector>
#include <algorithm>
using namespace std;

void insertionSortSwapBased(vector<int>& nums) {
    int n = nums.size();
    for (int i = 1; i < n; i++) {
        int j = i;
        while (j > 0 && nums[j - 1] > nums[j]) {
            swap(nums[j - 1], nums[j]);
            j--;
        }
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n^2)$ worst case, $\mathcal{O}(n)$ best case.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Performs 3 memory writes per swap inside the inner loop; shifting only writes once.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
In-place Shift Insertion: Save `key = nums[i]`. Shift all elements in sorted prefix greater than `key` rightward by 1 slot. Insert `key` at index `j + 1`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

void insertionSort(vector<int>& nums) {
    int n = nums.size();
    for (int i = 1; i < n; i++) {
        int key = nums[i];
        int j = i - 1;
        
        // Shift elements of nums[0..i-1] that are greater than key to one position ahead
        while (j >= 0 && nums[j] > key) {
            nums[j + 1] = nums[j];
            j--;
        }
        nums[j + 1] = key;
    }
}
```

### Complexity Derivation
- **Time Complexity**: Best Case: $\mathcal{O}(n)$ (already sorted). Average & Worst Case: $\mathcal{O}(n^2)$ (reverse sorted).
- **Space Complexity**: $\mathcal{O}(1)$ — in-place.
- **Why this is optimal**: Optimal for small arrays ($n \le 16-32$) and nearly sorted arrays (runs in $\mathcal{O}(n + d)$ where $d$ is inversion count).

---

## 6. Dry Run

`nums = [12, 11, 13, 5, 6]`

| Step | Trace / Comparisons | Result State |
|---|---|---|
| `i=1, key=11` | 12 > 11 -> shift 12 right -> nums[1]=12, nums[0]=11 | [11, 12, 13, 5, 6] |
| `i=2, key=13` | 12 <= 13 -> no shift -> nums[2]=13 | [11, 12, 13, 5, 6] |
| `i=3, key=5` | 13>5, 12>5, 11>5 -> shift all -> insert 5 at nums[0] | [5, 11, 12, 13, 6] |
| `i=4, key=6` | 13>6, 12>6, 11>6 -> shift 13,12,11 -> insert 6 at nums[1] | [5, 6, 11, 12, 13] ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already sorted array (`[1, 2, 3, 4]` -> takes exactly $n-1$ comparisons, $\mathcal{O}(n)$).
- Reverse sorted array (`[4, 3, 2, 1]` -> maximum shifts $\frac{n(n-1)}{2}$).
- Array with duplicate elements (`[3, 1, 3, 2]` -> stable preservation).

### Common Bugs to Avoid
- Loop condition `j > 0` instead of `j >= 0`, failing to insert at index 0.
- Writing `nums[j] = key` instead of `nums[j + 1] = key` after loop termination.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Insertion Sort used in standard library sort hybrids (Timsort, Introsort)?**  
  **A**: For small array partitions ($n \le 16-32$), Insertion Sort has low constant factors, zero recursion overhead, and excellent CPU L1 cache locality, outperforming QuickSort and MergeSort.

- **Q2: What is Binary Insertion Sort?**  
  **A**: Instead of linear scan to find insertion slot, use Binary Search in $\mathcal{O}(\log i)$ comparisons. Total comparisons reduce to $\mathcal{O}(n \log n)$, but shifts remain $\mathcal{O}(n^2)$ in an array.

- **Q3: Is Insertion Sort stable?**  
  **A**: Yes, because elements are shifted only when `nums[j] > key`. Equal elements never trigger shifts, preserving original relative order.

- **Q4: What is the time complexity on nearly sorted arrays with k misplaced elements?**  
  **A**: If each element is at most $k$ positions away from its target position, Insertion Sort runs in $\mathcal{O}(n \cdot k)$ time (linear $\mathcal{O}(n)$ when $k = \mathcal{O}(1)$).

- **Q5: How does Shell Sort improve upon Insertion Sort?**  
  **A**: Shell Sort compares elements separated by a gap sequence (e.g. $gap = n/2, n/4... 1$), allowing distant elements to jump across the array and resolving multiple inversions per shift, reducing time to $\mathcal{O}(n^{1.3})$ or $\mathcal{O}(n \log^2 n)$.


---

## 9. Tags & Related Problems

- **Tags**: `Sorting`, `Stable-Sort`, `Online-Algorithm`, `Adaptive`
- **Related problems to practice next**:
- **Recursive Insertion Sort**: Recursive divide and conquer formulation.
- **Selection Sort**: Non-adaptive selection counterpart.
- **Shell Sort**: Diminishing increment gap generalization of Insertion Sort.
