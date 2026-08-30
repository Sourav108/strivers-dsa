# Find Min and Max Element in BST (Step 14.1 — Concepts)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Find Min and Max Element in BST](https://takeuforward.org/data-structure/find-minimum-maximum-element-in-bst/)
- **Difficulty**: Easy
- **Statement**: Given the root of a Binary Search Tree, find the minimum and maximum values stored in the tree in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

---

## 1. Problem, Restated

Minimum is the leftmost node (`while (curr->left) curr = curr->left`); Maximum is the rightmost node (`while (curr->right) curr = curr->right`).

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

By BST property: 1) Every left child is smaller than its parent. Thus the **minimum element** is the deepest leftmost node in the tree! 2) Every right child is larger than its parent. Thus the **maximum element** is the deepest rightmost node in the tree!

- **Underlying Pattern**: `Leftmost / Rightmost Spine Traversal`.

---

## 3. Approach 1 — Naive / Full Traversal

### Idea
Inorder traversal into a vector in $\mathcal{O}(N)$ time and taking `v.front()` and `v.back()`.

### C++17 Code
```cpp
// O(N) inorder dump
```

### Java Code
```java
// Java equivalent
// O(N) inorder dump
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's not good enough**: Traverses entire tree unnecessarily.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative pointer navigation below directly achieves optimal $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ auxiliary space.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Leftmost & Rightmost Pointer Iteration in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

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
    int minValue(TreeNode* root) {
        if (root == nullptr) return -1;
        
        TreeNode* curr = root;
        // Dive all the way to the leftmost leaf
        while (curr->left != nullptr) {
            curr = curr->left;
        }
        return curr->val;
    }
    
    int maxValue(TreeNode* root) {
        if (root == nullptr) return -1;
        
        TreeNode* curr = root;
        // Dive all the way to the rightmost leaf
        while (curr->right != nullptr) {
            curr = curr->right;
        }
        return curr->val;
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

    int minValue(TreeNode  root) {
        if (root == null) return -1;
        
        TreeNode  curr = root;
        // Dive all the way to the leftmost leaf
        while (curr.left != null) {
            curr = curr.left;
        }
        return curr.val;
    }
    
    int maxValue(TreeNode  root) {
        if (root == null) return -1;
        
        TreeNode  curr = root;
        // Dive all the way to the rightmost leaf
        while (curr.right != null) {
            curr = curr.right;
        }
        return curr.val;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ time ($H = \text{height}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Directly follows the extreme left/right spine without visiting other subtrees.

---

## 6. Dry Run

BST: `5 -> left: 4 (left: 3 (left: 1)), right: 6 (right: 7)`

| Step | Action / State Change | Result |
|---|---|---|
| `Min Search` | 5 -> 4 -> 3 -> 1 (left is null) -> returns 1 | Min = 1 ✅ |
| `Max Search` | 5 -> 6 -> 7 (right is null) -> returns 7 | Max = 7 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node tree (root is both min and max).
- Leftmost node has a right child (leftmost node is STILL minimum!).

### Common Bugs to Avoid
- Condition `while (curr != nullptr)` instead of `while (curr->left != nullptr)` (leads to accessing `curr->val` after `curr` becomes null).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Can the minimum node in a BST have a right child?**  
  **A**: YES! The minimum node has NO left child, but it CAN have a right child (which will be larger than the minimum node but smaller than the minimum node's ancestors).

- **Q2: How does finding Min/Max help in deleting a node with 2 children in a BST?**  
  **A**: When deleting a node with two children, we replace its value with either its **Inorder Successor** (minimum element in its right subtree) or its **Inorder Predecessor** (maximum element in its left subtree)!

- **Q3: What is the time complexity in a balanced AVL tree?**  
  **A**: Strictly $\mathcal{O}(\log N)$ operations.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `Easy`
- **Related problems to practice next**:
- **Delete Node in BST**: Inorder successor replacement.
- **Inorder Successor in BST**: Successor lookup.
