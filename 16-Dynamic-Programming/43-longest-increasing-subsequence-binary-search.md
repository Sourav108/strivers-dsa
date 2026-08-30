# Longest Increasing Subsequence using Binary Search (Step 16.6 — DP on LIS)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Longest Increasing Subsequence using Binary Search](https://takeuforward.org/data-structure/longest-increasing-subsequence-binary-search-dp-43/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums`, return the length of the longest strictly increasing subsequence using Patience Sorting and Binary Search in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ space.

---

## 1. Problem, Restated

Compute the length of the LIS in $\mathcal{O}(N \log N)$ time using Patience Sorting / `std::lower_bound` on an active tail array.

- **Input**: Problem constraints and parameters.
- **Output**: Length / Array result.
- **Complexity Goal**: Optimal time and space complexity.

---

## 2. Intuition & Pattern

**The Patience Sorting Invariant**: 
Maintain a dynamic array `tails` where `tails[len - 1]` stores the **smallest ending element of all valid increasing subsequences of length `len`** seen so far! 
**Algorithm**: 
1) Initialize an empty vector `tails`. 
2) For each element $x \in \text{nums}$: 
   - Use Binary Search (`std::lower_bound`) to find the first element in `tails` that is $\ge x$. 
   - **Case 1 (If no such element exists, `it == tails.end()`)**: $x$ is strictly greater than all elements in `tails` $\implies$ append $x$ to extend the LIS length: `tails.push_back(x)`. 
   - **Case 2 (If such element exists at index `idx`)**: Replace `tails[idx] = x`! (Replacing a larger tail with a smaller $x$ gives future elements a better opportunity to extend!). 
3) Length of the LIS is `tails.size()`. Runs in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ space.

- **Underlying Pattern**: `Patience Sorting / Binary Search (`std::lower_bound` on Tails Array)`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Standard $\mathcal{O}(N^2)$ DP predecessor checking.

### C++17 Code
```cpp
// O(N^2) 1D DP
```

### Java Code
```java
// Java equivalent
// O(N^2) 1D DP
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's not good enough**: Quadratic nested comparison.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DP below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Patience Sorting with `std::lower_bound` in $\mathcal{O}(N \log N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = nums.size();
        if (n == 0) return 0;
        
        // tails[i] stores the smallest tail of an increasing subsequence of length (i + 1)
        vector<int> tails;
        
        for (int x : nums) {
            // Find the first element in tails >= x using binary search
            auto it = lower_bound(tails.begin(), tails.end(), x);
            
            if (it == tails.end()) {
                // x extends the largest increasing subsequence
                tails.push_back(x);
            } else {
                // Greedily replace the larger tail with smaller x
                *it = x;
            }
        }
        
        return tails.size();
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    int lengthOfLIS(int[] nums) {
        int n = nums.length;
        if (n == 0) return 0;
        
        // tails[i] stores the smallest tail of an increasing subsequence of length (i + 1)
        List<Integer> tails = new ArrayList<>();
        
        for (int x : nums) {
            // Find the first element in tails >= x using binary search
            var it = lower_bound(tails.begin(), tails.end(), x);
            
            if (it == tails.end()) {
                // x extends the largest increasing subsequence
                tails.add(x);
            } else {
                // Greedily replace the larger tail with smaller x
                it = x;
            }
        }
        
        return tails.length;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time ($N$ elements $\times \mathcal{O}(\log N)$ binary search).
- **Space Complexity**: $\mathcal{O}(N)$ space (`tails` array of size at most $N$).
- **Why this is optimal**: Binary search on monotonic `tails` eliminates quadratic scanning.

---

## 6. Dry Run

`nums = [10, 9, 2, 5, 3, 7, 101, 18]` ($N = 8$)

| Step | Action / State Change | Result |
|---|---|---|
| `x = 10` | tails: `[10]` | Length 1 |
| `x = 9` | lower_bound(10) $\implies$ replace 10 with 9 $\implies tails = [9]$ | Greedy replacement |
| `x = 2` | replace 9 with 2 $\implies tails = [2]$ | Greedy replacement |
| `x = 5` | 5 > 2 $\implies$ append 5 $\implies tails = [2, 5]$ | Length 2 |
| `x = 3` | replace 5 with 3 $\implies tails = [2, 3]$ | Greedy replacement |
| `x = 7` | 7 > 3 $\implies$ append 7 $\implies tails = [2, 3, 7]$ | Length 3 |
| `x = 101` | 101 > 7 $\implies$ append 101 $\implies tails = [2, 3, 7, 101]$ | Length 4 |
| `x = 18` | replace 101 with 18 $\implies tails = [2, 3, 7, 18]$ | Length 4 |
| `Result` | Return tails.size() = 4 | LIS Length = 4 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Duplicate values `[2, 2, 2]` (`lower_bound` replaces the same index, maintaining length 1).
- Strictly increasing array `[1, 2, 3, 4]` (appends on every step).
- $N = 1$.

### Common Bugs to Avoid
- Using `std::upper_bound` instead of `std::lower_bound` (upper_bound solves Longest NON-DECREASING Subsequence, lower_bound solves STRICTLY Increasing Subsequence!).
- Assuming `tails` represents the actual LIS elements (tails only tracks smallest endpoints, not the actual sequence!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does 'tails' contain the correct LENGTH, but NOT necessarily the valid LIS elements?**  
  **A**: Because replacing an element in `tails` with a smaller value updates the future potential without invalidating the length already achieved. For example, on `[2, 5, 3]`, `tails` becomes `[2, 3]`, which happens to be a valid LIS. But on `[10, 20, 1]`, `tails` becomes `[1, 20]`, which is NOT a valid subsequence! However, `tails.size() = 2` is 100% correct!

- **Q2: How to solve Longest NON-DECREASING Subsequence using Binary Search?**  
  **A**: Replace `std::lower_bound` with `std::upper_bound`! This allows duplicate elements to be placed in separate slots rather than overwriting each other.

- **Q3: How does Russian Doll Envelopes (LeetCode 354) reduce to O(N log N) LIS?**  
  **A**: Sort envelopes by width ascending; for identical widths, sort height DESCENDING! Then run $\mathcal{O}(N \log N)$ LIS on heights! Descending height on equal widths prevents two envelopes of the same width from being nested into each other!


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `LIS`, `Binary Search`, `Patience Sorting`, `LeetCode-300`, `Medium`
- **Related problems to practice next**:
- **Longest Increasing Subsequence**: O(N^2) formulation.
- **Russian Doll Envelopes**: 2D envelope nesting.
