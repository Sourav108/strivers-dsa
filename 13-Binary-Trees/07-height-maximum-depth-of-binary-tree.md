# Height / Maximum Depth of a Binary Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Height / Maximum Depth of a Binary Tree](https://takeuforward.org/data-structure/maximum-depth-of-a-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, return its maximum depth. A binary tree's maximum depth is the number of nodes along the longest path from the root node down to the farthest leaf node.

---

## 1. Problem, Restated

Compute tree height: $\text{height}(node) = 1 + \max(\text{height}(node\to left), \text{height}(node\to right))$.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vectors or metric calculation (depth, diameter, path sum).
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

If `root == nullptr`, depth is 0. Otherwise, recursively compute the maximum depth of the left subtree `lh` and right subtree `rh`. The depth of current subtree is $1 + \max(lh, rh)$. Alternatively, BFS level-order traversal counts the total number of level iterations.

- **Underlying Pattern**: `Bottom-Up Postorder Divide & Conquer ($1 + \max(L, R)$)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
BFS level order counting depth level by level.

### C++17 Code
```cpp
// BFS level count
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(W)$ queue.
- **Why it's not good enough**: Queue space.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Divide & Conquer in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <algorithm>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }
        
        int leftHeight = maxDepth(root->left);
        int rightHeight = maxDepth(root->right);
        
        return 1 + max(leftHeight, rightHeight);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass visiting each node once.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space ($H = \log N$ balanced, $H = N$ skewed).
- **Why this is optimal**: Optimal bottom-up aggregation.

---

## 6. Dry Run

Tree: `3 -> left: 9, right: 20 (left: 15, right: 7)`

| Step | Action / State Change | Result |
|---|---|---|
| `Leaf 9` | lh=0, rh=0 -> return 1+max(0,0)=1 | height=1 |
| `Leaf 15, 7` | return 1 each | height=1 |
| `Node 20` | lh=1, rh=1 -> return 1+max(1,1)=2 | height=2 |
| `Root 3` | lh=1, rh=2 -> return 1+max(1,2)=3 | Max Depth = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns 0).
- Single node `[1]` (returns 1).
- Skewed linear chain of $N$ nodes (returns $N$).

### Common Bugs to Avoid
- Returning `max(lh, rh)` without adding $+1$ for current node.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the difference between Depth of a Node and Height of a Tree?**  
  **A**: **Depth of a Node** is the number of edges from the root to that node (root depth = 0). **Height of a Tree** is the number of nodes (or edges, depending on convention) on the longest path from root to leaf.

- **Q2: How to prevent stack overflow on a degenerate tree with N = 10^5 nodes?**  
  **A**: Use BFS queue level-order traversal, which bounds memory to the maximum level width rather than tree depth.

- **Q3: Can height be computed iteratively without a queue?**  
  **A**: Yes, using iterative Postorder traversal with a stack storing pairs of `{TreeNode*, current_depth}`.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `LeetCode-104`, `Easy`
- **Related problems to practice next**:
- **Balanced Binary Tree**: Height balance check.
- **Diameter of Binary Tree**: Longest path.
