# Kth Element of Two Sorted Arrays (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Kth Element of Two Sorted Arrays](https://takeuforward.org/data-structure/k-th-element-of-two-sorted-arrays/)
- **Difficulty**: Hard
- **Statement**: Given two sorted arrays `arr1` and `arr2` of size $m$ and $n$ respectively, and an integer $k$. Find the element that would be at the $k^{\text{th}}$ position of the final combined sorted array.

---

## 1. Problem, Restated

Find the $k$-th smallest number in the merged sorted sequence of two sorted arrays in $\mathcal{O}(\log(\min(m, n)))$ time without merging them.

- **Input**: Matrix or array inputs with query parameters.
- **Output**: Value or boolean meeting the specification.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Generalization of Median of Two Sorted Arrays. We want the left partition of the combined array to contain exactly $k$ elements. If we take $px$ elements from `arr1`, we must take $py = k - px$ elements from `arr2`.

### Crucial Search Space Bounds for $px$:
- Minimum elements taken from `arr1`: $\max(0, k - n)$ (if $k > n$, even taking all $n$ elements from `arr2` requires taking at least $k - n$ from `arr1`).
- Maximum elements taken from `arr1`: $\min(k, m)$ (we only need $k$ elements in total, and `arr1` only has $m$ elements).

When $l1 \le r2$ and $l2 \le r1$, the partition is valid, and the $k$-th element is simply $\max(l1, l2)$.

- **Underlying Pattern**: `Binary Search on Partition Cut (k Elements on Left)`.
- **The "Aha!" Moment**: Mapping 2D coordinates or partition cuts into a unified 1D search space.

---

## 3. Approach 1 — Brute Force

### Idea
Merge Arrays up to index K: Use two pointers to count up to the $k$-th element in $\mathcal{O}(k)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int kthElementBrute(const vector<int>& arr1, const vector<int>& arr2, int k) {
    int m = arr1.size(), n = arr2.size();
    int i = 0, j = 0, count = 0;
    while (i < m && j < n) {
        int val = (arr1[i] <= arr2[j]) ? arr1[i++] : arr2[j++];
        count++;
        if (count == k) return val;
    }
    while (i < m) {
        count++;
        if (count == k) return arr1[i];
        i++;
    }
    while (j < n) {
        count++;
        if (count == k) return arr2[j];
        j++;
    }
    return -1;
}
```

### Java Code
```java
class Solution {
    int kthElementBrute(int[] arr1, int[] arr2, int k) {
        int m = arr1.length, n = arr2.length;
        int i = 0, j = 0, count = 0;
        while (i < m && j < n) {
            int val = (arr1[i] <= arr2[j]) ? arr1[i++] : arr2[j++];
            count++;
            if (count == k) return val;
        }
        while (i < m) {
            count++;
            if (count == k) return arr1[i];
            i++;
        }
        while (j < n) {
            count++;
            if (count == k) return arr2[j];
            j++;
        }
        return -1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(k)$ — worst case $\mathcal{O}(m + n)$.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's not good enough**: Fails the $\mathcal{O}(\log(m+n))$ logarithmic requirement when $k \approx 10^5$.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Binary Search on Partition Range $[\max(0, k-n), \min(k, m)]$: Ensure `arr1` is the smaller array. Binary search $px$. Calculate $py = k - px$. Read sentinels $l1, r1, l2, r2$. If $l1 \le r2$ and $l2 \le r1$, return $\max(l1, l2)$. If $l1 > r2$, `high = px - 1`; else `low = px + 1`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
public:
    int kthElement(vector<int>& arr1, vector<int>& arr2, int k) {
        int m = arr1.size();
        int n = arr2.size();
        
        // Ensure arr1 is the smaller array for O(log(min(m, n)))
        if (m > n) {
            return kthElement(arr2, arr1, k);
        }
        
        // Bounded search space for elements taken from arr1
        int low = max(0, k - n);
        int high = min(k, m);
        
        while (low <= high) {
            int px = low + (high - low) / 2;
            int py = k - px;
            
            int l1 = (px == 0) ? INT_MIN : arr1[px - 1];
            int r1 = (px == m) ? INT_MAX : arr1[px];
            int l2 = (py == 0) ? INT_MIN : arr2[py - 1];
            int r2 = (py == n) ? INT_MAX : arr2[py];
            
            if (l1 <= r2 && l2 <= r1) {
                return max(l1, l2); // The k-th element is the maximum of the left partition
            } else if (l1 > r2) {
                high = px - 1;      // too many elements from arr1
            } else {
                low = px + 1;       // too few elements from arr1
            }
        }
        
        return -1;
    }
};
```

### Java Code
```java
class Solution {

