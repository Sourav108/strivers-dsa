# Find Minimum in Rotated Sorted Array (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find Minimum in Rotated Sorted Array](https://takeuforward.org/data-structure/minimum-in-rotated-sorted-array/)
- **Difficulty**: Medium
- **Statement**: Given the sorted rotated array `nums` of unique elements, return the minimum element of this array. You must write an algorithm that runs in $\mathcal{O}(\log n)$ time.

---

## 1. Problem, Restated

Find the smallest number (the inflection/pivot point) in a rotated sorted array of distinct integers.

- **Input**: Vector of integers `nums`, plus query parameters.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, elements fit in 32-bit signed integers.

---

## 2. Intuition & Pattern

In a rotated sorted array, the sorted half's minimum is always its leftmost element (`nums[low]`).
1. If the current subarray `[low..high]` is already completely sorted (`nums[low] <= nums[high]`), then `nums[low]` is the candidate minimum, and we can stop!
2. Otherwise, check which half is sorted:
   - If left half is sorted (`nums[low] <= nums[mid]`), then `nums[low]` is the minimum of this entire left half. Record `ans = min(ans, nums[low])` and eliminate left half (`low = mid + 1`).
   - Else, right half is sorted. `nums[mid]` is the minimum of this right half. Record `ans = min(ans, nums[mid])` and eliminate right half (`high = mid - 1`).

- **Underlying Pattern**: `Inflection Point Binary Search`.
- **The "Aha!" Moment**: Recognizing how monotonic invariants or gradient direction eliminate half the search space.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Linear Scan: Find minimum element by scanning all $n$ numbers in $\mathcal{O}(n)$.

### C++17 Code
```cpp
#include <vector>
#include <climits>
#include <algorithm>
using namespace std;

int findMinLinear(const vector<int>& nums) {
    int minVal = INT_MAX;
    for (int x : nums) minVal = min(minVal, x);
    return minVal;
}
```

### Java Code
```java
class Solution {
    int findMinLinear(int[] nums) {
        int minVal = Integer.MAX_VALUE;
        for (int x : nums) minVal = Math.min(minVal, x);
        return minVal;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Fails the $\mathcal{O}(\log n)$ complexity requirement.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Binary Search on Unsorted Half: In each iteration, take the minimum of the sorted half, then search inside the unsorted half where the inflection drop must reside.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

int findMin(const vector<int>& nums) {
    int low = 0, high = (int)nums.size() - 1;
    int ans = INT_MAX;
    
    while (low <= high) {
        // If search space is already sorted, nums[low] is the minimum
        if (nums[low] <= nums[high]) {
            ans = min(ans, nums[low]);
            break;
        }
        
        int mid = low + (high - low) / 2;
        
        // If left half is sorted, min of left half is nums[low]
        if (nums[low] <= nums[mid]) {
            ans = min(ans, nums[low]);
            low = mid + 1; // search unsorted right half
        } 
        // If right half is sorted, min of right half is nums[mid]
        else {
            ans = min(ans, nums[mid]);
            high = mid - 1; // search unsorted left half
        }
    }
    
    return ans;
}
```

### Java Code
```java
class Solution {
    int findMin(int[] nums) {
        int low = 0, high = nums.length - 1;
        int ans = Integer.MAX_VALUE;
        
        while (low <= high) {
            // If search space is already sorted, nums[low] is the minimum
            if (nums[low] <= nums[high]) {
                ans = Math.min(ans, nums[low]);
                break;
            }
            
            int mid = low + (high - low) / 2;
            
            // If left half is sorted, min of left half is nums[low]
            if (nums[low] <= nums[mid]) {
                ans = Math.min(ans, nums[low]);
                low = mid + 1; // search unsorted right half
            } 
            // If right half is sorted, min of right half is nums[mid]
            else {
                ans = Math.min(ans, nums[mid]);
                high = mid - 1; // search unsorted left half
            }
        }
        
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — search space halves on every step.
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary memory.
- **Why this is optimal**: Achieves strict $\mathcal{O}(\log n)$ time by discarding the sorted half after recording its minimum.

---

## 6. Dry Run

`nums = [4, 5, 6, 7, 0, 1, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | low=0 (4), high=6 (2), mid=3 (7) | nums[0]=4 <= nums[3]=7 (Left sorted). ans = min(MAX, 4) = 4, low = mid + 1 = 4 |
| `Iter 2` | low=4 (0), high=6 (2) | nums[4]=0 <= nums[6]=2 (Completely sorted!). ans = min(4, 0) = 0. Break loop! |
| `Result` | Return ans = 0 | Final Minimum: 0 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Array not rotated (`[1, 2, 3, 4]` -> returns 1 on first check).
- Single element array (`[10]` -> returns 10).
- Rotated by 1 place (`[5, 1, 2, 3, 4]` -> correctly finds 1).

### Common Bugs to Avoid
- Forgetting the `nums[low] <= nums[high]` optimization which allows $\mathcal{O}(1)$ early exit on sorted subranges.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the alternative implementation comparing `nums[mid]` against `nums[high]`?**  
  **A**: If `nums[mid] > nums[high]`, minimum must lie strictly in right half $\implies low = mid + 1$. Else, minimum lies in left half including mid $\implies high = mid$. When `low == high`, `nums[low]` is the minimum!

- **Q2: How does finding the minimum also give the Rotation Count?**  
  **A**: The index of the minimum element equals the exact number of times the array was rotated counter-clockwise!

- **Q3: What if the array has duplicates?**  
  **A**: When duplicates exist, if `nums[mid] == nums[high]`, we cannot know which side holds the pivot, requiring `high--` and degrading worst case to $\mathcal{O}(n)$.

- **Q4: Can this be applied to find the Maximum in a rotated sorted array?**  
  **A**: Yes, the maximum is always at index `(min_index - 1 + n) % n`.

- **Q5: Why does `high = mid` (not `mid - 1`) appear in some BS minimum solutions?**  
  **A**: Because `nums[mid]` itself might be the minimum element, so setting `high = mid - 1` would erroneously exclude it unless stored in an accumulator.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Rotated Array`, `Inflection Point`, `LeetCode-153`, `Medium`
- **Related problems to practice next**:
- **Find out how many times array has been rotated**: Rotation count = index of minimum.
- **Search in Rotated Sorted Array I**: Search target using pivot properties.
- **Find Peak Element**: Local extremum search.
