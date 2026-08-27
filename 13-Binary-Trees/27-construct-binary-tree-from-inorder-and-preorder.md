# Construct Binary Tree from Inorder and Preorder Traversal (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Construct Binary Tree from Inorder and Preorder Traversal](https://takeuforward.org/data-structure/construct-a-binary-tree-from-inorder-and-preorder-traversal/)
- **Difficulty**: Medium
- **Statement**: Given two integer arrays `preorder` and `inorder` where `preorder` is the preorder traversal of a binary tree and `inorder` is the inorder traversal of the same tree, construct and return the binary tree in $\mathcal{O}(N)$ time.

---

## 1. Problem, Restated

Root is `preorder[preStart]`. Find its index in `inorder` via hash map to partition left and right subtrees recursively.

- **Input**: Traversal arrays / Tree pointers / Serialized strings.
- **Output**: Tree root `TreeNode*` / Serialized string / Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Root of current subtree is always `preorder[preStart]`. 2) Look up root's index in `inorder` using `unordered_map<int, int> inMap` $\implies$ `inRoot = inMap[rootVal]`. 3) Number of nodes in left subtree is `numsLeft = inRoot - inStart`. 4) **Left Subtree**: `preorder` range is `[preStart + 1, preStart + numsLeft]`, `inorder` range is `[inStart, inRoot - 1]`. 5) **Right Subtree**: `preorder` range is `[preStart + numsLeft + 1, preEnd]`, `inorder` range is `[inRoot + 1, inEnd]`.

- **Underlying Pattern**: `Divide & Conquer Traversal Reconstruction with Inorder Index Hash Map`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Linear scan of inorder array on every recursive step in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) linear search in inorder
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ time on skewed trees.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Linear search in inorder.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard divide-and-conquer / string parsing / Morris threading algorithm below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Hash Map Inorder Index Lookups in $\mathcal{O}(N)$ time and $\mathcal{O}(N)$ space.

### C++17 Code
```cpp
#include <vector>
#include <unordered_map>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    TreeNode* build(vector<int>& preorder, int preStart, int preEnd,
                    vector<int>& inorder, int inStart, int inEnd,
                    unordered_map<int, int>& inMap) {
        if (preStart > preEnd || inStart > inEnd) {
            return nullptr;
        }
        
        // Root is the first element in current preorder segment
        int rootVal = preorder[preStart];
        TreeNode* root = new TreeNode(rootVal);
        
        // Position of root in inorder array
        int inRoot = inMap[rootVal];
        int numsLeft = inRoot - inStart; // count of nodes in left subtree
        
        // Build left and right subtrees recursively
        root->left = build(preorder, preStart + 1, preStart + numsLeft,
                           inorder, inStart, inRoot - 1, inMap);
        
        root->right = build(preorder, preStart + numsLeft + 1, preEnd,
                            inorder, inRoot + 1, inEnd, inMap);
        
        return root;
    }

public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        unordered_map<int, int> inMap;
        for (size_t i = 0; i < inorder.size(); i++) {
            inMap[inorder[i]] = i;
        }
        
        return build(preorder, 0, preorder.size() - 1,
                     inorder, 0, inorder.size() - 1, inMap);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each node constructed in $\mathcal{O}(1)$ hash map lookup).
- **Space Complexity**: $\mathcal{O}(N)$ hash map and call stack space.
- **Why this is optimal**: Hash map converts $O(N)$ linear subarray search into $O(1)$ constant time lookup.

---

## 6. Dry Run

`preorder = [3, 9, 20, 15, 7]`, `inorder = [9, 3, 15, 20, 7]`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 3` | inRoot = 1, numsLeft = 1-0 = 1 | root->val = 3 |
| `Left Subtree` | pre `[9]`, in `[9]` -> leaf 9 | root->left = 9 |
| `Right Subtree` | pre `[20, 15, 7]`, in `[15, 20, 7]` -> root 20, left 15, right 7 | root->right = 20 |
| `Result` | Tree reconstructed | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node `[1]`, `[1]`.
- Left-skewed tree vs Right-skewed tree.

### Common Bugs to Avoid
- Incorrect index arithmetic on right preorder range `preStart + numsLeft + 1`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does this algorithm require node values to be UNIQUE?**  
  **A**: If duplicate values exist, `inMap` cannot uniquely determine which occurrence corresponds to the current subtree root, causing incorrect subtree splits!

- **Q2: How to handle non-unique duplicate values?**  
  **A**: Without unique values, multiple valid binary trees exist. Finding all of them requires backtracking branch search in $\mathcal{O}(2^N)$ time.

- **Q3: Can this tree be constructed iteratively?**  
  **A**: Yes! Using a stack storing nodes along the current branch and tracking inorder matching with an index pointer.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Divide and Conquer`, `Recursion`, `LeetCode-105`, `Medium`
- **Related problems to practice next**:
- **Construct from Post & In**: Postorder counterpart.
- **Unique Binary Tree**: Requirements.
