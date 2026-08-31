# Preorder Traversal of Binary Tree (Recursive & Iterative) (Step 13.1 — Traversals)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Preorder Traversal of Binary Tree (Recursive & Iterative)](https://takeuforward.org/data-structure/preorder-traversal-of-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, return the preorder traversal of its nodes' values (`Root -> Left -> Right`) both recursively and iteratively using an explicit stack.

---

## 1. Problem, Restated

Process root node, then recursively traverse left subtree, then right subtree.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vector or tree node construction.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Recursive**: `visit(node->val)`, `dfs(node->left)`, `dfs(node->right)`. 2) **Iterative Stack**: Push `root` to stack. Pop `curr = st.top()`, record `curr->val`. Push `curr->right` FIRST, then push `curr->left` SECOND (so LIFO pops left child first on next iteration!). Runs in $\mathcal{O}(N)$ time.

- **Underlying Pattern**: `Root $\to$ Left $\to$ Right DFS Traversal (LIFO Stack: push Right before Left)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard recursive DFS.

### C++17 Code
```cpp
#include <vector>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
void preorderRec(TreeNode* root, vector<int>& ans) {
    if (!root) return;
    ans.push_back(root->val);
    preorderRec(root->left, ans);
    preorderRec(root->right, ans);
}
```

### Java Code
```java
static class TreeNode { int val; TreeNode left, right; };
void preorderRec(TreeNode  root, int[] ans) {
    if (root == null) return;
    ans.add(root.val);
    preorderRec(root.left, ans);
    preorderRec(root.right, ans);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why it's not good enough**: Recursive call stack overhead.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Iterative / Production)

### Idea
Iterative LIFO Stack in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ auxiliary space.

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
public:
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> preorder;
        if (root == nullptr) return preorder;
        
        stack<TreeNode*> st;
        st.push(root);
        
        while (!st.empty()) {
            TreeNode* curr = st.top();
            st.pop();
            
            preorder.push_back(curr->val);
            
            // Push RIGHT child first so that LEFT child is processed first (LIFO)
            if (curr->right != nullptr) {
                st.push(curr->right);
            }
            if (curr->left != nullptr) {
                st.push(curr->left);
            }
        }
        
        return preorder;
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

    int[] preorderTraversal(TreeNode  root) {
        int[] preorder;
        if (root == null) return preorder;
        
        Stack<TreeNode> st = new Stack<>();
        st.push(root);
        
        while (!st.isEmpty()) {
            TreeNode  curr = st.peek();
            st.pop();
            
            preorder.add(curr.val);
            
            // Push RIGHT child first so that LEFT child is processed first (LIFO)
            if (curr.right != null) {
                st.push(curr.right);
            }
            if (curr.left != null) {
                st.push(curr.left);
            }
        }
        
        return preorder;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each node is pushed and popped exactly once).
- **Space Complexity**: $\mathcal{O}(H)$ stack space ($H = \log N$ for balanced, $H = N$ for skewed tree).
- **Why this is optimal**: Explicit stack avoids system stack overflow for deep recursion trees.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4, right: 5), right: 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Push root 1` | st: `[1]` | init |
| `Pop 1` | ans: `[1]`, push 3, push 2 -> st: `[3, 2]` | ans: [1] |
| `Pop 2` | ans: `[1, 2]`, push 5, push 4 -> st: `[3, 5, 4]` | ans: [1, 2] |
| `Pop 4, Pop 5` | ans: `[1, 2, 4, 5]`, st: `[3]` | ans: [1, 2, 4, 5] |
| `Pop 3` | ans: `[1, 2, 4, 5, 3]`, st empty | Preorder Result ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Single node tree `[1]` $\implies$ `[1]`.

### Common Bugs to Avoid
- Pushing `left` before `right` in stack (causes right subtree to be visited before left!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do we push the right child before the left child on the stack?**  
  **A**: Because a stack is a Last-In-First-Out (LIFO) data structure. Pushing the right child first ensures that the left child sits on top of the stack and is popped/processed first on the next iteration.

- **Q2: Can Preorder Traversal be done in O(1) auxiliary space without a stack?**  
  **A**: Yes! Using **Morris Preorder Traversal** with threaded binary trees: link the rightmost node of the left subtree back to the current node, achieving $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ space.

- **Q3: What is the real-world application of preorder traversal?**  
  **A**: Preorder traversal is used to clone/copy trees, serialize trees to disk, and evaluate mathematical prefix expressions.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Stack`, `LeetCode-144`, `Easy`
- **Related problems to practice next**:
- **Inorder Traversal**: Inorder counterpart.
- **Morris Preorder Traversal**: O(1) space.
