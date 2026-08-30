# Right and Left View of Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Right and Left View of Binary Tree](https://takeuforward.org/data-structure/right-left-view-of-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, imagine yourself standing on the right side of it, return the values of the nodes you can see ordered from top to bottom (Right View). Also implement Left View.

---

## 1. Problem, Restated

First node visited at each depth level: `Root -> Right -> Left` DFS for Right View; `Root -> Left -> Right` DFS for Left View.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / `TreeNode*` pointer.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Right View (Root -> Right -> Left DFS)**: Pass current `level`. If `level == result.size()`, this is the VERY FIRST time we have reached this depth level $\implies$ `result.push_back(node->val)`. Visit `node->right` first, then `node->left`. 2) **Left View (Root -> Left -> Right DFS)**: Visit `node->left` first, then `node->right`. Runs in strict $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space!

- **Underlying Pattern**: `Reverse Preorder Depth-Indexed First-Encounter DFS (`if (level == result.size())`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
BFS level-order queue taking the last (or first) element of each level vector in $\mathcal{O}(N)$ time and $\mathcal{O}(W)$ space.

### C++17 Code
```cpp
// BFS level order approach
```

### Java Code
```java
// Java equivalent
// BFS level order approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(W)$ queue.
- **Why it's not good enough**: Queue allocation.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Reverse Preorder DFS in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    void getRightViewDFS(TreeNode* node, int level, vector<int>& rightView) {
        if (node == nullptr) return;
        
        // First node visited at this level from the right
        if (level == (int)rightView.size()) {
            rightView.push_back(node->val);
        }
        
        getRightViewDFS(node->right, level + 1, rightView); // visit Right first
        getRightViewDFS(node->left, level + 1, rightView);  // then Left
    }

    void getLeftViewDFS(TreeNode* node, int level, vector<int>& leftView) {
        if (node == nullptr) return;
        
        // First node visited at this level from the left
        if (level == (int)leftView.size()) {
            leftView.push_back(node->val);
        }
        
        getLeftViewDFS(node->left, level + 1, leftView);   // visit Left first
        getLeftViewDFS(node->right, level + 1, leftView);  // then Right
    }

public:
    vector<int> rightSideView(TreeNode* root) {
        vector<int> rightView;
        getRightViewDFS(root, 0, rightView);
        return rightView;
    }
    
    vector<int> leftSideView(TreeNode* root) {
        vector<int> leftView;
        getLeftViewDFS(root, 0, leftView);
        return leftView;
    }
};
```

### Java Code
```java
import java.util.*;

static class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    public TreeNode(int x) { /* initialized: val(x), left(null), right(null)  */  }
};

class Solution {

    void getRightViewDFS(TreeNode  node, int level, int[] rightView) {
        if (node == null) return;
        
        // First node visited at this level from the right
        if (level == rightView.length) {
            rightView.add(node.val);
        }
        
        getRightViewDFS(node.right, level + 1, rightView); // visit Right first
        getRightViewDFS(node.left, level + 1, rightView);  // then Left
    }

    void getLeftViewDFS(TreeNode  node, int level, int[] leftView) {
        if (node == null) return;
        
        // First node visited at this level from the left
        if (level == leftView.length) {
            leftView.add(node.val);
        }
        
        getLeftViewDFS(node.left, level + 1, leftView);   // visit Left first
        getLeftViewDFS(node.right, level + 1, leftView);  // then Right
    }

    int[] rightSideView(TreeNode  root) {
        List<Integer> rightView = new ArrayList<>();
        getRightViewDFS(root, 0, rightView);
        return rightView;
    }
    
    int[] leftSideView(TreeNode  root) {
        List<Integer> leftView = new ArrayList<>();
        getLeftViewDFS(root, 0, leftView);
        return leftView;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Recursive DFS with right-first branching encounters the rightmost visible node at each depth on the very first visit.

---

## 6. Dry Run

Tree: `1 -> left: 2 (right: 5), right: 3 (right: 4)`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 1 (level 0)` | level 0 == size 0 -> push 1 | rightView: `[1]` |
| `Right child 3 (level 1)` | level 1 == size 1 -> push 3 | rightView: `[1, 3]` |
| `Right child 4 (level 2)` | level 2 == size 2 -> push 4 | rightView: `[1, 3, 4]` |
| `Left branch 2->5` | level 1, 2 already visited -> ignore | rightView: `[1, 3, 4]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Tree where left subtree is deeper than right subtree (left nodes become visible at lower depths!).

### Common Bugs to Avoid
- Assuming Right View only traverses `root->right` (if left subtree is deeper than right, lower left nodes ARE visible in right view!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is recursive DFS preferred over BFS for Right View?**  
  **A**: Because DFS uses only $\mathcal{O}(H)$ call stack space ($\\mathcal{O}(\log N)$ for balanced trees), whereas BFS queue memory is proportional to the maximum level width $\mathcal{O}(W) = \mathcal{O}(N/2)$!

- **Q2: What happens when the left subtree is deeper than the right subtree in Right View?**  
  **A**: The DFS naturally steps into the left subtree after exhausting the right subtree, correctly capturing the deep left-side nodes that stick out!

- **Q3: Can this approach be used to count total levels of a tree?**  
  **A**: Yes! `rightView.size()` at the end is exactly equal to the tree's maximum depth/height.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `LeetCode-199`, `Easy`
- **Related problems to practice next**:
- **Top View of Binary Tree**: Top-down view.
- **Boundary Traversal**: Perimeter traversal.
