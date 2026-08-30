# Check for Symmetrical Binary Tree (Mirror Reflection) (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Check for Symmetrical Binary Tree (Mirror Reflection)](https://takeuforward.org/data-structure/check-for-symmetrical-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, check whether it is a mirror of itself (i.e., symmetric around its center).

---

## 1. Problem, Restated

Verify if left subtree is a mirror reflection of right subtree: `t1->val == t2->val && isMirror(t1->left, t2->right) && isMirror(t1->right, t2->left)`.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / `TreeNode*` pointer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

A tree is symmetric if its left subtree is a mirror reflection of its right subtree. Define helper `isMirror(t1, t2)`: 1) Both null $\implies$ `true`. 2) One null $\implies$ `false`. 3) Values differ $\implies$ `false`. 4) Recursively verify: `isMirror(t1->left, t2->right) && isMirror(t1->right, t2->left)`.

- **Underlying Pattern**: `Mirror Dual-Pointer Traversal (`t1->left` vs `t2->right` & `t1->right` vs `t2->left`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Invert right subtree, then check if left subtree and inverted right subtree are identical in 2 passes.

### C++17 Code
```cpp
// Invert and check identical
```

### Java Code
```java
// Java equivalent
// Invert and check identical
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ 2 passes.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Two full tree passes.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Simultaneous Mirror DFS in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    bool isMirror(TreeNode* t1, TreeNode* t2) {
        if (t1 == nullptr && t2 == nullptr) return true;
        if (t1 == nullptr || t2 == nullptr) return false;
        
        return (t1->val == t2->val) &&
               isMirror(t1->left, t2->right) &&
               isMirror(t1->right, t2->left);
    }

public:
    bool isSymmetric(TreeNode* root) {
        if (root == nullptr) return true;
        return isMirror(root->left, root->right);
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

    boolean isMirror(TreeNode  t1, TreeNode  t2) {
        if (t1 == null && t2 == null) return true;
        if (t1 == null || t2 == null) return false;
        
        return (t1.val == t2.val) &&
               isMirror(t1.left, t2.right) &&
               isMirror(t1.right, t2.left);
    }

    boolean isSymmetric(TreeNode  root) {
        if (root == null) return true;
        return isMirror(root.left, root.right);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Direct mirror recursion checks opposing branches simultaneously.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 3, right: 4), right: 2 (left: 4, right: 3)`

| Step | Action / State Change | Result |
|---|---|---|
| `Root` | call `isMirror(2(left), 2(right))` | valid |
| `Branch 1` | `isMirror(3(outer left), 3(outer right))` -> true | valid |
| `Branch 2` | `isMirror(4(inner right), 4(inner left))` -> true | valid |
| `Result` | All mirror subtrees match | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `true`).
- Single node tree (returns `true`).
- Asymmetric structure `[1, 2, 2, null, 3, null, 3]` $\implies$ `false`.

### Common Bugs to Avoid
- Comparing `t1->left` with `t2->left` (this checks identical trees, NOT symmetric mirror trees!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to solve this iteratively without recursion?**  
  **A**: Use a `std::queue<TreeNode*>`: push `root->left` and `root->right`. In loop, pop `t1` and `t2`, compare values, then push pairs: `{t1->left, t2->right}` and `{t1->right, t2->left}`.

- **Q2: Why is checking t1->left vs t2->right required for symmetry?**  
  **A**: Because reflection across the vertical axis swaps left with right. The leftmost leaf must mirror the rightmost leaf!

- **Q3: Can checking palindrome on inorder traversal determine symmetry?**  
  **A**: No! Inorder of `[1, 2, 2, 2, null, 2]` is `[2, 2, 1, 2, 2]` (a palindrome), but the tree is structurally asymmetric. Structural pointers must be verified directly.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `LeetCode-101`, `Easy`
- **Related problems to practice next**:
- **Same Tree**: Identical check.
- **Invert Binary Tree**: Tree reflection.
