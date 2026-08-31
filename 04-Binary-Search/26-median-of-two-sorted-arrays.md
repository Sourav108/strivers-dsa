# Median of Two Sorted Arrays of Different Sizes (Step 4.2 — BS on Answers)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Median of Two Sorted Arrays of Different Sizes](https://takeuforward.org/data-structure/median-of-two-sorted-arrays-of-different-sizes/)
- **Difficulty**: Hard
- **Statement**: Given two sorted arrays `nums1` and `nums2` of size $m$ and $n$ respectively, return the median of the two sorted arrays. The overall run time complexity must be $\mathcal{O}(\log(m + n))$.

---

## 1. Problem, Restated

Find the median value that would appear at the center if both sorted arrays were merged together, without actually creating a merged array.

- **Input**: Input arrays/parameters.
- **Output**: Value meeting the specification.
- **Constraints**: $1 \le n, m \le 10^5$, standard 32/64-bit limits.

---

## 2. Intuition & Pattern

The median divides the combined array into a **Left Half** and a **Right Half** of equal size: $\text{leftSize} = \lfloor (m + n + 1) / 2 \rfloor$.
If we take $px$ elements from `nums1` into the left half, we MUST take $py = \text{leftSize} - px$ elements from `nums2` into the left half.
We binary search $px \in [0, m]$ on the smaller array ($m \le n$).
A partition $(px, py)$ is valid if and only if:
1. $l1 \le r2$ (`nums1[px-1] <= nums2[py]`)
2. $l2 \le r1$ (`nums2[py-1] <= nums1[px]`)
Once valid:
- If total length is **odd**: $\text{median} = \max(l1, l2)$.
- If total length is **even**: $\text{median} = \frac{\max(l1, l2) + \min(r1, r2)}{2.0}$.

- **Underlying Pattern**: `Binary Search on Partition Cut of Smaller Array`.
- **The "Aha!" Moment**: Recognizing the monotonic boundary or partition cut that enables logarithmic halving.

---

## 3. Approach 1 — Brute Force

### Idea
Merge Both Arrays: Merge `nums1` and `nums2` into a new vector of size $m+n$ using two pointers in $\mathcal{O}(m+n)$ time and space, then return the median element.

### C++17 Code
```cpp
#include <vector>
using namespace std;

double findMedianSortedArraysBrute(vector<int>& nums1, vector<int>& nums2) {
    int m = nums1.size(), n = nums2.size();
    vector<int> merged;
    int i = 0, j = 0;
    while (i < m && j < n) {
        if (nums1[i] <= nums2[j]) merged.push_back(nums1[i++]);
        else merged.push_back(nums2[j++]);
    }
    while (i < m) merged.push_back(nums1[i++]);
    while (j < n) merged.push_back(nums2[j++]);
    
    int total = m + n;
    if (total % 2 == 1) return merged[total / 2];
    return (merged[total / 2 - 1] + merged[total / 2]) / 2.0;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    double findMedianSortedArraysBrute(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length;
        int[] merged;
        int i = 0, j = 0;
        while (i < m && j < n) {
            if (nums1[i] <= nums2[j]) merged.add(nums1[i++]);
            else merged.add(nums2[j++]);
        }
        while (i < m) merged.add(nums1[i++]);
        while (j < n) merged.add(nums2[j++]);
        
        int total = m + n;
        if (total % 2 == 1) return merged[total / 2];
        return (merged[total / 2 - 1] + merged[total / 2]) / 2.0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(m + n)$ time.
- **Space Complexity**: $\mathcal{O}(m + n)$ space.
- **Why it's not good enough**: Fails the problem's strict $\mathcal{O}(\log(m+n))$ time requirement and uses extra memory.

---

## 4. Approach 2 — Better

### Idea
Two Pointers without Extra Space: Count indices up to $(m+n)/2$ using two scanning pointers without storing the merged elements.

### C++17 Code
```cpp
#include <vector>
using namespace std;

double findMedianSortedArraysBetter(vector<int>& nums1, vector<int>& nums2) {
    int m = nums1.size(), n = nums2.size();
    int total = m + n;
    int idx2 = total / 2;
    int idx1 = idx2 - 1;
    int el1 = -1, el2 = -1;
    
    int i = 0, j = 0, count = 0;
    while (i < m && j < n) {
        int val = (nums1[i] <= nums2[j]) ? nums1[i++] : nums2[j++];
        if (count == idx1) el1 = val;
        if (count == idx2) el2 = val;
        count++;
    }
    while (i < m) {
        int val = nums1[i++];
        if (count == idx1) el1 = val;
        if (count == idx2) el2 = val;
        count++;
    }
    while (j < n) {
        int val = nums2[j++];
        if (count == idx1) el1 = val;
        if (count == idx2) el2 = val;
        count++;
    }
    if (total % 2 == 1) return el2;
    return (el1 + el2) / 2.0;
}
```

### Java Code
```java
import java.util.*;

