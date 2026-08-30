# Count Total Nodes in a COMPLETE Binary Tree in O((log N)^2) (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Count Total Nodes in a COMPLETE Binary Tree in O((log N)^2)](https://takeuforward.org/data-structure/count-number-of-nodes-in-a-binary-tree/)
- **Difficulty**: Medium
- **Statement**: Given the root of a complete binary tree, return the number of the nodes in the tree in strictly less than $\mathcal{O}(N)$ time (specifically $\mathcal{O}((\log N)^2)$).

---

## 1. Problem, Restated

Exploit Complete Binary Tree property: if left height equals right height, subtree is a Perfect Binary Tree with $2^h - 1$ nodes.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Integer width / count / list of nodes / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Find leftmost height `lh` (keep going `curr = curr->left`). 2) Find rightmost height `rh` (keep going `curr = curr->right`). 3) **Key Invariant**: If `lh == rh`, the subtree is a **Perfect Binary Tree** $\implies$ node count is exactly $2^{lh} - 1 = (1 \ll lh) - 1$ in $\mathcal{O}(\log N)$ time! 4) If `lh != rh`, recursively return $1 + \text{countNodes}(root\to left) + \text{countNodes}(root\to right)$. At each level, at least one of the two subtrees is guaranteed to be perfect!

- **Underlying Pattern**: `Divide & Conquer Left/Right Height Comparison ($2^h - 1$ Formula Shortcut)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard recursive traversal visiting all $N$ nodes in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
int countNodesBrute(TreeNode* root) {
    if (!root) return 0;
    return 1 + countNodesBrute(root->left) + countNodesBrute(root->right);
}
```

### Java Code
```java
class Solution {
    int countNodesBrute(TreeNode  root) {
        if (root == null) return 0;
        return 1 + countNodesBrute(root.left) + countNodesBrute(root.right);
    }
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ stack.
- **Why it's not good enough**: Fails the $\mathcal{O}((\log N)^2)$ requirement.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard BFS / DFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}((\log N)^2)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Divide & Conquer Height Formula in $\mathcal{O}((\log N)^2)$ time.

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
    int getLeftHeight(TreeNode* node) {
        int height = 0;
        while (node != nullptr) {
            height++;
            node = node->left;
        }
        return height;
    }
    
    int getRightHeight(TreeNode* node) {
        int height = 0;
        while (node != nullptr) {
            height++;
            node = node->right;
        }
        return height;
    }

public:
    int countNodes(TreeNode* root) {
        if (root == nullptr) return 0;
        
        int lh = getLeftHeight(root);
        int rh = getRightHeight(root);
        
        // If leftmost height equals rightmost height, subtree is Perfect!
        if (lh == rh) {
            return (1 << lh) - 1; // Formula 2^h - 1
        }
        
        // Otherwise at least one child subtree is guaranteed to be perfect
        return 1 + countNodes(root->left) + countNodes(root->right);
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

    int getLeftHeight(TreeNode  node) {
        int height = 0;
        while (node != null) {
            height++;
            node = node.left;
        }
        return height;
    }
    
    int getRightHeight(TreeNode  node) {
        int height = 0;
        while (node != null) {
            height++;
            node = node.right;
        }
        return height;
    }

    int countNodes(TreeNode  root) {
        if (root == null) return 0;
        
        int lh = getLeftHeight(root);
        int rh = getRightHeight(root);
        
        // If leftmost height equals rightmost height, subtree is Perfect!
        if (lh == rh) {
            return (1 << lh) - 1; // Formula 2^h - 1
        }
        
        // Otherwise at least one child subtree is guaranteed to be perfect
        return 1 + countNodes(root.left) + countNodes(root.right);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}((\log N)^2)$ time (tree depth is $\log N$; at each step, computing heights takes $\mathcal{O}(\log N)$ and only one branch recurses down).
- **Space Complexity**: $\mathcal{O}(\log N)$ call stack space.
- **Why this is optimal**: Halves the non-perfect subtree search space at every depth level.

---

## 6. Dry Run

Complete Tree: `1 -> left: 2 (left: 4, right: 5), right: 3 (left: 6)` ($N = 6$)

| Step | Action / State Change | Result |
|---|---|---|
| `Root 1` | lh = 3 (1->2->4), rh = 2 (1->3) -> lh != rh | recurse left and right |
| `Left child 2` | lh = 2 (2->4), rh = 2 (2->5) -> lh == rh! -> return $(1\ll 2)-1 = 3$ | count = 3 |
| `Right child 3` | lh = 2 (3->6), rh = 1 (3) -> lh != rh -> recurse | count = 1 + 1 + 0 = 2 |
| `Total` | $1 + 3 + 2 = 6$ | Total Nodes = 6 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns 0).
- Perfect Binary Tree where $lh == rh$ at root (calculates count in $\mathcal{O}(\log N)$ immediately!).

### Common Bugs to Avoid
- Computing height recursively instead of following left/right spine pointers iteratively (wastes $\mathcal{O}(N)$ time).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the time complexity O((log N)^2)?**  
  **A**: The tree height is $H = \log_2 N$. At each recursive step, `getLeftHeight` and `getRightHeight` take $\mathcal{O}(\log N)$ by traversing spine pointers. Since a Complete Binary Tree ensures at least one child is always Perfect, we only recurse down one non-perfect child branch $\implies \log N$ levels $\times \log N$ height computations $= \mathcal{O}((\log N)^2)$!

- **Q2: Can this be solved using Binary Search on the leaf level?**  
  **A**: Yes! Binary search the index range $[0, 2^{h-1} - 1]$ of the last level to find the rightmost existing leaf in $\mathcal{O}((\log N)^2)$ time.

- **Q3: What is (1 << lh) - 1?**  
  **A**: Bitwise left shift `1 << lh` computes $2^{lh}$ in a single CPU instruction cycle, subtracting 1 gives the exact geometric series sum $1 + 2 + 4 + \dots + 2^{lh-1} = 2^{lh} - 1$.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Divide and Conquer`, `Binary Search`, `LeetCode-222`, `Medium`
- **Related problems to practice next**:
- **Maximum Width of Binary Tree**: CBT properties.
- **Binary Tree Representation**: Tree structure.
