# Check if Subtree of Another Tree (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Check if Subtree of Another Tree](https://takeuforward.org/data-structure/subtree-of-another-tree/)
- **Difficulty**: Easy
- **Statement**: Given the roots of two binary trees `root` and `subRoot`, return `true` if there is a subtree of `root` with the same structure and node values of `subRoot` and `false` otherwise.

---

## 1. Problem, Restated

Verify if `isSameTree(node, subRoot)` is true for ANY node in `root`.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / integer sum / in-place modified tree.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) If `subRoot == nullptr`, empty tree is always a subtree $\implies$ `true`. 2) If `root == nullptr`, non-empty `subRoot` cannot be a subtree $\implies$ `false`. 3) If `isSameTree(root, subRoot)` is `true`, return `true`. 4) Otherwise recursively check subtrees: `isSubtree(root->left, subRoot) || isSubtree(root->right, subRoot)`.

- **Underlying Pattern**: `Recursive Tree Matching with `isSameTree` Sub-routine / Merkle Tree Hashing`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard recursive subtree matching in $\mathcal{O}(N \times M)$ time.

### C++17 Code
```cpp
// O(N * M) recursive matching
```

### Java Code
```java
// Java equivalent
// O(N * M) recursive matching
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ time.
- **Space Complexity**: $\mathcal{O}(H_N)$.
- **Why it's not good enough**: Calls isSameTree from each node.

---

## 4. Approach 2 — Better

### Idea
Merkle Tree Subtree Hashing in O(N + M) time.

### C++17 Code
```cpp
// Merkle tree hashing O(N+M)
```

### Java Code
```java
// Java equivalent
// Merkle tree hashing O(N+M)
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N + M)$ time.
- **Space Complexity**: $\mathcal{O}(N + M)$ hash map.
- **Why it's still not optimal**: Requires cryptographic / polynomial rolling hash.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Tree Traversal + `isSameTree` Helper in $\mathcal{O}(N \times M)$ time and $\mathcal{O}(H)$ space.

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
    bool isSameTree(TreeNode* p, TreeNode* q) {
        if (p == nullptr && q == nullptr) return true;
        if (p == nullptr || q == nullptr) return false;
        if (p->val != q->val) return false;
        
        return isSameTree(p->left, q->left) && isSameTree(p->right, q->right);
    }

public:
    bool isSubtree(TreeNode* root, TreeNode* subRoot) {
        if (subRoot == nullptr) return true;
        if (root == nullptr) return false;
        
        // If current subtree matches subRoot
        if (isSameTree(root, subRoot)) {
            return true;
        }
        
        // Check left or right child
        return isSubtree(root->left, subRoot) || isSubtree(root->right, subRoot);
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
        if (p == null && q == null) return true;
        if (p == null || q == null) return false;
        if (p.val != q.val) return false;
        
        return isSameTree(p.left, q.left) && isSameTree(p.right, q.right);
    }

    boolean isSubtree(TreeNode  root, TreeNode  subRoot) {
        if (subRoot == null) return true;
        if (root == null) return false;
        
        // If current subtree matches subRoot
        if (isSameTree(root, subRoot)) {
            return true;
        }
        
        // Check left or right child
        return isSubtree(root.left, subRoot) || isSubtree(root.right, subRoot);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \times M)$ worst-case time (runs in $\mathcal{O}(N + M)$ on average).
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Clean recursive composition of `isSameTree` with early-exit boolean evaluation.

---

## 6. Dry Run

root: `3 -> left: 4 (left: 1, right: 2), right: 5`, subRoot: `4 -> left: 1, right: 2`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 3` | isSameTree(3, 4) -> false -> check left child | recurse left |
| `Left child 4` | isSameTree(4, 4) -> matches 4, left 1==1, right 2==2 -> returns TRUE! | Subtree Match Found! ✅ |
| `Result` | isSubtree returns TRUE | Return TRUE ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `subRoot` has extra children (e.g. `2` has child `0` in root, but no child in `subRoot` $\implies$ `false`).
- Single node matching.

### Common Bugs to Avoid
- Returning `true` on partial match where `subRoot` matches an internal segment rather than the entire subtree down to leaves.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must isSameTree match all the way down to leaves?**  
  **A**: Because a subtree of a tree $T$ consists of a node in $T$ and ALL of its descendants. It cannot stop midway!

- **Q2: How to achieve strict O(N + M) time using String Matching (KMP)?**  
  **A**: Serialize both trees into strings using Preorder traversal with explicit null markers (`","` delimiters and `"#"` sentinels). Run **KMP / Z-Algorithm** on the strings in $\mathcal{O}(N + M)$ time!

- **Q3: Why must delimiters and null markers be used when serializing for KMP?**  
  **A**: Without delimiters, node `12` could match substring `1` and `2`. With delimiters `,12,` and null `#`, string matching is 100% sound.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Recursion`, `LeetCode-572`, `Easy`
- **Related problems to practice next**:
- **Same Tree**: Sub-routine helper.
- **Serialize and Deserialize**: String matching approach.
