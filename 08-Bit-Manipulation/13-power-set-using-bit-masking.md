# Power Set (Print all subsequences using Bit Masking) (Step 8.2 — Interview Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Power Set (Print all subsequences using Bit Masking)](https://takeuforward.org/data-structure/power-set-print-all-subsequences/)
- **Difficulty**: Medium
- **Statement**: Given an array `nums` of $N$ integers, generate all $2^N$ subsets using bit masking iteration from $0$ to $2^N - 1$.

---

## 1. Problem, Restated

Map each integer mask in $[0, 2^N - 1]$ to a unique subset configuration.

- **Input**: Parameters specified.
- **Output**: Resulting bitwise integer / boolean / list.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

An array of size $N$ has $2^N$ subsets. Every integer `mask` from $0$ to $2^N - 1$ corresponds to a unique subset: if the $j^{\text{th}}$ bit of `mask` is set (`(mask & (1 << j)) != 0`), include `nums[j]` in the subset.

- **Underlying Pattern**: `Bitmask Binary Subset Mapping ($mask \in [0, 2^N - 1]$)`.

---

## 3. Approach 1 — Naive / Common Pitfall

### Idea
Recursive backtracking.

### C++17 Code
```cpp
// Recursive tree approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 2^N)$.
- **Space Complexity**: $\mathcal{O}(N)$ stack.
- **Why it's not good enough**: Recursion stack overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — direct $\mathcal{O}(1)$ bitwise operation below is optimal.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative Bitmasking: outer loop runs $2^N$ masks, inner loop tests $N$ bits.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        int n = nums.size();
        int totalSubsets = 1 << n; // 2^n
        vector<vector<int>> result;
        result.reserve(totalSubsets);
        
        for (int mask = 0; mask < totalSubsets; mask++) {
            vector<int> subset;
            for (int i = 0; i < n; i++) {
                if (mask & (1 << i)) {
                    subset.push_back(nums[i]);
                }
            }
            result.push_back(subset);
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 2^N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (excluding returned result).
- **Why this is optimal**: Iterative bit shifts eliminate call stack overhead entirely.

---

## 6. Dry Run

`nums = [1, 2, 3]` ($N = 3$, masks $0$ to $7$)

| Step | Action / State Change | Result |
|---|---|---|
| `mask = 0 (`000`)` | no bits set | `[]` |
| `mask = 3 (`011`)` | bits 0 and 1 set | `[1, 2]` |
| `mask = 7 (`111`)` | bits 0, 1, 2 set | `[1, 2, 3]` |
| `Result` | All 8 subsets generated | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 0$ (returns `[[]]`).
- $N = 30$ (use `1LL << n` for 64-bit bounds).

### Common Bugs to Avoid
- Using `1 << n` when $n \ge 31$ (overflows 32-bit signed integer).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is bitmask power set preferred in competitive programming?**  
  **A**: It is iterative, cache-friendly, requires zero recursion stack memory, and easily parallelizable.


---

## 9. Tags & Related Problems

- **Tags**: `Bit Manipulation`, `Bitmask`, `Power Set`, `LeetCode-78`, `Medium`
- **Related problems to practice next**:
- **Subsets (Recursion)**: Recursive counterpart.
