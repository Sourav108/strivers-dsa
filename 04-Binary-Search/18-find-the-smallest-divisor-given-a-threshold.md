# Find the Smallest Divisor Given a Threshold (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [LeetCode #1283 - Find the Smallest Divisor Given a Threshold](https://leetcode.com/problems/find-the-smallest-divisor-given-a-threshold/) | [TakeUForward](https://takeuforward.org/binary-search/find-the-smallest-divisor-given-a-threshold/)
- **Difficulty**: Medium
- **Statement**: Given an array of integers `nums` and an integer `threshold`, we will choose a positive integer `divisor`, divide all the array by it, and sum the division's result. Find the smallest divisor such that the result mentioned above is less than or equal to `threshold`. Each result of division is rounded to the nearest integer greater than or equal to that element (i.e. $\lceil nums[i] / \text{divisor} \rceil$).

---

## 1. Problem, Restated

Find the minimum integer $d \ge 1$ such that dividing each element in `nums` by $d$ (rounding each division UP to the nearest integer) yields a sum less than or equal to `threshold`:
$$\sum_{i=0}^{n-1} \left\lceil \frac{\text{nums}[i]}{d} \right\rceil \le \text{threshold}$$

- **Input**: Vector `nums` of size $n$, and an integer `threshold` ($threshold \ge n$).
- **Output**: The minimum integer divisor $d$.
- **Constraints**: $1 \le n \le 5 \times 10^4$, $1 \le \text{nums}[i] \le 10^6$, $n \le \text{threshold} \le 10^6$.

---

## 2. Intuition & Pattern

### Monotonic Inverse Predicate
Let $S(d) = \sum_{i=0}^{n-1} \lceil \text{nums}[i] / d \rceil$ be the sum of divisions using divisor $d$:
- If $d = 1$, the sum is maximum: $S(1) = \sum \text{nums}[i]$.
- If $d = \max(\text{nums})$, each division $\lceil \text{nums}[i] / \max \rceil = 1$, so the sum is minimum: $S(\max) = n \times 1 = n$.
- Since $\text{threshold} \ge n$, a valid divisor is always guaranteed to exist in the range $[1, \max(\text{nums})]$.
- As the divisor $d$ **increases**, each fraction $\text{nums}[i]/d$ shrinks, so the total sum $S(d)$ **monotonically decreases**.

The feasibility condition $S(d) \le \text{threshold}$ produces a monotonic boolean array over the search space $[1, \max(\text{nums})]$:
- `[false, false, ..., false, true, true, ..., true]`

We want the **FIRST `true`** (the smallest valid divisor $d$).

```
Search Space for nums = [1, 2, 5, 9], threshold = 6 (max = 9):
Divisor d:       1    2    3    4  |   5    6    7    8    9
Sum of ceils:   17   10    7    7  |   5    5    4    4    4
<= 6:            F    F    F    F  |   T    T    T    T    T
                                       ^
                                First 'true' = 5
```

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Iterate divisor $d$ from $1$ to $\max(\text{nums})$. Compute $S(d)$. Return the first $d$ where $S(d) \le \text{threshold}$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

int smallestDivisorLinear(const vector<int>& nums, int threshold) {
    int maxVal = *max_element(nums.begin(), nums.end());
    
    for (int d = 1; d <= maxVal; d++) {
        long long sum = 0;
        for (int x : nums) {
            sum += (x + d - 1) / d; // integer ceiling
        }
        if (sum <= threshold) {
            return d;
        }
    }
    return maxVal;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\max(\text{nums}) \times n)$ — with $\max = 10^6$ and $n = 5 \times 10^4$, this takes $5 \times 10^{10}$ operations, triggering Time Limit Exceeded.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Linear scan checks millions of redundant divisor candidates one by one.

---

## 4. Approach 2 — Better (Bounded Lower-Scan)

### Idea
The theoretical absolute minimum divisor cannot be smaller than $\lceil \sum \text{nums} / \text{threshold} \rceil$. We can start our linear search from $\max(1LL, \lfloor \sum \text{nums} / \text{threshold} \rfloor)$.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <numeric>
using namespace std;

int smallestDivisorBetter(const vector<int>& nums, int threshold) {
    long long totalSum = 0;
    int maxVal = 0;
    for (int x : nums) {
        totalSum += x;
        maxVal = max(maxVal, x);
    }
    
    int startD = max(1LL, totalSum / threshold);
    for (int d = startD; d <= maxVal; d++) {
        long long sum = 0;
        for (int x : nums) {
            sum += (x + d - 1) / d;
        }
        if (sum <= threshold) {
            return d;
        }
    }
    return maxVal;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\max(\text{nums}) - \text{avg}) \times n)$ — still linear in the value space, failing for skewed arrays.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: It only adjusts the start point; logarithmic search is needed for guaranteed optimal bounds.

---

## 5. Approach 3 — Optimal (Binary Search on Answer Space)

### Idea
1. Set the search space: `low = 1`, `high = *max_element(nums.begin(), nums.end())`.
2. While `low <= high`:
   - Compute `mid = low + (high - low) / 2`.
   - Calculate `sum = calculateSum(nums, mid)`.
   - If `sum <= threshold`: `mid` is a valid divisor. Store `ans = mid` and search left `high = mid - 1` to find a smaller valid divisor.
   - Else: `mid` is too small (`sum > threshold`). Search right `low = mid + 1`.
3. Return `ans` (or `low` at termination).

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    long long calculateSum(const vector<int>& nums, int divisor, int threshold) {
        long long sum = 0;
        for (int x : nums) {
            // Integer ceiling division: ceil(x / divisor) = (x + divisor - 1) / divisor
            sum += ((long long)x + divisor - 1) / divisor;
            
            // Early exit optimization: stop immediately if running sum exceeds threshold
            if (sum > threshold) return sum;
        }
        return sum;
    }

