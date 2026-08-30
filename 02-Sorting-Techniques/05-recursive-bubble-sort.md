# Recursive Bubble Sort (Step 2.2 — Sorting-II)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Recursive Bubble Sort](https://takeuforward.org/data-structure/recursive-bubble-sort-algorithm/)
- **Difficulty**: Easy
- **Statement**: Implement Bubble Sort recursively. Base case: array size 1 is sorted. Recursive step: do 1 pass of bubbling to place the largest element at index $n-1$, then recursively sort the prefix of size $n-1$.

---

## 1. Problem, Restated

Implement Bubble Sort recursively. Base case: array size 1 is sorted. Recursive step: do 1 pass of bubbling to place the largest element at index $n-1$, then recursively sort the prefix of size $n-1$.

- **Input**: Vector of integers `nums`.
- **Output**: Array sorted in non-decreasing order in-place.
- **Key Constraints**: $n$ up to $10^5$, integers can be positive, negative, or zero.

---

## 2. Intuition & Pattern

Recursive Suffix Fixing. 1) Base case: if $n == 1$, return. 2) Fix largest: iterate $j$ from 0 to $n-2$; if `nums[j] > nums[j+1]`, swap. 3) Recurse: call `recursiveBubbleSort(nums, n - 1)`.

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

void recursiveBubbleSortBrute(vector<int>& nums, int n) {
    if (n <= 1) return;
    for (int j = 0; j < n - 1; j++) {
        if (nums[j] > nums[j + 1]) {
            swap(nums[j], nums[j + 1]);
        }
    }
    recursiveBubbleSortBrute(nums, n - 1);
}
```

### Java Code
```java
class Solution {
    void recursiveBubbleSortBrute(int[] nums, int n) {
        if (n <= 1) return;
        for (int j = 0; j < n - 1; j++) {
            if (nums[j] > nums[j + 1]) {
                int temp = nums[j]; nums[j] = nums[j + 1]; nums[j + 1] = temp;
            }
        }
        recursiveBubbleSortBrute(nums, n - 1);
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n^2)$ time.
- **Space Complexity**: $\mathcal{O}(n)$ call stack depth.
- **Why it's not good enough**: Does not include early exit on sorted inputs.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Recursive Bubble Sort with Early Exit Flag: If `swapped == false` after the pass, return immediately, achieving $\mathcal{O}(n)$ best-case time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

void recursiveBubbleSort(vector<int>& nums, int n) {
    // Base Case: 1 element is already sorted
    if (n <= 1) return;
    
    bool swapped = false;
    // One pass of bubble sort to fix largest element at index n - 1
    for (int j = 0; j < n - 1; j++) {
        if (nums[j] > nums[j + 1]) {
            swap(nums[j], nums[j + 1]);
            swapped = true;
        }
    }
    
    // If no swaps occurred, array is already sorted
    if (!swapped) return;
    
    // Recurse for remaining n - 1 elements
    recursiveBubbleSort(nums, n - 1);
}
```

### Java Code
```java
class Solution {
    void recursiveBubbleSort(int[] nums, int n) {
        // Base Case: 1 element is already sorted
        if (n <= 1) return;
        
        boolean swapped = false;
        // One pass of bubble sort to fix largest element at index n - 1
        for (int j = 0; j < n - 1; j++) {
            if (nums[j] > nums[j + 1]) {
                int temp = nums[j]; nums[j] = nums[j + 1]; nums[j + 1] = temp;
                swapped = true;
            }
        }
        
        // If no swaps occurred, array is already sorted
        if (swapped == null) return;
        
        // Recurse for remaining n - 1 elements
        recursiveBubbleSort(nums, n - 1);
    }
}
```

### Complexity Derivation
- **Time Complexity**: Best Case: $\mathcal{O}(n)$ (sorted). Worst/Avg Case: $\mathcal{O}(n^2)$.
- **Space Complexity**: $\mathcal{O}(n)$ auxiliary recursion stack space in worst case; $\mathcal{O}(1)$ auxiliary heap memory.
- **Why this is optimal**: Demonstrates recursive state decomposition.

---

## 6. Dry Run

`nums = [3, 2, 1]`, `n=3`

| Step | Trace / Comparisons | Result State |
|---|---|---|
| `Call 1 (n=3)` | Pass bubbles 3 to index 2: [2, 1, 3], swapped=true | Recurse with n=2 |
| `Call 2 (n=2)` | Pass bubbles 2 to index 1: [1, 2, 3], swapped=true | Recurse with n=1 |
| `Call 3 (n=1)` | Base case n <= 1 reached -> return | Array sorted [1, 2, 3] ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already sorted array (`[1, 2, 3]` -> returns after Call 1 in $\mathcal{O}(n)$).
- Single element array (`[5]` -> base case hit immediately).

### Common Bugs to Avoid
- Forgetting the base case $n \le 1$, causing infinite recursion and Stack Overflow.
- Recursing with $n$ instead of $n-1$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is iterative Bubble Sort preferred over recursive Bubble Sort?**  
  **A**: Iterative Bubble Sort uses $\mathcal{O}(1)$ memory, while recursive Bubble Sort consumes $\mathcal{O}(n)$ stack space, risking Stack Overflow for $n > 10^5$.

- **Q2: Can the inner loop also be made recursive?**  
  **A**: Yes, by passing `(i, j)` where $j$ increments until $n-1$, but this increases stack depth to $\mathcal{O}(n^2)$.

- **Q3: How does tail-call optimization (TCO) apply here?**  
  **A**: Because the recursive call `recursiveBubbleSort(nums, n - 1)` is the final instruction (tail position), a TCO-capable compiler can optimize the stack frame into a jump instruction.

- **Q4: Is recursive Bubble Sort stable?**  
  **A**: Yes, because only strict inequalities `nums[j] > nums[j+1]` trigger swaps.

- **Q5: What is the recurrence relation?**  
  **A**: $T(n) = T(n-1) + \mathcal{O}(n)$, expanding to $\sum_{i=1}^n i = \mathcal{O}(n^2)$.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Sorting`, `Bubble-Sort`
- **Related problems to practice next**:
- **Bubble Sort Algorithm**: Iterative equivalent.
- **Recursive Insertion Sort**: Recursive sibling.
- **Quick Sort Algorithm**: Divide and conquer recursive partitioning.
