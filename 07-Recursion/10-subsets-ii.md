# Subsets II (Unique Subsets with Duplicates) (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Subsets II (Unique Subsets with Duplicates)](https://takeuforward.org/data-structure/subset-ii-print-all-the-unique-subsets/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums` that may contain duplicates, return all possible subsets (the power set). The solution set must not contain duplicate subsets. Return the solution in any order.

---

## 1. Problem, Restated

Generate unique subsets from array with duplicates in $\mathcal{O}(2^N)$ time without using a slow `std::set`.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Sort the array so duplicates are adjacent. 2) At each recursion level `ind`, loop `for (int i = ind; i < n; i++)`: if `i > ind && nums[i] == nums[i-1]`, skip `continue;` (avoids duplicate subsets at the same depth level). 3) Push `nums[i]`, recurse on `i + 1`, backtrack `pop_back()`.

- **Underlying Pattern**: `Sort + Loop-Based Backtracking Duplicate Pruning (`if (i > ind && nums[i] == nums[i-1]) continue;`)`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Generate all subsets, insert into `std::set<vector<int>>` in $\mathcal{O}(2^N \log(2^N) \cdot N)$ time.

### C++17 Code
```cpp
#include <set>
#include <vector>
#include <algorithm>
using namespace std;
set<vector<int>> setSubsets(vector<int>& nums) {
    // slow set insertion approach
    set<vector<int>> st;
    return st;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N \cdot N \log(2^N))$ time.
- **Space Complexity**: $\mathcal{O}(2^N \cdot N)$ set memory.
- **Why it's not good enough**: Heavy Red-Black Tree set overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Sort + In-Place Loop Level Duplicate Skipping in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    void findSubsets(int ind, vector<int>& nums, vector<int>& ds, vector<vector<int>>& ans) {
        ans.push_back(ds); // record subset at current node
        
        for (int i = ind; i < (int)nums.size(); i++) {
            // Skip duplicates at the same tree depth
            if (i > ind && nums[i] == nums[i - 1]) continue;
            
            ds.push_back(nums[i]);
            findSubsets(i + 1, nums, ds, ans);
            ds.pop_back(); // backtrack
        }
    }

public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<vector<int>> ans;
        vector<int> ds;
        sort(nums.begin(), nums.end()); // sort to group duplicates
        findSubsets(0, nums, ds, ans);
        return ans;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N + 2^N \cdot N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Level-based loop skipping eliminates duplicate branches entirely without set overhead.

---

## 6. Dry Run

`nums = [1, 2, 2]`

| Step | Action / State Change | Result |
|---|---|---|
| `Depth 0` | ans += `[]` | Starts with empty |
| `i = 0 (1)` | ds = `[1]`, ans += `[1]` | Recurse on [2, 2] |
| `i = 1 (2)` | ds = `[1, 2]`, ans += `[1, 2]` | Recurse on [2] |
| `i = 2 (2)` | ds = `[1, 2, 2]`, ans += `[1, 2, 2]` | Leaf |
| `Backtrack to Depth 1` | i = 2 (val 2 == nums[1]) -> `i > ind` skips duplicate! | Duplicate prevented |
| `Result` | Unique subsets | `[[], [1], [1,2], [1,2,2], [2], [2,2]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- All elements identical `[2, 2, 2]` $\implies$ `[[], [2], [2,2], [2,2,2]]`.
- Empty array.

### Common Bugs to Avoid
- Writing `if (nums[i] == nums[i-1])` without `i > ind` check (would incorrectly skip the first valid duplicate in the subset!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is i > ind condition crucial?**  
  **A**: When $i == ind$, we are picking the FIRST occurrence of the duplicate number at this level (which is valid). When $i > ind$, we are trying to replace the first occurrence with an identical number at the SAME level (which creates a duplicate subset).


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Subsets`, `LeetCode-90`, `Medium`
- **Related problems to practice next**:
- **Combination Sum II**: Same duplicate skipping pattern.
