# Find Peak Element in Array (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find Peak Element in Array](https://takeuforward.org/data-structure/peak-element-in-array/)
- **Difficulty**: Medium
- **Statement**: A peak element is an element that is strictly greater than its neighbors. Given an integer array `nums`, find a peak element, and return its index. If the array contains multiple peaks, return the index to any of the peaks. You may imagine that `nums[-1] = nums[n] = -\infty`. You must write an algorithm that runs in $\mathcal{O}(\log n)$ time.

---

## 1. Problem, Restated

Find any local maximum element in an unsorted array where adjacent elements are never equal ($nums[i] \neq nums[i+1]$).

- **Input**: Vector of integers `nums`, plus query parameters.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, elements fit in 32-bit signed integers.

---

## 2. Intuition & Pattern

Think of the array as a mountain terrain. At index `mid`:
- If `nums[mid] > nums[mid-1]` and `nums[mid] > nums[mid+1]`, then `mid` is a peak!
- If `nums[mid] > nums[mid-1]` (we are on an **upward slope**), then walking rightwards is guaranteed to lead to a peak (since the right boundary eventually drops to $-\infty$). Move `low = mid + 1`.
- If `nums[mid] < nums[mid-1]` (we are on a **downward slope**), walking leftwards is guaranteed to lead to a peak. Move `high = mid - 1`.

- **Underlying Pattern**: `Binary Search on Slope / Local Invariant`.
- **The "Aha!" Moment**: Recognizing how monotonic invariants or gradient direction eliminate half the search space.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Check every element from 0 to $n-1$. If $nums[i] > nums[i-1]$ and $nums[i] > nums[i+1]$, return $i$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int findPeakElementLinear(const vector<int>& nums) {
    int n = nums.size();
    for (int i = 0; i < n; i++) {
        bool leftOk = (i == 0 || nums[i] > nums[i - 1]);
        bool rightOk = (i == n - 1 || nums[i] > nums[i + 1]);
        if (leftOk && rightOk) return i;
    }
    return 0;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Fails the $\mathcal{O}(\log n)$ time requirement.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Binary Search on Gradient / Slope: Check boundaries index 0 and $n-1$ first. Run binary search on `[1..n-2]`. If `nums[mid] > nums[mid-1] && nums[mid] > nums[mid+1]`, return `mid`. If `nums[mid] > nums[mid-1]`, search right `low = mid + 1`; else search left `high = mid - 1`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int findPeakElement(const vector<int>& nums) {
    int n = nums.size();
    
    // Boundary conditions
    if (n == 1) return 0;
    if (nums[0] > nums[1]) return 0;
    if (nums[n - 1] > nums[n - 2]) return n - 1;
    
    int low = 1, high = n - 2;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        
        // Peak condition: strictly greater than both neighbors
        if (nums[mid] > nums[mid - 1] && nums[mid] > nums[mid + 1]) {
            return mid;
        }
        
        // Upward slope: peak must exist in right half
        if (nums[mid] > nums[mid - 1]) {
            low = mid + 1;
        } 
        // Downward slope: peak must exist in left half
        else {
            high = mid - 1;
        }
    }
    
    return -1;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — strictly halves search window on each iteration.
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary space.
- **Why this is optimal**: Guaranteed to converge to a local maximum in logarithmic time by following the positive gradient.

---

## 6. Dry Run

`nums = [1, 2, 1, 3, 5, 6, 4]`, $n = 7$

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | low=1, high=5 | ready |
| `Iter 1` | mid = 3 (val 3). nums[3]=3 > nums[2]=1 (upward slope) | low = mid + 1 = 4 |
| `Iter 2` | mid = 4 (val 5). nums[4]=5 > nums[3]=3 (upward slope) | low = mid + 1 = 5 |
| `Iter 3` | mid = 5 (val 6). nums[5]=6 > nums[4]=5 AND nums[5]=6 > nums[6]=4! | **Peak found at index 5! Return 5** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Strictly increasing array (`[1, 2, 3, 4]` -> returns index 3).
- Strictly decreasing array (`[4, 3, 2, 1]` -> returns index 0).
- Single element array (`[1]` -> returns index 0).

### Common Bugs to Avoid
- Out-of-bounds check at $i = 0$ or $i = n-1$ (eliminated by checking boundaries upfront).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is a peak GUARANTEED to exist if we follow the upward slope?**  
  **A**: Because the boundaries are defined as $-\infty$. If you walk uphill ($nums[mid] > nums[mid-1]$), either you keep going up until the last element (which is $> -\infty$, hence a peak) or you encounter a drop (which forms a peak). A local maximum is mathematically guaranteed by the Extreme Value Theorem on discrete sequences.

- **Q2: Can Binary Search be used on unsorted arrays?**  
  **A**: Yes! Binary search does not require monotonic data; it only requires a **monotonic decision rule** (a rule that guarantees the target exists in one half).

- **Q3: How does this extend to 2D matrices (Find Peak Element II)?**  
  **A**: Find the max element in the middle column in $\mathcal{O}(m)$, then binary search on column slope in $\mathcal{O}(m \log n)$ time.

- **Q4: What if duplicate adjacent elements are allowed (e.g. `[1, 2, 2, 1]`)?**  
  **A**: If adjacent duplicates exist, flat plateaus prevent knowing which direction goes up, degrading worst-case complexity to $\mathcal{O}(n)$.

- **Q5: Can multiple threads search for different peaks simultaneously?**  
  **A**: Yes, each independent binary search branch can converge to a different valid local peak.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Local Maximum`, `Gradient`, `LeetCode-162`, `Medium`
- **Related problems to practice next**:
- **Find Peak Element II**: 2D matrix peak search.
- **Single Element in Sorted Array**: Index parity search.
- **Find Minimum in Rotated Sorted Array**: Inflection point search.
