# Selection Sort Algorithm (Step 2.1 — Sorting-I)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Selection Sort Algorithm](https://takeuforward.org/sorting/selection-sort-algorithm/)
- **Difficulty**: Easy
- **Statement**: Given an unsorted array of integers `nums`, sort the array in non-decreasing order using the Selection Sort algorithm. Selection Sort works by repeatedly finding the minimum element from the unsorted part and placing it at the beginning.

---

## 1. Problem, Restated

Given an unsorted array of integers `nums`, sort the array in non-decreasing order using the Selection Sort algorithm. Selection Sort works by repeatedly finding the minimum element from the unsorted part and placing it at the beginning.

- **Input**: Vector of integers `nums`.
- **Output**: Array sorted in non-decreasing order in-place.
- **Key Constraints**: $n$ up to $10^5$, integers can be positive, negative, or zero.

---

## 2. Intuition & Pattern

Select the Minimum & Swap Pattern. In each iteration $i$ from 0 to $n-2$, find the index `minIdx` of the minimum element in the subarray `nums[i..n-1]`. Then swap `nums[i]` with `nums[minIdx]`. After $i$ steps, the first $i+1$ elements are in their permanent sorted positions.

- **Underlying Pattern**: Sorting / Partitioning / Divide and Conquer.
- **The "Aha!" Moment**: Understanding how each comparison or swap establishes a permanent sorted boundary invariant.

---

## 3. Approach 1 — Brute Force

### Idea
Naive or non-optimized formulation of the sorting algorithm.

### C++17 Code
```cpp
// Selection sort has a fixed deterministic comparison structure.
// Naive implementation creates a new array and repeatedly finds/removes minimums:
#include <vector>
#include <climits>
using namespace std;

vector<int> selectionSortNaive(vector<int> nums) {
    int n = nums.size();
    vector<int> sortedArr;
    vector<bool> visited(n, false);
    for (int i = 0; i < n; i++) {
        int minVal = INT_MAX, minIdx = -1;
        for (int j = 0; j < n; j++) {
            if (!visited[j] && nums[j] < minVal) {
                minVal = nums[j];
                minIdx = j;
            }
        }
        visited[minIdx] = true;
        sortedArr.push_back(minVal);
    }
    return sortedArr;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n^2)$ — $n$ passes finding minimum in $n$ elements.
- **Space Complexity**: $\mathcal{O}(n)$ — auxiliary array and visited vector.
- **Why it's not good enough**: Uses extra memory and does not sort in-place.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
In-place Selection Sort: Maintain sorted prefix $[0..i-1]$ and unsorted suffix $[i..n-1]$. Find `minIdx` in $[i..n-1]$ and swap `nums[i]` with `nums[minIdx]`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

void selectionSort(vector<int>& nums) {
    int n = nums.size();
    for (int i = 0; i < n - 1; i++) {
        int minIdx = i;
        for (int j = i + 1; j < n; j++) {
            if (nums[j] < nums[minIdx]) {
                minIdx = j;
            }
        }
        if (minIdx != i) {
            swap(nums[i], nums[minIdx]);
        }
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n^2)$ in Best, Average, and Worst cases — total comparisons $= (n-1) + (n-2) + \dots + 1 = \frac{n(n-1)}{2} = \mathcal{O}(n^2)$.
- **Space Complexity**: $\mathcal{O}(1)$ — sorts strictly in-place with zero heap allocations.
- **Why this is optimal**: Minimizes memory writes: executes at most $n - 1$ swaps across the entire sorting process (useful when write operations are expensive, e.g. Flash/EEPROM).

---

## 6. Dry Run

`nums = [64, 25, 12, 22, 11]`

| Step | Trace / Comparisons | Result State |
|---|---|---|
| `i=0` | min in [64,25,12,22,11] is 11 at j=4 | swap(nums[0], nums[4]) -> [11, 25, 12, 22, 64] |
| `i=1` | min in [25,12,22,64] is 12 at j=2 | swap(nums[1], nums[2]) -> [11, 12, 25, 22, 64] |
| `i=2` | min in [25,22,64] is 22 at j=3 | swap(nums[2], nums[3]) -> [11, 12, 22, 25, 64] |
| `i=3` | min in [25,64] is 25 at j=3 | minIdx == i -> no swap -> [11, 12, 22, 25, 64] ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already sorted array (`[1, 2, 3, 4]` -> still takes $\mathcal{O}(n^2)$ comparisons).
- Reverse sorted array (`[5, 4, 3, 2, 1]` -> takes $n/2$ swaps).
- Array with duplicates (`[4, 2, 2, 1]` -> note: Selection Sort is NOT stable by default).

### Common Bugs to Avoid
- Running the outer loop until $n$ instead of $n-1$ (redundant last iteration).
- Swapping inside the inner loop instead of after finding the minimum index.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Is Selection Sort stable? Why or why not?**  
  **A**: No, standard Selection Sort is **unstable**. Example: `[4a, 4b, 1]` -> minimum is `1`, swapped with `4a` -> `[1, 4b, 4a]`. The relative order of duplicate `4`s is inverted. It can be made stable by shifting elements instead of swapping, taking $\mathcal{O}(n^2)$ writes.

- **Q2: When is Selection Sort preferred over QuickSort or MergeSort?**  
  **A**: When the cost of writing to memory is significantly higher than reading (e.g. Flash EEPROM memory writes where write cycles wear out hardware). Selection Sort makes at most $\mathcal{O}(n)$ swaps.

- **Q3: How does Selection Sort compare to HeapSort?**  
  **A**: HeapSort is essentially an accelerated Selection Sort: instead of finding the minimum linearly in $\mathcal{O}(n)$, a Min-Heap finds and extracts the minimum in $\mathcal{O}(\log n)$, reducing total time from $\mathcal{O}(n^2)$ to $\mathcal{O}(n \log n)$.

- **Q4: Can Selection Sort be adapted for a Singly Linked List?**  
  **A**: Yes, iterate through nodes, find node with minimum value in remaining list, and swap node values (or relink pointers) in $\mathcal{O}(n^2)$ time and $\mathcal{O}(1)$ space.

- **Q5: How to perform Bi-directional Selection Sort (Cocktail/Double Selection Sort)?**  
  **A**: In each pass, simultaneously find both the Minimum and Maximum elements in the unsorted portion, placing the minimum at the left boundary and maximum at the right boundary, reducing passes by $50\%$ (comparisons remain $\mathcal{O}(n^2)$).


---

## 9. Tags & Related Problems

- **Tags**: `Sorting`, `Comparison-Sort`, `In-Place`
- **Related problems to practice next**:
- **Bubble Sort**: Another $\mathcal{O}(n^2)$ algorithm swapping adjacent inverted pairs.
- **Insertion Sort**: Adaptive $\mathcal{O}(n^2)$ sort with $\mathcal{O}(n)$ best-case for sorted data.
- **Heap Sort**: Optimal $\mathcal{O}(n \log n)$ evolution of Selection Sort using binary heaps.
