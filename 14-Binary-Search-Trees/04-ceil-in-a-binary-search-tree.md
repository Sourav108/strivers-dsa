# Ceil in a Binary Search Tree (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Ceil in a Binary Search Tree](https://takeuforward.org/data-structure/ceil-in-a-binary-search-tree-bst/)
- **Difficulty**: Medium
- **Statement**: Given a BST and a key $X$, find Ceil of $X$ in the BST. Ceil of $X$ is the smallest node value in the BST which is greater than or equal to $X$. If no such value exists, return -1.

---

## 1. Problem, Restated

Find smallest node value $\ge X$: if `curr->val == X` return `X`; if `curr->val > X` record candidate `ceil = curr->val` and search left for smaller valid values; else search right.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Start with `ceil = -1`. While `curr != nullptr`: 1) If `curr->val == X`, exact match $\implies$ return `curr->val`. 2) If `curr->val > X`: `curr->val` is a valid candidate $\implies$ record `ceil = curr->val`, and move `curr = curr->left` (to search for an even smaller value that is still $\ge X$). 3) If `curr->val < X`: `curr->val` is too small $\implies$ move `curr = curr->right`. Returns optimal ceil in $\mathcal{O}(H)$ time!

- **Underlying Pattern**: `BST Lower Bound Binary Search (`curr->val >= X` $\implies$ Record & Go Left)`.

---

## 3. Approach 1 — Naive / Full Traversal

### Idea
Inorder traversal finding first element $\ge X$ in $\mathcal{O}(N)$ time.

### C++17 Code
```cpp
// O(N) inorder search
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
    int findCeil(TreeNode* root, int input) {
        int ceil = -1;
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            if (curr->val == input) {
                return curr->val; // exact match
            }
            
            if (curr->val > input) {
                ceil = curr->val;  // valid candidate (>= input)
                curr = curr->left; // try to find a smaller valid value
            } else {
                curr = curr->right; // too small, must go right
            }
        }
        
        return ceil;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H)$ time ($H = \text{height}$).
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space.
- **Why this is optimal**: Pruning one subtree at each step matches `std::lower_bound` on BST pointers.

---

## 6. Dry Run

BST: `8 -> left: 4 (left: 2, right: 6), right: 12 (left: 10, right: 14)`, Key $X = 5$

| Step | Action / State Change | Result |
|---|---|---|
| `curr = 8` | $8 > 5 \implies$ candidate ceil = 8, move left to 4 | ceil = 8 |
| `curr = 4` | $4 < 5 \implies$ too small, move right to 6 | ceil = 8 |
| `curr = 6` | $6 > 5 \implies$ candidate ceil = 6, move left to null | ceil = 6 |
| `Result` | Smallest value $\ge 5$ in BST is 6 | Ceil = 6 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $X$ is larger than maximum element in BST (returns -1).
- Exact match exists.

### Common Bugs to Avoid
- Moving right when `curr->val > input` (right child would only have even LARGER values!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why do we move LEFT when curr->val > input?**  
  **A**: Because `curr->val` is already $\ge input$, which is valid. Any node in its right subtree will be even LARGER, which cannot be a better (smaller) ceil. We move left to search for a smaller valid candidate!

- **Q2: How does Ceil relate to std::set::lower_bound in C++?**  
  **A**: `std::set::lower_bound(X)` implements this exact Ceil algorithm on an internal Red-Black Tree in $\mathcal{O}(\log N)$ time.

- **Q3: What is the difference between Ceil and Floor?**  
  **A**: Ceil finds the SMALLEST value $\ge X$ (records on `>` and moves left); Floor finds the LARGEST value $\le X$ (records on `<` and moves right).


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `Binary Search`, `Medium`
- **Related problems to practice next**:
- **Floor in BST**: Floor counterpart.
- **Search in BST**: Exact search.
