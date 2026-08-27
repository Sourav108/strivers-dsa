# Subset Sum I (Sum of all subsets) (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Subset Sum I (Sum of all subsets)](https://takeuforward.org/data-structure/subset-sum-sum-of-all-subsets/)
- **Difficulty**: Easy
- **Statement**: Given an array `arr` of $N$ integers, return the sums of all subsets in sorted ascending order.

---

## 1. Problem, Restated

Compute $\sum s$ for all $2^N$ subsets and return the sorted list of sums.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain running sum `sum` during Take / Not-Take recursion. 1) Take: `recurse(i + 1, sum + arr[i])`. 2) Not-Take: `recurse(i + 1, sum)`. Base case: when `i == N`, append `sum` to result array.

- **Underlying Pattern**: `Take / Not-Take Running Sum Accumulation`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Generate all subset vectors, compute sum for each in $\mathcal{O}(N \cdot 2^N)$.

### C++17 Code
```cpp
// Generate subsets and sum
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 2^N)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Redundant sum recomputation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Running Sum Accumulation + Sort in $\mathcal{O}(2^N + 2^N \log(2^N))$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    void subsetSumsHelper(int ind, int sum, const vector<int>& arr, int n, vector<int>& ans) {
        if (ind == n) {
            ans.push_back(sum);
            return;
        }
        
        // Element is picked
        subsetSumsHelper(ind + 1, sum + arr[ind], arr, n, ans);
        
        // Element is not picked
        subsetSumsHelper(ind + 1, sum, arr, n, ans);
    }

public:
    vector<int> subsetSums(vector<int>& arr, int n) {
        vector<int> ans;
        subsetSumsHelper(0, 0, arr, n, ans);
        sort(ans.begin(), ans.end());
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N + 2^N \log(2^N)) = \mathcal{O}(2^N \cdot N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Accumulates sum in $\mathcal{O}(1)$ per branch step.

---

## 6. Dry Run

`arr = [2, 3]`, $N = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Pick 2, Pick 3` | sum = 2 + 3 = 5 | Push 5 |
| `Pick 2, Skip 3` | sum = 2 | Push 2 |
| `Skip 2, Pick 3` | sum = 3 | Push 3 |
| `Skip 2, Skip 3` | sum = 0 | Push 0 |
| `Sort` | sort(`[5, 2, 3, 0]`) | `[0, 2, 3, 5]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ `arr = [5]` $\implies$ `[0, 5]`.
- Array with 0s.

### Common Bugs to Avoid
- Passing `ans` by value instead of reference `vector<int>& ans`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the total number of subset sums?**  
  **A**: An array of size $N$ always has exactly $2^N$ subsets and thus $2^N$ subset sums (including the empty subset sum of 0).


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Subsets`, `Easy`
- **Related problems to practice next**:
- **Subsets II**: Duplicate subsets.
- **Subset Sum Equals K**: DP subset sum.
