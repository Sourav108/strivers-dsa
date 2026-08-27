# Search in a Binary Search Tree (Step 14.1 — Concepts)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Search in a Binary Search Tree](https://takeuforward.org/data-structure/search-in-a-binary-search-tree-bst/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary search tree (BST) and an integer `val`, find the node in the BST that the node's value equals `val` and return the subtree rooted with that node. If such a node does not exist, return `nullptr`.

---

## 1. Problem, Restated

Navigate BST iteratively: if `val < curr->val` move left, if `val > curr->val` move right, else return `curr`.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Start `curr = root`. While `curr != nullptr && curr->val != val`: 1) If `val < curr->val`, target must be in left subtree $\implies$ `curr = curr->left`. 2) If `val > curr->val`, target must be in right subtree $\implies$ `curr = curr->right`. Return `curr` in $\mathcal{O}(H)$ time and strict $\mathcal{O}(1)$ auxiliary space!

- **Underlying Pattern**: `Iterative BST Pointer Navigation (`curr = (val < curr->val) ? curr->left : curr->right`)`.

---

## 3. Approach 1 — Naive / Full Traversal

### Idea
Standard recursive DFS on general binary tree in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ stack space.

### C++17 Code
```cpp
// O(N) general DFS search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ stack.
- **Why it's not good enough**: Fails to prune branches.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative pointer navigation below directly achieves optimal $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ auxiliary space.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative While Loop Pointer Navigation in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

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
    TreeNode* searchBST(TreeNode* root, int val) {
        TreeNode* curr = root;
        
        while (curr != nullptr && curr->val != val) {
            if (val < curr->val) {
                curr = curr->left; // target is smaller -> explore left
            } else {
                curr = curr->right; // target is larger -> explore right
            }
        }
        
        return curr; // returns matching node or nullptr if not found
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ time ($H = \log N$ for balanced, $H = N$ for skewed).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Iterative loop avoids recursion call-stack overhead and eliminates half of the subtree at every branch.

---

## 6. Dry Run

BST: `4 -> left: 2 (left: 1, right: 3), right: 7`, Search `val = 2`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = 4` | $2 < 4 \implies$ curr = curr->left (node 2) | step left |
| `curr = 2` | $2 == 2 \implies$ Match found! Loop terminates | Return Node 2 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Target not present in BST (returns `nullptr`).
- Empty tree `root == nullptr`.
- Target is root itself.

### Common Bugs to Avoid
- Using `curr->val < val` instead of `val < curr->val` (inverting navigation direction).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is iterative search preferred over recursive search in C++?**  
  **A**: Iterative search runs in strict $\mathcal{O}(1)$ memory with zero function call overhead and no risk of stack overflow on deep skewed trees of depth $10^5$.

- **Q2: What is the average vs worst-case time complexity of BST search?**  
  **A**: Average time is $\mathcal{O}(\log N)$ (for randomly built balanced BSTs), while worst-case is $\mathcal{O}(N)$ (for degenerate line trees).

- **Q3: How does B-Tree search differ from Binary Search Tree search?**  
  **A**: A B-Tree node holds multiple keys (e.g. $M$ keys with $M+1$ children) optimized for block storage/disk cache lines, reducing tree height to $\mathcal{O}(\log_M N)$.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `LeetCode-700`, `Easy`
- **Related problems to practice next**:
- **Find Min/Max in BST**: Extreme element search.
- **Insert Node in BST**: Insertion.
