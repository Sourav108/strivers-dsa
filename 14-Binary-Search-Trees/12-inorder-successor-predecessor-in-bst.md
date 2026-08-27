# Inorder Successor and Predecessor in BST (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Inorder Successor and Predecessor in BST](https://takeuforward.org/data-structure/inorder-successor-predecessor-in-bst/)
- **Difficulty**: Medium
- **Statement**: Given a BST and a target node `p`, find the Inorder Successor (smallest node with value $> p\to val$) and Inorder Predecessor (largest node with value $< p\to val$) of `p` in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

---

## 1. Problem, Restated

Successor: if `curr > p` record candidate and dive left; else dive right. Predecessor: if `curr < p` record candidate and dive right; else dive left.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean / class methods.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Inorder Successor**: Start `successor = nullptr`. While `curr != nullptr`: if `p->val < curr->val`, `curr` is a valid candidate $\implies$ `successor = curr`, `curr = curr->left` (look for smaller valid successor); else `curr = curr->right`. 2) **Inorder Predecessor**: Start `predecessor = nullptr`. While `curr != nullptr`: if `p->val > curr->val`, `curr` is a valid candidate $\implies$ `predecessor = curr`, `curr = curr->right` (look for larger valid predecessor); else `curr = curr->left`.

- **Underlying Pattern**: `BST Directional Boundary Pruning (Successor $\to$ Ceil; Predecessor $\to$ Floor)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Inorder traversal into vector, finding element before and after `p` in $\mathcal{O}(N)$ memory.

### C++17 Code
```cpp
// O(N) inorder dump
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector.
- **Why it's not good enough**: Stores entire tree.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / Iterator / Morris algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(H)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative BST Candidate Tracking in $\mathcal{O}(H)$ time and strict $\mathcal{O}(1)$ space.

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
    TreeNode* inorderSuccessor(TreeNode* root, TreeNode* p) {
        TreeNode* successor = nullptr;
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            if (p->val < curr->val) {
                successor = curr;   // candidate successor (> p->val)
                curr = curr->left;  // try to find a smaller valid candidate
            } else {
                curr = curr->right;
            }
        }
        
        return successor;
    }
    
    TreeNode* inorderPredecessor(TreeNode* root, TreeNode* p) {
        TreeNode* predecessor = nullptr;
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            if (p->val > curr->val) {
                predecessor = curr; // candidate predecessor (< p->val)
                curr = curr->right; // try to find a larger valid candidate
            } else {
                curr = curr->left;
            }
        }
        
        return predecessor;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ time ($H = \text{height}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Follows a single root-to-leaf path pruning subtrees without parent pointers or memory allocation.

---

## 6. Dry Run

BST: `5 -> left: 2 (left: 1, right: 4 (left: 3)), right: 6`, Target $p = 2$

| Step | Action / State Change | Result |
|---|---|---|
| `Root 5` | $2 < 5 \implies$ successor = 5, move left to 2 | succ = 5 |
| `Node 2` | $2 \ge 2 \implies$ move right to 4 | succ = 5 |
| `Node 4` | $2 < 4 \implies$ successor = 4, move left to 3 | succ = 4 |
| `Node 3` | $2 < 3 \implies$ successor = 3, move left to null | succ = 3 |
| `Result` | Inorder Successor of 2 is 3 (`1 -> 2 -> 3 -> 4 -> 5 -> 6`) | Successor = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target is maximum element in BST (successor is `nullptr`).
- Target is minimum element (predecessor is `nullptr`).

### Common Bugs to Avoid
- Moving right on `p->val < curr->val` (right subtree contains even larger elements!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What if the target node p has a right child?**  
  **A**: If $p$ has a right child, its Inorder Successor is simply the **minimum element in its right subtree** (`curr = p->right; while (curr->left) curr = curr->left;`). The algorithm handles this case uniformly!

- **Q2: What if target node has a parent pointer?**  
  **A**: With parent pointers: if right child exists, find min in right subtree; else walk up parent pointers until the current node is the left child of its parent.

- **Q3: How does this differ from Ceil in BST?**  
  **A**: Ceil allows equality ($\ge X$). Successor requires STRICT inequality ($> X$).


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `LeetCode-285`, `Medium`
- **Related problems to practice next**:
- **Ceil in BST**: Ceil search.
- **Delete Node in BST**: Successor replacement.
