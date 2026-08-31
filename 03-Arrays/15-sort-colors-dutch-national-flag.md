# Sort Colors (0s, 1s, 2s) (Step 3.2)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/sort-an-array-of-0s-1s-and-2s/
- **Difficulty**: Medium
- **Statement**: Sort an array consisting only of 0s, 1s, and 2s in-place in single pass.

---

## 1. Problem, Restated

Sort an array consisting only of 0s, 1s, and 2s in-place in single pass.

- **Input**: Array / Vector of integers `nums`.
- **Output**: Array sorted in-place in non-decreasing order (`[0, ..., 0, 1, ..., 1, 2, ..., 2]`).
- **Key Constraints**: $n$ up to $10^5$, elements are only $0, 1, 2$, requires $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ auxiliary space in a single pass.

---

## 2. Intuition & Pattern

Dutch National Flag (3-Way Partitioning with low, mid, high pointers).

- **Underlying Pattern**: Three-Way In-Place Partitioning / Invariant Pointers.
- **The "Aha!" Moment**: Maintain three pointers to partition the array into 4 distinct regions:
  1. `nums[0 .. low - 1]` $	o$ all `0`s
  2. `nums[low .. mid - 1]` $	o$ all `1`s
  3. `nums[mid .. high]` $	o$ unexamined / unprocessed elements
  4. `nums[high + 1 .. n - 1]` $	o$ all `2`s
  
  When `nums[mid] == 0`, swap with `nums[low]` and increment both `low++` and `mid++`. When `nums[mid] == 1`, increment `mid++`. When `nums[mid] == 2`, swap with `nums[high]` and decrement `high--` (leaving `mid` in place to inspect the newly swapped element).

---

## 3. Approach 1 — Brute Force

### Idea
Check all possibilities exhaustively using standard comparison sorting.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    void sortColorsBrute(vector<int>& nums) {
        sort(nums.begin(), nums.end());
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void sortColorsBrute(int[] nums) {
        Arrays.sort(nums);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n \log n)$ — standard dual-pivot QuickSort / comparison-based sort.
- **Space Complexity**: $\mathcal{O}(1)$ in C++ (or $\mathcal{O}(\log n)$ stack frames in Java Dual-Pivot QuickSort).
- **Why it's not good enough**: Standard comparison sort takes $\mathcal{O}(n \log n)$ time, ignoring the small constant range of only 3 distinct colors.

---

## 4. Approach 2 — Better

### Idea
Counting Sort (2 Passes): First pass counts the total occurrences of `0`s, `1`s, and `2`s. Second pass overwrites the original array with the counted number of `0`s, then `1`s, then `2`s.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    void sortColorsBetter(vector<int>& nums) {
        int count0 = 0, count1 = 0, count2 = 0;
        for (int x : nums) {
            if (x == 0) count0++;
            else if (x == 1) count1++;
            else count2++;
        }
        int idx = 0;
        while (count0--) nums[idx++] = 0;
        while (count1--) nums[idx++] = 1;
        while (count2--) nums[idx++] = 2;
    }
};
```

### Java Code
```java
class Solution {

    void sortColorsBetter(int[] nums) {
        int count0 = 0, count1 = 0, count2 = 0;
        for (int x : nums) {
            if (x == 0) count0++;
            else if (x == 1) count1++;
            else count2++;
        }
        int idx = 0;
        while (count0--) nums[idx++] = 0;
        while (count1--) nums[idx++] = 1;
        while (count2--) nums[idx++] = 2;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2n) = \mathcal{O}(n)$ — one pass for counting, one pass for rewriting.
- **Space Complexity**: $\mathcal{O}(1)$ — three scalar integer counters.
- **Why it's still not optimal**: Requires 2 passes over the array and multiple memory writes; Dutch National Flag partitions the array in a single pass with minimum swaps.

---

## 5. Approach 3 — Optimal

### Idea
Dutch National Flag 3-Way Partitioning: Maintain pointers `low = 0, mid = 0, high = n - 1`. While `mid <= high`: if `nums[mid] == 0`, swap `nums[low]` with `nums[mid]` and increment `low++`, `mid++`. If `nums[mid] == 1`, increment `mid++`. If `nums[mid] == 2`, swap `nums[mid]` with `nums[high]` and decrement `high--`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    void sortColors(vector<int>& nums) {
        int low = 0, mid = 0, high = (int)nums.size() - 1;
        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums[low], nums[mid]);
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                swap(nums[mid], nums[high]);
                high--;
            }
        }
    }
};
```

### Java Code
```java
class Solution {

