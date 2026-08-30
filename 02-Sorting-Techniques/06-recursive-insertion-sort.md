# Recursive Insertion Sort (Step 2.2 — Sorting-II)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Recursive Insertion Sort](https://takeuforward.org/data-structure/recursive-insertion-sort-algorithm/)
- **Difficulty**: Easy
- **Statement**: Implement Insertion Sort recursively. Base case: array of size $\le 1$ is sorted. Recursive step: recursively sort the first $n-1$ elements, then insert the $n$-th element (`nums[n-1]`) into its correct position in the sorted prefix.

---

## 1. Problem, Restated

Implement Insertion Sort recursively. Base case: array of size $\le 1$ is sorted. Recursive step: recursively sort the first $n-1$ elements, then insert the $n$-th element (`nums[n-1]`) into its correct position in the sorted prefix.

- **Input**: Vector of integers `nums`.
- **Output**: Array sorted in non-decreasing order in-place.
- **Key Constraints**: $n$ up to $10^5$, integers can be positive, negative, or zero.

---

## 2. Intuition & Pattern

Inductive Insertion. 1) Base case: if $n \le 1$, return. 2) Recursively sort the prefix: `recursiveInsertionSort(nums, n - 1)`. 3) Insert last element `key = nums[n-1]` into sorted prefix `nums[0..n-2]` by shifting larger elements rightward.

- **Underlying Pattern**: Sorting / Partitioning / Divide and Conquer.
- **The "Aha!" Moment**: Understanding how each comparison or swap establishes a permanent sorted boundary invariant.

---

## 3. Approach 1 — Brute Force

### Idea
Naive or non-optimized formulation of the sorting algorithm.

### C++17 Code
```cpp
#include <vector>
using namespace std;

void recursiveInsertionSort(vector<int>& nums, int n) {
    if (n <= 1) return;
    recursiveInsertionSort(nums, n - 1);
    
    int key = nums[n - 1];
    int j = n - 2;
    while (j >= 0 && nums[j] > key) {
        nums[j + 1] = nums[j];
        j--;
    }
    nums[j + 1] = key;
}
```

### Java Code
```java
class Solution {
    void recursiveInsertionSort(int[] nums, int n) {
        if (n <= 1) return;
        recursiveInsertionSort(nums, n - 1);
        
        int key = nums[n - 1];
        int j = n - 2;
        while (j >= 0 && nums[j] > key) {
            nums[j + 1] = nums[j];
            j--;
        }
        nums[j + 1] = key;
    }
}
```

### Complexity Derivation
- **Time Complexity**: Best Case: $\mathcal{O}(n)$. Worst/Avg Case: $\mathcal{O}(n^2)$.
- **Space Complexity**: $\mathcal{O}(n)$ recursion stack frames.
- **Why it's not good enough**: Recursion adds stack space overhead over iterative insertion sort.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Inductive Recursive Insertion Sort: Recursively sort $n-1$ elements, then perform shifting to place `nums[n-1]`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

void recursiveInsertionSort(vector<int>& nums, int n) {
    // Base Case: 1 element is already sorted
    if (n <= 1) return;
    
    // Sort first n - 1 elements
    recursiveInsertionSort(nums, n - 1);
    
    // Insert last element at its correct position in sorted array
    int key = nums[n - 1];
    int j = n - 2;
    
    while (j >= 0 && nums[j] > key) {
        nums[j + 1] = nums[j];
        j--;
    }
    nums[j + 1] = key;
}
```

### Java Code
```java
class Solution {
    void recursiveInsertionSort(int[] nums, int n) {
        // Base Case: 1 element is already sorted
        if (n <= 1) return;
        
        // Sort first n - 1 elements
        recursiveInsertionSort(nums, n - 1);
        
        // Insert last element at its correct position in sorted array
        int key = nums[n - 1];
        int j = n - 2;
        
        while (j >= 0 && nums[j] > key) {
            nums[j + 1] = nums[j];
            j--;
        }
        nums[j + 1] = key;
    }
}
```

### Complexity Derivation
- **Time Complexity**: Best Case: $\mathcal{O}(n)$ (sorted). Worst/Avg Case: $\mathcal{O}(n^2)$.
- **Space Complexity**: $\mathcal{O}(n)$ call stack depth.
- **Why this is optimal**: Matches iterative complexity while illustrating mathematical induction.

---

## 6. Dry Run

`nums = [3, 1, 2]`, `n=3`

| Step | Trace / Comparisons | Result State |
|---|---|---|
| `Call 1 (n=3)` | Calls recursiveInsertionSort(nums, 2) | waiting for prefix sort |
| `Call 2 (n=2)` | Calls recursiveInsertionSort(nums, 1) | waiting for base case |
| `Call 3 (n=1)` | Base case n=1 hit -> returns | prefix [3] is sorted |
| `Resume Call 2` | Insert nums[1]=1 into [3] -> shift 3 -> [1, 3] | prefix [1, 3] sorted |
| `Resume Call 1` | Insert nums[2]=2 into [1, 3] -> shift 3 -> [1, 2, 3] | Array sorted [1, 2, 3] ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already sorted array (`[1, 2, 3]` -> each insert makes 1 comparison, $\mathcal{O}(n)$).
- Single element (`[42]` -> base case returns immediately).

### Common Bugs to Avoid
- Placing the insert logic *before* the recursive call instead of *after* (the prefix must be sorted before inserting).
- Shifting condition `nums[j] >= key` instead of `nums[j] > key`, destroying stability.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does recursive Insertion Sort sort the prefix BEFORE inserting?**  
  **A**: Because the binary/linear insertion shift requires the subarray `nums[0..n-2]` to already be sorted. Mathematical induction dictates solving $P(n-1)$ before extending to $P(n)$.

- **Q2: What is the recurrence relation?**  
  **A**: $T(n) = T(n-1) + \mathcal{O}(n)$, identical to recursive Bubble Sort, solving to $\mathcal{O}(n^2)$.

- **Q3: Is recursive Insertion Sort stable?**  
  **A**: Yes, equal elements never shift past each other (`nums[j] > key`), preserving initial relative order.

- **Q4: Can we avoid Stack Overflow for large n?**  
  **A**: For $n > 10^5$, iterative insertion sort should always be used to avoid exhausting CPU call stack memory.

- **Q5: How does this compare to Merge Sort's divide and conquer?**  
  **A**: Recursive Insertion Sort divides the problem into $1$ and $n-1$ ($T(n) = T(n-1) + \mathcal{O}(n)$), while Merge Sort divides into $n/2$ and $n/2$ ($T(n) = 2T(n/2) + \mathcal{O}(n)$), reducing tree depth from $n$ to $\log n$.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Sorting`, `Insertion-Sort`
- **Related problems to practice next**:
- **Insertion Sort Algorithm**: Iterative variant.
- **Merge Sort Algorithm**: Balanced divide and conquer sort.
- **Recursive Bubble Sort**: Recursive sibling.
