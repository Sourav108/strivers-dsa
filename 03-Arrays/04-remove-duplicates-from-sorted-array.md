# Remove Duplicates from Sorted Array (Step 3.1)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/remove-duplicates-in-place-from-sorted-array/
- **Difficulty**: Easy
- **Statement**: Remove duplicate elements in-place from a sorted array and return the count of unique elements.

---

## 1. Problem, Restated

Remove duplicate elements in-place from a sorted array and return the count of unique elements.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Two pointers (Slow writer `i`, fast reader `j`). When nums[j] != nums[i], advance i and copy.

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

int removeDuplicatesBrute(vector<int>& nums) {
    set<int> st(nums.begin(), nums.end());
    int i = 0; for (int x : st) nums[i++] = x;
    return i;
}
```

### Complexity Derivation
- **Time Complexity**: O(n log n)
- **Space Complexity**: O(n)
- **Why it's not good enough**: Using `std::set` requires $\mathcal{O}(n \log n)$ time and $\mathcal{O}(n)$ extra memory, failing the in-place requirement.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Slow and Fast Two Pointers: Pointer `i` marks the write boundary of unique elements. Pointer `j` scans from index 1 to $n-1$. When `nums[j] != nums[i]`, advance `i++` and copy `nums[i] = nums[j]`. Return `i + 1`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

int removeDuplicatesOptimal(vector<int>& nums) {
    if (nums.empty()) return 0;
    int i = 0;
    for (int j = 1; j < (int)nums.size(); j++) {
        if (nums[j] != nums[i]) {
            i++;
            nums[i] = nums[j];
        }
    }
    return i + 1;
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Modifies the array in-place in a single $\mathcal{O}(n)$ pass with $\mathcal{O}(1)$ auxiliary space.

---

## 6. Dry Run

`nums = [0, 0, 1, 1, 1, 2, 2, 3, 3, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `j=1` | nums[1]=0 == nums[i=0]=0 -> skip | i=0, nums=[0...] |
| `j=2` | nums[2]=1 != nums[i=0]=0 -> i=1, nums[1]=1 | nums=[0, 1...] |
| `j=3..4` | nums[j]=1 == nums[i=1]=1 -> skip | i=1 |
| `j=5` | nums[5]=2 != nums[i=1]=1 -> i=2, nums[2]=2 | nums=[0, 1, 2...] |
| `j=6` | nums[6]=2 == nums[i=2]=2 -> skip | i=2 |
| `j=7` | nums[7]=3 != nums[i=2]=2 -> i=3, nums[3]=3 | nums=[0, 1, 2, 3...] |
| `j=8` | nums[8]=3 == nums[i=3]=3 -> skip | i=3 |
| `j=9` | nums[9]=4 != nums[i=3]=3 -> i=4, nums[4]=4 | nums=[0, 1, 2, 3, 4...] (Return i+1 = 5) ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Array with no duplicates (`[1, 2, 3]` -> returns 3).
- Array with all identical elements (`[7, 7, 7]` -> returns 1).
- Single element array (`[1]` -> returns 1).

### Common Bugs to Avoid
- Starting `j` from 0 instead of 1, causing redundant self-assignment.
- Returning `i` instead of `i + 1` (since `i` is 0-indexed).

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to allow at most K duplicates (e.g. LeetCode 80 where K = 2)?**  
  **A**: Generalize the write condition: write `nums[i++] = nums[j]` if `i < K || nums[j] != nums[i - K]`. This elegantly handles any $K \ge 1$ in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

- **Q2: What if the array is unsorted and must be modified in-place with O(1) extra space?**  
  **A**: Without extra space, we must sort the array first in $\mathcal{O}(n \log n)$, then apply the two-pointer in-place removal in $\mathcal{O}(n)$. If order must be preserved, an $\mathcal{O}(n)$ hash set is required.

- **Q3: How does `std::unique` in C++ STL work under the hood?**  
  **A**: `std::unique(first, last)` uses the identical two-pointer algorithm: it iterates through the range and overwrites duplicate consecutive elements, returning an iterator to the new logical end.

- **Q4: Can this duplicate removal be parallelized across multiple threads?**  
  **A**: Yes, using a 2-pass parallel prefix scan: Thread $T_k$ marks unique boundaries `1` and duplicates `0`, computes parallel prefix sums to determine destination indices, and scatters elements into the target array.

- **Q5: How would you remove duplicates from a Singly Linked List?**  
  **A**: Traverse with pointer `curr`. If `curr->val == curr->next->val`, update `curr->next = curr->next->next` and `delete` the skipped node in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Easy`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
