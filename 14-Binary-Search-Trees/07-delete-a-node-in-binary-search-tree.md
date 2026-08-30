# Delete a Node in Binary Search Tree (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Delete a Node in Binary Search Tree](https://takeuforward.org/data-structure/delete-a-node-in-binary-search-tree-bst/)
- **Difficulty**: Medium
- **Statement**: Given a root node reference of a BST and a `key`, delete the node with the given key in the BST. Return the root node reference of the BST.

---

## 1. Problem, Restated

Locate target node and rewire: 1) Leaf: delete directly; 2) 1 Child: replace node with its child; 3) 2 Children: attach right subtree to rightmost node of left subtree (or replace with Inorder Successor).

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

When deleting node $U$ with 2 children: 1) Extract left child $L = U\to left$ and right child $R = U\to right$. 2) Find the rightmost node of left subtree $L$ (`lastRight = L; while (lastRight->right) lastRight = lastRight->right;`). 3) Attach $R$ to `lastRight->right = R`. 4) Return $L$ to replace $U$! This cleanly preserves all BST invariants in $\mathcal{O}(H)$ time.

- **Underlying Pattern**: `BST Inorder Successor / Right-to-Left-Spine Subtree Splicing`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Inorder traversal into vector, remove element, rebuild balanced BST in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// O(N) full rebuild
```

### Java Code
```java
// Java equivalent
// O(N) full rebuild
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's not good enough**: Rebuilds entire tree.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative pointer navigation below directly achieves optimal $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative Subtree Splicing in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

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
    TreeNode* helper(TreeNode* root) {
        if (root->left == nullptr) return root->right;
        if (root->right == nullptr) return root->left;
        
        TreeNode* rightChild = root->right;
        TreeNode* lastRight = root->left;
        while (lastRight->right != nullptr) {
            lastRight = lastRight->right;
        }
        
        // Attach right subtree to the rightmost node of left subtree
        lastRight->right = rightChild;
        return root->left;
    }

public:
    TreeNode* deleteNode(TreeNode* root, int key) {
        if (root == nullptr) return nullptr;
        
        if (root->val == key) {
            return helper(root);
        }
        
        TreeNode* curr = root;
        while (curr != nullptr) {
            if (key < curr->val) {
                if (curr->left != nullptr && curr->left->val == key) {
                    curr->left = helper(curr->left);
                    break;
                } else {
                    curr = curr->left;
                }
            } else {
                if (curr->right != nullptr && curr->right->val == key) {
                    curr->right = helper(curr->right);
                    break;
                } else {
                    curr = curr->right;
                }
            }
        }
        
        return root;
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

    TreeNode  helper(TreeNode  root) {
        if (root.left == null) return root.right;
        if (root.right == null) return root.left;
        
        TreeNode  rightChild = root.right;
        TreeNode  lastRight = root.left;
        while (lastRight.right != null) {
            lastRight = lastRight.right;
        }
        
        // Attach right subtree to the rightmost node of left subtree
        lastRight.right = rightChild;
        return root.left;
    }

    TreeNode  deleteNode(TreeNode  root, int key) {
        if (root == null) return null;
        
        if (root.val == key) {
            return helper(root);
        }
        
        TreeNode  curr = root;
        while (curr != null) {
            if (key < curr.val) {
                if (curr.left != null && curr.left.val == key) {
                    curr.left = helper(curr.left);
                    break;
                } else {
                    curr = curr.left;
                }
            } else {
                if (curr.right != null && curr.right.val == key) {
                    curr.right = helper(curr.right);
                    break;
                } else {
                    curr = curr.right;
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
- **Why this is optimal**: Direct pointer splicing rewires subtrees without node reallocation or value copying.

---

## 6. Dry Run

BST: `5 -> left: 3 (left: 2, right: 4), right: 6 (right: 7)`, Delete `key = 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Locate key 3` | curr = 5, $3 < 5 \implies$ curr->left is 3! Calls `helper(3)` | node 3 located |
| `helper(3)` | left=2, right=4 -> lastRight of left is 2 -> `2->right = 4` -> returns 2 | subtrees merged |
| `Rewire` | `5->left = 2` -> new structure: `5 -> left: 2 (right: 4)` | Node 3 deleted ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target is root node.
- Target node has 0 children (leaf).
- Target node not present in BST.

### Common Bugs to Avoid
- Dangling pointer when deleting root without returning helper result.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the alternative standard deletion method using Inorder Successor value copying?**  
  **A**: Find the Inorder Successor $S$ (minimum node in right subtree), copy $S\to val$ into $U\to val$, and recursively delete $S$ from the right subtree. This requires modifying node values rather than rewiring pointers.

- **Q2: Why is pointer splicing preferred in systems programming over value copying?**  
  **A**: Because in real-world C++ systems, tree nodes may contain large, non-copyable, or mutex-guarded objects. Splicing pointers avoids expensive value copies!

- **Q3: Can deleting nodes cause tree imbalance?**  
  **A**: Yes. Repeated deletions in an unbalanced BST can increase tree height, which is why AVL and Red-Black trees perform rebalancing rotations after deletions.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `LeetCode-450`, `Medium`
- **Related problems to practice next**:
- **Insert Node in BST**: Insertion.
- **Find Min/Max in BST**: Inorder successor lookup.
