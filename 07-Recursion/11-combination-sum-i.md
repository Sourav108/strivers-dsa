# Combination Sum I (Unlimited Reuse) (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Combination Sum I (Unlimited Reuse)](https://takeuforward.org/data-structure/combination-sum-1/)
- **Difficulty**: Medium
- **Statement**: Given an array of distinct integers `candidates` and a target integer `target`, return a list of all unique combinations where the chosen numbers sum to `target`. The same number may be chosen unlimited times.

---

## 1. Problem, Restated

Find all combinations summing to target allowing unlimited repetition of elements.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

At index `ind`: 1) **Take**: if `candidates[ind] <= target`, push `candidates[ind]`, recurse on SAME index `ind` with `target - candidates[ind]`, then `pop_back()`. 2) **Not-Take**: advance to `ind + 1` with unchanged `target`.

- **Underlying Pattern**: `Unbounded Take / Not-Take Backtracking (`recurse(ind, target - arr[ind])`)`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Generating all combinations up to max depth with set filtering.

### C++17 Code
```cpp
// Unbounded permutation with set
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^T)$ exponential.
- **Space Complexity**: $\mathcal{O}(T)$.
- **Why it's not good enough**: Redundant explorations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Take (stay at index) vs Not-Take (advance index) Backtracking.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    void findCombinations(int ind, int target, const vector<int>& arr, vector<int>& ds, vector<vector<int>>& ans) {
        if (ind == (int)arr.size()) {
            if (target == 0) {
                ans.push_back(ds);
            }
            return;
        }
        
        // Choice 1: Pick the element (can pick again, so ind stays the same)
        if (arr[ind] <= target) {
            ds.push_back(arr[ind]);
            findCombinations(ind, target - arr[ind], arr, ds, ans);
            ds.pop_back(); // backtrack
        }
        
        // Choice 2: Do not pick the element (move to next index)
        findCombinations(ind + 1, target, arr, ds, ans);
    }

public:
    vector<vector<int>> combinationSum(vector<int>& candidates, int target) {
        vector<vector<int>> ans;
        vector<int> ds;
        findCombinations(0, target, candidates, ds, ans);
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^T \cdot K)$ where $T = \text{target} / \min(\text{candidates})$ is max recursion depth.
- **Space Complexity**: $\mathcal{O}(T)$ recursion call stack space.
- **Why this is optimal**: Pruning when `arr[ind] > target` cuts branch exploration early.

---

## 6. Dry Run

`candidates = [2, 3, 6, 7], target = 7`

| Step | Action / State Change | Result |
|---|---|---|
| `Pick 2, Pick 2, Pick 3` | 2 + 2 + 3 = 7 -> target == 0 | Push `[2, 2, 3]` |
| `Pick 7` | 7 = 7 -> target == 0 | Push `[7]` |
| `Result` | Valid combinations | `[[2, 2, 3], [7]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target cannot be formed (returns `[]`).
- Single element `[2], target = 4` $\implies$ `[[2, 2]]`.

### Common Bugs to Avoid
- Advancing `ind + 1` on Pick (disallows unlimited reuse).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does ind stay the same on the Pick branch?**  
  **A**: Because elements can be reused indefinitely. Only when we decide to NOT pick the element do we advance to `ind + 1`.


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `LeetCode-39`, `Medium`
- **Related problems to practice next**:
- **Combination Sum II**: Single use constraint.
