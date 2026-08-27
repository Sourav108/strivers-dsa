# Check for Balanced Binary Tree (O(N) check) (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Check for Balanced Binary Tree (O(N) check)](https://takeuforward.org/data-structure/check-for-balanced-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given a binary tree, determine if it is height-balanced (a binary tree in which the left and right subtrees of every node differ in height by no more than 1).

---

## 1. Problem, Restated

Compute subtree heights bottom-up; return -1 immediately if $|lh - rh| > 1$ or if any child is unbalanced.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vectors or metric calculation (depth, diameter, path sum).
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Naive $\mathcal{O}(N^2)$**: Compute `height(node->left)` and `height(node->right)` at every node and check $|lh - rh| \le 1$. 2) **Optimal $\mathcal{O}(N)$**: Modify height function: if left subtree is unbalanced (`lh == -1`), return -1 immediately. If right subtree is unbalanced (`rh == -1`), return -1 immediately. If $|lh - rh| > 1$, return -1. Otherwise, return $1 + \max(lh, rh)$. Tree is balanced iff `checkHeight(root) != -1`.

- **Underlying Pattern**: `Bottom-Up Early-Exit Sentinel Propagating DFS (-1 on Imbalance)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Top-down recursive height calculation at each node in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
#include <algorithm>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
int getHeight(TreeNode* root) {
    if (!root) return 0;
    return 1 + max(getHeight(root->left), getHeight(root->right));
}
bool isBalancedBrute(TreeNode* root) {
    if (!root) return true;
    int lh = getHeight(root->left), rh = getHeight(root->right);
    if (abs(lh - rh) > 1) return false;
    return isBalancedBrute(root->left) && isBalancedBrute(root->right);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time on skewed trees.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Recomputes height repeatedly for lower nodes.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Bottom-Up Early-Exit DFS in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <algorithm>
#include <cmath>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    int checkHeight(TreeNode* root) {
        if (root == nullptr) return 0;
        
        int leftHeight = checkHeight(root->left);
        if (leftHeight == -1) return -1; // left subtree is unbalanced
        
        int rightHeight = checkHeight(root->right);
        if (rightHeight == -1) return -1; // right subtree is unbalanced
        
        if (abs(leftHeight - rightHeight) > 1) {
            return -1; // current node violates balance property
        }
        
        return 1 + max(leftHeight, rightHeight);
    }

public:
    bool isBalanced(TreeNode* root) {
        return checkHeight(root) != -1;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single bottom-up pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Propagates -1 error code upward immediately on discovering violation, eliminating repeated subtree scans.

---

## 6. Dry Run

Tree: `[1, 2, 2, 3, 3, null, null, 4, 4]`

| Step | Action / State Change | Result |
|---|---|---|
| `Node 4` | checkHeight(4) = 1 | valid |
| `Node 3` | lh=1, rh=0 -> return 1+max(1,0)=2 | valid |
| `Node 2 (left)` | lh=2, rh=0 -> $|2 - 0| = 2 > 1 \implies$ Return -1 | imbalance detected! |
| `Root 1` | leftHeight = -1 -> early exit return -1 | Return FALSE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `true`).
- Tree of size 1 (returns `true`).

### Common Bugs to Avoid
- Calling separate `height()` inside `isBalanced` (causes quadratic $\mathcal{O}(N^2)$ slowdown).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the top-down approach O(N^2) while bottom-up is O(N)?**  
  **A**: Top-down computes height from scratch at every depth ($N + (N-1) + (N-2) + \dots = \mathcal{O}(N^2)$). Bottom-up computes height from leaves upward, checking balance simultaneously in a single pass of each node ($\\mathcal{O}(N)$).

- **Q2: What is an AVL Tree?**  
  **A**: An **AVL Tree** is a self-balancing Binary Search Tree where the height difference (balance factor) of left and right subtrees is strictly $\le 1$ for all nodes, guaranteed via tree rotations in $\mathcal{O}(\log N)$ insertion/deletion.

- **Q3: What is the maximum number of nodes in an unbalanced tree of height H?**  
  **A**: A tree of height $H$ can have between $H$ nodes (skewed) and $2^H - 1$ nodes (perfect).


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `LeetCode-110`, `Easy`
- **Related problems to practice next**:
- **Maximum Depth of Binary Tree**: Height calculation.
- **Diameter of Binary Tree**: Bottom-up metric.
