# Maximum Subarray Sum (Kadane's Algorithm) (Step 3.2)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/kadanes-algorithm-maximum-subarray-sum-in-an-array/
- **Difficulty**: Medium
- **Statement**: Find the contiguous subarray with the largest sum.

---

## 1. Problem, Restated

Find the contiguous subarray with the largest sum.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Kadane's Algorithm: accumulate running sum, update global max, reset running sum to 0 if negative.

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

int maxSubarrayBrute(const vector<int>& nums) {
    int maxS = INT_MIN, n = nums.size();
    for (int i = 0; i < n; i++) {
        int sum = 0;
        for (int j = i; j < n; j++) { sum += nums[j]; maxS = max(maxS, sum); }
    }
    return maxS;
}
```

### Complexity Derivation
- **Time Complexity**: O(n^2)
- **Space Complexity**: O(1)
- **Why it's not good enough**: Evaluating all $n(n+1)/2$ subarrays with 3 or 2 loops takes $\mathcal{O}(n^3)$ or $\mathcal{O}(n^2)$ time.

---

## 4. Approach 2 — Better

### Idea
Two Nested Loops (Running Sum): Fix starting index $i$, iterate ending index $j$ from $i$ to $n-1$, accumulating running sum in $\mathcal{O}(1)$ per subarray.

### C++17 Code
```cpp
#include <vector>
#include <climits>
using namespace std;

int maxSubArrayBetter(const vector<int>& nums) {
    int maxSum = INT_MIN, n = nums.size();
    for (int i = 0; i < n; i++) {
        int currentSum = 0;
        for (int j = i; j < n; j++) {
            currentSum += nums[j];
            if (currentSum > maxSum) maxSum = currentSum;
        }
    }
    return maxSum;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n^2)$ — considers all $n(n+1)/2$ contiguous subarrays.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's still not optimal**: Still $\mathcal{O}(n^2)$ which triggers TLE at $n = 10^5$. Kadane's algorithm reduces this to linear $\mathcal{O}(n)$ time.

---

## 5. Approach 3 — Optimal

### Idea
Kadane's Algorithm: Maintain `currentSum = 0` and `maxSum = INT_MIN`. For each $x$: add $x$ to `currentSum`, update `maxSum = max(maxSum, currentSum)`, and if `currentSum < 0`, reset `currentSum = 0`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

int maxSubArrayOptimal(const vector<int>& nums) {
    int maxSum = INT_MIN, currentSum = 0;
    for (int x : nums) {
        currentSum += x;
        maxSum = max(maxSum, currentSum);
        if (currentSum < 0) currentSum = 0;
    }
    return maxSum;
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Dynamic programming state reduction achieves optimal $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

---

## 6. Dry Run

`nums = [-2, 1, -3, 4, -1, 2, 1, -5, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `x=-2` | cur=-2, max=-2, cur<0 -> cur=0 | max=-2 |
| `x=1` | cur=1, max=1 | max=1 |
| `x=-3` | cur=-2, max=1, cur<0 -> cur=0 | max=1 |
| `x=4` | cur=4, max=4 | max=4 |
| `x=-1` | cur=3, max=4 | max=4 |
| `x=2` | cur=5, max=5 | max=5 |
| `x=1` | cur=6, max=6 | max=6 |
| `x=-5` | cur=1, max=6 | max=6 |
| `x=4` | cur=5, max=6 | Final maxSum: 6 (subarray [4, -1, 2, 1]) ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- All negative numbers (`[-5, -2, -8]` -> correctly returns `-2` because max is updated before reset to 0).
- Single element (`[-1]` -> returns -1).

### Common Bugs to Avoid
- Resetting `currentSum = 0` before updating `maxSum`, returning 0 for all-negative arrays.

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does Kadane's algorithm handle arrays with ALL NEGATIVE numbers?**  
  **A**: Initialize `maxSum = INT_MIN`. Update `maxSum = max(maxSum, currentSum)` *before* checking `if (currentSum < 0) currentSum = 0`. This correctly captures the single least-negative element (e.g. `[-5, -2, -8] -> -2`).

- **Q2: How to find Maximum Subarray Sum on a CIRCULAR array (LeetCode 918)?**  
  **A**: The circular max subarray is $\max(\text{KadaneMax}, \text{TotalSum} - \text{KadaneMin})$. If all numbers are negative (`TotalSum == KadaneMin`), return `KadaneMax`.

- **Q3: How does Kadane's algorithm generalize to 2D matrices (Maximum Submatrix Sum)?**  
  **A**: Fix row pairs $(r_1, r_2)$, compress columns into a 1D array of column sums, and run 1D Kadane's algorithm. Runs in $\mathcal{O}(R^2 \cdot C)$ time and $\mathcal{O}(C)$ space.

- **Q4: Can Kadane's algorithm be parallelized using Divide and Conquer?**  
  **A**: Yes, each tree node computes 4 values: `totalSum`, `maxPrefixSum`, `maxSuffixSum`, and `maxSubarraySum`. Two nodes merge in $\mathcal{O}(1)$, allowing $\mathcal{O}(n)$ work in $\mathcal{O}(\log n)$ parallel depth.

- **Q5: What if we can delete at most ONE element from the subarray to maximize sum (LeetCode 1186)?**  
  **A**: Maintain two DP states: `maxNoDelete` (standard Kadane) and `maxOneDelete = max(prevNoDelete, prevOneDelete + x)`. Max overall is the peak across both states in $\mathcal{O}(n)$ time.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
