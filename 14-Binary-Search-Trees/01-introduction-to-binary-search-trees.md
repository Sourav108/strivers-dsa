# Introduction to Binary Search Trees & Inorder Invariant (Step 14.1 — Concepts)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Introduction to Binary Search Trees & Inorder Invariant](https://takeuforward.org/data-structure/binary-search-trees/)
- **Difficulty**: Easy
- **Statement**: Explain the defining mathematical properties of a Binary Search Tree (BST), the Inorder Traversal Monotonicity Invariant (strictly ascending order), why balanced BSTs achieve $\mathcal{O}(\log N)$ search, insertion, and deletion, and how degenerate BSTs degrade to $\mathcal{O}(N)$.

---

## 1. Problem, Restated

Comprehensive foundation on BST invariants, logarithmic search pruning, and strict ordering properties.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

For EVERY node $u$ in a BST: all values in the left subtree are strictly $< u\to val$, and all values in the right subtree are strictly $> u\to val$. **Crucial Invariant**: Inorder traversal of a BST produces a **strictly sorted array in ascending order**! By comparing target $X$ with `curr->val`, we discard half of the remaining subtree at each step, achieving $\mathcal{O}(H)$ search time.

- **Underlying Pattern**: `BST Property Invariant (`All Left Subtree Nodes < Root < All Right Subtree Nodes`)`.

---

## 3. Approach 1 — Naive / Full Traversal

### Idea
General binary tree search scanning all $N$ nodes without exploiting BST ordering in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// O(N) general search
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Ignores BST property.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative pointer navigation below directly achieves optimal $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ auxiliary space.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
BST Inorder Verification and Logarithmic Search in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <iostream>
#include <vector>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class BSTDemo {
public:
    // Inorder traversal on BST strictly visits nodes in sorted ascending order
    void printInorder(TreeNode* root) {
        if (!root) return;
        printInorder(root->left);
        cout << root->val << " ";
        printInorder(root->right);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ for search/insert/delete where $H = \log N$ for balanced BST, $H = N$ for skewed BST.
- **Space Complexity**: $\mathcal{O}(1)$ iterative space.
- **Why this is optimal**: Pruning one entire subtree at each step mimics Binary Search on linked pointer nodes.

---

## 6. Dry Run

BST: `8 -> left: 3 (left: 1, right: 6), right: 10 (right: 14)`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 8` | Left subtree `{1, 3, 6} < 8`, Right subtree `{10, 14} > 8` | BST Invariant holds |
| `Node 3` | Left child 1 < 3, Right child 6 > 3 | BST Invariant holds |
| `Inorder Output` | `1 -> 3 -> 6 -> 8 -> 10 -> 14` | Strictly Ascending Sorted ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Duplicate values (standard BSTs forbid duplicates or store duplicate counts in node metadata).
- Skewed BST `1 -> 2 -> 3 -> 4`.

### Common Bugs to Avoid
- Assuming only direct children need to satisfy `left < root < right` (ALL descendants in left subtree must be $< root$, and ALL descendants in right must be $> root$!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is BST search O(H) and NOT always O(log N)?**  
  **A**: Because the height $H$ depends on the insertion order. If elements are inserted in already sorted order ($1, 2, 3, 4$), the BST degenerates into a linear linked list of height $H = N$, degrading search to $\mathcal{O}(N)$! Self-balancing BSTs (AVL, Red-Black Trees) guarantee $H = \mathcal{O}(\log N)$.

- **Q2: How does std::set and std::map in C++ STL maintain O(log N) operations?**  
  **A**: They are implemented internally as **Red-Black Trees** (self-balancing BSTs) which maintain $H \le 2 \log_2(N + 1)$ via color invariants and tree rotations.

- **Q3: Why does Inorder traversal of a BST always yield sorted elements?**  
  **A**: By induction: Inorder visits `Left Subtree -> Root -> Right Subtree`. Since Left $< Root < Right$, the resulting sequence is strictly monotonically increasing.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `Basics`, `Easy`
- **Related problems to practice next**:
- **Search in BST**: Search operation.
- **Check if Valid BST**: Validation.
