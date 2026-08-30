# Merge Sort Algorithm (Step 2.2 — Sorting-II)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Merge Sort Algorithm](https://takeuforward.org/data-structure/merge-sort-algorithm/)
- **Difficulty**: Medium
- **Statement**: Sort an array `nums` in non-decreasing order using Merge Sort. Merge Sort is a classic Divide and Conquer algorithm that divides the array into two equal halves, recursively sorts each half, and merges the sorted halves.

---

## 1. Problem, Restated

Sort an array `nums` in non-decreasing order using Merge Sort. Merge Sort is a classic Divide and Conquer algorithm that divides the array into two equal halves, recursively sorts each half, and merges the sorted halves.

- **Input**: Vector of integers `nums`.
- **Output**: Array sorted in non-decreasing order in-place.
- **Key Constraints**: $n$ up to $10^5$, integers can be positive, negative, or zero.

---

## 2. Intuition & Pattern

Divide & Conquer + Two-Pointer Merge. 1) Divide: split array at `mid = low + (high - low) / 2`. 2) Conquer: recursively call `mergeSort(low, mid)` and `mergeSort(mid + 1, high)`. 3) Combine: merge two sorted subarrays into a temporary buffer in $\mathcal{O}(n)$ time and copy back. Recurrence: $T(n) = 2T(n/2) + \mathcal{O}(n) \implies \mathcal{O}(n \log n)$ by Master Theorem.

- **Underlying Pattern**: Sorting / Partitioning / Divide and Conquer.
- **The "Aha!" Moment**: Understanding how each comparison or swap establishes a permanent sorted boundary invariant.

---

## 3. Approach 1 — Brute Force

### Idea
Naive or non-optimized formulation of the sorting algorithm.

### C++17 Code
```cpp
// Merge sort is inherently O(n log n).
// Naive implementation allocates new vectors in each recursive level:
#include <vector>
using namespace std;

vector<int> mergeSortNaive(vector<int> nums) {
    if (nums.size() <= 1) return nums;
    int mid = nums.size() / 2;
    vector<int> left(nums.begin(), nums.begin() + mid);
    vector<int> right(nums.begin() + mid, nums.end());
    left = mergeSortNaive(left);
    right = mergeSortNaive(right);
    vector<int> res;
    int i = 0, j = 0;
    while (i < left.size() && j < right.size()) {
        if (left[i] <= right[j]) res.push_back(left[i++]);
        else res.push_back(right[j++]);
    }
    while (i < left.size()) res.push_back(left[i++]);
    while (j < right.size()) res.push_back(right[j++]);
    return res;
}
```

### Java Code
```java
class Solution {
    // Merge sort is inherently O(n log n).
    // Naive implementation allocates new vectors in each recursive level:
    int[] mergeSortNaive(int[] nums) {
        if (nums.length <= 1) return nums;
        int mid = nums.length / 2;
        int[] left(nums.begin(), nums.begin() + mid);
        int[] right(nums.begin() + mid, nums.end());
        left = mergeSortNaive(left);
        right = mergeSortNaive(right);
        int[] res;
        int i = 0, j = 0;
        while (i < left.length && j < right.length) {
            if (left[i] <= right[j]) res.add(left[i++]);
            else res.add(right[j++]);
        }
        while (i < left.length) res.add(left[i++]);
        while (j < right.length) res.add(right[j++]);
        return res;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n)$ time.
- **Space Complexity**: $\mathcal{O}(n \log n)$ — allocates temporary subvectors at every recursion level.
- **Why it's not good enough**: Excessive dynamic memory allocations and vector copying degrade real-world runtime.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Single Pre-allocated Temporary Buffer: Allocate a single `temp` buffer of size $n$ at the top level and reuse it across all recursive merges, eliminating reallocations.

### C++17 Code
```cpp
#include <vector>
using namespace std;

void merge(vector<int>& arr, int low, int mid, int high, vector<int>& temp) {
    int left = low, right = mid + 1, k = low;
    
    while (left <= mid && right <= high) {
        if (arr[left] <= arr[right]) {
            temp[k++] = arr[left++];
        } else {
            temp[k++] = arr[right++];
        }
    }
    while (left <= mid) temp[k++] = arr[left++];
    while (right <= high) temp[k++] = arr[right++];
    
    for (int i = low; i <= high; i++) {
        arr[i] = temp[i];
    }
}

void mergeSortHelper(vector<int>& arr, int low, int high, vector<int>& temp) {
    if (low >= high) return;
    int mid = low + (high - low) / 2;
    mergeSortHelper(arr, low, mid, temp);
    mergeSortHelper(arr, mid + 1, high, temp);
    merge(arr, low, mid, high, temp);
}

