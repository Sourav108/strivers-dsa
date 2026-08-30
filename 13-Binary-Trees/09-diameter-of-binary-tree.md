# Diameter of Binary Tree (Longest path between two nodes) (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Diameter of Binary Tree (Longest path between two nodes)](https://takeuforward.org/data-structure/calculate-the-diameter-of-a-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, return the length of the diameter of the tree. The diameter of a binary tree is the length of the longest path between any two nodes in a tree. This path may or may not pass through the root.

---

## 1. Problem, Restated

Find $\max(lh + rh)$ across all nodes in the tree during a single bottom-up height computation.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vectors or metric calculation (depth, diameter, path sum).
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

The longest path passing through any node $u$ as its highest turning point has length $= \text{leftHeight}(u) + \text{rightHeight}(u)$ (in terms of edges). We recursively compute height: at each node, update `maxDiameter = max(maxDiameter, lh + rh)` and return $1 + \max(lh, rh)$ to the parent!

- **Underlying Pattern**: `Bottom-Up Diameter Accumulation via Global Invariant (`maxDia = max(maxDia, lh + rh)`)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
For every node, call `getHeight(left)` and `getHeight(right)` in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) top down diameter search
```

### Java Code
```java
// Java equivalent
// O(N^2) top down diameter search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Recomputes subtree heights quadratic times.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single Bottom-Up DFS Pass in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

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
private:
    int calculateHeight(TreeNode* root, int& maxDiameter) {
        if (root == nullptr) return 0;
        
        int leftHeight = calculateHeight(root->left, maxDiameter);
        int rightHeight = calculateHeight(root->right, maxDiameter);
        
        // Longest path through current node
        maxDiameter = max(maxDiameter, leftHeight + rightHeight);
        
        // Return height to parent
        return 1 + max(leftHeight, rightHeight);
    }

public:
    int diameterOfBinaryTree(TreeNode* root) {
        int maxDiameter = 0;
        calculateHeight(root, maxDiameter);
        return maxDiameter;
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

    int calculateHeight(TreeNode  root, int maxDiameter) {
        if (root == null) return 0;
        
        int leftHeight = calculateHeight(root.left, maxDiameter);
        int rightHeight = calculateHeight(root.right, maxDiameter);
        
        // Longest path through current node
        maxDiameter = Math.max(maxDiameter, leftHeight + rightHeight);
        
        // Return height to parent
        return 1 + Math.max(leftHeight, rightHeight);
    }

    int diameterOfBinaryTree(TreeNode  root) {
        int maxDiameter = 0;
        calculateHeight(root, maxDiameter);
        return maxDiameter;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single postorder DFS pass.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Embeds diameter candidate update directly into the bottom-up height aggregation.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4, right: 5), right: 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Leaves 4, 5, 3` | return height = 1, diameter updated to 0 | h = 1 |
| `Node 2` | lh=1, rh=1 -> diameter = max(0, 1+1) = 2, return h=2 | maxDia = 2 |
| `Root 1` | lh=2, rh=1 -> path through root = 2+1 = 3 -> maxDia = 3, return h=3 | maxDia = 3 |
| `Result` | Longest path is `4 -> 2 -> 1 -> 3` (length 3 edges) | Diameter = 3 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node tree `[1]` (diameter = 0 edges).
- Diameter does NOT pass through root (e.g. deep subtree on left).

### Common Bugs to Avoid
- Assuming the diameter path MUST pass through the root node (diameter can be entirely contained in a deep left or right subtree).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is diameter defined in terms of edges vs nodes?**  
  **A**: LeetCode 543 defines diameter as the number of EDGES along the path ($lh + rh$). Some GFG/textbook problems define it as number of NODES ($1 + lh + rh$). Always confirm edge vs node count in interviews!

- **Q2: Why does diameter not always pass through the root?**  
  **A**: Consider a tree where the left child has deep left and right subtrees of height 10 each, but the right child of root is null. The longest path ($10 + 10 = 20$) lies entirely within the left child's subtree!

- **Q3: Can this be solved iteratively?**  
  **A**: Yes, by performing an iterative postorder traversal and storing computed heights in an `unordered_map<TreeNode*, int>`.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `LeetCode-543`, `Easy`
- **Related problems to practice next**:
- **Maximum Depth of Binary Tree**: Height calculation.
- **Maximum Path Sum**: Value-weighted diameter.
