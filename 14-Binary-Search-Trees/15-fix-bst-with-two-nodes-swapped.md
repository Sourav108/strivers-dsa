# Recover BST: Correct BST with Two Nodes Swapped (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Recover BST: Correct BST with Two Nodes Swapped](https://takeuforward.org/data-structure/fix-bst-with-two-nodes-swapped/)
- **Difficulty**: Hard
- **Statement**: You are given the root of a binary search tree (BST), where the values of exactly two nodes of the tree were swapped by mistake. Recover the tree without changing its structure in strict $\mathcal{O}(1)$ auxiliary space.

---

## 1. Problem, Restated

Find the two nodes violating Inorder strictly increasing order: `first` is first `prev` where `prev->val > curr->val`, and `second` is the last `curr` where violation occurs. Swap `first->val` and `second->val`.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean / class methods.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Inorder traversal of a valid BST must be strictly increasing. When two nodes are swapped: 1) **Two Non-Adjacent Nodes Swapped**: Creates TWO inversion anomalies where `prev->val > curr->val` (e.g. `[1, 5, 3, 4, 2, 6]` has inversions $(5,3)$ and $(4,2)$ $\implies$ `first = 5, second = 2`). 2) **Two Adjacent Nodes Swapped**: Creates ONE inversion anomaly (e.g. `[1, 3, 2, 4]` has inversion $(3,2)$ $\implies$ `first = 3, second = 2`). Using **Morris Inorder Traversal**, identify `first`, `middle`, and `last` in strict $\mathcal{O}(1)$ space, then `swap(first->val, last ? last->val : middle->val)`!

- **Underlying Pattern**: `Inorder Inversion Detection (`prev->val > curr->val`) + Morris Traversal $\mathcal{O}(1)$ Space`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Inorder traversal into vector, sort values, and copy sorted values back into tree in $\mathcal{O}(N)$ memory.

### C++17 Code
```cpp
// O(N) vector sort approach
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector.
- **Why it's not good enough**: Fails the $\mathcal{O}(1)$ space constraint.

---

## 4. Approach 2 — Better

### Idea
Recursive Inorder DFS tracking first, middle, last in O(H) stack space.

### C++17 Code
```cpp
struct TreeNode { int val; TreeNode *left, *right; };
class SolutionRec {
    TreeNode *first = nullptr, *prev = nullptr, *middle = nullptr, *last = nullptr;
    void inorder(TreeNode* root) {
        if (!root) return;
        inorder(root->left);
        if (prev && root->val < prev->val) {
            if (!first) { first = prev; middle = root; }
            else last = root;
        }
        prev = root;
        inorder(root->right);
    }
public:
    void recoverTree(TreeNode* root) {
        inorder(root);
        if (first && last) swap(first->val, last->val);
        else if (first && middle) swap(first->val, middle->val);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ stack space.
- **Why it's still not optimal**: Uses recursion stack.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Morris Inorder Traversal Inversion Detection in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ space.

### C++17 Code
```cpp
#include <algorithm>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
public:
    void recoverTree(TreeNode* root) {
        TreeNode* first = nullptr;
        TreeNode* middle = nullptr;
        TreeNode* last = nullptr;
        TreeNode* prev = nullptr;
        
        TreeNode* curr = root;
        
        // Morris Inorder Traversal to detect inversions in O(1) space
        while (curr != nullptr) {
            if (curr->left == nullptr) {
                // Process current node
                if (prev != nullptr && curr->val < prev->val) {
                    if (first == nullptr) {
                        first = prev;
                        middle = curr;
                    } else {
                        last = curr;
                    }
                }
                prev = curr;
                curr = curr->right;
            } else {
                TreeNode* predecessor = curr->left;
                while (predecessor->right != nullptr && predecessor->right != curr) {
                    predecessor = predecessor->right;
                }
                
                if (predecessor->right == nullptr) {
                    predecessor->right = curr; // create thread
                    curr = curr->left;
                } else {
                    predecessor->right = nullptr; // cut thread
                    // Process current node
                    if (prev != nullptr && curr->val < prev->val) {
                        if (first == nullptr) {
                            first = prev;
                            middle = curr;
                        } else {
                            last = curr;
                        }
                    }
                    prev = curr;
                    curr = curr->right;
                }
            }
        }
        
        // Swap values of the two faulty nodes
        if (first != nullptr && last != nullptr) {
            swap(first->val, last->val);   // Non-adjacent swapped
        } else if (first != nullptr && middle != nullptr) {
            swap(first->val, middle->val); // Adjacent swapped
        }
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ single Morris pass.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (zero stack or recursion).
- **Why this is optimal**: Morris threading checks inorder monotonicity in-place without memory allocation.

---

## 6. Dry Run

Tree with swapped nodes: `3 -> left: 1, right: 4 (left: 2)` (Inorder: `[1, 3, 2, 4]`)

| Step | Action / State Change | Result |
|---|---|---|
| `Inorder visit 1` | prev = 1, no violation | valid |
| `Inorder visit 3` | prev = 3, no violation | valid |
| `Inorder visit 2` | $2 < 3 \implies$ violation! first = 3, middle = 2 | Inversion 1 found |
| `Inorder visit 4` | prev = 4, no further violation | last remains null |
| `Swap` | Adjacent swap: `swap(first(3), middle(2))` | Tree Recovered: `[1, 2, 3, 4]` ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Adjacent swapped nodes (only 1 inversion detected).
- Non-adjacent swapped nodes (2 inversions detected).

### Common Bugs to Avoid
- Setting `second = prev` instead of `second = curr` on second inversion.

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why does swapped adjacent nodes produce 1 inversion while non-adjacent produces 2?**  
  **A**: In sorted array $A$, swapping adjacent elements $A[i], A[i+1]$ creates exactly one inversion ($A[i+1] < A[i]$). Swapping non-adjacent elements $A[i], A[j]$ creates an inversion at $i$ ($A[j] < A[i-1]$ or $A[i] > A[i+1]$) AND an inversion at $j$ ($A[j] < A[j-1]$).

- **Q2: Why is middle needed alongside last?**  
  **A**: If the two swapped nodes are adjacent, `last` will remain `nullptr` because there is only one inversion. Swapping `first` with `middle` handles this case correctly!

- **Q3: Does modifying node values violate pointer integrity?**  
  **A**: The problem explicitly asks to recover values without restructuring tree topology. Swapping integer values satisfies all requirements in $\mathcal{O}(1)$ space.


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Morris Traversal`, `LeetCode-99`, `Hard`
- **Related problems to practice next**:
- **Check if Valid BST**: Validation.
- **Morris Inorder Traversal**: Underlying engine.
