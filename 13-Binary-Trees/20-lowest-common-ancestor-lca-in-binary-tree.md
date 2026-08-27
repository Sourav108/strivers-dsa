# Lowest Common Ancestor (LCA) in Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Lowest Common Ancestor (LCA) in Binary Tree](https://takeuforward.org/data-structure/lowest-common-ancestor-for-two-given-nodes/)
- **Difficulty**: Medium
- **Statement**: Given a binary tree, find the lowest common ancestor (LCA) of two given nodes $p$ and $q$. The LCA is defined between two nodes $p$ and $q$ as the lowest node in $T$ that has both $p$ and $q$ as descendants (where we allow a node to be a descendant of itself).

---

## 1. Problem, Restated

Find the deepest node whose subtrees contain both $p$ and $q$ in a single postorder DFS pass.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / `TreeNode*` pointer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Base cases: 1) If `root == nullptr || root == p || root == q`, return `root`. 2) Recursively search left: `left = lowestCommonAncestor(root->left, p, q)`. 3) Recursively search right: `right = lowestCommonAncestor(root->right, p, q)`. 4) **Decision**: If both `left != nullptr && right != nullptr`, $p$ and $q$ are in separate subtrees $\implies$ `root` is the LCA! If only one is non-null, propagate that non-null node upward. If both null, return `nullptr`.

- **Underlying Pattern**: `Bottom-Up Postorder Node Convergence DFS`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Find root-to-node path for $p$ and path for $q$, then compare paths in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
// Path comparison LCA
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (2 passes).
- **Space Complexity**: $\mathcal{O}(N)$ path storage.
- **Why it's not good enough**: Stores full path vectors.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single-Pass Postorder Convergence in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // Base case: null or found target node
        if (root == nullptr || root == p || root == q) {
            return root;
        }
        
        TreeNode* left = lowestCommonAncestor(root->left, p, q);
        TreeNode* right = lowestCommonAncestor(root->right, p, q);
        
        // If both left and right return non-null, root is the LCA
        if (left != nullptr && right != nullptr) {
            return root;
        }
        
        // Otherwise return the non-null result
        return (left != nullptr) ? left : right;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Single bottom-up pass eliminates auxiliary path memory allocations.

---

## 6. Dry Run

Tree: `3 -> left: 5 (left: 6, right: 2 (left: 7, right: 4)), right: 1 (left: 0, right: 8)`, $p = 5, q = 1$

| Step | Action / State Change | Result |
|---|---|---|
| `Call root 3` | calls left(5) and right(1) | searching |
| `Node 5` | root == p (5) -> return 5 immediately | left = 5 |
| `Node 1` | root == q (1) -> return 1 immediately | right = 1 |
| `At Root 3` | left=5 != null AND right=1 != null -> Return 3 (LCA)! | LCA = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $p$ is an ancestor of $q$ (e.g. $p = 5, q = 4 \implies$ returns 5).
- Single root node tree.

### Common Bugs to Avoid
- Continuing recursive search down when `root == p` (no need to explore subtrees of $p$; if $q$ is below $p$, $p$ is already the LCA!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do we return root immediately when root == p || root == q?**  
  **A**: Because if $q$ is in the subtree of $p$, $p$ is the LCA. If $q$ is elsewhere in the tree, $p$ will be returned up to the true LCA where it meets $q$!

- **Q2: How does LCA differ in a Binary Search Tree (BST)?**  
  **A**: In a BST, if both $p, q < root$, move left; if both $p, q > root$, move right; the split point is the LCA in $\mathcal{O}(H)$ time without full tree traversal!

- **Q3: How to solve LCA for $K$ queries in O(1) time per query?**  
  **A**: Use **Binary Lifting** (Sparse Table over tree depths) in $\mathcal{O}(N \log N)$ preprocessing and $\mathcal{O}(\log N)$ query, or **Tarjan's Offline LCA** with DSU in $\mathcal{O}(N + Q \alpha(N))$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `LeetCode-236`, `Medium`
- **Related problems to practice next**:
- **LCA in BST**: BST specialization.
- **Print Root to Node Path**: Path approach.
