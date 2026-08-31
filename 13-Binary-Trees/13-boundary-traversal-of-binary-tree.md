# Boundary Traversal of Binary Tree (Anti-Clockwise) (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Boundary Traversal of Binary Tree (Anti-Clockwise)](https://takeuforward.org/data-structure/boundary-traversal-of-a-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given a Binary Tree, find its Boundary Traversal in anti-clockwise direction starting from the root: 1) Left Boundary (excluding leaves), 2) Leaf Nodes (left to right), 3) Right Boundary in reverse order (bottom to top, excluding root and leaves).

---

## 1. Problem, Restated

3-Stage Perimeter Traversal: Left Boundary $\to$ Bottom Leaves $\to$ Reverse Right Boundary.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Root added if not leaf. 2) **Left Boundary**: Start `curr = root->left`. While `curr` is not leaf: add `curr->val`; if `curr->left` exists, `curr = curr->left`, else `curr = curr->right`. 3) **Leaf Nodes**: Standard DFS preorder/inorder collecting leaves (`!left && !right`). 4) **Right Boundary**: Start `curr = root->right`. While `curr` is not leaf: push `curr->val` to temporary stack; if `curr->right` exists, `curr = curr->right`, else `curr = curr->left`. Pop stack to append in reverse order!

- **Underlying Pattern**: `3-Phase Geometric Boundary Aggregation (Left Path + Inorder Leaves + Stack Inverted Right Path)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Complex full-tree boundary graph construction in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// Graph perimeter search
```

### Java Code
```java
// Java equivalent
// Graph perimeter search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(N)$.
- **Why it's not good enough**: Unnecessary graph modeling.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(N \log N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Three-Phase Linear Boundary Aggregation in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    bool isLeaf(TreeNode* node) {
        return node->left == nullptr && node->right == nullptr;
    }
    
    void addLeftBoundary(TreeNode* root, vector<int>& res) {
        TreeNode* curr = root->left;
        while (curr != nullptr) {
            if (!isLeaf(curr)) res.push_back(curr->val);
            if (curr->left != nullptr) curr = curr->left;
            else curr = curr->right;
        }
    }
    
    void addLeaves(TreeNode* root, vector<int>& res) {
        if (root == nullptr) return;
        if (isLeaf(root)) {
            res.push_back(root->val);
            return;
        }
        addLeaves(root->left, res);
        addLeaves(root->right, res);
    }
    
    void addRightBoundary(TreeNode* root, vector<int>& res) {
        TreeNode* curr = root->right;
        vector<int> temp;
        while (curr != nullptr) {
            if (!isLeaf(curr)) temp.push_back(curr->val);
            if (curr->right != nullptr) curr = curr->right;
            else curr = curr->left;
        }
        // Append in reverse order (bottom-up)
        for (int i = (int)temp.size() - 1; i >= 0; i--) {
            res.push_back(temp[i]);
        }
    }

public:
    vector<int> boundary(TreeNode* root) {
        vector<int> res;
        if (root == nullptr) return res;
        
        // Add root if not leaf
        if (!isLeaf(root)) res.push_back(root->val);
        
        addLeftBoundary(root, res);
        addLeaves(root, res);
        addRightBoundary(root, res);
        
        return res;
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

    boolean isLeaf(TreeNode  node) {
        return node.left == null && node.right == null;
    }
    
    void addLeftBoundary(TreeNode  root, int[] res) {
        TreeNode  curr = root.left;
        while (curr != null) {
            if (!isLeaf(curr)) res.add(curr.val);
            if (curr.left != null) curr = curr.left;
            else curr = curr.right;
        }
    }
    
    void addLeaves(TreeNode  root, int[] res) {
        if (root == null) return;
        if (isLeaf(root)) {
            res.add(root.val);
            return;
        }
        addLeaves(root.left, res);
        addLeaves(root.right, res);
    }
    
    void addRightBoundary(TreeNode  root, int[] res) {
        TreeNode  curr = root.right;
        int[] temp;
        while (curr != null) {
            if (!isLeaf(curr)) temp.add(curr.val);
            if (curr.right != null) curr = curr.right;
            else curr = curr.left;
        }
        // Append in reverse order (bottom-up)
        for (int i = temp.length - 1; i >= 0; i--) {
            res.add(temp[i]);
        }
    }

    int[] boundary(TreeNode  root) {
        int[] res;
        if (root == null) return res;
        
        // Add root if not leaf
        if (!isLeaf(root)) res.add(root.val);
        
        addLeftBoundary(root, res);
        addLeaves(root, res);
        addRightBoundary(root, res);
        
        return res;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass over boundary and leaves.
- **Space Complexity**: $\mathcal{O}(H)$ call stack for leaf traversal + temporary right boundary vector.
- **Why this is optimal**: Three disjoint phases guarantee each node is processed at most once with no duplicate insertions.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4, right: 5), right: 3 (left: 6, right: 7)`

| Step | Action / State Change | Result |
|---|---|---|
| `Root` | add 1 (not leaf) | res: `[1]` |
| `Left Boundary` | node 2 (not leaf) -> add 2 | res: `[1, 2]` |
| `Leaves` | 4, 5, 6, 7 (leaves in L->R order) | res: `[1, 2, 4, 5, 6, 7]` |
| `Right Boundary` | node 3 (not leaf) -> temp `[3]` -> reverse add 3 | res: `[1, 2, 4, 5, 6, 7, 3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node tree `[1]` (only 1 is added, leaf check prevents duplicate additions).
- Tree missing left subtree or right subtree.

### Common Bugs to Avoid
- Adding leaf nodes twice (once in boundary traversal and once in leaf traversal). Guard with `!isLeaf(curr)`!

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is !isLeaf(curr) guard strictly required in addLeftBoundary and addRightBoundary?**  
  **A**: Because all leaf nodes must be added strictly in Left-to-Right order during the `addLeaves()` phase. Including leaves during boundary traversals would corrupt order and cause duplicate entries.

- **Q2: What happens if a node on left boundary has no left child?**  
  **A**: If `curr->left == nullptr`, the left boundary continues down `curr->right`!

- **Q3: Can this be modified for Clockwise boundary traversal?**  
  **A**: Yes! Add root $\to$ Right Boundary (top-down) $\to$ Leaves (Right-to-Left) $\to$ Left Boundary (bottom-up in reverse).


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Traversal`, `Medium`
- **Related problems to practice next**:
- **Level Order Traversal**: BFS traversal.
- **Right and Left View**: Tree projection views.
