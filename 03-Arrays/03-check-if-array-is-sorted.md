# Check if Array Is Sorted and Rotated (Step 3.1)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/check-if-an-array-is-sorted/
- **Difficulty**: Easy
- **Statement**: Determine if an array was originally sorted in non-decreasing order and rotated.

---

## 1. Problem, Restated

Determine if an array was originally sorted in non-decreasing order and rotated.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Circular breakpoint counter. In sorted-rotated array, nums[i] > nums[(i+1)%n] happens at most once.

- **Underlying Pattern**: Array Manipulation / Mathematical Invariants / Pointers.
- **The "Aha!" Moment**: Recognizing how to avoid redundant work by storing running state or leveraging sorting invariants.

---

## 3. Approach 1 — Brute Force

### Idea
Check all possibilities exhaustively using nested loops.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <set>
#include <unordered_map>
using namespace std;

bool checkBrute(vector<int>& nums) {
    vector<int> s = nums; sort(s.begin(), s.end());
    int n = nums.size();
    for (int k = 0; k < n; k++) {
        bool ok = true;
        for (int i = 0; i < n; i++) if (nums[(i+k)%n] != s[i]) { ok = false; break; }
        if (ok) return true;
    }
    return false;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    boolean checkBrute(int[] nums) {
        int[] s = nums; Arrays.sort(s);
        int n = nums.length;
        for (int k = 0; k < n; k++) {
            boolean ok = true;
            for (int i = 0; i < n; i++) if (nums[(i+k)%n] != s[i]) { ok = false; break; }
            if (ok) return true;
        }
        return false;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(n)
- **Why it's not good enough**: Brute-forcing all $n$ rotation offsets takes $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Circular Breakpoint Counting: Count how many times `nums[i] > nums[(i + 1) % n]`. The circular modulo `(i+1)%n` checks the boundary wrap `nums[n-1] > nums[0]`. If `countDrops <= 1`, the array is sorted and rotated.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

bool checkOptimal(const vector<int>& nums) {
    int countDrops = 0, n = nums.size();
    for (int i = 0; i < n; i++) {
        if (nums[i] > nums[(i + 1) % n]) countDrops++;
    }
    return countDrops <= 1;
}
```

### Java Code
```java
class Solution {
    boolean checkOptimal(int[] nums) {
        int countDrops = 0, n = nums.length;
        for (int i = 0; i < n; i++) {
            if (nums[i] > nums[(i + 1) % n]) countDrops++;
        }
        return countDrops <= 1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Verifies the single drop invariant in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space, inspecting every element once.

---

## 6. Dry Run

`nums = [3, 4, 5, 1, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `i=0` | 3 <= 4 | drops = 0 |
| `i=1` | 4 <= 5 | drops = 0 |
| `i=2` | 5 > 1 (Drop point!) | drops = 1 |
| `i=3` | 1 <= 2 | drops = 1 |
| `i=4 (wrap)` | 2 <= 3 | drops = 1 <= 1 -> return TRUE ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already sorted array (`[1, 2, 3]` -> drops = 1 on wrap 3>1 -> true).
- All elements equal (`[1, 1, 1]` -> drops = 0 -> true).
- Multiple drops (`[2, 1, 3, 4]` -> drops at 2>1 and 4>2 -> count=2 -> false).

### Common Bugs to Avoid
- Forgetting the circular wrap check `nums[n-1] > nums[0]`.
- Rejecting arrays with `countDrops == 0` (unrotated sorted array has 0 or 1 drop).

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to find the exact rotation index K if the array is confirmed sorted and rotated?**  
  **A**: The rotation index $K$ is the index of the minimum element, which is $(i + 1) \% n$ where `nums[i] > nums[(i+1)%n]`. If `countDrops == 0`, $K = 0$.

- **Q2: Why does counting `nums[i] > nums[(i+1)%n] <= 1` work for an unrotated sorted array?**  
  **A**: For an unrotated sorted array `[1, 2, 3]`, `nums[0] < nums[1] < nums[2]`, but `nums[2] > nums[0]` on the circular wrap, producing exactly 1 drop. For all-equal array `[1, 1, 1]`, drops = 0. Both satisfy $\le 1$.

- **Q3: How does this behave with duplicates like `[2, 2, 2, 3, 2, 2]`?**  
  **A**: The single drop condition `nums[i] > nums[(i+1)%n] <= 1` still holds correctly: the only drop is `3 > 2`, so it correctly returns `true`.

- **Q4: Can we determine if a rotated array is sorted in sub-linear time?**  
  **A**: No, because even a single out-of-order element hidden anywhere in the array would invalidate the property, requiring $\Omega(n)$ worst-case checks.

- **Q5: How does this problem relate to Search in Rotated Sorted Array (LeetCode 33)?**  
  **A**: The single drop point divides the rotated array into two sorted subarrays. Binary search determines which half is sorted by checking `nums[low] <= nums[mid]`.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Easy`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
