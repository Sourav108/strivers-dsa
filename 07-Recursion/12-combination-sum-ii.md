# Combination Sum II (Single Use with Duplicates) (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Combination Sum II (Single Use with Duplicates)](https://takeuforward.org/data-structure/combination-sum-ii-find-all-unique-combinations/)
- **Difficulty**: Medium
- **Statement**: Given a collection of candidate numbers (`candidates`) and a target number (`target`), find all unique combinations where candidate numbers sum to `target`. Each number in `candidates` may only be used **once** in the combination, and the solution set must not contain duplicate combinations.

---

## 1. Problem, Restated

Find unique subsets summing to target with duplicate pruning and single-use constraint.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Sort `candidates`. 2) Loop `for (int i = ind; i < n; i++)`: if `arr[i] > target`, break early (array is sorted). If `i > ind && arr[i] == arr[i-1]`, continue (skip duplicate branches). 3) Push `arr[i]`, recurse on `i + 1` with `target - arr[i]`, backtrack.

- **Underlying Pattern**: `Sort + Loop-Based Backtracking with `if (i > ind && arr[i] == arr[i-1]) continue;``.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Combination Sum I with set filtering in $\mathcal{O}(2^N \log(2^N))$ time.

### C++17 Code
```cpp
// Set deduplication
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N \cdot N \log(2^N))$.
- **Space Complexity**: $\mathcal{O}(2^N)$.
- **Why it's not good enough**: Times out on large arrays with duplicate values.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sort + Level-based Loop Skipping with Early Break.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    void findCombinations(int ind, int target, const vector<int>& arr, vector<int>& ds, vector<vector<int>>& ans) {
        if (target == 0) {
            ans.push_back(ds);
            return;
        }
        
        for (int i = ind; i < (int)arr.size(); i++) {
            // Skip duplicate elements at the same level
            if (i > ind && arr[i] == arr[i - 1]) continue;
            
            // If current element exceeds target, no future element can fit (since sorted)
            if (arr[i] > target) break;
            
            ds.push_back(arr[i]);
            findCombinations(i + 1, target - arr[i], arr, ds, ans);
            ds.pop_back(); // backtrack
        }
    }

public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> ds;
        sort(candidates.begin(), candidates.end()); // essential for duplicate skipping and early break
        findCombinations(0, target, candidates, ds, ans);
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N \cdot K)$ time.
- **Space Complexity**: $\mathcal{O}(K)$ recursion call stack space.
- **Why this is optimal**: Sorted break + duplicate pruning eliminates duplicate branches upfront.

---

## 6. Dry Run

`candidates = [10, 1, 2, 7, 6, 1, 5], target = 8`

| Step | Action / State Change | Result |
|---|---|---|
| `Sorted` | `[1, 1, 2, 5, 6, 7, 10]` | Sorted candidates |
| `Comb 1` | `[1, 1, 6]` -> sum = 8 | Found |
| `Comb 2` | `[1, 2, 5]` -> sum = 8 | Found |
| `Comb 3` | `[1, 7]` -> sum = 8 | Found |
| `Comb 4` | `[2, 6]` -> sum = 8 | Found |
| `Result` | Unique combinations | `[[1, 1, 6], [1, 2, 5], [1, 7], [2, 6]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target smaller than all elements (early break returns `[]`).
- All elements identical `[1, 1, 1, 1], target = 2` $\implies$ `[[1, 1]]`.

### Common Bugs to Avoid
- Using `continue` instead of `break` on `arr[i] > target` (wastes checks on larger subsequent elements).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is break valid when arr[i] > target?**  
  **A**: Because the array is sorted in ascending order. If `arr[i] > target`, every subsequent element `arr[j]` for $j > i$ will also satisfy `arr[j] >= arr[i] > target`.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `LeetCode-40`, `Medium`
- **Related problems to practice next**:
- **Combination Sum I**: Unlimited use.
- **Subsets II**: Duplicate skipping.
