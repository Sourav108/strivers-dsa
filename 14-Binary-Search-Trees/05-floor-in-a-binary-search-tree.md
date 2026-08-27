# Floor in a Binary Search Tree (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Floor in a Binary Search Tree](https://takeuforward.org/data-structure/floor-in-a-binary-search-tree-bst/)
- **Difficulty**: Medium
- **Statement**: Given a BST and a key $X$, find Floor of $X$ in the BST. Floor of $X$ is the greatest node value in the BST which is smaller than or equal to $X$. If no such value exists, return -1.

---

## 1. Problem, Restated

Find largest node value $\le X$: if `curr->val == X` return `X`; if `curr->val < X` record candidate `floor = curr->val` and search right for larger valid values; else search left.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Start with `floor = -1`. While `curr != nullptr`: 1) If `curr->val == X`, exact match $\implies$ return `curr->val`. 2) If `curr->val < X`: `curr->val` is a valid candidate $\implies$ record `floor = curr->val`, and move `curr = curr->right` (to search for an even larger value that is still $\le X$). 3) If `curr->val > X`: `curr->val` is too large $\implies$ move `curr = curr->left`. Returns optimal floor in $\mathcal{O}(H)$ time!

- **Underlying Pattern**: `BST Upper Bound Inversion (`curr->val <= X` $\implies$ Record & Go Right)`.

---

## 3. Approach 1 — Naive / Full Traversal

### Idea
Inorder traversal finding the last element $\le X$ in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// O(N) inorder traversal
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Scans all nodes.

---

## 4. Approach 2 — Better

No intermediate sub-optimal variation — standard iterative pointer navigation below directly achieves optimal $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ auxiliary space.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Iterative BST Binary Search Pruning in $\mathcal{O}(H)$ time and $\mathcal{O}(1)$ space.

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
    int floor(TreeNode* root, int x) {
        int floorVal = -1;
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            if (curr->val == x) {
                return curr->val; // exact match
            }
            
            if (curr->val < x) {
                floorVal = curr->val; // valid candidate (<= x)
                curr = curr->right;   // try to find a larger valid value
            } else {
                curr = curr->left;    // too large, must go left
            }
        }
        
        return floorVal;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ time ($H = \text{height}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Subtree pruning guarantees logarithmic traversal on balanced trees.

---

## 6. Dry Run

BST: `8 -> left: 4 (left: 2, right: 6), right: 12 (left: 10, right: 14)`, Key $X = 7$

| Step | Action / State Change | Result |
|---|---|---|
| `curr = 8` | $8 > 7 \implies$ too large, move left to 4 | floor = -1 |
| `curr = 4` | $4 < 7 \implies$ candidate floor = 4, move right to 6 | floor = 4 |
| `curr = 6` | $6 < 7 \implies$ candidate floor = 6, move right to null | floor = 6 |
| `Result` | Largest value $\le 7$ in BST is 6 | Floor = 6 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $X$ is smaller than minimum element in BST (returns -1).
- Exact match exists.

### Common Bugs to Avoid
- Moving left when `curr->val < x` (left child would only have smaller values, missing larger valid candidates!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do we move RIGHT when curr->val < x?**  
  **A**: Because `curr->val` is already $\le x$, which is valid. Any node in its left subtree will be even SMALLER. To find a closer (larger) valid floor value, we must explore its right subtree!

- **Q2: How does Floor relate to database index lookups?**  
  **A**: B+ Tree range scans and SQL `SELECT MAX(val) WHERE val <= x` queries execute this exact Floor search in $\mathcal{O}(\log N)$ I/O reads.

- **Q3: Can duplicate keys affect Ceil/Floor?**  
  **A**: If duplicates exist, finding an exact match returns immediately, preserving correct semantics.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `Binary Search`, `Medium`
- **Related problems to practice next**:
- **Ceil in BST**: Ceil counterpart.
- **Search in BST**: Exact search.
