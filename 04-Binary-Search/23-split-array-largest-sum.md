# Split Array - Largest Sum (LeetCode 410) (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Split Array - Largest Sum (LeetCode 410)](https://takeuforward.org/binary-search/split-array-largest-sum/)
- **Difficulty**: Hard
- **Statement**: Given an integer array `nums` and an integer $k$, split `nums` into $k$ non-empty contiguous subarrays such that the largest sum of any subarray is minimized. Return the minimized largest sum of the split.

---

## 1. Problem, Restated

Divide the array into $k$ contiguous pieces to minimize the maximum sum among all pieces.

- **Input**: Vector of integers `arr`/`stalls`/`nums`, plus query parameters.
- **Output**: Minimized or maximized value meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, values fit in 32/64-bit integer ranges.

---

## 2. Intuition & Pattern

This problem is mathematically identical to the Book Allocation Problem and Capacity to Ship Packages. The search space for the largest subarray sum $S$ is $[\max(nums), \sum nums]$. For any candidate $S$, we greedily count how many contiguous subarrays are needed. If $\text{count} \le k$, $S$ is feasible and we try smaller sums `high = mid - 1`; else `low = mid + 1`.

- **Underlying Pattern**: `Minimise the Maximum Subarray Sum`.
- **The "Aha!" Moment**: Recognizing how the feasibility predicate divides the answer domain into monotonic boolean halves.

---

## 3. Approach 1 — Brute Force (Linear Range Scan)

### Idea
Linear Scan of Possible Sums: Iterate $S$ from $\max(nums)$ to $\sum nums$. Return the first $S$ that splits into $\le k$ subarrays.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

int countSplits(const vector<int>& nums, int maxSum) {
    int splits = 1, currentSum = 0;
    for (int x : nums) {
        if (currentSum + x > maxSum) {
            splits++;
            currentSum = x;
        } else {
            currentSum += x;
        }
    }
    return splits;
}

int splitArrayLinear(vector<int>& nums, int k) {
    int low = *max_element(nums.begin(), nums.end());
    int high = accumulate(nums.begin(), nums.end(), 0);
    for (int sum = low; sum <= high; sum++) {
        if (countSplits(nums, sum) <= k) return sum;
    }
    return low;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\sum - \max) \times n)$ — linear range scan.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Fails on large sum ranges.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
Binary Search on Maximum Subarray Sum: `low = *max_element(nums), high = accumulate(nums)`. If `countSplits(nums, mid) <= k`, record `ans = mid, high = mid - 1`; else `low = mid + 1`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

class Solution {
private:
    int countSplits(const vector<int>& nums, long long maxSum) {
        int splits = 1;
        long long currentSum = 0;
        
        for (int x : nums) {
            if (currentSum + x > maxSum) {
                splits++;
                currentSum = x;
            } else {
                currentSum += x;
            }
        }
        return splits;
    }

public:
    int splitArray(vector<int>& nums, int k) {
        int low = *max_element(nums.begin(), nums.end());
        long long high = accumulate(nums.begin(), nums.end(), 0LL);
        int ans = low;
        
        while (low <= high) {
            long long mid = low + (high - low) / 2;
            
            if (countSplits(nums, mid) <= k) {
                ans = mid;        // valid split, try to find smaller maximum sum
                high = mid - 1;
            } else {
                low = mid + 1;    // too many splits required, increase allowed sum
            }
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log_2(\sum nums))$ — logarithmic iterations over sum interval.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Optimal $\mathcal{O}(n \log(\sum))$ bound for minimax contiguous sequence partitioning.

---

## 6. Dry Run

`nums = [7, 2, 5, 10, 8]`, $k = 2$. `low = 10, high = 32`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | mid = 21. [7, 2, 5] (14), [10, 8] (18) -> 2 splits | splits=2 <= 2 -> ans = 21, high = 20 |
| `Iter 2` | mid = 15. [7, 2, 5] (14), [10] (10), [8] (8) -> 3 splits | splits=3 > 2 -> low = 16 |
| `Iter 3` | mid = 18. [7, 2, 5] (14), [10, 8] (18) -> 2 splits | splits=2 <= 2 -> ans = 18, high = 17 |
| `Iter 4` | mid = 16. [7, 2, 5] (14), [10] (10), [8] (8) -> 3 splits | splits=3 > 2 -> low = 17 |
| `Iter 5` | mid = 17. [7, 2, 5] (14), [10] (10), [8] (8) -> 3 splits | splits=3 > 2 -> low = 18 |
| `Exit` | low (18) > high (17) -> loop terminates | Return ans = 18 ✅ ([7,2,5]=14, [10,8]=18) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- k == 1 (no split allowed $\implies$ return $\sum nums$).
- k == n (each element is its own subarray $\implies$ return $\max(nums)$).
- nums containing 0s (handled cleanly without extra splits).

### Common Bugs to Avoid
- Starting `low` from 0 instead of `*max_element(nums)`.
- Using 32-bit signed integer for sum accumulation when sums exceed $2^{31}-1$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is DP O(n^2 * k) inferior to Binary Search O(n log(sum))?**  
  **A**: DP requires $O(n^2 k)$ states and transitions, which for $n=1000, k=50$ takes $5 \times 10^7$ operations. Binary Search does only $30 \times 1000 = 3 \times 10^4$ operations, making it $> 1500\times$ faster.

- **Q2: Can this be extended to circular arrays?**  
  **A**: For circular arrays, we must fix the cut point or duplicate the array, requiring $\mathcal{O}(n^2 \log(\sum))$ time.

- **Q3: How does this relate to Load Balancing in distributed computing?**  
  **A**: Assigning $n$ contiguous tasks to $k$ servers to minimize peak server load is the exact real-world embodiment of this problem.

- **Q4: What if negative numbers are allowed in nums?**  
  **A**: If negative numbers exist, the sum is no longer monotonic, and binary search fails; Dynamic Programming or Segment Tree DP becomes necessary.

- **Q5: Can we achieve branchless inner loop simulation?**  
  **A**: Yes, with arithmetic predicate accumulation: `splits += (curr + x > mid); curr = (curr + x > mid) ? x : (curr + x);`.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `LeetCode-410`, `Hard`
- **Related problems to practice next**:
- **Book Allocation Problem**: Exact identical clone.
- **Painter's Partition Problem**: Exact identical clone.
- **Capacity to Ship Packages**: Conveyor belt capacity variant.
