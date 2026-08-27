# Find Path Sum III (Sub-paths equal to Target Sum) (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find Path Sum III (Sub-paths equal to Target Sum)](https://takeuforward.org/data-structure/path-sum-iii/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary tree and an integer `targetSum`, return the number of paths where the sum of the values along the path equals `targetSum`. The path does not need to start or end at the root or a leaf, but it must go downwards (traveling only from parent nodes to child nodes).

---

## 1. Problem, Restated

Count downward paths summing to `targetSum` using Prefix Sum Hash Map + Backtracking DFS in $\mathcal{O}(N)$ time.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / integer sum / in-place modified tree.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Tree analog of 'Subarray Sum Equals K'. Maintain running prefix sum `currSum` along current root-to-node path in `unordered_map<long long, int> prefixMap` (initialized with `{0: 1}`). At node $u$: 1) `currSum += node->val`. 2) Add `prefixMap[currSum - targetSum]` to `totalPaths`. 3) Increment `prefixMap[currSum]++`. 4) Recurse on `left` and `right`. 5) **Backtrack**: `prefixMap[currSum]--` (so paths in sibling subtrees don't see this node's prefix sum!). Runs in $\mathcal{O}(N)$ time!

- **Underlying Pattern**: `Prefix Sum Frequency Hash Map Backtracking on Tree (`prefixCount[currSum - target]`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
For every node, run a DFS to find paths starting at that node in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <algorithm>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
class SolutionBrute {
    int countPathsFromNode(TreeNode* node, long long target) {
        if (!node) return 0;
        return (node->val == target) + countPathsFromNode(node->left, target - node->val) + countPathsFromNode(node->right, target - node->val);
    }
public:
    int pathSum(TreeNode* root, int targetSum) {
        if (!root) return 0;
        return countPathsFromNode(root, targetSum) + pathSum(root->left, targetSum) + pathSum(root->right, targetSum);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time on skewed trees.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Recomputes prefix sums repeatedly.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / Prefix Map / Morris algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(1)$ space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Prefix Sum Hash Map Backtracking DFS in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <unordered_map>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    int dfs(TreeNode* node, long long currSum, int targetSum, unordered_map<long long, int>& prefixMap) {
        if (node == nullptr) return 0;
        
        currSum += node->val;
        
        // Count sub-paths ending at current node with sum equal to targetSum
        int totalPaths = 0;
        if (prefixMap.count(currSum - targetSum)) {
            totalPaths += prefixMap[currSum - targetSum];
        }
        
        // Record current prefix sum
        prefixMap[currSum]++;
        
        // Recurse on children
        totalPaths += dfs(node->left, currSum, targetSum, prefixMap);
        totalPaths += dfs(node->right, currSum, targetSum, prefixMap);
        
        // Backtrack: remove current node's prefix sum before returning to parent
        prefixMap[currSum]--;
        
        return totalPaths;
    }

public:
    int pathSum(TreeNode* root, int targetSum) {
        unordered_map<long long, int> prefixMap;
        prefixMap[0] = 1; // base case for paths starting from root
        return dfs(root, 0, targetSum, prefixMap);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single DFS pass with $\mathcal{O}(1)$ average hash map lookups.
- **Space Complexity**: $\mathcal{O}(H)$ map and recursion call stack space.
- **Why this is optimal**: Prefix sum caching enables constant-time path verification at each node.

---

## 6. Dry Run

Tree: `10 -> left: 5 (left: 3, right: 2 (right: 1)), right: -3 (right: 11)`, `targetSum = 8`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 10` | currSum = 10, check 10-8=2 (count 0), map[10]=1 | paths = 0 |
| `Node 5` | currSum = 15, check 15-8=7 (count 0), map[15]=1 | paths = 0 |
| `Node 3` | currSum = 18, check 18-8=10 (found map[10]=1!) -> paths += 1 (`5->3`) | paths = 1 |
| `Node 2->1` | currSum = 18, check 18-8=10 (found map[10]=1!) -> paths += 1 (`5->2->1`) | paths = 2 |
| `Node -3->11` | currSum = 18, check 18-8=10 (found map[10]=1!) -> paths += 1 (`-3->11`) | Total Paths = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target sum requires 64-bit `long long` to prevent signed 32-bit integer overflow.
- Negative node values `[1000000000, 1000000000]`.

### Common Bugs to Avoid
- Using `int` for `currSum` (overflows on large node values!).
- Forgetting to backtrack `prefixMap[currSum]--` (causes branches in other subtrees to count invalid non-ancestor prefix sums).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is prefixMap[0] = 1 initialized at the beginning?**  
  **A**: Because if a path starting directly from the root has sum equal to `targetSum` (`currSum == targetSum`), then `currSum - targetSum = 0`. Initializing `prefixMap[0] = 1` accounts for all paths that begin at the root!

- **Q2: Why is prefixMap[currSum]-- mandatory after exploring children?**  
  **A**: Because a path must go STRICTLY DOWNWARDS from ancestor to descendant. If we didn't remove `currSum` on returning, a node in the right subtree could see a prefix sum from the left subtree (which is not an ancestor)!

- **Q3: What is the time complexity difference between the brute force and prefix map approach?**  
  **A**: Brute force runs in $\mathcal{O}(N^2)$ worst-case time by starting a new DFS from every node. Prefix Map runs in strictly linear $\mathcal{O}(N)$ time.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Prefix Sum`, `Hash Map`, `Backtracking`, `LeetCode-437`, `Medium`
- **Related problems to practice next**:
- **Maximum Path Sum**: Path sum optimization.
- **Subarray Sum Equals K**: Array counterpart.
