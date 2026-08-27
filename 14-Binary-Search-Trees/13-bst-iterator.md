# BST Iterator (O(H) memory iterator for next() & hasNext()) (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [BST Iterator (O(H) memory iterator for next() & hasNext())](https://takeuforward.org/data-structure/bst-iterator/)
- **Difficulty**: Medium
- **Statement**: Implement the `BSTIterator` class that represents an iterator over the in-order traversal of a binary search tree (BST): `BSTIterator(TreeNode* root)` initializes object; `int next()` returns next smallest number in average $\mathcal{O}(1)$ time; `boolean hasNext()` returns `true` if there exists next number in $\mathcal{O}(1)$ time. Memory must be bounded to $\mathcal{O}(H)$.

---

## 1. Problem, Restated

Controlled Inorder Traversal Stack: maintain stack of left spine nodes, popping top on `next()` and diving left on popped node's right child.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean / class methods.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

We do NOT dump all $N$ nodes into an array! 1) In constructor: push `root` and all its left descendants onto stack `pushAll(root)`. 2) `hasNext()`: returns `!st.empty()`. 3) `next()`: pop `topNode = st.top()`. Push all left descendants of `topNode->right` (`pushAll(topNode->right)`). Return `topNode->val`. Memory is strictly bounded to the height $\mathcal{O}(H)$, and amortized time per `next()` is strictly $\mathcal{O}(1)$!

- **Underlying Pattern**: `Controlled Lazy Inorder Stack Iterator ($\\mathcal{O}(H)$ Memory)`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Flatten entire BST into a vector in constructor using $\mathcal{O}(N)$ memory.

### C++17 Code
```cpp
// O(N) full array iterator
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ next, but $\mathcal{O}(N)$ memory.
- **Space Complexity**: $\mathcal{O}(N)$ memory.
- **Why it's not good enough**: Violates $\mathcal{O}(H)$ memory constraint.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard DFS / Iterator / Morris algorithm below directly achieves optimal $\mathcal{O}(N)$ or $\mathcal{O}(H)$ bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Lazy Left-Spine Stack in amortized $\mathcal{O}(1)$ time and strict $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <stack>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class BSTIterator {
private:
    stack<TreeNode*> st;
    
    void pushAll(TreeNode* node) {
        while (node != nullptr) {
            st.push(node);
            node = node->left; // dive left
        }
    }

public:
    BSTIterator(TreeNode* root) {
        pushAll(root);
    }
    
    int next() {
        TreeNode* topNode = st.top();
        st.pop();
        
        // Push all left descendants of the right child
        if (topNode->right != nullptr) {
            pushAll(topNode->right);
        }
        
        return topNode->val;
    }
    
    bool hasNext() {
        return !st.empty();
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ amortized time per `next()` call (across $N$ calls, each node is pushed and popped at most once $\implies N/N = 1$); strictly $\mathcal{O}(1)$ for `hasNext()`.
- **Space Complexity**: $\mathcal{O}(H)$ stack memory ($H = \log N$ for balanced BST).
- **Why this is optimal**: Lazy evaluation only loads nodes along the active path of the tree.

---

## 6. Dry Run

BST: `7 -> left: 3, right: 15 (left: 9, right: 20)`

| Step | Action / State Change | Result |
|---|---|---|
| `Init` | pushAll(7) -> st: `[7, 3]` | size = 2 |
| `next()` | pop 3 (3->right null) -> returns 3, st: `[7]` | returns 3 |
| `next()` | pop 7 -> pushAll(15): pushes 15, 9 -> st: `[15, 9]` | returns 7 |
| `next()` | pop 9 -> returns 9, st: `[15]` | returns 9 |
| `next()` | pop 15 -> pushAll(20): pushes 20 -> returns 15 | returns 15 |
| `next()` | pop 20 -> returns 20, st empty | returns 20 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node tree.
- Right-skewed tree vs Left-skewed tree.

### Common Bugs to Avoid
- Pushing right child directly `st.push(topNode->right)` instead of calling `pushAll(topNode->right)` (misses left descendants of the right child).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is next() amortized O(1) time?**  
  **A**: Over the entire lifetime of iterating all $N$ elements, each node in the tree is pushed onto the stack exactly once and popped exactly once. Total operations for $N$ calls $= 2N \implies$ Average time per call $= 2N / N = \mathcal{O}(1)$!

- **Q2: How to implement a Reverse BST Iterator (for descending order)?**  
  **A**: In `pushAll(node)`, dive RIGHT instead of left: `while (node) { st.push(node); node = node->right; }`. On `next()`, call `pushAll(topNode->left)`!

- **Q3: How does BSTIterator enable solving Two Sum in BST in O(N) time and O(H) space?**  
  **A**: Instantiate one forward iterator (starting from minimum) and one reverse iterator (starting from maximum), and run standard Two-Pointer convergence in $\mathcal{O}(H)$ memory!


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Design`, `Stack`, `Iterator`, `LeetCode-173`, `Medium`
- **Related problems to practice next**:
- **Two Sum in BST**: Dual iterator application.
- **Inorder Traversal**: Iterative inorder.
