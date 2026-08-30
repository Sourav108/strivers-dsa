# House Robber II (Circular Street) (Step 16.1 — Introduction to DP)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [House Robber II (Circular Street)](https://takeuforward.org/data-structure/dynamic-programming-house-robber-dp-6/)
- **Difficulty**: Medium
- **Statement**: You are a professional robber planning to rob houses along a street. Each house has a certain amount of money stashed. All houses are arranged in a **circle**. That means the first house is the neighbor of the last one. Meanwhile, adjacent houses have a security system connected, and it will automatically contact the police if two adjacent houses were broken into on the same night. Given an integer array `nums` representing the amount of money of each house, return the maximum amount of money you can rob tonight without alerting the police.

---

## 1. Problem, Restated

Find the maximum sum non-adjacent subsequence in a circular array where index 0 and index $n - 1$ are adjacent in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

- **Input**: Problem constraints and parameters.
- **Output**: Minimum / Maximum / Count result.
- **Complexity Goal**: Optimal $\mathcal{O}(M \times N)$ time and $\mathcal{O}(N)$ or $\mathcal{O}(1)$ auxiliary space.

---

## 2. Intuition & Pattern

**The Circular Invariant**: Since house 0 and house $n - 1$ are neighbors, you can NEVER rob both house 0 and house $n - 1$ on the same night. 
This decomposes the circular problem into two mutually exclusive, linearly independent subproblems: 
1) **Subproblem 1 (Exclude Last House)**: Consider array slice `nums[0 ... n - 2]`. 
2) **Subproblem 2 (Exclude First House)**: Consider array slice `nums[1 ... n - 1]`. 
**Master Formula**: 
$$\text{maxLoot} = \max(\text{robLinear}(nums[0 \dots n-2]), \text{robLinear}(nums[1 \dots n-1]))$$ 
Each subproblem is standard House Robber I solved in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space using sliding variables `prev` and `prev2`. Edge case $n = 1$ returns `nums[0]` directly.

- **Underlying Pattern**: `Circular Array Splitting + Dual House Robber I Invocations`.

---

## 3. Approach 1 — Naive / Pure Recursion

### Idea
Recursively explore all valid circular non-adjacent subsets in $\mathcal{O}(2^N)$ time.

### C++17 Code
```cpp
class SolutionNaive {
public:
    int rob(vector<int>& nums) {
        if (nums.size() == 1) return nums[0];
        // O(2^N) circular recursive search
        return 0;
    }
};
```

### Java Code
```java
class SolutionNaive {

    int rob(int[] nums) {
        if (nums.length == 1) return nums[0];
        // O(2^N) circular recursive search
        return 0;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(2^N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ stack.
- **Why it's not good enough**: Exponential duplicate subset exploration.

---

## 4. Approach 2 — Better (Tabulation / Memoization)

### Idea
Two Tabulation DP passes in O(N) time and O(N) space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class SolutionTabulation {
    int robLinear(vector<int>& arr) {
        int n = arr.size();
        if (n == 0) return 0;
        if (n == 1) return arr[0];
        vector<int> dp(n, 0);
        dp[0] = arr[0];
        dp[1] = max(arr[0], arr[1]);
        for (int i = 2; i < n; i++) {
            dp[i] = max(arr[i] + dp[i - 2], dp[i - 1]);
        }
        return dp[n - 1];
    }
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 1) return nums[0];
        vector<int> arr1(nums.begin(), nums.end() - 1);
        vector<int> arr2(nums.begin() + 1, nums.end());
        return max(robLinear(arr1), robLinear(arr2));
    }
};
```