    int kthElement(int[] arr1, int[] arr2, int k) {
        int m = arr1.length;
        int n = arr2.length;
        
        // Ensure arr1 is the smaller array for O(log(Math.min(m, n)))
        if (m > n) {
            return kthElement(arr2, arr1, k);
        }
        
        // Bounded search space for elements taken from arr1
        int low = Math.max(0, k - n);
        int high = Math.min(k, m);
        
        while (low <= high) {
            int px = low + (high - low) / 2;
            int py = k - px;
            
            int l1 = (px == 0) ? Integer.MIN_VALUE : arr1[px - 1];
            int r1 = (px == m) ? Integer.MAX_VALUE : arr1[px];
            int l2 = (py == 0) ? Integer.MIN_VALUE : arr2[py - 1];
            int r2 = (py == n) ? Integer.MAX_VALUE : arr2[py];
            
            if (l1 <= r2 && l2 <= r1) {
                return Math.max(l1, l2); // The k-th element is the maximum of the left partition
            } else if (l1 > r2) {
                high = px - 1;      // too many elements from arr1
            } else {
                low = px + 1;       // too few elements from arr1
            }
        }
        
        return -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2(\min(m, n)))$ — binary search interval is at most $\min(m, n)$. For $m = 10^5$, takes at most $17$ iterations!
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Optimal bound for finding order statistics in multiple sorted arrays.

---

## 6. Dry Run

`arr1 = [2, 3, 6, 7, 9]`, $m=5$. `arr2 = [1, 4, 8, 10]`, $n=4$. $k = 5$.
`low = max(0, 5-4) = 1, high = min(5, 5) = 5`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | px = 3, py = 5-3 = 2. l1=6, r1=7, l2=4, r2=8 | l1(6) <= r2(8) and l2(4) <= r1(7) **VALID!** |
| `Result` | k-th Element = max(l1, l2) = max(6, 4) = 6 | Final Output = 6 ✅ (Merged: [1, 2, 3, 4, 6, 7, 8, 9, 10]) |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- k = 1 (returns minimum of `arr1[0]` and `arr2[0]`).
- k = m + n (returns maximum of `arr1.back()` and `arr2.back()`).
- k > n (requires `low = k - n` to prevent taking negative elements from `arr2`).

### Common Bugs to Avoid
- Initializing `low = 0` and `high = m` without clamping to `max(0, k - n)` and `min(k, m)`, causing negative indices $py < 0$ or $py > n$.
- Not swapping to make `arr1` smaller.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must low be max(0, k - n)?**  
  **A**: If $k = 7$ and $n = 4$, even if we take all 4 elements from `arr2`, we MUST take at least $7 - 4 = 3$ elements from `arr1`. Setting `low = 0` would attempt $py = 7 - 0 = 7 > n$, leading to out-of-bounds errors.

- **Q2: Why must high be min(k, m)?**  
  **A**: We only need $k$ elements in total on the left side, so we can never take more than $k$ elements from `arr1`. Also, `arr1` only has $m$ elements, so $px \le \min(k, m)$.

- **Q3: How does this compare to Median of Two Sorted Arrays?**  
  **A**: Median is a special case of K-th element where $k = (m + n + 1) / 2$.

- **Q4: What if we have 3 sorted arrays?**  
  **A**: Binary search on value space takes $\mathcal{O}(\log(\text{range}) \cdot (\log m + \log n + \log p))$. Partition-based is $\mathcal{O}(\log^2)$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Two Arrays`, `Divide and Conquer`, `Hard`
- **Related problems to practice next**:
- **Median of Two Sorted Arrays**: Symmetrical median case.
- **Matrix Median**: 2D matrix generalization.
- **Search in Rotated Sorted Array**: Partition invariant.
