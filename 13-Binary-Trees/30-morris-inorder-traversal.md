# Morris Inorder Traversal (Threaded Binary Tree O(1) space) (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Morris Inorder Traversal (Threaded Binary Tree O(1) space)](https://takeuforward.org/data-structure/morris-inorder-traversal-of-a-binary-tree/)
- **Difficulty**: Hard
- **Statement**: Given the root of a binary tree, return the Inorder traversal of its nodes' values in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ auxiliary space without using recursion or an explicit stack.

---

## 1. Problem, Restated

Threaded Binary Tree Traversal: create temporary pointer from inorder predecessor's right child to `curr` to backtrack upwards in $\mathcal{O}(1)$ space.

- **Input**: Traversal arrays / Tree pointers / Serialized strings.
- **Output**: Tree root `TreeNode*` / Serialized string / Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) If `curr->left == nullptr`: visit `curr->val`, move `curr = curr->right`. 2) If `curr->left != nullptr`: find the **inorder predecessor** (rightmost node in left subtree: `prev = curr->left; while (prev->right && prev->right != curr) prev = prev->right;`). 3) **If `prev->right == nullptr` (First time visit)**: Create temporary thread `prev->right = curr`, move `curr = curr->left`. 4) **If `prev->right == curr` (Second time visit, returning from left)**: Cut thread `prev->right = nullptr`, visit `curr->val`, move `curr = curr->right`!

- **Underlying Pattern**: `Morris Threaded Predecessor Right-Pointer Link & Cut`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Standard recursive inorder using $\mathcal{O}(H)$ stack space.

### C++17 Code
```cpp
// O(H) recursion
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ stack.
- **Why it's not good enough**: Uses recursion stack memory.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard divide-and-conquer / string parsing / Morris threading algorithm below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Morris Threaded Inorder in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ auxiliary space.

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
    vector<int> getInorder(TreeNode* root) {
        vector<int> inorder;
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            if (curr->left == nullptr) {
                // Case 1: No left child -> Visit current node and move Right
                inorder.push_back(curr->val);
                curr = curr->right;
            } else {
                // Case 2: Left child exists -> Find Inorder Predecessor
                TreeNode* prev = curr->left;
                while (prev->right != nullptr && prev->right != curr) {
                    prev = prev->right;
                }
                
                if (prev->right == nullptr) {
                    // Create temporary thread back to curr
                    prev->right = curr;
                    curr = curr->left; // dive left
                } else {
                    // Thread already exists -> Cut thread, visit curr, move Right
                    prev->right = nullptr;
                    inorder.push_back(curr->val);
                    curr = curr->right;
                }
            }
        }
        
        return inorder;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time (each edge is traversed at most 3 times: find predecessor, traverse, cut thread $\implies \le 3N$ operations).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (zero stack or recursion).
- **Why this is optimal**: Temporary threaded links allow upward parent backtracking without memory allocations, restoring original tree pointers upon exit.

---

## 6. Dry Run

Tree: `1 -> left: 2 (left: 4, right: 5), right: 3`

| Step | Action / State Change | Result |
|---|---|---|
| `curr = 1` | predecessor of 1 is 5 -> create thread `5->right = 1` -> curr = 2 | Thread `5->1` created |
| `curr = 2` | predecessor of 2 is 4 -> create thread `4->right = 2` -> curr = 4 | Thread `4->2` created |
| `curr = 4` | left is null -> visit 4 -> follow thread: curr = 2 | inorder: `[4]` |
| `curr = 2 (second visit)` | predecessor 4 has `prev->right == 2` -> cut thread `4->right = null` -> visit 2 -> curr = 5 | inorder: `[4, 2]` |
| `curr = 5` | left is null -> visit 5 -> follow thread: curr = 1 | inorder: `[4, 2, 5]` |
| `curr = 1 (second visit)` | cut thread `5->right = null` -> visit 1 -> curr = 3 | inorder: `[4, 2, 5, 1]` |
| `curr = 3` | left is null -> visit 3 -> curr = null | Inorder: `[4, 2, 5, 1, 3]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns `[]`).
- Tree is already a linked list.

### Common Bugs to Avoid
- Condition `while (prev->right != nullptr)` without `&& prev->right != curr` (causes infinite loop cycling through the created thread!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is the time complexity O(N) even though we find predecessor in a while loop?**  
  **A**: Every edge in the binary tree is traversed at most 3 times: once to create the thread, once to traverse downward, and once to find predecessor and remove the thread. Total operations $\le 3N = \mathcal{O}(N)$!

- **Q2: Does Morris Traversal modify the tree permanently?**  
  **A**: No! All temporary right-child threads are completely removed during the second visit, leaving the binary tree in its EXACT original structure.

- **Q3: How does Morris Preorder Traversal differ from Morris Inorder?**  
  **A**: In Morris Preorder, we visit `curr->val` on the FIRST time (when creating the thread `prev->right == nullptr`), before diving left!


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Morris Traversal`, `Threaded Binary Tree`, `Hard`
- **Related problems to practice next**:
- **Morris Preorder Traversal**: Preorder counterpart.
- **Inorder Traversal**: Standard traversal.