void mergeSort(vector<int>& nums) {
    if (nums.size() <= 1) return;
    vector<int> temp(nums.size());
    mergeSortHelper(nums, 0, (int)nums.size() - 1, temp);
}
```

### Java Code
```java
class Solution {
    void merge(int[] arr, int low, int mid, int high, int[] temp) {
        int left = low, right = mid + 1, k = low;
        
        while (left <= mid && right <= high) {
            if (arr[left] <= arr[right]) {
                temp[k++] = arr[left++];
            } else {
                temp[k++] = arr[right++];
            }
        }
        while (left <= mid) temp[k++] = arr[left++];
        while (right <= high) temp[k++] = arr[right++];
        
        for (int i = low; i <= high; i++) {
            arr[i] = temp[i];
        }
    }
    
    void mergeSortHelper(int[] arr, int low, int high, int[] temp) {
        if (low >= high) return;
        int mid = low + (high - low) / 2;
        mergeSortHelper(arr, low, mid, temp);
        mergeSortHelper(arr, mid + 1, high, temp);
        merge(arr, low, mid, high, temp);
    }
    
    void mergeSort(int[] nums) {
        if (nums.length <= 1) return;
        int[] temp(nums.length);
        mergeSortHelper(nums, 0, nums.length - 1, temp);
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n)$ in Best, Average, and Worst cases (guaranteed $\mathcal{O}(n \log n)$ performance).
- **Space Complexity**: $\mathcal{O}(n)$ auxiliary space for single temporary buffer + $\mathcal{O}(\log n)$ recursion stack.
- **Why this is optimal**: Optimal comparison-based sorting bound $\Omega(n \log n)$ with guaranteed worst-case predictability and stability.

---

## 6. Dry Run

`nums = [4, 2, 1, 3]`

| Step | Trace / Comparisons | Result State |
|---|---|---|
| `Split` | Divide into [4, 2] and [1, 3] | low=0, mid=1, high=3 |
| `Left Halves` | Sort [4] and [2] -> merge to [2, 4] | arr[0..1] = [2, 4] |
| `Right Halves` | Sort [1] and [3] -> merge to [1, 3] | arr[2..3] = [1, 3] |
| `Final Merge` | Merge [2, 4] and [1, 3]: 1 < 2, 2 < 3, 3 < 4, 4 remaining | arr = [1, 2, 3, 4] ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already sorted array (`[1, 2, 3, 4]` -> still $\mathcal{O}(n \log n)$).
- Array with duplicate elements (`[2, 2, 1, 1]` -> stable order preserved via `<=`).
- Single element / empty array (`[1]` -> returns immediately).

### Common Bugs to Avoid
- Calculating `mid = (low + high) / 2` which can overflow 32-bit signed integers; use `low + (high - low) / 2`.
- Using `<` instead of `<=` in `arr[left] <= arr[right]`, destroying stability.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Is Merge Sort stable?**  
  **A**: Yes! In the `merge` subroutine, when `arr[left] == arr[right]`, we pick `arr[left]` from the left subarray first (`arr[left] <= arr[right]`), preserving original index precedence.

- **Q2: Why is Merge Sort preferred for Linked Lists over QuickSort?**  
  **A**: In Linked Lists, inserting elements takes $\mathcal{O}(1)$ pointer operations without contiguous memory shifts. Merge Sort on Linked Lists requires $\mathcal{O}(1)$ extra auxiliary space (no array allocation) and accesses sequential nodes without random access.

- **Q3: What is Inversion Counting with Merge Sort?**  
  **A**: Whenever an element from the right subarray is picked (`arr[left] > arr[right]`), it forms an inversion with all remaining elements in the left subarray (`mid - left + 1`), allowing inversion counting in $\mathcal{O}(n \log n)$.

- **Q4: What is External Merge Sort and how is it used for multi-terabyte data?**  
  **A**: When data exceeds RAM capacity, chunks fitting in RAM are sorted and written to disk (runs). A K-Way Min-Heap merge stream reads from each run file into an output stream with $\mathcal{O}(1)$ RAM overhead.

- **Q5: Can Merge Sort be made in-place for arrays?**  
  **A**: Block Merge Sort algorithms (e.g. SymMerge) achieve in-place $\mathcal{O}(1)$ auxiliary space, but increase constant factors significantly.


---

## 9. Tags & Related Problems

- **Tags**: `Sorting`, `Divide-and-Conquer`, `Stable-Sort`
- **Related problems to practice next**:
- **Count Inversions in an Array**: Counts inversions using Merge Sort recursion.
- **Reverse Pairs**: Counts reverse pairs $nums[i] > 2 nums[j]$ with modified merge sort.
- **Quick Sort Algorithm**: In-place non-stable divide and conquer alternative.
