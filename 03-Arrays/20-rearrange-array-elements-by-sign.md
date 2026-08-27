# Rearrange Array Elements by Sign (Step 3.2)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: https://takeuforward.org/arrays/rearrange-array-elements-by-sign/
- **Difficulty**: Medium
- **Statement**: Rearrange array of equal positive and negative numbers alternately starting with positive.

---

## 1. Problem, Restated

Rearrange array of equal positive and negative numbers alternately starting with positive.

- **Input**: Vector of integers `nums`.
- **Output**: Result as specified by problem requirements.
- **Key Constraints**: $n$ up to $10^5$, elements can be negative/positive, time limit 1.0s.

---

## 2. Intuition & Pattern

Two Pointers Placement (Positives at even indices 0,2,4..., Negatives at odd 1,3,5...).

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

vector<int> rearrangeBrute(const vector<int>& nums) {
    vector<int> pos, neg;
    for (int x : nums) if (x > 0) pos.push_back(x); else neg.push_back(x);
    vector<int> res(nums.size());
    for (size_t i = 0; i < pos.size(); i++) {
        res[2 * i] = pos[i];
        res[2 * i + 1] = neg[i];
    }
    return res;
}
```

### Complexity Derivation
- **Time Complexity**: O(2n) = O(n)
- **Space Complexity**: O(n)
- **Why it's not good enough**: Segregating into two separate vectors and then interleaving takes two full passes.

---

## 4. Approach 2 — Better

No meaningful intermediate step — the optimal approach below removes the brute force's bottleneck directly.

---

## 5. Approach 3 — Optimal

### Idea
Two Pointers Even/Odd Placement: Allocate result vector of size $n$. Maintain `posIdx = 0` (even indices) and `negIdx = 1` (odd indices). For each $x$: if $x > 0$, place at `res[posIdx]` and advance `posIdx += 2`; else place at `res[negIdx]` and advance `negIdx += 2`.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
#include <climits>
#include <unordered_map>
#include <unordered_set>
using namespace std;

vector<int> rearrangeOptimal(const vector<int>& nums) {
    int n = nums.size(), posIdx = 0, negIdx = 1;
    vector<int> res(n);
    for (int x : nums) {
        if (x > 0) {
            res[posIdx] = x;
            posIdx += 2;
        } else {
            res[negIdx] = x;
            negIdx += 2;
        }
    }
    return res;
}
```

### Complexity Derivation
- **Time Complexity**: O(n)
- **Space Complexity**: O(n)
- **Why this is optimal**: Populates the alternate array in a single $\mathcal{O}(n)$ pass using $\mathcal{O}(n)$ output space.

---

## 6. Dry Run

`nums = [3, 1, -2, -5, 2, -4]`

| Step | Action / State Change | Result |
|---|---|---|
| `x=3` | posIdx=0 -> res[0]=3, posIdx=2 | res=[3, _, _, _, _, _] |
| `x=1` | posIdx=2 -> res[2]=1, posIdx=4 | res=[3, _, 1, _, _, _] |
| `x=-2` | negIdx=1 -> res[1]=-2, negIdx=3 | res=[3, -2, 1, _, _, _] |
| `x=-5` | negIdx=3 -> res[3]=-5, negIdx=5 | res=[3, -2, 1, -5, _, _] |
| `x=2` | posIdx=4 -> res[4]=2, posIdx=6 | res=[3, -2, 1, -5, 2, _] |
| `x=-4` | negIdx=5 -> res[5]=-4, negIdx=7 | Final: [3, -2, 1, -5, 2, -4] ✅ |

## 7. Edge Cases & Common Bugs

### Edge Cases
- Array of size 2 (`[1, -1]` -> returns `[1, -1]`).
- Alternating signs already (`[1, -2, 3, -4]` -> preserved).

### Common Bugs to Avoid
- Using 1-based indexing for `posIdx` or `negIdx` causing offset alignment errors.

## 8. Follow-Up Questions (Interview Style)

- **Q1: What if the count of positive and negative elements is NOT equal?**  
  **A**: Segregate into `pos` and `neg` lists. Alternate until the smaller list exhausts, then append remaining elements to the end in $\mathcal{O}(n)$ time and $\mathcal{O}(n)$ space.

- **Q2: Can we solve this in O(1) extra space while preserving relative order?**  
  **A**: Preserving order in $\mathcal{O}(1)$ space requires Right-Rotation / Block-Merge in $\mathcal{O}(n \log^2 n)$ or $\mathcal{O}(n^2)$ time. In practice, $\mathcal{O}(n)$ auxiliary memory is standard for $\mathcal{O}(n)$ time.

- **Q3: What if relative order does NOT matter?**  
  **A**: Use Two Pointers in $\mathcal{O}(1)$ space: `pos = 0`, `neg = 1`. Advance `pos` by 2 while `nums[pos] > 0`, advance `neg` by 2 while `nums[neg] < 0`. Swap `nums[pos]` and `nums[neg]` when both are misplaced.

- **Q4: How to rearrange such that all negatives come before all positives in O(1) space?**  
  **A**: Use two-pointer QuickSort partition: pointer `j` tracks positive boundary, swap `nums[i]` with `nums[j++]` when `nums[i] < 0` in $\mathcal{O}(n)$ time.

- **Q5: How to parallelize array rearrangement for n = 10^9?**  
  **A**: Count positive elements in parallel chunks, compute target destination offsets via prefix sums, and write elements to even/odd destination positions in parallel.

## 9. Tags & Related Problems

- **Tags**: `Array`, `TakeUForward`, `Strivers-A2Z`, `Medium`
- **Related problems**:
  - Similar Step 3 Array Problems in the curriculum.
