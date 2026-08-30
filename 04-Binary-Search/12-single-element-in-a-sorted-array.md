# Single Element in a Sorted Array (Index Parity Check) (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Single Element in a Sorted Array (Index Parity Check)](https://takeuforward.org/data-structure/search-single-element-in-a-sorted-array/)
- **Difficulty**: Medium
- **Statement**: You are given a sorted array consisting of only integers where every element appears exactly twice, except for one element which appears exactly once. Return the single element that appears only once. Your solution must run in $\mathcal{O}(\log n)$ time and $\mathcal{O}(1)$ space.

---

## 1. Problem, Restated

In a sorted array where all numbers form duplicate pairs except one unique number, find that unique number in $\mathcal{O}(\log n)$ time.

- **Input**: Vector of integers `nums`, plus query parameters.
- **Output**: Value or index meeting the specification.
- **Constraints**: $1 \le n \le 10^5$, elements fit in 32-bit signed integers.

---

## 2. Intuition & Pattern

Duplicate pairs follow a strict index parity before and after the single unique element:
- **LEFT of single element**: Pairs start on EVEN indices and end on ODD indices (`(even, odd)` pair $\implies nums[even] == nums[odd]$).
- **RIGHT of single element**: Due to the single element shifting indices by 1, pairs start on ODD indices and end on EVEN indices (`(odd, even)` pair $\implies nums[odd] == nums[even]$).

Therefore, for any `mid`: if `mid` is part of a valid `(even, odd)` pair, we are in the left half $\implies$ single element is on the RIGHT (`low = mid + 1`). Otherwise, we are in the right half $\implies$ single element is on the LEFT (`high = mid - 1`).

- **Underlying Pattern**: `Index Parity Pattern (Even-Odd vs Odd-Even Pairs)`.
- **The "Aha!" Moment**: Recognizing how monotonic invariants or gradient direction eliminate half the search space.

---

## 3. Approach 1 — Brute Force (Linear Scan)

