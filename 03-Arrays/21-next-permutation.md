# Next Permutation (Step 3.2)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/data-structure/next_permutation-find-next-lexicographically-greater-permutation/
- **Difficulty**: Medium
- **Statement**: Rearrange numbers into lexicographically next greater permutation in-place.

---

## 1. Problem, Restated

Rearrange numbers into lexicographically next greater permutation in-place.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Single Pass 3-Step: Find pivot (nums[i] < nums[i+1]), swap with smallest greater element to right, reverse suffix.

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

void nextPermutationBrute(vector<int>& nums) {
    // Generate all n! permutations, sort lexicographically, find next
}
```

### Complexity Derivation
- **Time Complexity**: O(n! * n)
- **Space Complexity**: O(n!)
- **Why it's not good enough**: Generating all $n!$ permutations takes $\mathcal{O}(n! \cdot n)$ time and triggers immediate TLE.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Single-Pass 3-Step Algorithm: 1) Find the pivot $i$: rightmost index where `nums[i] < nums[i+1]`. 2) If $i \ge 0$, find rightmost index $j$ where `nums[j] > nums[i]` and swap `nums[i], nums[j]`. 3) Reverse suffix `nums[i+1..n-1]` to make it ascending.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

void nextPermutationOptimal(vector<int>& nums) {
    int n = nums.size(), i = n - 2;
    while (i >= 0 && nums[i] >= nums[i + 1]) i--;
    if (i >= 0) {
        int j = n - 1;
        while (nums[j] <= nums[i]) j--;
        swap(nums[i], nums[j]);
    }
    reverse(nums.begin() + i + 1, nums.end());
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(1)
- **Why this is optimal**: Transforms into the next lexicographical permutation in-place in $\mathcal{O}(n)$ time and $\mathcal{O}(1)$ space.

---

## 6. Dry Run

`nums = [1, 2, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `Step 1` | nums[1]=2 < nums[2]=3 -> pivot index i=1 | pivot=2 |
| `Step 2` | nums[2]=3 > nums[1]=2 -> swap(nums[1], nums[2]) | nums=[1, 3, 2] |
| `Step 3` | Reverse suffix from i+1=2 to 2 | Final: [1, 3, 2] ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Already at maximum permutation (`[3, 2, 1]` -> pivot $i=-1$, reverses whole array to `[1, 2, 3]`).
- Array with duplicate elements (`[1, 1, 5]` -> gives `[1, 5, 1]`).

### Common Bugs to Avoid
- Using strict inequality `nums[i] <= nums[i+1]` instead of `<` during pivot search, missing equal elements.
- Sorting the suffix instead of reversing (reversing is $\mathcal{O}(n)$, sorting is $\mathcal{O}(n \log n)$).

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do we REVERSE the suffix from i+1 to n-1 instead of sorting it?**  
  **A**: Because by definition of the pivot search, `nums[i+1..n-1]` is in strictly non-increasing (descending) order. Reversing a descending range in $\mathcal{O}(n)$ time automatically transforms it into ascending order without sorting overhead.

- **Q2: How to compute the PREVIOUS permutation (lexicographically smaller)?**  
  **A**: Symmetric logic: find first $i$ where `nums[i] > nums[i+1]`, find largest $j$ where `nums[j] < nums[i]`, swap `nums[i]` and `nums[j]`, and reverse suffix `nums[i+1..n-1]`.

- **Q3: What if the array is already at the maximum permutation `[3, 2, 1]`?**  
  **A**: Index $i$ reaches $-1$ (no pivot). The algorithm skips the swap step and reverses the entire array to `[1, 2, 3]`, which is the minimal permutation as required.

- **Q4: How to find the K-th Permutation directly without calling nextPermutation K times (LeetCode 60)?**  
  **A**: Use the Factorial Number System: for $n$ numbers, there are $(n-1)!$ permutations starting with each digit. Compute index $k / (n-1)!$, pick that digit from a list, update $k = k \% (n-1)!$, and repeat in $\mathcal{O}(n^2)$ time.

- **Q5: How many unique permutations exist for an array with duplicates?**  
  **A**: Formula $\frac{n!}{c_1! \cdot c_2! \dots c_k!}$ where $c_i$ is the frequency of the $i$-th distinct element.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
