# Largest BST in a Binary Tree (Postorder with bounds validation) (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Largest BST in a Binary Tree (Postorder with bounds validation)](https://takeuforward.org/data-structure/maximum-sum-bst-in-binary-tree/)
- **Difficulty**: Hard
- **Statement**: Given a binary tree, find the size of the largest subtree which is also a Binary Search Tree (BST). Size of a subtree is the total number of nodes in that subtree.

---

## 1. Problem, Restated

Bottom-up postorder DFS returning `{minVal, maxVal, size}` to validate and count largest valid BST in $\mathcal{O}(N)$ time.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean / class methods.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

At node $u$: 1) Recursively query left child $L$ and right child $R$. 2) **BST Validity Condition**: Subtree at $u$ is a valid BST iff $L$ is a BST, $R$ is a BST, and $L.max < u\to val < R.min$! 3) If valid: return `{min(u->val, L.min), max(u->val, R.max), 1 + L.size + R.size}`. 4) If invalid: return `{-inf, +inf, max(L.size, R.size)}` (the range bounds `[-inf, +inf]` intentionally guarantee that no ancestor above $u$ can ever form a valid BST!).

- **Underlying Pattern**: `Bottom-Up Postorder Tuple Propagation (`{minVal, maxVal, maxSize}`)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
For every node, call `isValidBST` in $\mathcal{O}(N)$ and count nodes, taking $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) top-down validation
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Quadratic subtree re-validations.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / Iterator / Morris algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(H)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single-Pass Bottom-Up Postorder DFS in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <algorithm>
#include <climits>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class NodeValue {
public:
    int maxNode, minNode, maxSize;
    NodeValue(int minN, int maxN, int sz) : minNode(minN), maxNode(maxN), maxSize(sz) {}
};

class Solution {
private:
    NodeValue postorder(TreeNode* root) {
        // Base case: empty tree is a valid BST of size 0
        if (root == nullptr) {
            return NodeValue(INT_MAX, INT_MIN, 0);
        }
        
        // Postorder traversal: resolve left and right subtrees first
        auto left = postorder(root->left);
        auto right = postorder(root->right);
        
        // Check BST property: left.max < root->val < right.min
        if (left.maxNode < root->val && root->val < right.minNode) {
            // Valid BST!
            return NodeValue(
                min(root->val, left.minNode),
                max(root->val, right.maxNode),
                1 + left.maxSize + right.maxSize
            );
        }
        
        // Invalid BST: return (-inf, +inf) so no parent node can form a valid BST
        return NodeValue(INT_MIN, INT_MAX, max(left.maxSize, right.maxSize));
    }

public:
    int largestBst(TreeNode *root) {
        return postorder(root).maxSize;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single postorder pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Bottom-up tuple aggregation validates BST invariants and size in constant time per node.

---

## 6. Dry Run

Tree: `10 -> left: 5 (left: 1, right: 8), right: 15 (right: 7)`

| Step | Action / State Change | Result |
|---|---|---|
| `Left Subtree at 5` | left 1(valid), right 8(valid) -> 1 < 5 < 8 is FALSE ($5 < 8$) -> 5 is valid! size=3 | left.maxSize = 3 |
| `Right Subtree at 15` | right 7 < 15 -> invalid BST -> returns size 1 | right.maxSize = 1 |
| `Root 10` | left.max(8) < 10, but right is invalid (min=INT_MIN) -> root invalid | maxSize = max(3, 1) = 3 |
| `Result` | Largest BST subtree is rooted at 5 (size 3) | Largest BST = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Entire tree is a valid BST (returns $N$).
- Single node tree (returns 1).
- Empty tree (returns 0).

### Common Bugs to Avoid
- Returning `(INT_MAX, INT_MIN)` on invalid subtree instead of `(INT_MIN, INT_MAX)` (inverting the bounds causes parent to mistakenly accept the invalid subtree!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do we return (INT_MIN, INT_MAX) for an INVALID BST?**  
  **A**: Because when the parent checks `left.max < root->val && root->val < right.min`, having `left.max = INT_MAX` or `right.min = INT_MIN` mathematically guarantees that the condition evaluates to `false`, correctly propagating the invalid status to all ancestors!

- **Q2: Why is base case null initialized to (INT_MAX, INT_MIN)?**  
  **A**: Because for a leaf node, its left child is null (`left.max = INT_MIN < leaf->val`) and its right child is null (`right.min = INT_MAX > leaf->val`), naturally satisfying the BST condition!

- **Q3: How does LeetCode 1373 (Maximum Sum BST in Binary Tree) differ from this problem?**  
  **A**: LeetCode 1373 asks for the maximum SUM of keys in a valid BST subtree rather than the node count. Replace `maxSize` with `sum = root->val + left.sum + right.sum` and track `globalMaxSum`.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `DFS`, `Hard`
- **Related problems to practice next**:
- **Check if Valid BST**: Validation.
- **Maximum Path Sum**: Postorder metrics.
