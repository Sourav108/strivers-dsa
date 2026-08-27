# Lowest Common Ancestor (LCA) in BST (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Lowest Common Ancestor (LCA) in BST](https://takeuforward.org/data-structure/lca-in-binary-search-tree-bst/)
- **Difficulty**: Easy
- **Statement**: Given a binary search tree (BST), find the lowest common ancestor (LCA) node of two given nodes `p` and `q` in the BST in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

---

## 1. Problem, Restated

Exploit BST ordering: if both `p, q < curr` move left; if both `p, q > curr` move right; the split point is the LCA.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

In a BST, we do NOT need full tree traversal! Start `curr = root`. 1) If both `p->val < curr->val` and `q->val < curr->val`, both targets are in the left subtree $\implies$ move `curr = curr->left`. 2) If both `p->val > curr->val` and `q->val > curr->val`, both targets are in the right subtree $\implies$ move `curr = curr->right`. 3) **Split Point**: If one target is on left and one is on right (or `curr == p` or `curr == q`), `curr` is the Lowest Common Ancestor! Return `curr`.

- **Underlying Pattern**: `BST Split-Point Binary Search Pruning`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
General binary tree LCA algorithm traversing subtrees in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// O(N) general LCA
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Fails to exploit BST ordering.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative pointer navigation below directly achieves optimal $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative Split-Point Pointer Navigation in $\mathcal{O}(H)$ time and strict $\mathcal{O}(1)$ space.

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
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            // Both nodes lie in the left subtree
            if (p->val < curr->val && q->val < curr->val) {
                curr = curr->left;
            }
            // Both nodes lie in the right subtree
            else if (p->val > curr->val && q->val > curr->val) {
                curr = curr->right;
            }
            // Split point reached: one on left, one on right, or curr is p/q
            else {
                return curr;
            }
        }
        
        return nullptr;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ time ($H = \text{height}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Pruning one entire subtree at each step finds the LCA along the single search path from root to split point.

---

## 6. Dry Run

BST: `6 -> left: 2 (left: 0, right: 4 (left: 3, right: 5)), right: 8 (left: 7, right: 9)`, $p = 2, q = 8$

| Step | Action / State Change | Result |
|---|---|---|
| `curr = 6` | $p(2) < 6$ and $q(8) > 6 \implies$ Split point! | LCA = 6 ✅ |
| `Case 2: $p=2, q=4$` | At root 6: both $< 6 \implies$ move left to 2. At 2: $p=2 \implies$ Split point! LCA = 2 | LCA = 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $p$ is direct ancestor of $q$ (returns $p$).
- $p$ and $q$ in opposite subtrees.

### Common Bugs to Avoid
- Continuing traversal after reaching the split point.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the split point guaranteed to be the Lowest Common Ancestor?**  
  **A**: Because if $p < curr < q$, $p$ must be in $curr$'s left subtree and $q$ in $curr$'s right subtree. Any ancestor above $curr$ would have both $p$ and $q$ in the same subtree. Thus $curr$ is the lowest node where their paths diverge!

- **Q2: How does BST LCA compare with general Binary Tree LCA in complexity?**  
  **A**: General BT LCA requires $\mathcal{O}(N)$ time because it must inspect all subtrees. BST LCA requires only $\mathcal{O}(H) = \mathcal{O}(\log N)$ time by following a single path.

- **Q3: Can this be implemented recursively in one line?**  
  **A**: `return (p->val < root->val && q->val < root->val) ? LCA(root->left, p, q) : ((p->val > root->val && q->val > root->val) ? LCA(root->right, p, q) : root);`


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `LeetCode-235`, `Easy`
- **Related problems to practice next**:
- **LCA in Binary Tree**: General BT LCA.
- **Search in BST**: BST navigation.
