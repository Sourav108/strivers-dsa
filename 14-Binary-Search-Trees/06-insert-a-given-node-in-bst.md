# Insert a Given Node in BST (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Insert a Given Node in BST](https://takeuforward.org/data-structure/insert-a-given-node-in-binary-search-tree-bst/)
- **Difficulty**: Medium
- **Statement**: You are given the root node of a binary search tree (BST) and a value `val` to insert into the tree. Return the root node of the BST after the insertion. It is guaranteed that the new value does not exist in the original BST.

---

## 1. Problem, Restated

Navigate down to the appropriate null leaf position and attach `new TreeNode(val)` in $\mathcal{O}(H)$ time.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) If `root == nullptr`, return `new TreeNode(val)`. 2) Start `curr = root`. 3) While `true`: if `val < curr->val`: if `curr->left != nullptr`, `curr = curr->left`; else attach `curr->left = new TreeNode(val)`, break. If `val > curr->val`: if `curr->right != nullptr`, `curr = curr->right`; else attach `curr->right = new TreeNode(val)`, break. Return `root`.

- **Underlying Pattern**: `Iterative BST Leaf Attachment Pointer Traversal`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard recursive insertion creating call frames in $\mathcal{O}(H)$ stack space.

### C++17 Code
```cpp
TreeNode* insertIntoBSTRec(TreeNode* root, int val) {
    if (!root) return new TreeNode(val);
    if (val < root->val) root->left = insertIntoBSTRec(root->left, val);
    else root->right = insertIntoBSTRec(root->right, val);
    return root;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ stack.
- **Why it's not good enough**: Uses recursion stack memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative pointer navigation below directly achieves optimal $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative While Loop Pointer Attachment in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

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
    TreeNode* insertIntoBST(TreeNode* root, int val) {
        if (root == nullptr) {
            return new TreeNode(val);
        }
        
        TreeNode* curr = root;
        while (true) {
            if (val < curr->val) {
                if (curr->left != nullptr) {
                    curr = curr->left;
                } else {
                    curr->left = new TreeNode(val);
                    break;
                }
            } else {
                if (curr->right != nullptr) {
                    curr = curr->right;
                } else {
                    curr->right = new TreeNode(val);
                    break;
                }
            }
        }
        
        return root;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ time ($H = \text{height}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Iterative navigation directly locates and links the new leaf node with zero extra memory overhead.

---

## 6. Dry Run

BST: `4 -> left: 2 (left: 1, right: 3), right: 7`, Insert `val = 5`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = 4` | $5 > 4 \implies$ curr->right is 7 != null -> curr = 7 | step right |
| `curr = 7` | $5 < 7 \implies$ curr->left is null -> attach `curr->left = new TreeNode(5)` | attached leaf 5 |
| `Result` | Tree root returned with 5 attached under 7 | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Empty tree `root == nullptr` (returns `new TreeNode(val)`).
- Inserting value that becomes new minimum or new maximum.

### Common Bugs to Avoid
- Overwriting non-null child pointer without checking `curr->left != nullptr`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Is the position of a newly inserted node in a standard BST always a leaf?**  
  **A**: YES! In standard BST insertion without rebalancing, the newly inserted node ALWAYS becomes a leaf node at the bottom of the tree.

- **Q2: What happens to BST height if elements are inserted in sorted order?**  
  **A**: If elements are inserted in ascending sorted order ($1, 2, 3, \dots, N$), the BST degenerates into a right-skewed linked list of height $N$, degrading future searches to $\mathcal{O}(N)$.

- **Q3: How does an AVL Tree prevent degeneration on insertions?**  
  **A**: An AVL tree performs $O(1)$ tree rotations (LL, RR, LR, RL) whenever an insertion causes a node's balance factor $|lh - rh| > 1$, strictly maintaining height $H \le 1.44 \log_2 N$.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `LeetCode-701`, `Medium`
- **Related problems to practice next**:
- **Search in BST**: Locate position.
- **Delete Node in BST**: Deletion operation.
