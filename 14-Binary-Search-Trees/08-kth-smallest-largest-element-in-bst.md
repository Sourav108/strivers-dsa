# Find K-th Smallest / Largest Element in BST (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ following the standard 9-section format.

- **Source**: [Find K-th Smallest / Largest Element in BST](https://takeuforward.org/data-structure/kth-largest-smallest-element-in-binary-search-tree-bst/)
- **Difficulty**: Medium
- **Statement**: Given the root of a binary search tree and an integer $k$, return the $k^{\text{th}}$ smallest value (1-indexed) of all the values of the nodes in the tree. Also find the $k^{\text{th}}$ largest value.

---

## 1. Problem, Restated

Inorder traversal visits nodes in ascending order: the $k^{\text{th}}$ visited node is the $k^{\text{th}}$ smallest. Reverse Inorder (`Right -> Root -> Left`) finds the $k^{\text{th}}$ largest.

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

1) **K-th Smallest**: Inorder traversal (`Left -> Root -> Right`). Maintain counter `count = 0`. At each visit, `count++`. When `count == k`, record answer and return early! 2) **K-th Largest**: Reverse Inorder traversal (`Right -> Root -> Left`). At each visit, `count++`. When `count == k`, record answer. Alternatively, K-th largest $= (N - k + 1)^{\text{th}}$ smallest! 3) **Morris Traversal** achieves this in strict $\mathcal{O}(1)$ auxiliary space.

- **Underlying Pattern**: `Inorder Traversal Counter / Morris Inorder $\mathcal{O}(1)$ Space`.

---

## 3. Approach 1 — Naive / Recursive

### Idea
Dump full inorder into a vector and return `v[k - 1]` in $\mathcal{O}(N)$ memory.

### C++17 Code
```cpp
// Vector inorder dump
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ vector.
- **Why it's not good enough**: Stores entire tree.

---

## 4. Approach 2 — Better

### Idea
Iterative Inorder with Early Exit in O(H + K) time and O(H) stack.

### C++17 Code
```cpp
#include <stack>
using namespace std;
struct TreeNode { int val; TreeNode *left, *right; };
int kthSmallestIter(TreeNode* root, int k) {
    stack<TreeNode*> st;
    TreeNode* curr = root;
    while (curr || !st.empty()) {
        while (curr) { st.push(curr); curr = curr->left; }
        curr = st.top(); st.pop();
        if (--k == 0) return curr->val;
        curr = curr->right;
    }
    return -1;
}
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(H + K)$ time.
- **Space Complexity**: $\mathcal{O}(H)$ stack.
- **Why it's still not optimal**: Uses recursion/stack memory.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Morris Inorder Traversal in $\mathcal{O}(N)$ time and strict $\mathcal{O}(1)$ auxiliary space.

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
    int kthSmallest(TreeNode* root, int k) {
        int count = 0;
        int result = -1;
        TreeNode* curr = root;
        
        while (curr != nullptr) {
            if (curr->left == nullptr) {
                count++;
                if (count == k) result = curr->val;
                curr = curr->right;
            } else {
                TreeNode* prev = curr->left;
                while (prev->right != nullptr && prev->right != curr) {
                    prev = prev->right;
                }
                
                if (prev->right == nullptr) {
                    prev->right = curr;
                    curr = curr->left;
                } else {
                    prev->right = nullptr;
                    count++;
                    if (count == k) result = curr->val;
                    curr = curr->right;
                }
            }
        }
        
        return result;
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ time.
- **Space Complexity**: $\mathcal{O}(1)$ auxiliary space (Morris threading).
- **Why this is optimal**: Zero stack or heap memory allocations.

---

## 6. Dry Run

BST: `3 -> left: 1 (right: 2), right: 4`, $k = 1$

| Step | Action / State Change | Result |
|---|---|---|
| `Inorder visit 1` | count = 1 == k(1) -> result = 1 | Found! result = 1 |
| `Continue / Terminate` | Morris traversal finishes cleaning threads | Kth Smallest = 1 ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- $k = 1$ (minimum element).
- $k = N$ (maximum element).

### Common Bugs to Avoid
- Breaking early out of Morris Traversal without cleaning up temporary threaded pointers (corrupts tree structure!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why must Morris traversal continue until all threads are removed even after finding the answer?**  
  **A**: Because breaking early leaves temporary right-child thread pointers in the tree, permanently corrupting the binary tree structure into a cyclic graph!

- **Q2: How to support frequent K-th Smallest queries in O(log N) dynamic time?**  
  **A**: Augment each BST node with a `subtreeSize` field (Order Statistic Tree). By comparing $k$ with `left->subtreeSize + 1`, navigate left or right in $\mathcal{O}(\log N)$ without tree traversal!

- **Q3: How to find K-th Largest element?**  
  **A**: Either count total nodes $N$ and search for $(N - k + 1)^{\text{th}}$ smallest, or run Reverse Inorder (`Right -> Root -> Left`).


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Binary Search Tree`, `Morris Traversal`, `LeetCode-230`, `Medium`
- **Related problems to practice next**:
- **Inorder Traversal**: Base traversal.
- **BST Iterator**: Iterative traversal.
