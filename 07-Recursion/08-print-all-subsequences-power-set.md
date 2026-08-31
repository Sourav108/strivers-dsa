# Print All Subsequences / Power Set (Step 7.2 — Subsequences Pattern)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Print All Subsequences / Power Set](https://takeuforward.org/data-structure/power-set-print-all-subsequences/)
- **Difficulty**: Medium
- **Statement**: Given an integer array `nums` of unique elements, return all possible subsets (the power set). The solution set must not contain duplicate subsets.

---

## 1. Problem, Restated

Generate all $2^N$ subsets using Pick / Don't Pick recursion and Bit Manipulation.

- **Input**: Parameters specified.
- **Output**: Value or vector of combinations meeting constraints.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

At each index $i$: 1) **Pick**: include `nums[i]`, recurse `i + 1`, backtrack. 2) **Don't Pick**: exclude `nums[i]`, recurse `i + 1`. This creates a binary recursion tree of depth $N$ generating all $2^N$ subsets.

- **Underlying Pattern**: `Take / Not-Take Recursive Tree & Bitmask Power Set`.

---

## 3. Approach 1 — Naive / Pitfall

### Idea
Bit manipulation: iterate integer mask from $0$ to $2^N - 1$.

### C++17 Code
```cpp
#include <vector>
using namespace std;
vector<vector<int>> subsetsBitmask(vector<int>& nums) {
    int n = nums.size();
    vector<vector<int>> ans;
    for (int mask = 0; mask < (1 << n); mask++) {
        vector<int> sub;
        for (int i = 0; i < n; i++) {
            if (mask & (1 << i)) sub.push_back(nums[i]);
        }
        ans.push_back(sub);
    }
    return ans;
}
```

### Java Code
```java
class Solution {
    int[][] subsetsBitmask(int[] nums) {
        int n = nums.length;
        int[][] ans;
        for (int mask = 0; mask < (1 << n); mask++) {
            int[] sub;
            for (int i = 0; i < n; i++) {
                if (mask & (1 << i)) sub.add(nums[i]);
            }
            ans.add(sub);
        }
        return ans;
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 2^N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary.
- **Why it's not good enough**: Iterative bitmasking approach.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard idiomatic recursion/backtracking below directly resolves all constraints.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Take / Not-Take Backtracking Recursion in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
#include <vector>
using namespace std;

class Solution {
private:
    void generateSubsets(int index, vector<int>& nums, vector<int>& current, vector<vector<int>>& result) {
        if (index == (int)nums.size()) {
            result.push_back(current);
            return;
        }
        
        // Choice 1: Take nums[index]
        current.push_back(nums[index]);
        generateSubsets(index + 1, nums, current, result);
        current.pop_back(); // backtrack
        
        // Choice 2: Do Not Take nums[index]
        generateSubsets(index + 1, nums, current, result);
    }

public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> current;
        generateSubsets(0, nums, current, result);
        return result;
    }
};
```

### Java Code
```java
class Solution {

    void generateSubsets(int index, int[] nums, int[] current, int[][] result) {
        if (index == nums.length) {
            result.add(current);
            return;
        }
        
        // Choice 1: Take nums[index]
        current.add(nums[index]);
        generateSubsets(index + 1, nums, current, result);
        current.remove(); // backtrack
        
        // Choice 2: Do Not Take nums[index]
        generateSubsets(index + 1, nums, current, result);
    }

    int[][] subsets(int[] nums) {
        int[][] result;
        int[] current;
        generateSubsets(0, nums, current, result);
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \cdot 2^N)$ time (copying subset of average length $N/2$ into result).
- **Space Complexity**: $\mathcal{O}(N)$ recursion call stack space.
- **Why this is optimal**: Binary decision tree visits each state exactly once.

---

## 6. Dry Run

`nums = [1, 2, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `Root (i=0)` | Take 1 vs Skip 1 | 2 branches |
| `i=1` | Take 2 vs Skip 2 | 4 branches |
| `i=2` | Take 3 vs Skip 3 | 8 leaf nodes generated |
| `Result` | Total subsets = $2^3 = 8$ | `[[], [1], [2], [1,2], [3], [1,3], [2,3], [1,2,3]]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty array `[]` -> `[[]]`.
- Single element `[1]` -> `[[], [1]]`.

### Common Bugs to Avoid
- Forgetting `current.pop_back()` after recursive call (pollutes subsequent branches).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the difference between Substring, Subarray, and Subsequence?**  
  **A**: Subarrays/Substrings are strictly contiguous segments ($N(N+1)/2$). Subsequences maintain relative order but can skip intermediate elements ($2^N$).


---

## 9. Tags & Related Problems

- **Tags**: `Recursion`, `Backtracking`, `Bit Manipulation`, `LeetCode-78`, `Medium`
- **Related problems to practice next**:
- **Subsets II**: Duplicates handling.
- **Subset Sum I**: Sum calculation.
