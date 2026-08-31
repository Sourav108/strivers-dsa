# Morris Preorder Traversal (Threaded Binary Tree O(1) space) (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Morris Preorder Traversal (Threaded Binary Tree O(1) space)](https://takeuforward.org/data-structure/morris-preorder-traversal-of-a-binary-tree/)
- **Difficulty**: Hard
- **Statement**: Given the root of a binary tree, return the Preorder traversal (`Root -> Left -> Right`) of its nodes' values in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ auxiliary space without recursion or stack.

---

## 1. Problem, Restated

Threaded Binary Tree Preorder: visit node on FIRST encounter when creating thread from predecessor.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / integer sum / in-place modified tree.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) If `curr->left == nullptr`: visit `curr->val`, move `curr = curr->right`. 2) If `curr->left != nullptr`: find inorder predecessor `prev = curr->left; while (prev->right && prev->right != curr) prev = prev->right;`. 3) **If `prev->right == nullptr` (First encounter)**: Visit `curr->val` (`preorder.push_back(curr->val)`), create thread `prev->right = curr`, move `curr = curr->left`. 4) **If `prev->right == curr` (Second encounter)**: Cut thread `prev->right = nullptr`, move `curr = curr->right` (do NOT visit node again!).

- **Underlying Pattern**: `Morris Threading with Visit-On-Thread-Creation`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard recursive preorder using $\mathcal{O}(H)$ call stack space.

### C++17 Code
```cpp
// O(H) recursive preorder
```

### Java Code
```java
// Java equivalent
// O(H) recursive preorder
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ stack.
- **Why it's not good enough**: Recursion stack space.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / Prefix Map / Morris algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(1)$ space bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Morris Threaded Preorder in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <vector>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    vector<int> getPreorder(TreeNode* root) {
        vector<int> preorder;
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            if (curr->left == nullptr) {
                // Case 1: No left child -> Visit current node and move Right
                preorder.push_back(curr->val);
                curr = curr->right;
            } else {
                // Case 2: Left child exists -> Find Inorder Predecessor
                TreeNode* prev = curr->left;
                while (prev->right != nullptr && prev->right != curr) {
                    prev = prev->right;
                }
                
                if (prev->right == nullptr) {
                    // FIRST VISIT: Visit root before diving into left subtree!
                    preorder.push_back(curr->val);
                    prev->right = curr; // create thread
                    curr = curr->left;  // dive left
                } else {
                    // SECOND VISIT: Left subtree finished -> Cut thread and move Right
                    prev->right = nullptr;
                    curr = curr->right;
                }
            }
        }
        
        return preorder;
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

    int[] getPreorder(TreeNode  root) {
        int[] preorder;
        TreeNode  curr = root;
        
        while (curr != null) {
            if (curr.left == null) {
                // Case 1: No left child . Visit current node and move Right
                preorder.add(curr.val);
                curr = curr.right;
            } else {
                // Case 2: Left child exists . Find Inorder Predecessor
                TreeNode  prev = curr.left;
                while (prev.right != null && prev.right != curr) {
                    prev = prev.right;
                }
                
                if (prev.right == null) {
                    // FIRST VISIT: Visit root before diving into left subtree!
                    preorder.add(curr.val);
                    prev.right = curr; // create thread
                    curr = curr.left;  // dive left
                } else {
                    // SECOND VISIT: Left subtree finished . Cut thread and move Right
                    prev.right = null;
                    curr = curr.right;
                }
            }
        }
        
        return preorder;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each edge traversed $\le 3$ times).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Root is visited before thread creation, matching `Root -> Left -> Right` preorder invariant.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4, right: 5), right: 3`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = 1` | visit 1! -> thread `5->right = 1` -> curr = 2 | preorder: `[1]` |
| `curr = 2` | visit 2! -> thread `4->right = 2` -> curr = 4 | preorder: `[1, 2]` |
| `curr = 4` | left null -> visit 4! -> follow thread: curr = 2 | preorder: `[1, 2, 4]` |
| `curr = 2 (2nd visit)` | cut thread `4->right = null` -> move right: curr = 5 | thread cut |
| `curr = 5` | left null -> visit 5! -> follow thread: curr = 1 | preorder: `[1, 2, 4, 5]` |
| `curr = 1 (2nd visit)` | cut thread `5->right = null` -> move right: curr = 3 | thread cut |
| `curr = 3` | left null -> visit 3! -> curr = null | Preorder: `[1, 2, 4, 5, 3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Single node.

### Common Bugs to Avoid
- Visiting `curr->val` during the second visit (this produces Inorder instead of Preorder!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: What is the exact difference between Morris Preorder and Morris Inorder?**  
  **A**: In Morris Preorder, `preorder.push_back(curr->val)` happens when `prev->right == nullptr` (on first encounter). In Morris Inorder, `inorder.push_back(curr->val)` happens when `prev->right == curr` (on second encounter) or when `curr->left == nullptr`!

- **Q2: Can Morris Traversal be done on read-only memory?**  
  **A**: No! Morris Traversal requires write access to create temporary thread pointers in `prev->right`. On ROM/read-only hardware, standard stack traversal is required.

- **Q3: How does Morris Traversal restore tree pointers in case of exceptions?**  
  **A**: In production code with exceptions, RAII wrappers or try-catch blocks must guarantee a cleanup pass to remove any lingering threaded pointers.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Morris Traversal`, `Threaded Binary Tree`, `Hard`
- **Related problems to practice next**:
- **Morris Inorder Traversal**: Inorder counterpart.
- **Preorder Traversal**: Standard traversal.
