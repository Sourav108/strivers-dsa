# Postorder Traversal of Binary Tree (1 Stack & 2 Stacks) (Step 13.1 — Traversals)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Postorder Traversal of Binary Tree (1 Stack & 2 Stacks)](https://takeuforward.org/data-structure/post-order-traversal-of-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given the root of a binary tree, return the postorder traversal of its nodes' values (`Left -> Right -> Root`) using: 1) Standard recursion, 2) 2-Stack Iterative method, and 3) Optimal 1-Stack Iterative method.

---

## 1. Problem, Restated

Traverse left subtree, traverse right subtree, process root. Bottom-up post-processing.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vector or tree node construction.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **2-Stack Method**: Push root to `st1`. Pop `curr`, push to `st2`. Push `curr->left` to `st1`, then `curr->right` to `st1`. At the end, `st2` holds reverse postorder (`Root -> Right -> Left`). Popping `st2` yields `Left -> Right -> Root`! 2) **1-Stack Method**: Dive left. When at leaf, check if right child was already visited (`curr->right == lastVisited`). If so, visit `curr` and pop; else step into right child.

- **Underlying Pattern**: `Left $\to$ Right $\to$ Root DFS Traversal (2-Stack Inversion / 1-Stack Last-Visited Tracking)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard recursive DFS.

### C++17 Code
```cpp
#include <vector>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
void postorderRec(TreeNode* root, vector<int>& ans) {
    if (!root) return;
    postorderRec(root->left, ans);
    postorderRec(root->right, ans);
    ans.push_back(root->val);
}
```

### Java Code
```java
static class TreeNode { int val; TreeNode left, right; };
void postorderRec(TreeNode  root, int[] ans) {
    if (root == null) return;
    postorderRec(root.left, ans);
    postorderRec(root.right, ans);
    ans.add(root.val);
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why it's not good enough**: Recursive stack.

---

## 4. Approach 2 — Better

### Idea
2-Stack Iterative Postorder in O(N) time and O(2N) space.

### C++17 Code
```cpp
#include <vector>
#include <stack>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
vector<int> postorder2Stacks(TreeNode* root) {
    vector<int> ans;
    if (!root) return ans;
    stack<TreeNode*> st1, st2;
    st1.push(root);
    while (!st1.empty()) {
        TreeNode* curr = st1.top(); st1.pop();
        st2.push(curr);
        if (curr->left) st1.push(curr->left);
        if (curr->right) st1.push(curr->right);
    }
    while (!st2.empty()) { ans.push_back(st2.top()->val); st2.pop(); }
    return ans;
}
```

### Java Code
```java
import java.util.*;

static class TreeNode { int val; TreeNode left, right; };
int[] postorder2Stacks(TreeNode  root) {
    List<Integer> ans = new ArrayList<>();
    if (root == null) return ans;
    Stack<TreeNode> st1, st2;
    st1.push(root);
    while (!st1.isEmpty()) {
        TreeNode  curr = st1.peek(); st1.pop();
        st2.push(curr);
        if (curr.left) st1.push(curr.left);
        if (curr.right) st1.push(curr.right);
    }
    while (!st2.isEmpty()) { ans.add(st2.peek().val); st2.pop(); }
    return ans;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(2N)$ space (2 stacks).
- **Why it's still not optimal**: Uses 2 stacks.

---

## 5. Approach 3 — Optimal / Idiomatic C++17 (Iterative / Production)

### Idea
1-Stack Iterative Postorder with `lastVisited` Pointer in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

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
    vector<int> postorderTraversal(TreeNode* root) {
        vector<int> postorder;
        if (root == nullptr) return postorder;
        
        stack<TreeNode*> st;
        TreeNode* curr = root;
        TreeNode* lastVisited = nullptr;
        
        while (curr != nullptr || !st.empty()) {
            if (curr != nullptr) {
                st.push(curr);
                curr = curr->left; // dive left
            } else {
                TreeNode* topNode = st.top();
                
                // If right child exists and has not been processed yet, move right
                if (topNode->right != nullptr && lastVisited != topNode->right) {
                    curr = topNode->right;
                } else {
                    postorder.push_back(topNode->val);
                    lastVisited = topNode;
                    st.pop();
                }
            }
        }
        
        return postorder;
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

    int[] postorderTraversal(TreeNode  root) {
        List<Integer> postorder = new ArrayList<>();
        if (root == null) return postorder;
        
        Stack<TreeNode> st = new Stack<>();
        TreeNode  curr = root;
        TreeNode  lastVisited = null;
        
        while (curr != null || !st.isEmpty()) {
            if (curr != null) {
                st.push(curr);
                curr = curr.left; // dive left
            } else {
                TreeNode  topNode = st.peek();
                
                // If right child exists and has not been processed yet, move right
                if (topNode.right != null && lastVisited != topNode.right) {
                    curr = topNode.right;
                } else {
                    postorder.add(topNode.val);
                    lastVisited = topNode;
                    st.pop();
                }
            }
        }
        
        return postorder;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single pass (each node visited at most twice).
- **Space Complexity**: $\mathcal{O}(H)$ single stack space.
- **Why this is optimal**: 1-Stack algorithm uses half the memory of the 2-stack approach.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4, right: 5), right: 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Dive left to 4` | st: `[1, 2, 4]`, 4 has no right -> visit 4 | ans: `[4]`, last=4 |
| `Back to 2` | 2 has right 5 (not last) -> dive 5 -> visit 5 | ans: `[4, 5]`, last=5 |
| `Back to 2` | 2 right is 5 == last -> visit 2 | ans: `[4, 5, 2]`, last=2 |
| `Back to 1` | 1 right is 3 (not last) -> dive 3 -> visit 3 | ans: `[4, 5, 2, 3]`, last=3 |
| `Back to 1` | 1 right is 3 == last -> visit 1 | Postorder: `[4, 5, 2, 3, 1]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Single node.

### Common Bugs to Avoid
- Re-visiting right child indefinitely in 1-stack without `lastVisited` tracking.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Postorder traversal used in tree deletion and destructor logic?**  
  **A**: Because Postorder visits children BEFORE parent (`Left -> Right -> Root`). In languages like C++, deleting children first ensures we never access deallocated parent memory!

- **Q2: How does 2-stack postorder relate to modified preorder?**  
  **A**: Modified preorder visits `Root -> Right -> Left`. Pushing these elements into a second stack reverses the order to `Left -> Right -> Root`, producing exact Postorder!

- **Q3: What is the real-world application of postorder traversal in compilers?**  
  **A**: Postorder is used in Bottom-Up evaluation of Abstract Syntax Trees (ASTs) and postfix Polish notation arithmetic evaluation.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Stack`, `LeetCode-145`, `Easy`
- **Related problems to practice next**:
- **Inorder Traversal**: Inorder DFS.
- **All Traversals in One Pass**: Single pass.
