# All Traversals in One Pass (Pre, In, Post with Stack) (Step 13.1 — Traversals)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [All Traversals in One Pass (Pre, In, Post with Stack)](https://takeuforward.org/data-structure/preorder-inorder-postorder-in-a-single-traversal/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary tree, return the Preorder, Inorder, and Postorder traversals of its nodes' values in a single traversal pass using a single stack.

---

## 1. Problem, Restated

Compute Preorder, Inorder, and Postorder in a single unified pass using a stack of `{TreeNode*, visit_state}`.

- **Input**: `TreeNode* root` pointer to the root of a binary tree.
- **Output**: Traversal vectors or metric calculation (depth, diameter, path sum).
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Each node in DFS is visited at three distinct milestones: 1) **State 1 (First visit)**: Record to `preorder`, increment `state = 2`, push `left` child if present. 2) **State 2 (Second visit, returning from left)**: Record to `inorder`, increment `state = 3`, push `right` child if present. 3) **State 3 (Third visit, returning from right)**: Record to `postorder`, pop node from stack. Total time is $3N = \mathcal{O}(N)$ using a single stack!

- **Underlying Pattern**: `State-Machine Stack Traversal (State 1: Preorder, State 2: Inorder, State 3: Postorder)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Three independent recursive passes for preorder, inorder, and postorder.

### C++17 Code
```cpp
// Three separate recursive passes taking 3 passes over the tree
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3N) = \mathcal{O}(N)$ time (3 passes).
- **Space Complexity**: $\mathcal{O}(H)$ stack.
- **Why it's not good enough**: Requires 3 separate tree traversals.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative and recursive algorithms below directly achieve optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Single Stack State Machine in $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ auxiliary space.

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
    vector<vector<int>> preInPostTraversal(TreeNode* root) {
        vector<int> pre, in, post;
        if (root == nullptr) return {pre, in, post};
        
        // Stack stores pair of: {TreeNode*, state (1, 2, or 3)}
        stack<pair<TreeNode*, int>> st;
        st.push({root, 1});
        
        while (!st.empty()) {
            auto& [node, state] = st.top();
            
            if (state == 1) {
                // Milestone 1: Preorder (Root)
                pre.push_back(node->val);
                state = 2;
                if (node->left != nullptr) {
                    st.push({node->left, 1});
                }
            } else if (state == 2) {
                // Milestone 2: Inorder (Left finished, visiting Root)
                in.push_back(node->val);
                state = 3;
                if (node->right != nullptr) {
                    st.push({node->right, 1});
                }
            } else {
                // Milestone 3: Postorder (Right finished, finishing Root)
                post.push_back(node->val);
                st.pop();
            }
        }
        
        return {pre, in, post};
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(3N) = \mathcal{O}(N)$ time (each node pushed and transitions through 3 states).
- **Space Complexity**: $\mathcal{O}(H)$ stack space ($H = \text{height}$).
- **Why this is optimal**: Single stack manages all 3 traversal orders synchronously with zero code duplication.

---

## 6. Dry Run

Tree: `1 -> left: 2, right: 3`

| Step | Action / State Change | Result |
|---|---|---|
| `Push (1, 1)` | State 1: pre += 1, state=2 -> push (2, 1) | pre: `[1]` |
| `Top (2, 1)` | State 1: pre += 2, state=2 (no left) | pre: `[1, 2]` |
| `Top (2, 2)` | State 2: in += 2, state=3 (no right) | in: `[2]` |
| `Top (2, 3)` | State 3: post += 2, pop 2 | post: `[2]` |
| `Top (1, 2)` | State 2: in += 1, state=3 -> push (3, 1) | in: `[2, 1]` |
| `Top (3, 1..3)` | Processes 3: pre += 3, in += 3, post += 3, pop 3 | pre: `[1,2,3]`, in: `[2,1,3]`, post: `[2,3,1]` |
| `Top (1, 3)` | State 3: post += 1, pop 1 | All 3 Traversals Complete ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- `root == nullptr` (returns empty lists).
- Single node tree.

### Common Bugs to Avoid
- Popping from stack when state is 1 or 2 instead of mutating state in-place `auto& [node, state] = st.top()`.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is auto& reference essential in auto& [node, state] = st.top()?**  
  **A**: Because we mutate `state` from 1 to 2, and 2 to 3. If captured by value without reference, the state in the actual stack element would never update, creating an infinite loop!

- **Q2: How does this algorithm model Euler Tour traversal?**  
  **A**: The 3 states correspond precisely to the entry, intermediate, and exit timestamps of the node in an Euler Tour / Tree Flattening algorithm.

- **Q3: Can this state machine pattern be used to flatten trees to strings?**  
  **A**: Yes! Emitting parenthesis '(' at state 1 and ')' at state 3 generates canonical tree serialization.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `DFS`, `Stack`, `Medium`
- **Related problems to practice next**:
- **Preorder Traversal**: Single traversal.
- **Inorder Traversal**: Single traversal.
