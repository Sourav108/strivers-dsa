# Quick Sort Algorithm (Step 2.2 — Sorting-II)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Quick Sort Algorithm](https://takeuforward.org/data-structure/quick-sort-algorithm/)
- **Difficulty**: Medium
- **Statement**: Sort an array `nums` in non-decreasing order using Quick Sort. Quick Sort is an in-place Divide and Conquer algorithm that picks a 'pivot' element, partitions the array around the pivot (all elements smaller than pivot to its left, larger to its right), and recursively sorts the left and right partitions.

---

## 1. Problem, Restated

Sort an array `nums` in non-decreasing order using Quick Sort. Quick Sort is an in-place Divide and Conquer algorithm that picks a 'pivot' element, partitions the array around the pivot (all elements smaller than pivot to its left, larger to its right), and recursively sorts the left and right partitions.

- **Input**: Vector of integers `nums`.
- **Output**: Array sorted in non-decreasing order in-place.
- **Key Constraints**: $n$ up to $10^5$, integers can be positive, negative, or zero.

---

## 2. Intuition & Pattern

Pivot Partitioning. 1) Pick pivot (e.g. `pivot = nums[low]`). 2) Partition: pointer `i` scans rightward for elements $> \text{pivot}$, pointer `j` scans leftward for elements $\le \text{pivot}$. When $i < j$, swap `nums[i]` and `nums[j]`. When $i \ge j$, swap `pivot` with `nums[j]`. Now `nums[j]` is at its permanent sorted position. 3) Recurse: `quickSort(low, j - 1)` and `quickSort(j + 1, high)`.

- **Underlying Pattern**: Sorting / Partitioning / Divide and Conquer.
- **The "Aha!" Moment**: Understanding how each comparison or swap establishes a permanent sorted boundary invariant.

---

## 3. Approach 1 — Brute Force

### Idea
Naive or non-optimized formulation of the sorting algorithm.

### C++17 Code
```cpp
// Quick sort with extra auxiliary memory:
#include <vector>
using namespace std;

vector<int> quickSortNaive(vector<int> nums) {
    if (nums.size() <= 1) return nums;
    int pivot = nums[0];
    vector<int> left, equal, right;
    for (int x : nums) {
        if (x < pivot) left.push_back(x);
        else if (x == pivot) equal.push_back(x);
        else right.push_back(x);
    }
    vector<int> sortedLeft = quickSortNaive(left);
    vector<int> sortedRight = quickSortNaive(right);
    sortedLeft.insert(sortedLeft.end(), equal.begin(), equal.end());
    sortedLeft.insert(sortedLeft.end(), sortedRight.begin(), sortedRight.end());
    return sortedLeft;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n)$ average, $\mathcal{O}(n^2)$ worst case.
- **Space Complexity**: $\mathcal{O}(n \log n)$ auxiliary vector allocations.
- **Why it's not good enough**: Fails the strict $\mathcal{O}(1)$ auxiliary space in-place requirement of standard QuickSort.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
In-Place Hoare / Lomuto Partitioning: Partition strictly in-place using two scanning pointers `i` and `j`, requiring zero auxiliary array allocations.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

int partition(vector<int>& arr, int low, int high) {
    int pivot = arr[low];
    int i = low;
    int j = high;
    
    while (i < j) {
        while (i <= high - 1 && arr[i] <= pivot) i++;
        while (j >= low + 1 && arr[j] > pivot) j--;
        if (i < j) swap(arr[i], arr[j]);
    }
    swap(arr[low], arr[j]); // place pivot at partition index
    return j;
}

void quickSortHelper(vector<int>& arr, int low, int high) {
    if (low < high) {
        int pIndex = partition(arr, low, high);
        quickSortHelper(arr, low, pIndex - 1);
        quickSortHelper(arr, pIndex + 1, high);
    }
}

void quickSort(vector<int>& nums) {
    if (nums.size() <= 1) return;
    quickSortHelper(nums, 0, (int)nums.size() - 1);
}
```

