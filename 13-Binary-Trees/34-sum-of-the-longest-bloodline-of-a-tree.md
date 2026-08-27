# Sum of Nodes on the Longest Path from Root to Leaf (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Sum of Nodes on the Longest Path from Root to Leaf](https://takeuforward.org/data-structure/sum-of-the-longest-bloodline-of-a-tree/)
- **Difficulty**: Medium
- **Statement**: Given a binary tree of size $N$, find the sum of all nodes along the longest root-to-leaf path. If two or more paths have the same maximum length, the path with the maximum sum should be returned.

---

## 1. Problem, Restated

Find the maximum path sum among all root-to-leaf paths of maximum edge length.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / integer sum / in-place modified tree.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Pass `currentLength` and `currentSum` down the tree during DFS. When reaching a leaf node (`!node->left && !node->right`): 1) If `currentLength > maxLen`: update `maxLen = currentLength`, `maxSum = currentSum`. 2) If `currentLength == maxLen`: update `maxSum = max(maxSum, currentSum)`. Return `maxSum`.

- **Underlying Pattern**: `DFS Path Length & Sum Tuple Maximization (`{maxLen, maxSum}`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Store all root-to-leaf paths, find max length, then find max sum in $\mathcal{O}(N)$ time and $\mathcal{O}(N \cdot H)$ space.

### C++17 Code
```cpp
// Store all paths approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N \cdot H)$ path memory.
- **Why it's not good enough**: Stores full path vectors.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / Prefix Map / Morris algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(1)$ space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single DFS Tracking `{maxLen, maxSum}` in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <algorithm>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    void findLongestBloodline(TreeNode* node, int len, int sum, int& maxLen, int& maxSum) {
        if (node == nullptr) return;
        
        sum += node->val;
        len += 1;
        
        // When leaf node is reached
        if (node->left == nullptr && node->right == nullptr) {
            if (len > maxLen) {
                maxLen = len;
                maxSum = sum;
            } else if (len == maxLen) {
                maxSum = max(maxSum, sum);
            }
            return;
        }
        
        findLongestBloodline(node->left, len, sum, maxLen, maxSum);
        findLongestBloodline(node->right, len, sum, maxLen, maxSum);
    }

public:
    int sumOfLongRootToLeafPath(TreeNode* root) {
        if (root == nullptr) return 0;
        
        int maxLen = 0;
        int maxSum = 0;
        findLongestBloodline(root, 0, 0, maxLen, maxSum);
        
        return maxSum;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single DFS pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Direct scalar updates avoid auxiliary path vector allocations.

---

## 6. Dry Run

Tree: `4 -> left: 2 (left: 7, right: 1 (left: 6)), right: 5 (left: 2, right: 3)`

| Step | Action / State Change | Result |
|---|---|---|
| `Path 4->2->7` | len = 3, sum = 13 -> maxLen = 3, maxSum = 13 | maxLen = 3, maxSum = 13 |
| `Path 4->2->1->6` | len = 4, sum = 13 -> len > 3 -> maxLen = 4, maxSum = 13 | maxLen = 4, maxSum = 13 |
| `Path 4->5->2` | len = 3 -> shorter than 4 -> ignore | ignored |
| `Path 4->5->3` | len = 3 -> shorter than 4 -> ignore | ignored |
| `Result` | Longest path is `4->2->1->6` with sum 13 | Result = 13 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node tree `[5]` (returns 5).
- All paths equal length (returns path with maximum sum).

### Common Bugs to Avoid
- Updating `maxSum` on any node rather than strictly at leaf nodes (`!left && !right`).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must the comparison happen ONLY at leaf nodes?**  
  **A**: Because the problem specifies root-to-LEAF paths. An internal node with a large sum cannot be considered if it is not a complete root-to-leaf path.

- **Q2: Can this be solved bottom-up using pair<int, int>?**  
  **A**: Yes! Return `pair<int, int>` `{height, sum}` from subtrees: choose the child with larger height (or larger sum on tie), and return `{1 + maxChild.height, node->val + maxChild.sum}`.

- **Q3: How to handle negative node values?**  
  **A**: Initialize `maxSum = INT_MIN` to correctly handle trees where all node values are negative.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `Medium`
- **Related problems to practice next**:
- **Height of Binary Tree**: Longest path length.
- **Maximum Path Sum**: Path sum optimization.