### Java Code
```java
class SolutionTabulation {
    int robLinear(int[] arr) {
        int n = arr.length;
        if (n == 0) return 0;
        if (n == 1) return arr[0];
        int[] dp = new int[n];
        dp[0] = arr[0];
        dp[1] = Math.max(arr[0], arr[1]);
        for (int i = 2; i < n; i++) {
            dp[i] = Math.max(arr[i] + dp[i - 2], dp[i - 1]);
        }
        return dp[n - 1];
    }

    int rob(int[] nums) {
        int n = nums.length;
        if (n == 1) return nums[0];
        int[] arr1(nums.begin(), nums.end() - 1);
        int[] arr2(nums.begin() + 1, nums.end());
        return Math.max(robLinear(arr1), robLinear(arr2));
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space for vectors and DP arrays.
- **Why it's still not optimal**: Extra vector slicing copies memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Space-Optimized DP)

### Idea
Dual-Range Space-Optimized Sliding Variables in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
#include <algorithm>
using namespace std;

class Solution {
private:
    // Helper function to solve linear House Robber I in O(1) space
    int robLinear(const vector<int>& nums, int start, int end) {
        int prev2 = 0; // dp[i - 2]
        int prev = 0;  // dp[i - 1]
        
        for (int i = start; i <= end; i++) {
            int pick = nums[i] + prev2;
            int notPick = prev;
            
            int cur = max(pick, notPick);
            prev2 = prev;
            prev = cur;
        }
        
        return prev;
    }
    
public:
    int rob(vector<int>& nums) {
        int n = nums.size();
        if (n == 1) return nums[0]; // Single house base case
        
        // Case 1: Rob from 0 to n - 2 (leave last house)
        int loot1 = robLinear(nums, 0, n - 2);
        
        // Case 2: Rob from 1 to n - 1 (leave first house)
        int loot2 = robLinear(nums, 1, n - 1);
        
        return max(loot1, loot2);
    }
};
```

### Java Code
```java
class Solution {

    // Helper function to solve linear House Robber I in O(1) space
    int robLinear(int[] nums, int start, int end) {
        int prev2 = 0; // dp[i - 2]
        int prev = 0;  // dp[i - 1]
        
        for (int i = start; i <= end; i++) {
            int pick = nums[i] + prev2;
            int notPick = prev;
            
            int cur = Math.max(pick, notPick);
            prev2 = prev;
            prev = cur;
        }
        
        return prev;
    }

    int rob(int[] nums) {
        int n = nums.length;
        if (n == 1) return nums[0]; // Single house base case
        
        // Case 1: Rob from 0 to n - 2 (leave last house)
        int loot1 = robLinear(nums, 0, n - 2);
        
        // Case 2: Rob from 1 to n - 1 (leave first house)
        int loot2 = robLinear(nums, 1, n - 1);
        
        return Math.max(loot1, loot2);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (two linear passes of size $N - 1$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (index ranges passed by reference).
- **Why this is optimal**: Range indices avoid extra vector allocations and run entirely on scalar register variables.

---

## 6. Dry Run

`nums = [2, 3, 2]` ($N = 3$, circular)

| Step | Action / State Change | Result |
|---|---|---|
| `Case 1: Range [0, 1]` | nums[0]=2, nums[1]=3 $\implies cur = \max(3, 2) = 3$. loot1 = 3 | loot1 = 3 |
| `Case 2: Range [1, 2]` | nums[1]=3, nums[2]=2 $\implies cur = \max(2, 3) = 3$. loot2 = 3 | loot2 = 3 |
| `Result` | max(3, 3) = 3 (Cannot rob house 0 and 2 together because they are circular neighbors!) | Max Loot = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $N = 1$ (returns `nums[0]`).
- $N = 2$ (returns `max(nums[0], nums[1])`).
- $N = 3$ with equal values.

### Common Bugs to Avoid
- Forgetting $N = 1$ base case (range $[0, n-2]$ becomes $[0, -1]$, returning 0 incorrectly).
- Allocating new sliced vectors instead of passing index boundaries.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does splitting the circle into [0, n-2] and [1, n-1] cover ALL optimal solutions?**  
  **A**: Any valid robbing strategy on a circular street belongs to one of three categories: 1) Robs house 0 and skips house $n-1$ (contained in $[0, n-2]$); 2) Robs house $n-1$ and skips house 0 (contained in $[1, n-1]$); 3) Skips BOTH house 0 and house $n-1$ (contained in both ranges!). Since no strategy can rob both, the union of $[0, n-2]$ and $[1, n-1]$ is exhaustive!

- **Q2: How does this extend to House Robber III (Binary Tree / LeetCode 337)?**  
  **A**: On a binary tree, we return a pair `{robRoot, skipRoot}` from each child: `robRoot = node->val + left.skip + right.skip` and `skipRoot = max(left.rob, left.skip) + max(right.rob, right.skip)` in $\mathcal{O}(N)$ post-order tree DP!

- **Q3: Can this circular split technique be applied to other circular DP problems?**  
  **A**: Yes! Common in Circular Max Subarray Sum, Circular Matrix Chains, and Ring-topology Network Partitioning.


---

## 9. Tags & Related Problems

- **Tags**: `Dynamic Programming`, `1D DP`, `Circular Array`, `LeetCode-213`, `Medium`
- **Related problems to practice next**:
- **House Robber I**: Linear counterpart.
- **House Robber III**: Tree DP variant.