### Complexity Derivation
- **Time Complexity**: Best & Average Case: $\mathcal{O}(n \log n)$. Worst Case: $\mathcal{O}(n^2)$ (when pivot is consistently the smallest or largest element).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary heap memory + $\mathcal{O}(\log n)$ average recursion stack depth ($\mathcal{O}(n)$ worst-case stack depth).
- **Why this is optimal**: Fastest practical general-purpose internal sorting algorithm due to minimal data movement and exceptional CPU cache locality.

---

## 6. Dry Run

`nums = [4, 6, 2, 5, 7, 9, 1, 3]`, `low=0, high=7`

| Step | Trace / Comparisons | Result State |
|---|---|---|
| `Pivot=4` | i stops at 6 (6>4), j stops at 3 (3<=4) -> swap(6, 3) -> [4, 3, 2, 5, 7, 9, 1, 6] | i=1, j=7 |
| `Continue` | i stops at 5 (5>4), j stops at 1 (1<=4) -> swap(5, 1) -> [4, 3, 2, 1, 7, 9, 5, 6] | i=3, j=6 |
| `Cross` | i stops at 7 (idx 4), j stops at 1 (idx 3) -> i >= j -> exit loop | Crossed pointers |
| `Pivot Swap` | swap(arr[low], arr[j]) -> swap(4, 1) -> [1, 3, 2, 4, 7, 9, 5, 6] | Pivot 4 is placed at index 3 ✅ |
| `Recurse` | Recurse on Left [1, 3, 2] and Right [7, 9, 5, 6] | Subproblems sorted independently |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already sorted array with first element as pivot -> creates unbalanced partitions $(0, n-1)$, leading to $\mathcal{O}(n^2)$ worst case.
- All elements equal (`[5, 5, 5, 5]` -> 3-way Dutch National Flag partitioning resolves in $\mathcal{O}(n)$).
- Two-element array (`[2, 1]` -> single partition swap yields `[1, 2]`).

### Common Bugs to Avoid
- Missing `i <= high - 1` or `j >= low + 1` bounds in pointer increment loops, causing out-of-bounds memory access.
- Swapping `arr[low]` with `arr[i]` instead of `arr[j]` after loop termination.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to prevent the O(n^2) worst-case in QuickSort?**  
  **A**: 1) **Randomized QuickSort**: pick random pivot `int randIdx = low + rand() % (high - low + 1); swap(arr[low], arr[randIdx]);`. 2) **Median-of-Three**: choose median of `arr[low]`, `arr[mid]`, and `arr[high]`. 3) **Introsort**: switch to HeapSort if recursion depth exceeds $2 \log n$.

- **Q2: Why is QuickSort preferred over MergeSort for arrays?**  
  **A**: 1) **In-place**: QuickSort needs $\mathcal{O}(1)$ auxiliary heap space vs MergeSort's $\mathcal{O}(n)$. 2) **Cache Locality**: Sequential scanning pointers maximize CPU L1 cache hits with fewer memory writes.

- **Q3: Is QuickSort stable?**  
  **A**: No, standard QuickSort is **unstable** because non-adjacent elements are swapped during partitioning. Example: `[5a, 3, 5b, 2]`, pivot 5a swaps with 2, placing 5a after 5b.

- **Q4: How does 3-Way QuickSort (Dutch National Flag) handle duplicate elements?**  
  **A**: It partitions array into 3 sections: $[< \text{pivot}]$, $[== \text{pivot}]$, and $[> \text{pivot}]$. Recursion only processes the strictly smaller and strictly larger sections, sorting all-equal arrays in $\mathcal{O}(n)$ time.

- **Q5: What is Quickselect and how does it find the K-th smallest element in O(n) average time?**  
  **A**: Quickselect only recurses into the SINGLE partition that contains index $K$, discarding the other half. Recurrence $T(n) = T(n/2) + \mathcal{O}(n) \implies \mathcal{O}(n)$ average time.


---

## 9. Tags & Related Problems

- **Tags**: `Sorting`, `Divide-and-Conquer`, `In-Place`, `Partitioning`
- **Related problems to practice next**:
- **Sort Colors (Dutch National Flag)**: 3-way partitioning core algorithm.
- **Kth Largest Element in an Array**: Quickselect selection algorithm.
- **Merge Sort Algorithm**: Stable $\mathcal{O}(n \log n)$ sorting counterpart.
