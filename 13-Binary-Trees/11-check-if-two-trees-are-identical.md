# Check if Two Trees are Identical / Same Tree (Step 13.2 — Medium Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Check if Two Trees are Identical / Same Tree](https://takeuforward.org/data-structure/check-if-two-trees-are-identical/)
- **Difficulty**: Easy
- **Statement**: Given the roots of two binary trees `p` and `q`, write a function to check if they are the same or not. Two binary trees are considered the same if they are structurally identical, and the nodes have the same value.

---

## 1. Problem, Restated

Simultaneous recursive DFS or iterative queue verification of tree structure and node values.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Base cases: 1) If both `p == nullptr && q == nullptr`, return `true`. 2) If only one is null, return `false`. 3) If values differ `p->val != q->val`, return `false`. 4) Recursively check both subtrees: `isSameTree(p->left, q->left) && isSameTree(p->right, q->right)`.

- **Underlying Pattern**: `Simultaneous Dual-Pointer Tree DFS / BFS`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Serialize both trees to strings and compare strings in $\mathcal{O}(N)$ time and memory.

### C++17 Code
```cpp
// Tree serialization comparison
```

### Java Code
```java
// Java equivalent
// Tree serialization comparison
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ string memory.
- **Why it's not good enough**: Unnecessary string construction.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / BFS algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(N \log N)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Simultaneous Dual-Tree DFS in $\mathcal{O}(\min(N, M))$ time and $\mathcal{O}(H)$ space.

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
    bool isSameTree(TreeNode* p, TreeNode* q) {
        // Both null -> structurally identical
        if (p == nullptr && q == nullptr) return true;
        
        // One null and one non-null -> structural mismatch
        if (p == nullptr || q == nullptr) return false;
        
        // Value mismatch
        if (p->val != q->val) return false;
        
        // Check both left and right subtrees
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
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

    boolean isSameTree(TreeNode  p, TreeNode  q) {
        // Both null . structurally identical
        if (p == null && q == null) return true;
        
        // One null and one non-null . structural mismatch
        if (p == null || q == null) return false;
        
        // Value mismatch
        if (p.val != q.val) return false;
        
        // Check both left and right subtrees
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(\min(N, M))$ time with early exit on first mismatch.
- **Space Complexity**: $\mathcal{O}(\min(H_1, H_2))$ call stack space.
- **Why this is optimal**: Direct pointer comparisons stop immediately on structural or value divergence.

---

## 6. Dry Run

Tree 1: `[1, 2, 3]`, Tree 2: `[1, 2, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `Root` | p->val(1) == q->val(1) -> valid | check left and right |
| `Left child` | p->left(2) == q->left(2) -> valid | leaves null -> true |
| `Right child` | p->right(3) == q->right(3) -> valid | leaves null -> true |
| `Result` | All comparisons true | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Both trees empty `null, null` (returns `true`).
- One empty, one non-empty (returns `false`).

### Common Bugs to Avoid
- Comparing `p->val == q->val` before checking `p == nullptr || q == nullptr` (null pointer dereference crash!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How to solve this iteratively without recursion?**  
  **A**: Use a single `std::queue<pair<TreeNode*, TreeNode*>>` pushing `{p, q}` pairs. Dequeue, perform the 3 null and value checks, and push `{p->left, q->left}` and `{p->right, q->right}`.

- **Q2: How does this relate to LeetCode 101 (Symmetric Tree)?**  
  **A**: Symmetric Tree checks if a tree is a mirror image of itself, which calls `isMirror(t1->left, t2->right) && isMirror(t1->right, t2->left)`.

- **Q3: Can two trees have the same preorder and inorder traversals and NOT be identical?**  
  **A**: No! If both preorder and inorder traversals are identical, the binary tree structure is mathematically guaranteed to be identical.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `LeetCode-100`, `Easy`
- **Related problems to practice next**:
- **Symmetric Tree**: Mirror reflection check.
- **Subtree of Another Tree**: Subtree matching.
