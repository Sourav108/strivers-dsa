# Kth Missing Positive Number (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [LeetCode #1539 - Kth Missing Positive Number](https://leetcode.com/problems/kth-missing-positive-number/) | [TakeUForward](https://takeuforward.org/binary-search/kth-missing-positive-number/)
- **Difficulty**: Easy
- **Statement**: Given an array `arr` of positive integers sorted in a **strictly increasing order**, and an integer $k$. Return the $k^{\text{th}}$ positive integer that is missing from this array.

---

## 1. Problem, Restated

Given a sorted list of positive integers starting from $1$, find the $k$-th missing positive integer that does not appear in `arr`.

For example, if `arr = [2, 3, 4, 7, 11]` and $k = 5$:
- The positive integers are $1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, \dots$
- The missing numbers are: **1**, (2, 3, 4 present), **5**, **6**, (7 present), **8**, **9**, **10**, (11 present), **12**, $\dots$
- The $5^{\text{th}}$ missing number is **9**.

- **Input**: Sorted vector of positive integers `arr`, and integer $k$.
- **Output**: The $k$-th missing positive integer.
- **Constraints**: $1 \le \text{arr.length} \le 1000$, $1 \le \text{arr}[i] \le 1000$, $1 \le k \le 1000$.

---

## 2. Intuition & Pattern

### The Missing Count Index Formula
At any 0-based index $i$:
- The actual value stored in the array is `arr[i]`.
- In a hypothetical full array with zero missing numbers, index $i$ would store the value $i + 1$ (i.e. index 0 holds 1, index 1 holds 2, etc.).
- Therefore, the number of missing integers strictly to the left of index $i$ is:
  $$\text{missing}(i) = \text{arr}[i] - (i + 1)$$

Because `arr` is strictly increasing, $\text{missing}(i)$ is **monotonically non-decreasing**:
```
Index i:          0    1    2    3    4
arr[i]:           2    3    4    7   11
Ideal (i + 1):    1    2    3    4    5
missing(i):       1    1    1    3    6
```

If we want the $k=5^{\text{th}}$ missing number, we can binary search the index space to find the boundary where $\text{missing}(i)$ transitions from $< k$ to $\ge k$.

When the binary search loop `low <= high` terminates:
- `high` points to the largest index where $\text{missing}(high) < k$.
- `low` ($= high + 1$) points to the smallest index where $\text{missing}(low) \ge k$.

The answer is:
$$\begin{aligned}
\text{ans} &= \text{arr}[high] + (k - \text{missing}(high)) \\
&= \text{arr}[high] + k - (\text{arr}[high] - (high + 1)) \\
&= high + 1 + k \\
&= low + k
\end{aligned}$$

The entire formula simplifies with absolute mathematical beauty to: **$\text{ans} = low + k$**!

---

## 3. Approach 1 — Brute Force (Linear K-Shift Scan)

