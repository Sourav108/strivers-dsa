# Binary Search to Find X in Sorted Array (Step 4.1 — BS on 1D Arrays)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [LeetCode #704 - Binary Search](https://leetcode.com/problems/binary-search/) | [TakeUForward](https://takeuforward.org/data-structure/binary-search-explained/)
- **Difficulty**: Easy
- **Statement**: Given a sorted array of integers `nums` of size $n$ in ascending order and an integer `target`, write a function to search `target` in `nums`. If `target` exists, return its 0-based index. Otherwise, return `-1`. You must write an algorithm with $\mathcal{O}(\log n)$ runtime complexity.

---

## 1. Problem, Restated

You are given an array of numbers that is already sorted in non-decreasing order, and a target value `target`. Your goal is to find the index where `target` is located. If it doesn't exist anywhere in the array, return `-1`.

- **Input**: A sorted vector of integers `nums`, and an integer `target`.
- **Output**: The integer index of `target`, or `-1` if not found.
- **Constraints**: $1 \le n \le 10^5$, $-10^4 \le nums[i], target \le 10^4$, all integers in `nums` are unique and sorted in ascending order.

---

## 2. Intuition & Pattern

### The Search Space Halving Pattern
Because the array is already sorted, every element at index `mid` acts as a deterministic partition dividing the remaining search space into two halves:
1. If `nums[mid] == target`, we found our answer immediately.
2. If `nums[mid] < target`, then because the array is sorted, every element to the left of `mid` ($nums[0..mid]$) is strictly smaller than `target`. None of them can ever be the target. Thus, we can safely discard the entire left half and set `low = mid + 1`.
3. If `nums[mid] > target`, every element to the right of `mid` ($nums[mid..high]$) is strictly greater than `target`. We safely discard the entire right half and set `high = mid - 1`.

Every single comparison cuts the remaining search space strictly in half: $n \to \frac{n}{2} \to \frac{n}{4} \to \dots \to 1$.

```
Search Space: [ low . . . . . . . . mid . . . . . . . . high ]
                                      |
                 +--------------------+--------------------+
                 |                                         |
         nums[mid] < target                       nums[mid] > target
    (Target must be in RIGHT half)           (Target must be in LEFT half)
         low = mid + 1                            high = mid - 1
```

---

## 3. Approach 1 — Brute Force (Linear Search)

