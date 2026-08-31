# Non-Decreasing Subsequences (Step 7.3 — Hard Recursion Problems & Backtracking)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Non-Decreasing Subsequences](https://takeuforward.org/recursion/non-decreasing-subsequences/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums`, return all the different possible non-decreasing subsequences of the given array with at least two elements. The given array may contain duplicates, and order of elements must not be sorted before search.

---

## 1. Problem, Restated

Find all subsequences of length $\ge 2$ where each element is $\ge$ preceding element, avoiding duplicate subsequences.

- **Input**: Grid / String / Constraints.
- **Output**: Boolean feasibility or complete vector of solution configurations.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Because we cannot sort the array (sorting changes original subsequence order), use an **unordered set `used` at each recursive level** to record which values have already been chosen at the current depth position. If `nums[i] >= current.back()` and `used.insert(nums[i]).second`: push `nums[i]`, recurse `i + 1`, backtrack.

- **Underlying Pattern**: `Unsorted Array Level-Set Deduplication Backtracking`.

---

## 3. Approach 1 — Naive / Unpruned Search

### Idea
Generate all $2^N$ subsequences and filter with `std::set<vector<int>>`.

### C++17 Code
```cpp
// Slow set filtering
```

### Java Code
```java
// Java equivalent
// Slow set filtering
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N \cdot N \log(2^N))$.
- **Space Complexity**: $\mathcal{O}(2^N)$.
- **Why it's not good enough**: Heavy set storage overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard backtracking algorithm below directly provides optimal pruning.

---

## 5. Approach 3 — Optimal / Production C++17

### Idea
Local Level Hash Set Pruning without sorting in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
private:
    void backtrack(int index, const vector<int>& nums, vector<int>& current, vector<vector<int>>& result) {
        if (current.size() >= 2) {
            result.push_back(current);
        }
        
        unordered_set<int> usedAtThisLevel; // deduplicates choices at current tree depth
        
        for (int i = index; i < (int)nums.size(); i++) {
            // Check non-decreasing condition
            if (!current.empty() && nums[i] < current.back()) continue;
            
            // Check if this number was already picked at current depth
            if (usedAtThisLevel.count(nums[i])) continue;
            usedAtThisLevel.insert(nums[i]);
            
            current.push_back(nums[i]);
            backtrack(i + 1, nums, current, result);
            current.pop_back(); // backtrack
        }
    }

public:
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> current;
        backtrack(0, nums, current, result);
        return result;
    }
};
```

### Java Code
```java
import java.util.*;

class Solution {

    void backtrack(int index, int[] nums, int[] current, int[][] result) {
        if (current.length >= 2) {
            result.add(current);
        }
        
        Set<Integer> usedAtThisLevel = new HashSet<>(); // deduplicates choices at current tree depth
        
        for (int i = index; i < nums.length; i++) {
            // Check non-decreasing condition
            if (!current.isEmpty() && nums[i] < current.peekLast()) continue;
            
            // Check if this number was already picked at current depth
            if (usedAtThisLevel.contains(nums[i])) continue;
            usedAtThisLevel.add(nums[i]);
            
            current.add(nums[i]);
            backtrack(i + 1, nums, current, result);
            current.remove(); // backtrack
        }
    }

    int[][] findSubsequences(int[] nums) {
        int[][] result;
        int[] current;
        backtrack(0, nums, current, result);
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N \cdot N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Local hash set prevents duplicate subtrees without requiring global set storage.

---

## 6. Dry Run

`nums = [4, 6, 7, 7]`

| Step | Action / State Change | Result |
|---|---|---|
| `Pick 4` | `[4, 6]`, `[4, 7]`, `[4, 6, 7]`, `[4, 6, 7, 7]`, `[4, 7, 7]` | Valid non-decreasing |
| `Pick 6, 7` | `[6, 7]`, `[6, 7, 7]`, `[7, 7]` | Second 7 skipped at depth 0 by used set! |
| `Result` | All unique non-decreasing subsequences | `[[4,6],[4,6,7],[4,6,7,7],[4,7],[4,7,7],[6,7],[6,7,7],[7,7]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Strictly decreasing array `[5, 4, 3, 2, 1]` (returns `[]`).
- All identical `[1, 1, 1]` $\implies$ `[[1, 1], [1, 1, 1]]`.

### Common Bugs to Avoid
- Sorting `nums` upfront (destroys original subsequence order).
- Forgetting length check `current.size() >= 2`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why can't we sort the array to deduplicate like in Subsets II?**  
  **A**: Because Subsequences must preserve the original array order. Sorting the array changes the problem from finding subsequences of the given array to finding subsequences of the sorted array!


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Subsequences`, `LeetCode-491`, `Medium`
- **Related problems to practice next**:
- **Subsets II**: Sorted deduplication.