### Idea
Bitwise XOR: XOR all elements in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int singleNonDuplicateXOR(const vector<int>& nums) {
    int xorSum = 0;
    for (int x : nums) xorSum ^= x;
    return xorSum;
}
```

### Java Code
```java
class Solution {
    int singleNonDuplicateXOR(int[] nums) {
        int xorSum = 0;
        for (int x : nums) xorSum ^= x;
        return xorSum;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — linear scan.
- **Space Complexity**: $\mathcal{O}(1)$ — constant space.
- **Why it's not good enough**: Linear time fails the strict $\mathcal{O}(\log n)$ requirement.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal (Binary Search)

### Idea
Binary Search on Index Parity: Check boundary elements at index 0 and $n-1$. Search within `[1..n-2]`. At `mid`, check if `nums[mid]` is equal to its neighbors. If not, `nums[mid]` is the single element. If `(mid % 2 == 1 && nums[mid] == nums[mid-1]) || (mid % 2 == 0 && nums[mid] == nums[mid+1])`, we are in left half $\implies low = mid + 1$; else $high = mid - 1$.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int singleNonDuplicate(const vector<int>& nums) {
    int n = nums.size();
    
    // Boundary checks for single element or ends
    if (n == 1) return nums[0];
    if (nums[0] != nums[1]) return nums[0];
    if (nums[n - 1] != nums[n - 2]) return nums[n - 1];
    
    int low = 1, high = n - 2;
    while (low <= high) {
        int mid = low + (high - low) / 2;
        
        // If nums[mid] is not equal to left and right neighbor, it is the single element
        if (nums[mid] != nums[mid - 1] && nums[mid] != nums[mid + 1]) {
            return nums[mid];
        }
        
        // Check if we are in the left half:
        // (even, odd) pair -> (mid is odd & equals mid-1) OR (mid is even & equals mid+1)
        if ((mid % 2 == 1 && nums[mid] == nums[mid - 1]) ||
            (mid % 2 == 0 && nums[mid] == nums[mid + 1])) {
            low = mid + 1;  // Single element is in right half
        } else {
            high = mid - 1; // Single element is in left half
        }
    }
    
    return -1;
}
```

### Java Code
```java
class Solution {
    int singleNonDuplicate(int[] nums) {
        int n = nums.length;
        
        // Boundary checks for single element or ends
        if (n == 1) return nums[0];
        if (nums[0] != nums[1]) return nums[0];
        if (nums[n - 1] != nums[n - 2]) return nums[n - 1];
        
        int low = 1, high = n - 2;
        while (low <= high) {
            int mid = low + (high - low) / 2;
            
            // If nums[mid] is not equal to left and right neighbor, it is the single element
            if (nums[mid] != nums[mid - 1] && nums[mid] != nums[mid + 1]) {
                return nums[mid];
            }
            
            // Check if we are in the left half:
            // (even, odd) pair . (mid is odd equals mid-1) OR (mid is even equals mid+1)
            if ((mid % 2 == 1 && nums[mid] == nums[mid - 1]) ||
                (mid % 2 == 0 && nums[mid] == nums[mid + 1])) {
                low = mid + 1;  // Single element is in right half
            } else {
                high = mid - 1; // Single element is in left half
            }
        }
        
        return -1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — halves search space on each step.
- **Space Complexity**: $\mathcal{O}(1)$ — constant auxiliary memory.
- **Why this is optimal**: Exploits parity disruption in optimal logarithmic time with zero boundary index out-of-bounds risks.

---

## 6. Dry Run

`nums = [1, 1, 2, 3, 3, 4, 4, 8, 8]`, $n = 9$

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | low=1, high=7 | ready |
| `Iter 1` | mid = 4 (val 3). mid is even (4), nums[4]==nums[3] (odd, even pair!) | We are in RIGHT half -> high = mid - 1 = 3 |
| `Iter 2` | mid = 2 (val 2). nums[1]=1 != 2 and nums[3]=3 != 2 | **Found single element 2! Return 2** ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single element array (`[1]` -> returns 1).
- Unique element at index 0 (`[1, 2, 2, 3, 3]` -> returns 1).
- Unique element at last index (`[1, 1, 2, 2, 3]` -> returns 3).

### Common Bugs to Avoid
- Accessing `nums[mid-1]` or `nums[mid+1]` when `mid = 0` or `mid = n-1` (eliminated by handling boundaries first and searching `[1..n-2]`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does XOR bit manipulation `mid ^ 1` simplify the code?**  
  **A**: `mid ^ 1` flips the last bit: if `mid` is even, `mid ^ 1 = mid + 1`; if `mid` is odd, `mid ^ 1 = mid - 1`. The condition simplifies to `if (nums[mid] == nums[mid ^ 1]) low = mid + 1; else high = mid - 1;`!

- **Q2: Why is the array length always guaranteed to be odd?**  
  **A**: Each duplicate pair contributes 2 elements. The single unique element contributes 1 element. Total length $= 2k + 1$, which is always odd.

- **Q3: Can this approach work if duplicates appear 3 times (except one)?**  
  **A**: For 3 duplicates, index triples are `(0, 1, 2), (3, 4, 5)`. Check modulo 3 `mid % 3` alignment.

- **Q4: What if the array is unsorted?**  
  **A**: If unsorted, binary search is impossible; XOR bitwise fold in $\mathcal{O}(n)$ time is optimal.

- **Q5: Why is handling boundary indices [1..n-2] considered best practice?**  
  **A**: It completely avoids `if (mid == 0)` and `if (mid == n - 1)` branch clutter inside the inner while loop.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Index Parity`, `Bitwise`, `LeetCode-540`, `Medium`
- **Related problems to practice next**:
- **Single Number (Unsorted)**: XOR bit manipulation on unsorted array.
- **Find Peak Element**: Slope-based binary search.
- **Search in Rotated Sorted Array**: Partitioned binary search.
