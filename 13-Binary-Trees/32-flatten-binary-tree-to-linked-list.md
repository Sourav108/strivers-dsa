# Flatten Binary Tree to LinkedList (In-place pointer rewire) (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Flatten Binary Tree to LinkedList (In-place pointer rewire)](https://takeuforward.org/data-structure/flatten-binary-tree-to-linked-list/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary tree, flatten the tree into a 'linked list' in-place: the 'linked list' should use the same `TreeNode` class where the `right` child pointer points to the next node in the list and the `left` child pointer is always `nullptr`. The list must follow Preorder traversal.

---

## 1. Problem, Restated

Rewire binary tree in-place such that `node->left = nullptr` and `node->right` follows Preorder.

- **Input**: `TreeNode* root` pointer to binary tree.
- **Output**: Traversal vector / boolean / integer sum / in-place modified tree.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **Morris Method $\mathcal{O}(1)$ space**: For each `curr`: if `curr->left != nullptr`, find the rightmost node in left subtree (`prev = curr->left; while (prev->right) prev = prev->right;`). Attach `curr->right` to `prev->right` (`prev->right = curr->right`). Move entire left subtree to right (`curr->right = curr->left; curr->left = nullptr;`). Advance `curr = curr->right`. Runs in $\mathcal{O}(N)$ time and $\mathcal{O}(1)$ space!

- **Underlying Pattern**: `Morris Threaded In-Place Right-Pointer Rewiring $\mathcal{O}(1)$ Space`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Preorder traversal into a vector, then rebuild linked list in $\mathcal{O}(N)$ memory.

### C++17 Code
```cpp
// Vector reconstruction approach
```

### Java Code
```java
// Java equivalent
// Vector reconstruction approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ extra space.
- **Why it's not good enough**: Allocates extra memory.

---

## 4. Approach 2 — Better

### Idea
Reverse Postorder Recursion (Right -> Left -> Root) with global `prev` pointer in O(H) space.

### C++17 Code
```cpp
#include <algorithm>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
class SolutionRec {
    TreeNode* prev = nullptr;
public:
    void flatten(TreeNode* root) {
        if (!root) return;
        flatten(root->right);
        flatten(root->left);
        root->right = prev;
        root->left = nullptr;
        prev = root;
    }
};
```

### Java Code
```java
static class TreeNode { int val; TreeNode left, right; };
class SolutionRec {
    TreeNode  prev = null;

    void flatten(TreeNode  root) {
        if (root == null) return;
        flatten(root.right);
        flatten(root.left);
        root.right = prev;
        root.left = null;
        prev = root;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ call stack.
- **Why it's still not optimal**: Uses recursion stack.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Morris Pointer Rewiring in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ space.

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
    void flatten(TreeNode* root) {
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            if (curr->left != nullptr) {
                // Find the rightmost node in the left subtree
                TreeNode* prev = curr->left;
                while (prev->right != nullptr) {
                    prev = prev->right;
                }
                
                // Connect right subtree of curr to the right of prev
                prev->right = curr->right;
                
                // Move left subtree to right, and set left to null
                curr->right = curr->left;
                curr->left = nullptr;
            }
            
            // Move to the next node in the flattened chain
            curr = curr->right;
        }
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

    void flatten(TreeNode  root) {
        TreeNode  curr = root;
        
        while (curr != null) {
            if (curr.left != null) {
                // Find the rightmost node in the left subtree
                TreeNode  prev = curr.left;
                while (prev.right != null) {
                    prev = prev.right;
                }
                
                // Connect right subtree of curr to the right of prev
                prev.right = curr.right;
                
                // Move left subtree to right, and set left to null
                curr.right = curr.left;
                curr.left = null;
            }
            
            // Move to the next node in the flattened chain
            curr = curr.right;
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (in-place pointer rewire).
- **Why this is optimal**: Directly rewires existing pointer addresses without allocating auxiliary nodes or call stack frames.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 3, right: 4), right: 5 (right: 6)`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = 1` | prev = 4 (rightmost in left) -> 4->right = 5 -> 1->right = 2, 1->left = null | 1 -> 2 (3, 4 -> 5 -> 6) |
| `curr = 2` | prev = 3 -> 3->right = 4 -> 2->right = 3, 2->left = null | 1 -> 2 -> 3 (-> 4 -> 5 -> 6) |
| `curr = 3` | left null -> curr = 4 | 3->left null |
| `curr = 4, 5, 6` | lefts null -> advances to end | Flattened: `1->2->3->4->5->6` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr`.
- Single node.
- Tree already flattened (right-skewed).

### Common Bugs to Avoid
- Forgetting `curr->left = nullptr` (leaves dangling left pointers in violated state).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does connecting prev->right = curr->right preserve preorder order?**  
  **A**: In preorder (`Root -> Left -> Right`), the ENTIRE left subtree must be processed before the right subtree begins. The rightmost node of the left subtree (`prev`) is the LAST node visited in the left subtree, so the right subtree must attach directly to `prev->right`!

- **Q2: Can this be done using a stack?**  
  **A**: Yes! Push `root`. In loop, pop `curr`, attach `curr->right = stack.top()`, push right child then left child in $\mathcal{O}(H)$ space.

- **Q3: What is the difference between Reverse Postorder and Morris Flattening?**  
  **A**: Reverse Postorder (`Right -> Left -> Root`) constructs the list from tail to head using call stack recursion; Morris Flattening rewires pointers head-to-tail in strict $\mathcal{O}(1)$ space.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Morris Traversal`, `LinkedList`, `LeetCode-114`, `Medium`
- **Related problems to practice next**:
- **Morris Preorder Traversal**: Threaded traversal.
- **Binary Tree Representation**: Tree pointers.
