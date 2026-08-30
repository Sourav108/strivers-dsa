# Construct a BST from a Preorder Traversal in O(N) (Step 14.2 — Practice Problems)

This is a complete, interview-ready note in C++ and Java following the standard 9-section format.

- **Source**: [Construct a BST from a Preorder Traversal in O(N)](https://takeuforward.org/data-structure/construct-a-bst-from-a-preorder-traversal/)
- **Difficulty**: Medium
- **Statement**: Given an array of integers `preorder`, which represents the preorder traversal of a BST, construct the tree and return its root in strict $\mathcal{O}(N)$ time.

---

## 1. Problem, Restated

Construct BST from preorder in linear $\mathcal{O}(N)$ time using upper bound range limiting (`build(preorder, i, bound)`).

- **Input**: Parameters specified.
- **Output**: Value / Node pointer / boolean / class methods.
- **Constraints**: Standard competitive programming limits.

---

## 2. Intuition & Pattern

Maintain global index `i = 0`. Pass upper bound `bound` to each recursive call: 1) If `i == preorder.size() || preorder[i] > bound`, current node does NOT belong to this subtree $\implies$ return `nullptr`. 2) Create `root = new TreeNode(preorder[i++])`. 3) Left child must be smaller than `root->val` $\implies$ `root->left = build(preorder, i, root->val)`. 4) Right child must be smaller than parent's `bound` $\implies$ `root->right = build(preorder, i, bound)`. Each element is visited at most twice $\implies \mathcal{O}(N)$ total time!

- **Underlying Pattern**: `Preorder Index Progression with Upper Bound Value Constraint`.

---

## 3. Approach 1 — Naive / Brute Force

### Idea
Insert elements one by one into BST using standard insertion in $\mathcal{O}(N^2)$ time.

### C++17 Code
```cpp
// O(N^2) insertion per element
```

### Java Code
```java
// Java equivalent
// O(N^2) insertion per element
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N^2)$ on sorted preorder.
- **Space Complexity**: $\mathcal{O}(H)$.
- **Why it's not good enough**: Quadratic on skewed inputs.

---

## 4. Approach 2 — Better

### Idea
Sort preorder to obtain inorder, then run Preorder+Inorder construction in O(N log N) time.

### C++17 Code
```cpp
// O(N log N) sort + build
```

### Java Code
```java
// Java equivalent
// O(N log N) sort + build
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N \log N)$ time.
- **Space Complexity**: $\mathcal{O}(N)$ space.
- **Why it's still not optimal**: Requires sorting.

---

## 5. Approach 3 — Optimal / Idiomatic C++17

### Idea
Recursive Upper Bound Passing in strict $\mathcal{O}(N)$ time and $\mathcal{O}(H)$ space.

### C++17 Code
```cpp
#include <vector>
#include <climits>
using namespace std;

struct TreeNode {
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};

class Solution {
private:
    TreeNode* build(vector<int>& preorder, int& i, int bound) {
        // Stop if all elements consumed or next element exceeds upper bound
        if (i == (int)preorder.size() || preorder[i] > bound) {
            return nullptr;
        }
        
        TreeNode* root = new TreeNode(preorder[i++]);
        
        // Left subtree must have upper bound equal to current root's value
        root->left = build(preorder, i, root->val);
        // Right subtree inherits the parent's upper bound
        root->right = build(preorder, i, bound);
        
        return root;
    }

public:
    TreeNode* bstFromPreorder(vector<int>& preorder) {
        int i = 0;
        return build(preorder, i, INT_MAX);
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

    TreeNode  build(int[] preorder, int i, int bound) {
        // Stop if all elements consumed or next element exceeds upper bound
        if (i == preorder.length || preorder[i] > bound) {
            return null;
        }
        
        TreeNode  root = new TreeNode(preorder[i++]);
        
        // Left subtree must have upper bound equal to current root's value
        root.left = build(preorder, i, root.val);
        // Right subtree inherits the parent's upper bound
        root.right = build(preorder, i, bound);
        
        return root;
    }

    TreeNode  bstFromPreorder(int[] preorder) {
        int i = 0;
        return build(preorder, i, Integer.MAX_VALUE);
    }
};
```

### Complexity Derivation
- **Time Complexity**: $\mathcal{O}(N)$ strict time (index `i` increments from 0 to $N-1$ exactly once).
- **Space Complexity**: $\mathcal{O}(H)$ call stack space.
- **Why this is optimal**: Upper bound constraint guarantees linear time construction without sorting or searching.

---

## 6. Dry Run

`preorder = [8, 5, 1, 7, 10, 12]`

| Step | Action / State Change | Result |
|---|---|---|
| `Root 8 (bound inf)` | node 8 created, i=1 | root = 8 |
| `Left of 8 (bound 8)` | pre[1]=5 < 8 -> node 5 created, i=2 | left = 5 |
| `Left of 5 (bound 5)` | pre[2]=1 < 5 -> node 1 created, i=3 | 1->left/right null |
| `Right of 5 (bound 8)` | pre[3]=7 < 8 -> node 7 created, i=4 | 5->right = 7 |
| `Right of 8 (bound inf)` | pre[4]=10 < inf -> node 10 created -> right 12 | 8->right = 10 (12) ✅ |

---

## 7. Edge Cases & Common Bugs

### Edge Cases
- Single node `[1]`.
- Strictly decreasing preorder `[4, 3, 2, 1]` (all left children).

### Common Bugs to Avoid
- Passing index `i` by value instead of by reference `int& i` (causes duplicate subtree creations!).

---

## 8. Follow-Up Questions (Interview Style)

- **Q1: Why is only an upper bound needed rather than both lower and upper bounds?**  
  **A**: Because in preorder (`Root -> Left -> Right`), if an element is NOT smaller than `root->val`, we know for certain it must belong to the right subtree (where all elements are already $> root\to val$). Only the upper bound is necessary!

- **Q2: Can this be solved iteratively with a stack?**  
  **A**: Yes! Push `root`. For each element: if smaller than stack top, attach as left child and push. If larger, pop until finding parent where element is smaller than grandparent, attach as right child and push.

- **Q3: Why is Inorder not explicitly needed here unlike general binary trees?**  
  **A**: Because for any BST, Inorder is simply the sorted version of Preorder. The ordering property eliminates the ambiguity that existed in general binary trees!


---

## 9. Tags & Related Problems

- **Tags**: `BST`, `Divide and Conquer`, `Recursion`, `LeetCode-1008`, `Medium`
- **Related problems to practice next**:
- **Construct from Pre & In**: General binary tree.
- **Check if Valid BST**: Range bounds.