class Solution {
    double findMedianSortedArraysBetter(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length;
        int total = m + n;
        int idx2 = total / 2;
        int idx1 = idx2 - 1;
        int el1 = -1, el2 = -1;
        
        int i = 0, j = 0, count = 0;
        while (i < m && j < n) {
            int val = (nums1[i] <= nums2[j]) ? nums1[i++] : nums2[j++];
            if (count == idx1) el1 = val;
            if (count == idx2) el2 = val;
            count++;
        }
        while (i < m) {
            int val = nums1[i++];
            if (count == idx1) el1 = val;
            if (count == idx2) el2 = val;
            count++;
        }
        while (j < n) {
            int val = nums2[j++];
            if (count == idx1) el1 = val;
            if (count == idx2) el2 = val;
            count++;
        }
        if (total % 2 == 1) return el2;
        return (el1 + el2) / 2.0;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((m + n)/2) = \mathcal{O}(m + n)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why it's still not optimal**: Still linear time $\mathcal{O}(m+n)$, failing the logarithmic requirement.

---

## 5. Approach 3 — Optimal

### Idea
Binary Search on Partition of Smaller Array: Ensure $m \le n$. `low = 0, high = m`. Compute cut `px = (low + high) / 2` and `py = (m + n + 1) / 2 - px`. Read boundary values $(l1, r1, l2, r2)$ using `INT_MIN`/`INT_MAX` sentinels. If $l1 \le r2$ and $l2 \le r1$, return median. If $l1 > r2$, move left `high = px - 1`; else move right `low = px + 1`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
using namespace std;

class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int m = nums1.size();
        int n = nums2.size();
        
        // Ensure nums1 is the smaller array to optimize binary search to O(log(min(m, n)))
        if (m > n) {
            return findMedianSortedArrays(nums2, nums1);
        }
        
        int low = 0, high = m;
        int leftTotal = (m + n + 1) / 2; // total elements in left partition
        
        while (low <= high) {
            int px = low + (high - low) / 2; // elements taken from nums1
            int py = leftTotal - px;         // elements taken from nums2
            
            // Boundary values with sentinel guards for empty subpartitions
            int l1 = (px == 0) ? INT_MIN : nums1[px - 1];
            int r1 = (px == m) ? INT_MAX : nums1[px];
            int l2 = (py == 0) ? INT_MIN : nums2[py - 1];
            int r2 = (py == n) ? INT_MAX : nums2[py];
            
            // Valid partition condition: everything on left <= everything on right
            if (l1 <= r2 && l2 <= r1) {
                // Odd total elements: median is the maximum of left partition
                if ((m + n) % 2 == 1) {
                    return max(l1, l2);
                }
                // Even total elements: average of left max and right min
                return (max(l1, l2) + min(r1, r2)) / 2.0;
            } 
            // Too many elements from nums1
            else if (l1 > r2) {
                high = px - 1; // search left in nums1
            } 
            // Too few elements from nums1
            else {
                low = px + 1;  // search right in nums1
            }
        }
        
        return 0.0;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length;
        int n = nums2.length;
        
        // Ensure nums1 is the smaller array to optimize binary search to O(log(Math.min(m, n)))
        if (m > n) {
            return findMedianSortedArrays(nums2, nums1);
        }
        
        int low = 0, high = m;
        int leftTotal = (m + n + 1) / 2; // total elements in left partition
        
        while (low <= high) {
            int px = low + (high - low) / 2; // elements taken from nums1
            int py = leftTotal - px;         // elements taken from nums2
            
            // Boundary values with sentinel guards for empty subpartitions
            int l1 = (px == 0) ? Integer.MIN_VALUE : nums1[px - 1];
            int r1 = (px == m) ? Integer.MAX_VALUE : nums1[px];
            int l2 = (py == 0) ? Integer.MIN_VALUE : nums2[py - 1];
            int r2 = (py == n) ? Integer.MAX_VALUE : nums2[py];
            
            // Valid partition condition: everything on left <= everything on right
            if (l1 <= r2 && l2 <= r1) {
                // Odd total elements: median is the maximum of left partition
                if ((m + n) % 2 == 1) {
                    return Math.max(l1, l2);
                }
                // Even total elements: average of left max and right min
                return (Math.max(l1, l2) + Math.min(r1, r2)) / 2.0;
            } 
            // Too many elements from nums1
            else if (l1 > r2) {
                high = px - 1; // search left in nums1
            } 
            // Too few elements from nums1
            else {
                low = px + 1;  // search right in nums1
            }
        }
        
        return 0.0;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2(\min(m, n)))$ — binary search runs exclusively on the smaller array of size $m$. For $m = 10^5$, takes at most $17$ iterations!
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Optimal bound for selection in two sorted arrays, beating even $\mathcal{O}(\log(m+n))$.

---

## 6. Dry Run

`nums1 = [1, 3, 8]`, $m=3$. `nums2 = [7, 9, 10, 11]`, $n=4$. Total $= 7$ (Odd). `leftTotal = (3+4+1)/2 = 4`

| Step | Action / State Change | Result |
|---|---|---|
| `Iter 1` | px = 1, py = 4-1 = 3. l1=1, r1=3, l2=10, r2=11 | l2 (10) > r1 (3) -> need more from nums1 -> low = px + 1 = 2 |
| `Iter 2` | px = 2, py = 4-2 = 2. l1=3, r1=8, l2=9, r2=10 | l2 (9) > r1 (8) -> low = px + 1 = 3 |
| `Iter 3` | px = 3, py = 4-3 = 1. l1=8, r1=INT_MAX, l2=7, r2=9 | l1(8) <= r2(9) and l2(7) <= r1(MAX) **VALID!** |
| `Result` | Odd total (7) -> Median = max(l1, l2) = max(8, 7) = 8 | Final Median = 8.0 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- One array is completely empty (`nums1 = [], nums2 = [1]` -> returns 1.0).
- Disjoint non-overlapping arrays (`[1, 2]` and `[3, 4]` -> correctly partitions at boundaries).
- All elements in nums1 smaller than all in nums2.

### Common Bugs to Avoid
- Not ensuring `m <= n`, which can cause `py = leftTotal - px` to exceed array bounds of `nums2`.
- Using integer division `/ 2` instead of `/ 2.0` on even length arrays, losing decimal precision.
- Using `(m + n) / 2` instead of `(m + n + 1) / 2` for `leftTotal`, which misaligns odd-length medians.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does `leftTotal = (m + n + 1) / 2` work uniformly for both odd and even sums?**  
  **A**: For even sums (e.g. $4+4=8$), $(8+1)/2 = 4$, left half has 4 elements. For odd sums (e.g. $3+4=7$), $(7+1)/2 = 4$, left half has 4 elements and right half has 3 elements. Thus, for odd total length, the median is always strictly $\max(l1, l2)$!

- **Q2: Why must we swap to ensure nums1 is the smaller array?**  
  **A**: 1) Minimizes binary search iterations to $\mathcal{O}(\log(\min(m, n)))$. 2) Guarantees $py = \text{leftTotal} - px \ge 0$ and $py \le n$ for all $px \in [0, m]$, preventing out-of-bounds array reads.

- **Q3: How does this algorithm generalize to find the K-th element of two sorted arrays?**  
  **A**: Set $\text{leftTotal} = k$. The partition condition and binary search remain identical, returning $\max(l1, l2)$ in $\mathcal{O}(\log(\min(m, n)))$!

- **Q4: Why are INT_MIN and INT_MAX sentinels necessary?**  
  **A**: When $px = 0$, no elements are taken from `nums1` into the left half ($l1 = -\infty$). When $px = m$, all elements are taken ($r1 = +\infty$). Sentinels make the $l1 \le r2$ check seamless without branching.

- **Q5: Can we find the median of K sorted arrays?**  
  **A**: For $K$ sorted arrays, binary search on value range using upper_bound takes $\mathcal{O}(K \log n \cdot \log(\text{val\_range}))$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Two Arrays`, `Divide and Conquer`, `LeetCode-4`, `Hard`
- **Related problems to practice next**:
- **Kth Element of Two Sorted Arrays**: Generalization to arbitrary K.
- **Median in a Row-Wise Sorted Matrix**: 2D matrix median.
- **Search in 2D Matrix**: Matrix binary search.
