# Inorder Traversal of Binary Tree (Recursive & Iterative) (Step 13.1 — Traversals)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Inorder Traversal of Binary Tree (Recursive & Iterative)](https://takeuforward.org/data-structure/inorder-traversal-of-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, return the inorder traversal of its nodes' values (`Left -> Root -> Right`) both recursively and iteratively using an explicit stack.

---

## 1. Problem, Restated

Traverse left subtree, process root, traverse right subtree. For BSTs, inorder produces sorted ascending order.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vector or tree node construction.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Recursive**: `dfs(node->left)`, `visit(node->val)`, `dfs(node->right)`. 2) **Iterative Stack**: Maintain pointer `curr = root`. While `curr != nullptr` or stack not empty: dive left as far as possible (`st.push(curr); curr = curr->left;`). When `curr == nullptr`, pop `curr = st.top()`, visit `ans.push_back(curr->val)`, then step right `curr = curr->right`.

- **Underlying Pattern**: `Left $\to$ Root $\to$ Right DFS Traversal (Dive Left, Pop & Visit, Step Right)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard recursive DFS.

### C++17 Code
```cpp
#include <vector>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
void inorderRec(TreeNode* root, vector<int>& ans) {
    if (!root) return;
    inorderRec(root->left, ans);
    ans.push_back(root->val);
    inorderRec(root->right, ans);
}
```

### Java Code
```java
static class TreeNode { int val; TreeNode left, right; };
void inorderRec(TreeNode  root, int[] ans) {
    if (root == null) return;
    inorderRec(root.left, ans);
    ans.add(root.val);
    inorderRec(root.right, ans);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ stack.
- **Why it's not good enough**: Recursive call stack.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Iterative / Production)

### Idea
Iterative Dive-Left Stack in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

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
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> inorder;
        stack<TreeNode*> st;
        TreeNode* curr = root;
        
        while (curr != nullptr || !st.empty()) {
            // Dive left as deep as possible
            while (curr != nullptr) {
                st.push(curr);
                curr = curr->left;
            }
            
            // Pop the leftmost node
            curr = st.top();
            st.pop();
            inorder.push_back(curr->val);
            
            // Move to the right subtree
            curr = curr->right;
        }
        
        return inorder;
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

    int[] inorderTraversal(TreeNode  root) {
        int[] inorder;
        Stack<TreeNode> st = new Stack<>();
        TreeNode  curr = root;
        
        while (curr != null || !st.isEmpty()) {
            // Dive left as deep as possible
            while (curr != null) {
                st.push(curr);
                curr = curr.left;
            }
            
            // Pop the leftmost node
            curr = st.peek();
            st.pop();
            inorder.add(curr.val);
            
            // Move to the right subtree
            curr = curr.right;
        }
        
        return inorder;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each node is pushed and popped at most once).
- **Space Complexity**: $\mathcal{O}(H)$ stack space ($H = \text{height}$).
- **Why this is optimal**: Dive-left iterative stack mirrors call-stack unwinding without recursion limits.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4, right: 5), right: 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Dive Left from 1` | st: `[1, 2, 4]`, curr = null | Leftmost reached |
| `Pop 4` | ans: `[4]`, curr = 4->right = null | ans: [4] |
| `Pop 2` | ans: `[4, 2]`, curr = 2->right = 5 | ans: [4, 2] |
| `Dive Left from 5` | st: `[1, 5]`, curr = null -> pop 5: ans: `[4, 2, 5]` | ans: [4, 2, 5] |
| `Pop 1` | ans: `[4, 2, 5, 1]`, curr = 3 -> dive 3 -> pop 3 | Inorder: `[4, 2, 5, 1, 3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Skewed left tree `[3, 2, null, 1]` $\implies$ `[1, 2, 3]`.

### Common Bugs to Avoid
- Loop condition `while (!st.empty())` instead of `while (curr != nullptr || !st.empty())` (fails to start when stack is initially empty).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the unique property of Inorder Traversal on a Binary Search Tree (BST)?**  
  **A**: Inorder Traversal of a valid BST visits nodes in **strictly ascending sorted order**, running in $\mathcal{O}(N)$ time.

- **Q2: Can Inorder Traversal be done in O(1) space?**  
  **A**: Yes! **Morris Inorder Traversal** uses temporary rightmost predecessor pointers to traverse in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ auxiliary space without a stack or recursion.

- **Q3: Why does Inorder traversal alone not uniquely identify a binary tree?**  
  **A**: Because multiple different tree structures can share the exact same inorder traversal. A unique binary tree requires Inorder + Preorder (or Inorder + Postorder).


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Stack`, `LeetCode-94`, `Easy`
- **Related problems to practice next**:
- **Postorder Traversal**: Postorder counterpart.
- **Morris Inorder Traversal**: O(1) space.
