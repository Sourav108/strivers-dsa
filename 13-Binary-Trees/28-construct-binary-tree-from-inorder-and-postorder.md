# Construct Binary Tree from Inorder and Postorder Traversal (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Construct Binary Tree from Inorder and Postorder Traversal](https://takeuforward.org/data-structure/construct-binary-tree-from-inorder-and-postorder-traversal/)
- **Difficulty**: Medium
- **Statement**: Given two integer arrays `inorder` and `postorder` where `inorder` is the inorder traversal of a binary tree and `postorder` is the postorder traversal of the same tree, construct and return the binary tree in $\mathcal{O}(N)$ time.

---

## 1. Problem, Restated

Root is `postorder[postEnd]`. Find its index in `inorder` via hash map to partition left and right subtrees recursively.

- **Input**: Traversal arrays / Tree pointers / Serialized strings.
- **Output**: Tree root `TreeNode*` / Serialized string / Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) Root of current subtree is always the LAST element `postorder[postEnd]`. 2) Look up root's index in `inorder` using `unordered_map<int, int> inMap` $\implies$ `inRoot = inMap[rootVal]`. 3) Number of nodes in left subtree is `numsLeft = inRoot - inStart`. 4) **Left Subtree**: `postorder` range is `[postStart, postStart + numsLeft - 1]`, `inorder` range is `[inStart, inRoot - 1]`. 5) **Right Subtree**: `postorder` range is `[postStart + numsLeft, postEnd - 1]`, `inorder` range is `[inRoot + 1, inEnd]`.

- **Underlying Pattern**: `Divide & Conquer Traversal Reconstruction from Tail Root`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Linear scan of inorder array in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) linear scan
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Linear search.

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
    TreeNode* build(vector<int>& postorder, int postStart, int postEnd,
                    vector<int>& inorder, int inStart, int inEnd,
                    unordered_map<int, int>& inMap) {
        if (postStart > postEnd || inStart > inEnd) {
            return nullptr;
        }
        
        // Root is the last element in current postorder segment
        int rootVal = postorder[postEnd];
        TreeNode* root = new TreeNode(rootVal);
        
        // Position of root in inorder array
        int inRoot = inMap[rootVal];
        int numsLeft = inRoot - inStart; // count of nodes in left subtree
        
        // Build left and right subtrees recursively
        root->left = build(postorder, postStart, postStart + numsLeft - 1,
                           inorder, inStart, inRoot - 1, inMap);
        
        root->right = build(postorder, postStart + numsLeft, postEnd - 1,
                            inorder, inRoot + 1, inEnd, inMap);
        
        return root;
    }

public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        unordered_map<int, int> inMap;
        for (size_t i = 0; i < inorder.size(); i++) {
            inMap[inorder[i]] = i;
        }
        
        return build(postorder, 0, postorder.size() - 1,
                     inorder, 0, inorder.size() - 1, inMap);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ hash map and call stack space.
- **Why this is optimal**: Tail root indexing partitions both traversal arrays in constant time per node.

---

## 6. Dry Run

`inorder = [9, 3, 15, 20, 7]`, `postorder = [9, 15, 7, 20, 3]`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 3` | postEnd=4 (val 3) -> inRoot = 1, numsLeft = 1 | root = 3 |
| `Left Subtree` | post `[9]`, in `[9]` -> leaf 9 | root->left = 9 |
| `Right Subtree` | post `[15, 7, 20]`, in `[15, 20, 7]` -> root 20 | root->right = 20 |
| `Result` | Tree reconstructed | Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node `[1]`, `[1]`.
- Right-skewed tree.

### Common Bugs to Avoid
- Using `postEnd` instead of `postEnd - 1` in right recursive call (includes root in child subtree, causing stack overflow!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: How does postorder construction differ from preorder construction?**  
  **A**: In preorder, the root is at `preStart` (front) and left subtree immediately follows. In postorder, the root is at `postEnd` (back), left subtree is at the front, and right subtree sits just before `postEnd`.

- **Q2: Why is numsLeft = inRoot - inStart identical in both algorithms?**  
  **A**: Because the number of nodes in the left subtree is purely determined by the position of the root in the `inorder` array, regardless of whether preorder or postorder is used!

- **Q3: What is the time complexity without a hash map?**  
  **A**: Without a hash map, searching for `inRoot` in `inorder` takes $\mathcal{O}(N)$, degrading total time to $\mathcal{O}(N^2)$ on skewed trees.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Divide and Conquer`, `Recursion`, `LeetCode-106`, `Medium`
- **Related problems to practice next**:
- **Construct from Pre & In**: Preorder counterpart.
