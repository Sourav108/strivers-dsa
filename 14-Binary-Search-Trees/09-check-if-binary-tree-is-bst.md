# Check if a Binary Tree is BST or Not (Valid BST range [min, max]) (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Check if a Binary Tree is BST or Not (Valid BST range [min, max])](https://takeuforward.org/data-structure/check-if-a-tree-is-a-bst-or-bt/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary tree, determine if it is a valid binary search tree (BST). A valid BST is defined as: left subtree contains only nodes with keys strictly less than node's key; right subtree contains only nodes with keys strictly greater than node's key; both subtrees must also be binary search trees.

---

## 1. Problem, Restated

Validate if every node satisfies valid open range constraint $(minVal, maxVal)$ where left child inherits $(minVal, curr\to val)$ and right child inherits $(curr\to val, maxVal)$.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

It is NOT enough to check `node->left < node && node->right > node`. Every node in the left subtree must be smaller than ALL its ancestors. Pass valid value bounds `(minVal, maxVal)` to each node: 1) Root starts with `(LONG_MIN, LONG_MAX)`. 2) If `node->val <= minVal || node->val >= maxVal`, return `false`. 3) Left child must be in `(minVal, node->val)`. 4) Right child must be in `(node->val, maxVal)`.

- **Underlying Pattern**: `Recursive Range Bounding $[minVal, maxVal]$ with 64-bit Long Sentinels`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
For every node, check if max in left subtree < val and min in right subtree > val in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) max/min checks
```

### Java Code
```java
// Java equivalent
// O(N^2) max/min checks
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Quadratic subtree scanning.

---

## 4. Approach 2 — Better

### Idea
Inorder Traversal verifying strictly increasing values (`curr->val > prev->val`) in O(N) time and O(H) space.

### C++17 Code
```cpp
#include <climits>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
class SolutionInorder {
    TreeNode* prev = nullptr;
public:
    bool isValidBST(TreeNode* root) {
        if (!root) return true;
        if (!isValidBST(root->left)) return false;
        if (prev && root->val <= prev->val) return false;
        prev = root;
        return isValidBST(root->right);
    }
};
```

### Java Code
```java
static class TreeNode { int val; TreeNode left, right; };
class SolutionInorder {
    TreeNode  prev = null;

    boolean isValidBST(TreeNode  root) {
        if (root == null) return true;
        if (!isValidBST(root.left)) return false;
        if (prev && root.val <= prev.val) return false;
        prev = root;
        return isValidBST(root.right);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ call stack.
- **Why it's still not optimal**: Uses recursion stack.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Value Range Bounding in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <climits>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    bool validate(TreeNode* node, long long minVal, long long maxVal) {
        if (node == nullptr) return true;
        
        // Node value must be strictly within (minVal, maxVal)
        if (node->val <= minVal || node->val >= maxVal) {
            return false;
        }
        
        // Left child: range becomes (minVal, node->val)
        // Right child: range becomes (node->val, maxVal)
        return validate(node->left, minVal, node->val) &&
               validate(node->right, node->val, maxVal);
    }

public:
    bool isValidBST(TreeNode* root) {
        return validate(root, LLONG_MIN, LLONG_MAX);
    }
};
```

### Java Code
```java
static class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    public TreeNode(int x) { /* initialized: val(x), left(null), right(null)  */  }
};

class Solution {

    boolean validate(TreeNode  node, long minVal, long maxVal) {
        if (node == null) return true;
        
        // Node value must be strictly within (minVal, maxVal)
        if (node.val <= minVal || node.val >= maxVal) {
            return false;
        }
        
        // Left child: range becomes (minVal, node.val)
        // Right child: range becomes (node.val, maxVal)
        return validate(node.left, minVal, node.val) &&
               validate(node.right, node.val, maxVal);
    }

    boolean isValidBST(TreeNode  root) {
        return validate(root, Long.MIN_VALUE, Long.MAX_VALUE);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Range inheritance checks global ancestral invariants in constant time per node.

---

## 6. Dry Run

Tree: `5 -> left: 1, right: 4 (left: 3, right: 6)`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 5` | range (-inf, +inf) -> valid | check children |
| `Left child 1` | range (-inf, 5) -> valid | valid |
| `Right child 4` | range (5, +inf) -> $4 \le 5 \implies$ Violation! | Return FALSE ❌ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Node value equals `INT_MAX` or `INT_MIN` (using `long long` bounds `LLONG_MIN, LLONG_MAX` prevents false rejections).
- Duplicate values `[2, 2, 2]` $\implies$ `false`.

### Common Bugs to Avoid
- Checking only direct parent-child relation `root->left->val < root->val` (misses invalid trees like `5 -> left: 1, right: 4 (left: 3, right: 6)` where 3 is in right subtree of 5!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is checking local parent-child relations insufficient?**  
  **A**: Consider a tree with root 10, right child 15, and 15's left child 6. Locally, $6 < 15$ holds, but globally $6 < 10$ violates the BST property because 6 is in the RIGHT subtree of 10!

- **Q2: Why are 64-bit LLONG_MIN and LLONG_MAX needed?**  
  **A**: Because if a leaf node in the BST has value `INT_MIN` or `INT_MAX`, standard 32-bit `INT_MIN - 1` would cause signed integer underflow/overflow.

- **Q3: Can duplicate values exist in a standard valid BST?**  
  **A**: No. Standard LeetCode/strict BST definitions require strict inequalities ($< and >$). If duplicate values are present, `node->val <= minVal` correctly returns `false`.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `DFS`, `LeetCode-98`, `Medium`
- **Related problems to practice next**:
- **Recover BST**: Fix swapped nodes.
- **Largest BST in Binary Tree**: Subtree BST validation.