    void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;
        while (mid <= high) {
            if (nums[mid] == 0) {
                int temp = nums[low]; nums[low] = nums[mid]; nums[mid] = temp;
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                int temp = nums[mid]; nums[mid] = nums[high]; nums[high] = temp;
                high--;
            }
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(n)$ — each element is inspected at most once, and pointer `mid` advances or `high` decrements in each step.
- **Space Complexity**: $\mathcal{O}(1)$ — strictly in-place with three pointer variables and no heap allocations.
- **Why this is optimal**: Partitions 3 distinct colors in-place in a single $\mathcal{O}(n)$ pass with minimal swaps and zero extra space.

---

## 6. Dry Run

`nums = [2, 0, 2, 1, 1, 0]`, `low=0, mid=0, high=5`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | `nums[mid=0]=2` $	o$ swap(`nums[0]`, `nums[5]`) $	o$ `high=4` | `low=0, mid=0, high=4` (`nums=[0, 0, 2, 1, 1, 2]`) |
| `Step 2` | `nums[mid=0]=0` $	o$ swap(`nums[0]`, `nums[0]`) $	o$ `low=1, mid=1` | `low=1, mid=1, high=4` (`nums=[0, 0, 2, 1, 1, 2]`) |
| `Step 3` | `nums[mid=1]=0` $	o$ swap(`nums[1]`, `nums[1]`) $	o$ `low=2, mid=2` | `low=2, mid=2, high=4` (`nums=[0, 0, 2, 1, 1, 2]`) |
| `Step 4` | `nums[mid=2]=2` $	o$ swap(`nums[2]`, `nums[4]`) $	o$ `high=3` | `low=2, mid=2, high=3` (`nums=[0, 0, 1, 1, 2, 2]`) |
| `Step 5` | `nums[mid=2]=1` $	o$ `mid++` | `low=2, mid=3, high=3` |
| `Step 6` | `nums[mid=3]=1` $	o$ `mid++` | `low=2, mid=4, high=3` ($mid > high$, loop terminates) |
| `Final` | Process complete | `[0, 0, 1, 1, 2, 2]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All identical elements (`[0, 0, 0]` or `[2, 2, 2]` $	o$ handles gracefully without out-of-bounds error).
- Array with only 2 colors (`[2, 0, 2, 0]` $	o$ sorts to `[0, 0, 2, 2]`).
- Array already sorted (`[0, 1, 2]` $	o$ single pass, $\mathcal{O}(n)$ time).
- Array in reverse order (`[2, 1, 0]` $	o$ sorts in minimum swaps).

### Common Bugs to Avoid
- **Incrementing `mid` after swapping with `high`**: The element swapped from `high` has not been examined yet; advancing `mid` skips inspecting it and causes wrong results.
- **Using `mid < high` instead of `mid <= high`**: Skips the element at index `high`, leaving it unsorted.
- **Overwriting counts in Java**: Forgetting that `count0--` in Java does not evaluate to boolean; use `count0-- > 0` or standard counting loops.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do we NOT increment `mid` when swapping with `high` (`swap(nums[mid], nums[high])`)?**  
  **A**: Because the element swapped from `high` was previously unexamined; it could be 0, 1, or 2. We must keep `mid` at the same index to inspect it in the next iteration.

- **Q2: Why DO we increment `mid` when swapping with `low` (`swap(nums[low], nums[mid])`)?**  
  **A**: Because `low <= mid` and all elements before `mid` have already been processed. The element swapped from `low` is guaranteed to be 1, which is valid at `mid`.

- **Q3: How does Dutch National Flag generalize to 3-Way QuickSort (Fat Partitioning)?**  
  **A**: In QuickSort with many duplicate keys, 3-way partitioning divides the array into $[< 	ext{pivot}]$, $[== 	ext{pivot}]$, and $[> 	ext{pivot}]$. Recursion only recurses on the strictly smaller and strictly larger subarrays, reducing QuickSort from $\mathcal{O}(n^2)$ to $\mathcal{O}(n)$ on duplicate arrays.

- **Q4: How to generalize Dutch National Flag to 4 colors (0, 1, 2, 3)?**  
  **A**: Use 4 pointers (`p0, p1, p2, p3`) or execute two passes of 3-way partitioning in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

- **Q5: How would you sort 0s, 1s, and 2s in a Singly Linked List?**  
  **A**: Create 3 dummy heads (`zeroHead, oneHead, twoHead`), iterate the list appending nodes to their respective dummy chains in $\mathcal{O}(n)$ time, and link `zeroTail.next = oneHead.next; oneTail.next = twoHead.next`.

---

## 9. Tags & Related Problems

- **Tags**: `Array`, `Two Pointers`, `Sorting`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems to practice next**:
  - [Majority Element](https://takeuforward.org/data-structure/find-the-majority-element-that-occurs-more-than-n-2-times/) (Boyer-Moore Voting)
  - [Maximum Subarray Sum](https://takeuforward.org/data-structure/kadanes-algorithm-maximum-subarray-sum-in-an-array/) (Kadane's Algorithm)
  - [3-Sum Problem](https://takeuforward.org/data-structure/3-sum-find-triplets-that-add-up-to-a-zero/)