public:
    int smallestDivisor(vector<int>& nums, int threshold) {
        int low = 1;
        int high = *max_element(nums.begin(), nums.end());
        int ans = high;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            if (calculateSum(nums, mid, threshold) <= threshold) {
                ans = mid;        // valid divisor, try to find smaller on left
                high = mid - 1;
            } else {
                low = mid + 1;    // sum exceeds threshold, divisor too small
            }
        }
        
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \cdot \log_2(\max(\text{nums})))$ — binary search takes $\log_2(10^6) \approx 20$ iterations. In each iteration, we scan $n = 5 \times 10^4$ elements. Total operations: $20 \times 5 \times 10^4 = 10^6$, executing in $\approx 8\text{ms}$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary memory.
- **Why this is optimal**: Verifying a candidate divisor requires $\Omega(n)$ array inspection, and binary search achieves the theoretical minimum comparison queries $\Omega(\log(\text{range}))$.

---

## 6. Dry Run

`nums = [1, 2, 5, 9]`, `threshold = 6`. Search range: `low = 1, high = 9`.

| Iteration | `low` | `high` | `mid` (divisor) | Terms Breakdown $\sum \lceil x / \text{mid} \rceil$ | Total Sum | `sum <= 6` | Action |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| **1** | `1` | `9` | `5` | $\lceil 1/5 \rceil + \lceil 2/5 \rceil + \lceil 5/5 \rceil + \lceil 9/5 \rceil = 1 + 1 + 1 + 2$ | **5** | $5 \le 6$ (True) | `ans = 5, high = 4` |
| **2** | `1` | `4` | `2` | $\lceil 1/2 \rceil + \lceil 2/2 \rceil + \lceil 5/2 \rceil + \lceil 9/2 \rceil = 1 + 1 + 3 + 5$ | **10** | $10 \le 6$ (False) | `low = mid + 1 = 3` |
| **3** | `3` | `4` | `3` | $\lceil 1/3 \rceil + \lceil 2/3 \rceil + \lceil 5/3 \rceil + \lceil 9/3 \rceil = 1 + 1 + 2 + 3$ | **7** | $7 \le 6$ (False) | `low = mid + 1 = 4` |
| **4** | `4` | `4` | `4` | $\lceil 1/4 \rceil + \lceil 2/4 \rceil + \lceil 5/4 \rceil + \lceil 9/4 \rceil = 1 + 1 + 2 + 3$ | **7** | $7 \le 6$ (False) | `low = mid + 1 = 5` |
| **Exit** | `5` | `4` | - | `low (5) > high (4)` | - | - | **Return `ans = 5`** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **`threshold == n`**: Every element must evaluate to $\lceil nums[i] / d \rceil = 1$. The only valid divisor is strictly $\max(\text{nums})$.
- **`threshold` is very large ($10^6$)**: Divisor `1` easily satisfies condition $\implies$ returns `1`.
- **Single Element Array (`nums = [8], threshold = 3`)**: $\lceil 8 / d \rceil \le 3 \implies d = 3$ (since $\lceil 8/3 \rceil = 3$).

### Common Bugs to Avoid
- **Floating-Point Division**: Using `(int)ceil((double)x / divisor)` can introduce IEEE 754 precision inaccuracies on edge boundary quotients. Always use pure integer arithmetic: `((long long)x + divisor - 1) / divisor`.
- **Setting `high = threshold`**: The maximum possible divisor needed is never larger than $\max(\text{nums})$. Setting `high = threshold` can needlessly widen the search space when $\text{threshold} > \max(\text{nums})$.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is this problem mathematically isomorphic to Koko Eating Bananas (LeetCode 875)?**  
  **A**: In Koko Eating Bananas, $piles[i]$ represents banana count, $k$ is eating speed, and $h$ is total hours: $\sum \lceil piles[i] / k ceil \le h$. In Smallest Divisor, $nums[i]$ is the array element, $divisor$ is $d$, and $threshold$ is $T$: $\sum \lceil nums[i] / d ceil \le T$. The mathematical models and binary search proofs are 100% identical.

- **Q2: How can we implement early termination in the validation helper?**  
  **A**: While summing $\sum \lceil x / d ceil$, if `runningSum > threshold`, we can break the loop immediately without inspecting remaining elements. This provides a $2	imes - 3	imes$ constant factor speedup in practice.

- **Q3: What is the theoretical minimum divisor possible?**  
  **A**: By the inequality $\sum \lceil x_i / d ceil \ge (\sum x_i) / d$, any valid divisor must satisfy $d \ge \lceil (\sum x_i) / 	ext{threshold} ceil$.

- **Q4: What if `nums` can contain negative numbers?**  
  **A**: If elements can be negative, integer division truncation rules in C++ round toward zero, which is different from mathematical ceiling. We would need `floor` / `ceil` sign adjustments.

- **Q5: Can we use SIMD vectorization to compute the ceiling sum across 8 integers at once?**  
  **A**: Yes, with AVX2 instructions (`_mm256_add_epi32` and integer division via reciprocal multiplication), 8 elements can be divided and accumulated per CPU clock cycle.

---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `LeetCode-1283`, `Medium`
- **Related problems to practice next**:
  - **Koko Eating Bananas (LeetCode 875)**: Identical ceiling division formula.
  - **Capacity to Ship Packages within D Days (LeetCode 1011)**: Contiguous chunk packing.
  - **Split Array Largest Sum (LeetCode 410)**: Partition minimization.