### Idea
Initialize our candidate answer as $k$. Traverse through the array from left to right. If the current number `arr[i]` is $\le k$, it means an existing number is occupying a slot $\le k$, so the $k$-th missing number must be pushed forward by $1$ (`k++`). The moment `arr[i] > k`, all remaining elements in the array are too large to affect $k$, so we stop and return $k$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int findKthPositiveLinear(const vector<int>& arr, int k) {
    for (int num : arr) {
        if (num <= k) {
            k++; // push candidate forward for each present number <= k
        } else {
            break;
        }
    }
    return k;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — in the worst case, scans all $n$ elements.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Linear scan takes $\mathcal{O}(n)$ time, missing the opportunity to exploit the sorted structure via $\mathcal{O}(\log n)$ binary search.

---

## 4. Approach 2 — Better (Hash Set Lookup)

### Idea
Insert all elements of `arr` into an `unordered_set`. Iterate $x = 1, 2, 3, \dots$, decrementing $k$ whenever $x$ is not in the set, until $k == 0$.

### C++17 Code
```cpp
#include <vector>
#include <unordered_set>
using namespace std;

int findKthPositiveHashSet(const vector<int>& arr, int k) {
    unordered_set<int> present(arr.begin(), arr.end());
    int current = 1;
    while (true) {
        if (!present.count(current)) {
            k--;
            if (k == 0) return current;
        }
        current++;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n + k)$ — hash set insertion and iteration until $k$-th missing number.
- **Space Complexity**: $\mathcal{O}(n)$ for hash set.
- **Why it's still not optimal**: Uses $\mathcal{O}(n)$ extra heap memory and runs in linear time.

---

## 5. Approach 3 — Optimal (Binary Search on Missing Counts)

### Idea
1. Maintain index boundaries: `low = 0, high = n - 1`.
2. While `low <= high`:
   - Compute `mid = low + (high - low) / 2`.
   - Calculate missing count up to `mid`: `missing = arr[mid] - (mid + 1)`.
   - If `missing < k`: we need more missing numbers, search right `low = mid + 1`.
   - Else: `missing >= k`, search left `high = mid - 1`.
3. Return `low + k` (which is mathematically equal to `high + 1 + k`).

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    int findKthPositive(vector<int>& arr, int k) {
        int low = 0;
        int high = (int)arr.size() - 1;
        
        while (low <= high) {
            int mid = low + (high - low) / 2;
            int missing = arr[mid] - (mid + 1);
            
            if (missing < k) {
                low = mid + 1;  // need more missing numbers, look right
            } else {
                high = mid - 1; // enough missing numbers, look left
            }
        }
        
        // At loop exit: low = high + 1
        // ans = arr[high] + (k - missing(high)) = high + 1 + k = low + k
        return low + k;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — binary search on the array indices halves the range on every iteration (at most $10$ iterations for $n = 1000$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space — uses only two integer pointers (`low`, `high`).
- **Why this is optimal**: Achieves the theoretical lower bound $\Omega(\log n)$ on sorted array index inspection with strictly $\mathcal{O}(1)$ memory.

---

## 6. Dry Run

`arr = [2, 3, 4, 7, 11]`, $k = 5$, $n = 5$.
Search space: `low = 0, high = 4`.

| Iteration | `low` | `high` | `mid` | `arr[mid]` | `missing = arr[mid] - (mid + 1)` | `missing < 5` | Action / Pointer Update |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| **1** | `0` | `4` | `2` | `4` | $4 - (2 + 1) = 4 - 3 = \mathbf{1}$ | $1 < 5$ (True) | `low = mid + 1 = 3` |
| **2** | `3` | `4` | `3` | `7` | $7 - (3 + 1) = 7 - 4 = \mathbf{3}$ | $3 < 5$ (True) | `low = mid + 1 = 4` |
| **3** | `4` | `4` | `4` | `11` | $11 - (4 + 1) = 11 - 5 = \mathbf{6}$ | $6 < 5$ (False) | `high = mid - 1 = 3` |
| **Exit** | `4` | `3` | - | - | `low (4) > high (3)` | - | **Return `low + k = 4 + 5 = 9`** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **$k$ smaller than `arr[0]`**: `arr = [5, 6, 7], k = 2`
  - Binary search drives `high` to `-1`, `low` stays at `0`.
  - `ans = low + k = 0 + 2 = 2` ✅ (Missing numbers: 1, 2, 3, 4 $\implies$ 2nd is 2).
- **$k$ larger than all missing numbers in array**: `arr = [1, 2, 3, 4], k = 2`
  - `missing` is 0 everywhere, drives `low` to `n = 4`.
  - `ans = low + k = 4 + 2 = 6` ✅ (Missing numbers: 5, 6 $\implies$ 2nd is 6).
- **Single element array**: `arr = [2], k = 1` $\implies$ `missing = 2 - 1 = 1 >= 1` $\implies$ `high = -1, low = 0` $\implies$ `0 + 1 = 1`.

### Common Bugs to Avoid
- **Using `arr[high]` directly without boundary check**: If $k$ is smaller than `arr[0]`, `high` becomes `-1`. Attempting to access `arr[high]` causes out-of-bounds segmentation fault. Using the simplified formula `low + k` avoids any array access after the loop!
- **Using 1-based formula with 0-based arrays**: Forgetting the `+ 1` in `arr[mid] - (mid + 1)`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does `low + k` always work, even when `high == -1` or `low == n`?**  
  **A**:
  - When `high == -1`, all elements are $> k$, so no numbers before $k$ are taken; the $k$-th missing number is simply $k$ ($low + k = 0 + k = k$).
  - When `low == n`, all elements in `arr` are $\le$ the $k$-th missing number, so all $n$ present numbers shift $k$ by $n$; the answer is $n + k = low + k$.
  - In the interior, $low = high + 1$, and algebraic cancellation proves $	ext{arr}[high] + (k - 	ext{missing}(high)) = high + 1 + k = low + k$.

- **Q2: How would you solve this if the sequence is an Arithmetic Progression (AP) with common difference $d$?**  
  **A**:
  - The expected value at index $i$ in an AP starting at $A$ is $A + i \cdot d$.
  - The count of missing numbers before index $i$ becomes: $\text{missing}(i) = \frac{\text{arr}[i] - (A + i \cdot d)}{d}$.
  - Binary search logic remains identical.

- **Q3: What is the difference between BS on Answer Values vs BS on Array Indices here?**  
  **A**:
  - BS on answer values searches the range $[1, \text{arr}[n-1] + k]$ with feasibility checking in $\mathcal{O}(\log n)$, taking $\mathcal{O}(\log(\text{val}) \cdot \log n)$.
  - BS on array indices directly searches the index range $[0, n-1]$ in $\mathcal{O}(\log n)$ total time, which is strictly faster.

- **Q4: Can we answer $Q$ queries with different $k_j$ on a static array in $\mathcal{O}(\log n)$ per query?**  
  **A**: Yes! Since `arr` is static, each query $k_j$ executes binary search on index space independently in $\mathcal{O}(\log n)$ time and $\mathcal{O}(1)$ space.

- **Q5: What if the array contains duplicate numbers?**  
  **A**: If duplicates exist, the problem statement says "strictly increasing", but if duplicates were allowed, we would subtract distinct elements seen so far (using prefix uniqueness count).

---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `BS on Answers`, `Math`, `LeetCode-1539`, `Easy`
- **Related problems to practice next**:
  - **Missing Number (Unsorted)**: XOR cancellation on unsorted array.
  - **Find Missing and Repeating Number**: Mathematical system of equations.
  - **Capacity to Ship Packages**: Partition-based BS on answers.
