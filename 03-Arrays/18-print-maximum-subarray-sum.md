# Print Maximum Subarray (Step 3.2)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/kadanes-algorithm-maximum-subarray-sum-in-an-array/
- **Difficulty**: Medium
- **Statement**: Find and return the actual subarray that produces the maximum contiguous sum.

---

## 1. Problem, Restated

Find and return the actual subarray that produces the maximum contiguous sum.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Extended Kadane's tracking start and end indices of the maximal window.

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

vector<int> printMaxSubarrayBrute(const vector<int>& nums) {
    int maxS = INT_MIN, start = 0, end = 0, n = nums.size();
    for (int i = 0; i < n; i++) {
        int sum = 0;
        for (int j = i; j < n; j++) {
            sum += nums[j];
            if (sum > maxS) { maxS = sum; start = i; end = j; }
        }
    }
    return vector<int>(nums.begin() + start, nums.begin() + end + 1);
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Nested loop evaluation of all contiguous subarrays takes $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Extended Kadane Tracking Window: Maintain `start = 0`, `ansStart = 0`, `ansEnd = 0`. When `currentSum == 0`, reset `start = i`. When `currentSum + nums[i] > maxSum`, update `ansStart = start, ansEnd = i`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

vector<int> printMaxSubarrayOptimal(const vector<int>& nums) {
    int maxSum = INT_MIN, currentSum = 0;
    int start = 0, ansStart = 0, ansEnd = 0;
    for (int i = 0; i < (int)nums.size(); i++) {
        if (currentSum == 0) start = i;
        currentSum += nums[i];
        if (currentSum > maxSum) {
            maxSum = currentSum;
            ansStart = start;
            ansEnd = i;
        }
        if (currentSum < 0) currentSum = 0;
    }
    return vector<int>(nums.begin() + ansStart, nums.begin() + ansEnd + 1);
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Tracks the maximal bounding subarray in a single $\mathcal{O}(n)$ pass with $\mathcal{O}(1)$ auxiliary space.

---

## 6. Dry Run

`nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `i=0 (x=-2)` | cur=-2, max=-2, cur<0 -> cur=0, start=1 | max=-2, [0..0] |
| `i=1 (x=1)` | start=1, cur=1, max=1 -> ansStart=1, ansEnd=1 | max=1, [1..1] |
| `i=2 (x=-3)` | cur=-2 < 0 -> cur=0, start=3 | max=1, [1..1] |
| `i=3 (x=4)` | start=3, cur=4, max=4 -> ansStart=3, ansEnd=3 | max=4, [3..3] |
| `i=4..6` | accumulates to cur=6 at i=6 -> ansStart=3, ansEnd=6 | max=6, [3..6] |
| `i=7..8` | cur drops then ends | Final Subarray: `[4, -1, 2, 1]` ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- All negative elements (`[-4, -1, -2]` -> returns `[-1]`).
- Entire array positive (`[1, 2, 3]` -> returns entire array `[1, 2, 3]`).

### Common Bugs to Avoid
- Resetting `start` after adding `nums[i]` instead of at the moment `currentSum` becomes 0.

## 8. Follow-Up Questions (Interview Style)

- **Q1: How do we track the starting index `start` correctly when `currentSum` resets?**  
  **A**: Whenever `currentSum == 0`, set `start = i`. When `currentSum + nums[i] > maxSum`, update `ansStart = start` and `ansEnd = i`.

- **Q2: What if there are multiple subarrays with the exact same maximum sum?**  
  **A**: Strict inequality `currentSum > maxSum` retains the *first* occurring maximum subarray; `>=` captures the *last* occurring maximum subarray.

- **Q3: How to return the subarray indices with O(1) extra memory without allocating vectors?**  
  **A**: Return `std::pair<int, int> {ansStart, ansEnd}` containing the start and end indices in $\mathcal{O}(1)$ space; the caller slices the array view directly.

- **Q4: How to solve this in a Doubly Linked List?**  
  **A**: Traverse nodes maintaining `Node* startNode` and `Node* ansStart, *ansEnd` pointers with the same running accumulator logic in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

- **Q5: How to reconstruct the bounding box for the Maximum Submatrix in 2D?**  
  **A**: Track $(r_1, r_2)$ from the outer loops and $(c_1, c_2)$ from the 1D Kadane tracker on compressed columns, returning the rectangle coordinates $[r_1, c_1, r_2, c_2]$.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
