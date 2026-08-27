# Find out how many times array has been rotated (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find out how many times array has been rotated](https://takeuforward.org/arrays/find-out-how-many-times-has-an-array-been-rotated/)
- **Difficulty**: Easy
- **Statement**: Given an integer array `nums` of size $n$, sorted in ascending order with distinct values, which has been rotated right by $K$ positions, find the value of $K$.

---

## 1. Problem, Restated

Find the rotation count $K$. Since each right rotation moves the last element to index 0, the number of rotations is equal to the index of the minimum element.

- **Input**: Vector of integers `nums`, plus query parameters.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, elements fit in 32-bit signed integers.

---

## 2. Intuition & Pattern

In an unrotated array `[0, 1, 2, 3, 4]`, minimum is at index 0 ($K = 0$). After 1 right rotation `[4, 0, 1, 2, 3]`, minimum is at index 1 ($K = 1$). After $K$ right rotations, the minimum element moves from index 0 to index $K$. Therefore, **Rotation Count = Index of Minimum Element**.

- **Underlying Pattern**: `Index of Minimum Element`.
- **The "Aha!" Moment**: Recognizing how monotonic invariants or gradient direction eliminate half the search space.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Find the index of the minimum element by scanning the array in $\mathcal{O}(n)$.

### C++17 Code
```cpp
#include <vector>
#include <climits>
using namespace std;

int findKRotationLinear(const vector<int>& nums) {
    int minVal = INT_MAX, minIdx = 0;
    for (int i = 0; i < (int)nums.size(); i++) {
        if (nums[i] < minVal) {
            minVal = nums[i];
            minIdx = i;
        }
    }
    return minIdx;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Linear scan ignores the sorted structure.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Binary Search for Minimum Index: Modify Find Minimum in Rotated Sorted Array to track both `minVal` and its corresponding `minIdx`.

### C++17 Code
```cpp
#include <vector>
#include <climits>
using namespace std;

int findKRotation(const vector<int>& nums) {
    int low = 0, high = (int)nums.size() - 1;
    int minVal = INT_MAX;
    int minIdx = -1;
    
    while (low <= high) {
        // If subarray is completely sorted
        if (nums[low] <= nums[high]) {
            if (nums[low] < minVal) {
                minVal = nums[low];
                minIdx = low;
            }
            break;
        }
        
        int mid = low + (high - low) / 2;
        
        // Left half is sorted
        if (nums[low] <= nums[mid]) {
            if (nums[low] < minVal) {
                minVal = nums[low];
                minIdx = low;
            }
            low = mid + 1;
        } 
        // Right half is sorted
        else {
            if (nums[mid] < minVal) {
                minVal = nums[mid];
                minIdx = mid;
            }
            high = mid - 1;
        }
    }
    
    return minIdx;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — binary search halving.
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary memory.
- **Why this is optimal**: Finds minimum index in optimal logarithmic time.

---

## 6. Dry Run

`nums = [4, 5, 6, 7, 0, 1, 2, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | low=0 (4), high=7 (3), mid=3 (7) | Left sorted: minVal=4, minIdx=0, low = mid + 1 = 4 |
| `Iter 2` | low=4 (0), high=7 (3) | Subarray [4..7] sorted! nums[4]=0 < 4 -> minVal=0, minIdx=4. Break! |
| `Result` | Return minIdx = 4 | Array rotated 4 times ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Array not rotated (`[1, 2, 3]` -> returns index 0).
- Single element array (`[7]` -> returns index 0).

### Common Bugs to Avoid
- Returning `minVal` instead of `minIdx`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What if the rotation is left-rotation instead of right-rotation?**  
  **A**: If rotated left by $K$ positions, then $K_{left} = (n - minIdx) \% n$.

- **Q2: Why is this index guaranteed to be unique?**  
  **A**: Because all elements are distinct; exactly one unique minimum exists.

- **Q3: What if duplicate elements are present?**  
  **A**: Same as LeetCode 154, we must shrink `high--` when `nums[low] == nums[mid] == nums[high]`, taking $\mathcal{O}(n)$ worst case.

- **Q4: How does this compare to finding peak element?**  
  **A**: The peak element index is `(minIdx - 1 + n) % n`.

- **Q5: Can this be used to reconstruct the original sorted array?**  
  **A**: Yes, by reading elements starting from `minIdx` wrapping around with modulo $n$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Rotated Array`, `Easy`
- **Related problems to practice next**:
- **Find Minimum in Rotated Sorted Array**: Direct value calculation.
- **Search in Rotated Sorted Array I**: Target query on rotated array.
- **Rotate Array by K Places**: The inverse rotation operation.