### Idea
Iterate sequentially through each element of `nums` from index $0$ to $n-1$. If `nums[i] == target`, return $i$. If the loop terminates without finding a match, return `-1`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int searchLinear(const vector<int>& nums, int target) {
    int n = nums.size();
    for (int i = 0; i < n; i++) {
        if (nums[i] == target) {
            return i;
        }
        // Since array is sorted, early exit if current element exceeds target
        if (nums[i] > target) {
            break;
        }
    }
    return -1;
}
```

### Java Code
```java
class Solution {
    int searchLinear(int[] nums, int target) {
        int n = nums.length;
        for (int i = 0; i < n; i++) {
            if (nums[i] == target) {
                return i;
            }
            // Since array is sorted, early exit if current element exceeds target
            if (nums[i] > target) {
                break;
            }
        }
        return -1;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ in the worst case (when `target` is at the end or not present).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why it's not good enough**: Linear search inspects elements one by one, completely failing to exploit the sorted invariant of the array. For $n = 10^5$, linear search takes up to $10^5$ operations, whereas $\mathcal{O}(\log n)$ takes at most $\approx 17$ comparisons.

---

## 4. Approach 2 — Better (Recursive Binary Search)

### Idea
Formulate the search space halving using Divide and Conquer recursion. Define a recursive helper function `searchHelper(nums, low, high, target)`. 
- **Base Case**: If `low > high`, the search space is exhausted; return `-1`.
- **Recursive Step**: Calculate `mid = low + (high - low) / 2`. If `nums[mid] == target`, return `mid`. If `nums[mid] < target`, recurse on `searchHelper(nums, mid + 1, high, target)`. Else, recurse on `searchHelper(nums, low, mid - 1, target)`.

### C++17 Code
```cpp
#include <vector>
using namespace std;

int searchRecursiveHelper(const vector<int>& nums, int low, int high, int target) {
    if (low > high) {
        return -1;
    }
    
    // Prevent integer overflow
    int mid = low + (high - low) / 2;
    
    if (nums[mid] == target) {
        return mid;
    } else if (nums[mid] < target) {
        return searchRecursiveHelper(nums, mid + 1, high, target);
    } else {
        return searchRecursiveHelper(nums, low, mid - 1, target);
    }
}

int searchRecursive(const vector<int>& nums, int target) {
    return searchRecursiveHelper(nums, 0, (int)nums.size() - 1, target);
}
```

### Java Code
```java
class Solution {
    int searchRecursiveHelper(int[] nums, int low, int high, int target) {
        if (low > high) {
            return -1;
        }
        
        // Prevent integer overflow
        int mid = low + (high - low) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            return searchRecursiveHelper(nums, mid + 1, high, target);
        } else {
            return searchRecursiveHelper(nums, low, mid - 1, target);
        }
    }
    
    int searchRecursive(int[] nums, int target) {
        return searchRecursiveHelper(nums, 0, nums.length - 1, target);
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — the recurrence relation is $T(n) = T(n/2) + \mathcal{O}(1)$, which evaluates to $\mathcal{O}(\log n)$ by the Master Theorem.
- **Space Complexity**: $\mathcal{O}(\log_2 n)$ auxiliary stack memory due to recursive call frames.
- **Why it's still not optimal**: Although time complexity is optimal, recursion introduces call stack frame overhead and potential stack overflow for constrained embedded environments. The iterative version eliminates this overhead to achieve $\mathcal{O}(1)$ space.

---

## 5. Approach 3 — Optimal (Iterative Binary Search)

### Idea
Maintain two boundary pointers: `low = 0` and `high = n - 1`. While `low <= high`, compute the midpoint `mid = low + (high - low) / 2`. Narrow the search window iteratively until the target is found or the pointers cross (`low > high`).

### C++17 Code
```cpp
#include <vector>
using namespace std;

int search(const vector<int>& nums, int target) {
    int low = 0;
    int high = (int)nums.size() - 1;
    
    while (low <= high) {
        // Safe midpoint calculation avoiding integer overflow
        int mid = low + (high - low) / 2;
        
        if (nums[mid] == target) {
            return mid;
        } else if (nums[mid] < target) {
            low = mid + 1;  // Target lies in the right subarray
        } else {
            high = mid - 1; // Target lies in the left subarray
        }
    }
    
    return -1; // Target not found
}
```

### Java Code
```java
class Solution {
    int search(int[] nums, int target) {
        int low = 0;
        int high = nums.length - 1;
        
        while (low <= high) {
            // Safe midpoint calculation avoiding integer overflow
            int mid = low + (high - low) / 2;
            
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                low = mid + 1;  // Target lies in the right subarray
            } else {
                high = mid - 1; // Target lies in the left subarray
            }
        }
        
        return -1; // Target not found
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\log_2 n)$ — in each step, the search window size $k$ reduces to $k/2$. The search terminates when $n / 2^k = 1 \implies k = \log_2 n$.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space — uses only two integer pointers (`low`, `high`) and one scalar (`mid`).
- **Why this is optimal**: Information theory proves that any comparison-based search algorithm on a sorted array of size $n$ requires at least $\lceil \log_2(n + 1) \rceil$ comparisons in the worst case (decision tree model with $n+1$ possible outcomes). Binary Search achieves this lower bound $\Omega(\log n)$ with strictly $\mathcal{O}(1)$ auxiliary memory.

---

## 6. Dry Run

Search for `target = 9` in `nums = [-1, 0, 3, 5, 9, 12]`, size $n = 6$.

| Iteration | `low` | `high` | `mid` formula | `mid` | `nums[mid]` | Condition & Action | Search Space Left |
|:---:|:---:|:---:|:---:|:---:|:---:|---|---|
| **1** | `0` | `5` | `0 + (5-0)/2` | `2` | `3` | `3 < 9` $\implies$ `low = mid + 1 = 3` | `[5, 9, 12]` (indices 3..5) |
| **2** | `3` | `5` | `3 + (5-3)/2` | `4` | `9` | `9 == 9` $\implies$ **Target Found! Return `mid = 4`** | `[9]` (index 4) |

#### Dry Run on Not Found (`target = 2` in `nums = [-1, 0, 3, 5, 9, 12]`):

| Iteration | `low` | `high` | `mid` | `nums[mid]` | Condition & Action | Next Window |
|:---:|:---:|:---:|:---:|:---:|---|---|
| **1** | `0` | `5` | `2` | `3` | `3 > 2` $\implies$ `high = mid - 1 = 1` | `[-1, 0]` |
| **2** | `0` | `1` | `0` | `-1` | `-1 < 2` $\implies$ `low = mid + 1 = 1` | `[0]` |
| **3** | `1` | `1` | `1` | `0` | `0 < 2` $\implies$ `low = mid + 1 = 2` | Empty (`low > high`) |
| **Exit** | `2` | `1` | - | - | `low > high` $\implies$ **Return `-1`** | Not found ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- **Single-element array matching target**: `nums = [5], target = 5` $\implies$ `low=0, high=0, mid=0` $\implies$ returns index `0`.
- **Single-element array not matching**: `nums = [5], target = 2` $\implies$ `high` becomes `-1`, returns `-1`.
- **Target smaller than smallest element**: `nums = [2, 4, 6], target = 1` $\implies$ `high` quickly falls below 0, returns `-1`.
- **Target larger than largest element**: `nums = [2, 4, 6], target = 8` $\implies$ `low` surpasses $n-1$, returns `-1`.
- **Target at array boundaries**: Target at index $0$ or index $n-1$ is correctly returned without out-of-bounds access.

### Common Bugs to Avoid
- **Integer Overflow**: Writing `mid = (low + high) / 2`. If `low + high > INT_MAX` ($2^{31}-1$), it overflows into a negative number, causing a segmentation fault when indexing `nums[mid]`. Always use `low + (high - low) / 2`.
- **Loop Condition (`low <= high` vs `low < high`)**: Using `while (low < high)` causes the loop to exit early when `low == high`, skipping the inspection of the single remaining element.
- **Pointer Updates**: Writing `low = mid` or `high = mid` instead of `low = mid + 1` or `high = mid - 1` leads to an infinite loop when the search window shrinks to 2 elements.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does `mid = (low + high) / 2` cause a bug in C++ and Java, and how does `low + (high - low) / 2` fix it mathematically?**  
  **A**: When `low` and `high` are both large (e.g. $1.5 \times 10^9$), their sum $3.0 \times 10^9$ exceeds the 32-bit signed integer maximum ($2^{31}-1 \approx 2.14 \times 10^9$), overflowing to a negative integer. Rewriting as `low + (high - low) / 2` guarantees all intermediate terms stay strictly within $[0, high]$, completely avoiding overflow. Alternatively, in C++, bitwise shifting `low + ((high - low) >> 1)` or using unsigned types also works.

- **Q2: What is the exact decision-tree lower bound proof that Binary Search is optimal?**  
  **A**: Any comparison between two numbers has 3 outcomes ($<, ==, >$). For an array of size $n$, there are $n + 1$ possible search outcomes ($n$ valid indices + 1 "not found" state). A ternary decision tree with height $h$ can have at most $3^h$ leaves. Therefore, $3^h \ge n + 1 \implies h \ge \log_3(n + 1)$. In a 2-way comparison model ($<$ vs $\ge$), the tree is binary: $2^h \ge n + 1 \implies h \ge \lceil \log_2(n + 1) \rceil = \Omega(\log n)$.

- **Q3: How does Binary Search perform with CPU branch prediction and hardware prefetching?**  
  **A**: Standard binary search jumps across widely separated memory addresses, which frequently misses CPU L1/L2 cache lines and causes pipeline branch mispredictions. **Branchless Binary Search** (using conditional moves `cmov` or arithmetic index masks) and **Eytzinger Layout (Array flattening into BFS tree order)** keep hot tree levels packed in cache lines, accelerating lookup speeds by $2\times - 4\times$ in low-latency systems.

- **Q4: Can Binary Search be applied to a Singly Linked List? Why is it inefficient?**  
  **A**: While conceptually possible, finding the midpoint of a Singly Linked List requires $\mathcal{O}(k)$ pointer hops. The recurrence becomes $T(n) = T(n/2) + \mathcal{O}(n) = \mathcal{O}(n)$, which degrades to linear time. To achieve $\mathcal{O}(\log n)$ search on linked lists, a multi-level index structure like a **Skip List** is required.

- **Q5: How would you search for a target in an infinite or unbounded sorted array where $n$ is unknown?**  
  **A**: Use **Exponential Search**: initialize `low = 0, high = 1`. Repeatedly double `high = high * 2` and set `low = high / 2` until `nums[high] >= target` (or an out-of-bounds exception is caught). Once the bounding range $[low, high]$ of size $2^k$ is identified in $\mathcal{O}(\log p)$ steps (where $p$ is the target's position), run standard binary search on $[low, high]$ in $\mathcal{O}(\log p)$ time. Total time: $\mathcal{O}(\log p)$.

---

## 9. Tags & Related Problems

- **Tags**: `Binary Search`, `Divide and Conquer`, `Array`, `TakeUForward`, `Easy`
- **Related problems to practice next**:
  - **Implement Lower Bound**: Finds smallest index where $nums[i] \ge x$.
  - **Implement Upper Bound**: Finds smallest index where $nums[i] > x$.
  - **Search Insert Position**: Identifies exact insertion index in $\mathcal{O}(\log n)$.
  - **First and Last Occurrences of X**: Two binary searches to find bounding interval.
