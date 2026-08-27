# Requirements to Construct Unique Binary Tree (Inorder + Pre/Post) (Step 13.3 — Hard Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Requirements to Construct Unique Binary Tree (Inorder + Pre/Post)](https://takeuforward.org/data-structure/unique-binary-tree/)
- **Difficulty**: Easy
- **Statement**: Given pair of integer types `a` and `b` representing combinations of traversals (1: Preorder, 2: Inorder, 3: Postorder), determine if it is possible to construct a UNIQUE binary tree from these two traversals.

---

## 1. Problem, Restated

A unique binary tree can ONLY be constructed if INORDER (type 2) is present alongside Preorder (type 1) or Postorder (type 3). Preorder + Postorder CANNOT construct a unique binary tree.

- **Input**: Traversal arrays / Tree pointers / Serialized strings.
- **Output**: Tree root `TreeNode*` / Serialized string / Traversal vector / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Preorder/Postorder tells us the ROOT of the subtree, but cannot tell which nodes belong to the LEFT subtree vs RIGHT subtree. INORDER splits the elements into `[Left Subtree Elements] Root [Right Subtree Elements]`. Without Inorder, we cannot distinguish between a left child and a right child (e.g. Preorder `[1, 2]` and Postorder `[2, 1]` can represent both `1->left(2)` and `1->right(2)`!). Thus Inorder is strictly mandatory for unique reconstruction.

- **Underlying Pattern**: `Traversal Pair Uniqueness Theorem (Inorder Partition Requirement)`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Checking all combinations with branching if-else statements.

### C++17 Code
```cpp
// If-else checks
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$.
- **Space Complexity**: $\mathcal{O}(1)$.
- **Why it's not good enough**: Constant time boolean check.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard divide-and-conquer / string parsing / Morris threading algorithm below directly achieves optimal bounds.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Inorder Presence Verification in strict $\mathcal{O}(1)$ time.

### C++17 Code
```cpp
class Solution {
public:
    bool isPossible(int a, int b) {
        // Traversal types: 1 = Preorder, 2 = Inorder, 3 = Postorder
        // Must contain Inorder (2) and one distinct other traversal (1 or 3)
        if (a == b) return false;
        return (a == 2 || b == 2);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(1)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ space.
- **Why this is optimal**: Strict theoretical necessity of Inorder for binary tree left/right subtree boundaries.

---

## 6. Dry Run

Pairs: `(1, 2)`, `(2, 3)`, `(1, 3)`, `(2, 2)`

| Step | Action / State Change | Result |
|---|---|---|
| `Pair (1, 2) [Pre + In]` | Contains Inorder (2) and distinct -> Returns TRUE | Unique ✅ |
| `Pair (2, 3) [In + Post]` | Contains Inorder (2) and distinct -> Returns TRUE | Unique ✅ |
| `Pair (1, 3) [Pre + Post]` | No Inorder -> Returns FALSE (Ambiguous) | Not Unique ❌ |
| `Pair (2, 2) [In + In]` | Identical types -> Returns FALSE | Invalid ❌ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Same traversal type passed twice `(1, 1)`.
- Full Binary Trees (where Pre+Post CAN construct a unique full binary tree).

### Common Bugs to Avoid
- Assuming Preorder + Postorder can uniquely construct any binary tree.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is Inorder strictly necessary to reconstruct a binary tree?**  
  **A**: Because Preorder tells us WHICH node is the root, but only Inorder tells us HOW MANY nodes belong to the left subtree vs right subtree. The root's index in Inorder splits left and right subtrees!

- **Q2: Can Preorder + Postorder uniquely construct a FULL Binary Tree?**  
  **A**: YES! If the tree is guaranteed to be a **Full Binary Tree** (every node has 0 or 2 children), Preorder + Postorder can uniquely reconstruct the tree!

- **Q3: Can a Binary Search Tree (BST) be uniquely constructed from Preorder alone?**  
  **A**: YES! In a BST, Inorder is simply the sorted version of Preorder. Thus Preorder alone is sufficient to construct a unique BST.


---

## 9. Tags & Related Problems

- **Tags**: `Binary Tree`, `Theory`, `Traversals`, `Easy`
- **Related problems to practice next**:
- **Construct from Pre & In**: Pre+In construction.
- **Construct from Post & In**: Post+In construction.
